---
title: Hledání v zašifrovaném obsahu služby Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Naučte se indexovat a extrahovat text ze šifrovaných dokumentů v Azure Blob Storage s využitím Azure Kognitivní hledání.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: 4bab8def514df21d948d67f3cfba846c43917be2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96530931"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>Indexování šifrovaných objektů BLOB pomocí indexerů objektů BLOB a dovednosti v Azure Kognitivní hledání

V tomto článku se dozvíte, jak pomocí služby [azure kognitivní hledání](search-what-is-azure-search.md) naindexovat dokumenty, které byly dříve zašifrované v rámci [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) pomocí [Azure Key Vault](../key-vault/general/overview.md). V normálním případě indexer nemůže extrahovat obsah ze zašifrovaných souborů, protože nemá přístup k šifrovacímu klíči. Když ale použijete vlastní dovednost [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) a potom [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md), můžete poskytnout řízený přístup k klíči k dešifrování souborů a pak z nich extrahovat obsah. Tím se odemkne možnost indexování těchto dokumentů, aniž by došlo k narušení stavu šifrování vašich uložených dokumentů.

V Azure Blob Storage se od dříve šifrovaných celých dokumentů (nestrukturovaný text), jako je PDF, HTML, DOCX a PPTX, používá tato příručka k provádění následujících úloh:

> [!div class="checklist"]
> * Definujte kanál, který dešifruje dokumenty a extrahuje z nich text.
> * Definujte index pro uložení výstupu.
> * Spusťte kanál pro vytvoření a načtení indexu.
> * Prozkoumejte výsledky pomocí fulltextového vyhledávání a bohatou syntaxí dotazu.

Pokud ještě nemáte předplatné Azure, otevřete si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

V tomto příkladu se předpokládá, že jste už soubory nahráli do Azure Blob Storage a jste je zašifroval v procesu. Pokud potřebujete pomáhat s tím, jak se nahrávají a zašifrují vaše soubory, přečtěte si [Tento kurz](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md) .

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ve stejném předplatném jako Azure kognitivní hledání. V trezoru klíčů musí být povolená **Ochrana před** **příčtením a odstraněním** .
+ [Azure kognitivní hledání](search-create-service-portal.md) na [Fakturovatelné úrovni](search-sku-tier.md#tier-descriptions) (Basic nebo vyšší, v libovolné oblasti)
+ [Funkce Azure Functions](https://azure.microsoft.com/services/functions/)
+ [Desktopová aplikace Postman](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1. vytvoření služeb a shromažďování přihlašovacích údajů

### <a name="set-up-the-custom-skill"></a>Nastavení vlastní dovednosti

V tomto příkladu se používá ukázkový projekt [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile) z úložiště GitHub s [Azure Searchmi dovednostmi](https://github.com/Azure-Samples/azure-search-power-skills) . V této části nasadíte dovednost do funkce Azure Functions, aby ji bylo možné použít v dovednosti. Vestavěný skript nasazení vytvoří prostředek funkce Azure s názvem začínajícím **funkcí psdbf-Function-App-** a nahraje dovednost. Zobrazí se výzva k zadání předplatného a skupiny prostředků. Nezapomeňte vybrat stejné předplatné, ve kterém vaše Azure Key Vault instance žije.

V provozu DecryptBlobFile dovednost přebírá adresu URL a token SAS pro každý objekt BLOB jako vstupy a stahuje stažený a dešifrovaný soubor pomocí odkazu na soubor, který Azure Kognitivní hledání očekává. Odvolání, že DecryptBlobFile potřebuje šifrovací klíč k provedení dešifrování. V rámci nastavení vytvoříte také zásadu přístupu, která uděluje DecryptBlobFile přístup k šifrovacímu klíči v Azure Key Vault.

1. Klikněte na tlačítko **nasadit do Azure** , které se nachází na [úvodní stránce DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/master/Utils/DecryptBlobFile#deployment), ve které se otevře poskytnutá Šablona správce prostředků v Azure Portal.

1. Vyberte **předplatné, kde existuje vaše instance Azure Key Vault** (Tato příručka nebude fungovat, pokud vyberete jiné předplatné) a buď vyberte existující skupinu prostředků, nebo vytvořte novou. (Pokud vytvoříte novou, budete taky muset vybrat oblast, do které se má nasadit).

1. Vyberte **zkontrolovat + vytvořit**, ujistěte se, že souhlasíte s podmínkami, a pak vyberte **vytvořit** a nasaďte funkci Azure Functions.

    ![Šablona ARM na portálu](media/indexing-encrypted-blob-files/arm-template.jpg "Šablona ARM na portálu")

1. Počkejte na dokončení nasazení.

1. Na portálu přejděte na svou instanci Azure Key Vault. [Vytvořte v Azure Key Vault zásady přístupu](../key-vault/general/assign-access-policy-portal.md) , které přidělí klíč k vlastní dovednosti.
 
    1. V části **Nastavení** vyberte **zásady přístupu** a pak vyberte **Přidat zásady přístupu** .
     
       ![Přidání zásad přístupu k trezoru klíčů](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Zásady přístupu trezoru klíčů")

    1. V části **Konfigurovat ze šablony** vyberte **Azure Data Lake Storage nebo Azure Storage**.

    1. Jako objekt zabezpečení vyberte instanci funkce Azure, kterou jste nasadili. Můžete ho vyhledat pomocí předpony prostředků, která se použila k jeho vytvoření v kroku 2, který má výchozí hodnotu předpony **psdbf-Function-App**.

    1. Pro možnost autorizované aplikace nevybírejte vše.
     
        ![Přidání šablony zásad přístupu k trezoru klíčů](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Šablona zásad přístupu trezoru klíčů")

    1. Než budete chtít přidat zásady přístupu, nezapomeňte na stránce zásady přístupu kliknout na **Uložit** .
     
         ![Uložení zásad přístupu do trezoru klíčů](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "Uložit zásady přístupu k trezoru klíčů")

1. Na portálu přejděte na funkci **psdbf-Function-App** a poznamenejte si následující vlastnosti, které budete potřebovat později v průvodci:

    1. Adresa URL funkce, kterou najdete v části **základy** na hlavní stránce pro funkci.
    
        ![Adresa URL funkce](media/indexing-encrypted-blob-files/function-uri.jpg "Kde najít adresu URL funkce Azure")

    1. Kód klíče hostitele, který můžete najít tak, že přejdete na **klíče aplikace**, kliknete na Zobrazit **výchozí** klíč a zkopírujete hodnotu.
     
        ![Kód klíče hostitele funkce](media/indexing-encrypted-blob-files/function-host-key.jpg "Kde najít kód klíče hostitele funkce Azure Functions")

### <a name="cognitive-services"></a>Cognitive Services

Rozšíření AI a dovednosti provádění jsou zajištěny Cognitive Services, včetně Analýza textu a Počítačové zpracování obrazu pro zpracování přirozeného jazyka a obrazu. Pokud by vaším cílem bylo dokončit skutečný prototyp nebo projekt, měli byste v tomto okamžiku zřídit Cognitive Services (ve stejné oblasti jako Azure Kognitivní hledání), abyste ho mohli připojit k operacím indexování.

Pro toto cvičení ale můžete přeskočit zřizování prostředků, protože Azure Kognitivní hledání se může připojit k Cognitive Services na pozadí a poskytnout vám 20 bezplatných transakcí na indexer. Po zpracování 20 dokumentů indexer selže, pokud není k dovednosti připojen klíč Cognitive Services. Pro větší projekty Naplánujte zřizování Cognitive Services na úrovni průběžných plateb. Další informace najdete v tématu věnovaném [připojení Cognitive Services](cognitive-search-attach-cognitive-services.md). Všimněte si, že Cognitive Services klíč je nutný ke spuštění dovednosti s více než 20 dokumenty, i když žádná z vybraných dovedností rozpoznávání se nepřipojí k Cognitive Services (například se zadaným dovednosti, pokud se do něho přidají žádné dovednosti).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Poslední součást je Azure Kognitivní hledání, kterou můžete [vytvořit na portálu](search-create-service-portal.md). K dokončení této příručky můžete použít bezplatnou úroveň. 

Stejně jako u funkce Azure Functions si počkejte, než se shromáždí klíč správce. Když při zahájení strukturování požadavků začnete, budete muset zadat koncový bod a klíč rozhraní API pro správu, který se použije k ověření každého požadavku.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Získání klíčového rozhraní API pro správu a adresy URL pro Azure Kognitivní hledání

1. [Přihlaste se k Azure Portal](https://portal.azure.com/)a na stránce **Přehled** vyhledávací služby Získejte název vaší vyhledávací služby. Název služby můžete potvrdit zkontrolováním adresy URL koncového bodu. Pokud byla adresa URL koncového bodu `https://mydemo.search.windows.net` , název vaší služby by byl `mydemo` .

2. V části **Nastavení**  >  **klíče** Získejte klíč správce s úplnými právy k této službě. Existují dva zaměnitelné klíče správce poskytované pro zajištění kontinuity podnikových služeb pro případ, že byste museli nějakou dobu navrátit. V žádostech o přidání, úpravu a odstranění objektů můžete použít primární nebo sekundární klíč.

   ![Získání názvu služby a klíčů pro správu a dotazy](media/search-get-started-javascript/service-name-and-keys.png)

Všechny požadavky vyžadují klíč rozhraní API-Key v hlavičce všech požadavků odeslaných službě. Platný klíč vytváří vztah důvěryhodnosti na základě jednotlivých požadavků mezi aplikací odesílající požadavek a službu, která ho zpracovává.

## <a name="2---set-up-postman"></a>2 – nastavení post

Nainstalujte a nastavte post.

### <a name="download-and-install-postman"></a>Stáhnout a nainstalovat post

1. Stáhněte [zdrojový kód kolekce po](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json).
1. Vyberte   >  **importovat** soubor a importujte zdrojový kód do metody post.
1. Vyberte kartu **kolekce** a pak klikněte na tlačítko **...** (tři tečky).
1. Vyberte **Upravit**. 
   
   ![Pozálohovací aplikace ukazující navigaci](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "Přejít do nabídky upravit v poli post")
1. V dialogovém okně **Upravit** vyberte kartu **proměnné** . 

Na kartě **proměnné** můžete přidat hodnoty, které účtují swapy při každém výskytu konkrétní proměnné uvnitř dvojitých složených závorek. Například metoda post nahradí symbol `{{admin-key}}` aktuální hodnotou, pro kterou jste nastavili `admin-key` . Po nahrazení se provede náhrada v adresách URL, hlavičkách, textu žádosti atd. 

Pokud chcete získat hodnotu pro `admin-key` , použijte klíč rozhraní API pro správu Azure kognitivní hledání, který jste si poznamenali dříve. Nastavte `search-service-name` na název služby Azure kognitivní hledání, kterou používáte. Nastavte `storage-connection-string` pomocí hodnoty na kartě **přístupové klíče** účtu úložiště a nastavte `storage-container-name` název kontejneru objektů blob na daném účtu úložiště, kde jsou uložené šifrované soubory. Nastavte `function-uri` na adresu URL funkce Azure, kterou jste si poznamenali dříve, a nastavte `function-code` na kód klíče hostitele funkce Azure, který jste si předtím poznamenali. Pro ostatní hodnoty můžete ponechat výchozí hodnoty.

![Karta proměnné aplikace po odeslání](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Okno pro proměnné post")

| Proměnná    | Kde je lze získat |
|-------------|-----------------|
| `admin-key` | Na stránce **klíče** služby Azure kognitivní hledání.  |
| `search-service-name` | Název služby Azure Kognitivní hledání. Adresa URL je `https://{{search-service-name}}.search.windows.net` . |
| `storage-connection-string` | V účtu úložiště na kartě **přístupové klíče** vyberte   >  **připojovací řetězec** klíč1. |
| `storage-container-name` | Název kontejneru objektů blob, který obsahuje šifrované soubory, které mají být indexovány. |
| `function-uri` |  Ve funkci Azure v části **základy** na hlavní stránce. |
| `function-code` | V Azure Functions přejděte na **klíče aplikace**, kliknutím zobrazíte **výchozí** klíč a zkopírujete hodnotu. |
| `api-version` | Nechejte jako **2020-06-30**. |
| `datasource-name` | Ponechte jako **šifrované – objekty blob-DS**. |
| `index-name` | Ponechte jako **šifrované – objekty blob-IDX**. |
| `skillset-name` | Ponechte jako **šifrované – objekty blob – SS**. |
| `indexer-name` | Ponechte jako **šifrované – objekty blob – IXR**. |

### <a name="review-the-request-collection-in-postman"></a>Kontrola kolekce požadavků v poli pro odeslání

Po spuštění této příručky musíte vydat čtyři požadavky HTTP:

- **Vložit požadavek na vytvoření indexu**: Tento index obsahuje data, která Azure kognitivní hledání používá a vrací.
- **Post – požadavek na vytvoření zdroje dat**: Tento zdroj dat připojí vaši službu Azure kognitivní hledání k vašemu účtu úložiště, a proto zašifrované soubory objektů BLOB. 
- **Put dovednosti**: dovednosti určuje vlastní definici dovedností pro funkci Azure, která dešifruje data souboru objektu blob, a [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) pro extrakci textu z každého dokumentu po jeho dešifrování.
- **Vložit požadavek na vytvoření indexeru**: když spustíte indexer, načte data, použije dovednosti a výsledky uloží. Tuto žádost musíte spustit jako poslední.

[Zdrojový kód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json) obsahuje kolekci post, která obsahuje čtyři požadavky, a také některé užitečné následné požadavky. Chcete-li vydat požadavky, vyberte v poli post kartu požadavků a pro každou z nich vyberte **Odeslat** .

## <a name="3---monitor-indexing"></a>3. monitorování indexování

Indexování a obohacení začne ihned po odeslání žádosti o vytvoření indexeru. V závislosti na tom, kolik dokumentů je ve vašem účtu úložiště, může indexování chvíli trvat. Chcete-li zjistit, zda je indexer stále spuštěn, použijte požadavek **Get indexer status** , který je součástí kolekce post, a přečtěte si odpověď, kde se dozvíte, zda je indexer spuštěný, nebo zda chcete zobrazit informace o chybě a upozornění.  

Pokud používáte úroveň Free, očekává se následující zpráva: `"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"` . Tato zpráva se zobrazí, protože indexování objektů blob na bezplatné úrovni má [pro extrakci znaků limit 32 znaků](search-limits-quotas-capacity.md#indexer-limits). Tato zpráva se pro tuto sadu dat nezobrazuje na vyšších úrovních. 

## <a name="4---search"></a>4 – hledání

Po dokončení provádění indexeru můžete spustit některé dotazy, abyste ověřili, že se data úspěšně dešifrují a indexují. Přejděte do služby Azure Kognitivní hledání na portálu a pomocí [Průzkumníka služby Search](search-explorer.md) spusťte dotazy na indexovaná data.

## <a name="next-steps"></a>Další kroky

Teď, když máte úspěšně indexované šifrované soubory, můžete [iterovat v tomto kanálu přidáním dalších dovedností rozpoznávání](cognitive-search-defining-skillset.md). To vám umožní rozšířit a získat další přehledy o datech.

Pokud pracujete se dvěma šifrovanými daty, možná budete chtít prozkoumat funkce šifrování indexu dostupné v Azure Kognitivní hledání. I když indexer potřebuje dešifrovaná data pro účely indexování, jakmile index existuje, dá se šifrovat pomocí klíče spravovaného zákazníkem. Tím zajistíte, že vaše data budou vždy šifrována, pokud jsou v klidovém stavu. Další informace najdete v tématu [konfigurace klíčů spravovaných zákazníkem pro šifrování dat v Azure kognitivní hledání](search-security-manage-encryption-keys.md).
---
title: Vytváření požadavků s využitím Postmana
titleSuffix: Azure Digital Twins
description: Naučte se konfigurovat a používat metodu post pro testování rozhraní API pro digitální vlákna Azure.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 5dbe161af172b65919328fca0b272f3b658f1b32
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600485"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Jak používat metodu POST k posílání žádostí do rozhraní API digitálních vláken Azure

[Držitelem je nástroj](https://www.getpostman.com/) pro testování REST, který poskytuje funkce pro požadavky na klíč http v grafickém uživatelském rozhraní (GUI) a v grafickém rozhraní založeném na modulu Můžete ji použít k vytvoření požadavků HTTP a jejich odeslání do [rozhraní REST API pro digitální vlákna Azure](how-to-use-apis-sdks.md).

Tento článek popisuje, jak nakonfigurovat [klienta post REST](https://www.getpostman.com/) pro interakci s rozhraními API digitálních vláken Azure pomocí následujících kroků:

1. Pomocí [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) Získejte nosný token, který budete používat k provádění požadavků rozhraní API v post.
1. Nastavte si kolekci post a nakonfigurujte klienta post REST, aby k ověřování používal váš nosný token.
1. Použijte nakonfigurovaného Poster k vytvoření a odeslání žádosti do rozhraní API digitálních vláken Azure.

## <a name="prerequisites"></a>Požadavky

Pokud chcete pokračovat v používání metody post pro přístup k rozhraním API digitálních vláken Azure, musíte nastavit instanci digitálních vláken Azure a stáhnout post. Zbytek této části vás provede následujícími kroky.

### <a name="set-up-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Stáhnout příspěvek

Dále si stáhněte desktopovou verzi klienta. Přejděte na [*www.getpostman.com/apps*](https://www.getpostman.com/apps) a postupujte podle pokynů ke stažení aplikace.

## <a name="get-bearer-token"></a>Získat nosný token

Teď, když jste nastavili post a instanci digitálních vláken Azure, budete muset získat nosný token, který můžou žádosti POST použít k autorizaci s rozhraními API digitálních vláken Azure.

Existuje několik možných způsobů, jak tento token získat. Tento článek používá rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) k přihlášení k účtu Azure a získání tokenu tímto způsobem.

Pokud máte rozhraní příkazového řádku Azure [nainstalované lokálně](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), můžete na svém počítači spustit příkazový řádek a spustit následující příkazy.
V opačném případě můžete otevřít okno [Azure Cloud Shell](https://shell.azure.com) v prohlížeči a spustit příkazy tam.

1. Nejdřív se ujistěte, že jste se k Azure přihlásili pomocí příslušných přihlašovacích údajů, a to spuštěním tohoto příkazu:

    ```azurecli-interactive
    az login
    ```

1. V dalším kroku pomocí příkazu [AZ Account Get-Access-token](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token) Získejte nosný token s přístupem ke službě digitálního vlákna Azure.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Zkopírujte hodnotu `accessToken` ve výsledku a uložte ji pro použití v další části. Toto je **hodnota tokenu** , kterou poskytnete pro odeslání žádosti.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Zobrazení okna místní konzoly znázorňující výsledek příkazu AZ Account Get-Access-token Jedno z polí ve výsledku se nazývá accessToken a jeho Ukázková hodnota--začíná na enalezen--je zvýrazněna.":::

>[!TIP]
>Tento token je platný aspoň pět minut a maximálně 60 minut. V případě, že jste nedostali čas přidělený aktuálnímu tokenu, můžete postupovat podle kroků v této části a získat nový.

## <a name="set-up-postman-collection-and-authorization"></a>Nastavení následného shromažďování a autorizace

Dále nastavte post pro vytváření požadavků rozhraní API.
K těmto krokům dochází ve vaší místní aplikaci, takže pokračujte a otevřete na svém počítači aplikaci po odeslání.

### <a name="create-a-postman-collection"></a>Vytvoření kolekce Postman

Žádosti v části post jsou uloženy v **kolekcích** (skupiny požadavků). Když vytvoříte kolekci, která bude seskupovat vaše požadavky, můžete použít společná nastavení na mnoho požadavků najednou. To může výrazně zjednodušit autorizaci, pokud plánujete vytvořit více než jeden požadavek na rozhraní API digitálních vláken Azure, protože pro celou kolekci stačí pouze nakonfigurovat ověřování.

1. Chcete-li vytvořit kolekci, stiskněte tlačítko *+ Nová kolekce* .

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Zobrazení nově otevřeného okna po otevření. Tlačítko Nová kolekce je zvýrazněné.":::

1. V následujícím okně *vytvořit novou kolekci* zadejte **název** a volitelný **Popis** kolekce.

Dále pokračujte k další části a přidejte do kolekce nosný token pro autorizaci.

### <a name="add-authorization-token-and-finish-collection"></a>Přidat autorizační token a dokončit shromažďování

1. V dialogovém okně *vytvořit novou kolekci* přejděte na kartu *autorizace* . Místo toho umístíte **hodnotu tokenu** , kterou jste shromáždili v sekci [získat nosný token](#get-bearer-token) , abyste ji mohli použít pro všechny požadavky rozhraní API v kolekci.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="Okno pro vytvoření nové kolekce s vyznačením karty autorizace.":::

1. Nastavte *typ* na _**OAuth 2,0**_ a vložte svůj přístupový token do pole *přístupový token* .

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="Okno pro vytvoření nové kolekce s vyznačením karty autorizace. Je vybraný typ OAuth 2,0 a pole přístupového tokenu, ve kterém se dá vložit hodnota přístupového tokenu, se zvýrazní.":::

1. Po vložení do svého tokenu nosiče se *vytvoří vytvoření* a dokončí se vytváření kolekce.

Vaše nová kolekce se teď může zobrazit v hlavním zobrazení pro publikování v části *kolekce*.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Zobrazení hlavního okna po odeslání. Nově vytvořená kolekce je na kartě kolekce zvýrazněna.":::

## <a name="create-a-request"></a>Vytvoření žádosti

Po dokončení předchozích kroků můžete vytvořit požadavky na digitální vlákna Azure s použitím digitálních vláken.

1. Pokud chcete vytvořit žádost, stiskněte tlačítko *+ Nový* .

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Zobrazení hlavního okna po odeslání. Tlačítko Nový je zvýrazněné.":::

1. Vyberte *požadavek*.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Podívejte se na možnosti, které můžete vybrat a vytvořit něco nového. Možnost Request je zvýrazněna.":::

1. Tato akce otevře okno *Uložit žádost* , kde můžete zadat název žádosti, dát mu volitelný popis a vybrat kolekci, do které je součást. Vyplňte podrobnosti a uložte požadavek do kolekce, kterou jste vytvořili dříve.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Zobrazte okno Uložit požadavek, kde můžete vyplnit popsaná pole. Je zvýrazněné tlačítko Uložit do kolekce digitálních vláken Azure":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Teď si můžete svůj požadavek zobrazit v kolekci a vybrat ho, abyste si vyžádali jeho upravitelné podrobnosti.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Zobrazení hlavního okna po odeslání. Kolekce digitálních vláken Azure je rozbalená a zvýrazní se požadavek &quot;vyřízení dotazů&quot;. Podrobnosti žádosti se zobrazí ve středu stránky." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Nastavit podrobnosti žádosti

Pokud chcete poslat žádost o odeslání do jedné z rozhraní API digitálních vláken Azure, budete potřebovat adresu URL rozhraní API a informace o tom, jaké podrobnosti vyžaduje. Tyto informace najdete v [referenční dokumentaci ke službě Azure Digital Revlákens REST API](/rest/api/azure-digitaltwins/).

Chcete-li pokračovat s příkladem dotazu, bude tento článek používat rozhraní API pro dotazy (a jeho [referenční dokumentaci](/rest/api/digital-twins/dataplane/query/querytwins)) k dotazování na všechny digitální vlákna v instanci.

1. Získejte adresu URL a typ žádosti z referenční dokumentace. Pro rozhraní API pro dotazy je to *post `https://digitaltwins-name.digitaltwins.azure.net/query?api-version=2020-10-31`*.
1. V poli PSČ nastavte typ pro požadavek a zadejte adresu URL požadavku, podle potřeby vyplňte zástupné symboly v adrese URL. Tady budete používat **název hostitele** vaší instance z oddílu [*požadavky*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="V podrobnostech nové žádosti byla do pole Adresa URL žádosti vložena adresa URL dotazu z referenční dokumentace." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Ověřte, že parametry zobrazené pro požadavek na kartě *params* se shodují s hodnotami popsanými v referenční dokumentaci. Pro tento požadavek v poli `api-version` byl parametr automaticky vyplněn při zadání adresy URL požadavku v předchozím kroku. V případě rozhraní API pro dotazy se jedná o jediný požadovaný parametr, proto je tento krok hotový.
1. Na kartě *autorizace* nastavte *typ* pro *dědění ověřování z nadřazené položky*. To znamená, že tento požadavek bude používat ověřování, které jste nastavili dříve pro celou kolekci.
1. Ověřte, že hlavičky zobrazené pro požadavek na kartě *hlavičky* odpovídají názvům popsaným v referenční dokumentaci. Pro tento požadavek byly automaticky vyplněny několik záhlaví. V případě rozhraní API pro dotazy není nutné provádět žádné možnosti záhlaví, proto je tento krok hotový.
1. Ověřte, že text zobrazený pro požadavek na kartě *tělo* odpovídá potřebám popsaným v referenční dokumentaci. Pro rozhraní API pro dotazy je k zadání textu dotazu potřeba tělo JSON. Tady je příklad těla této žádosti, který se dotazuje na všechny digitální vlákna v instanci:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="V podrobnostech o nové žádosti se zobrazí karta tělo. Obsahuje nezpracované tělo JSON s dotazem SELECT * FROM DIGITALTWINS."lightbox="media/how-to-use-postman/postman-request-body.png":::

   Další informace o tom, jak vytvářet dotazy digitálních vláken Azure, najdete v tématu [*Postup: dotazování na dvojitou graf*](how-to-query-graph.md).

1. Podívejte se na referenční dokumentaci pro všechna další pole, která se můžou vyžadovat pro váš typ požadavku. V případě rozhraní API pro dotazování jsou nyní všechny požadavky splněny v žádosti post, takže tento krok je dokončen.
1. K odeslání dokončené žádosti použijte tlačítko *Odeslat* .
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="U podrobností o nové žádosti se zvýrazní tlačítko Odeslat." lightbox="media/how-to-use-postman/postman-request-send.png":::

Po odeslání žádosti se v okně po žádosti zobrazí podrobnosti o odpovědi. Můžete zobrazit stavový kód odpovědi a jakýkoliv hlavní text.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Podrobnosti o odpovědi jsou zvýrazněné pod podrobnostmi o odeslaném požadavku. Stav je 200 OK a text popisující digitální vlákna, která byla vrácena dotazem." lightbox="media/how-to-use-postman/postman-request-response.png":::

Můžete také porovnat odpověď na očekávaná data odpovědi, která jsou uvedena v referenční dokumentaci, a ověřit výsledek nebo se dozvědět více o chybách, ke kterým dojde.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních API digitálních vláken najdete v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)nebo zobrazení [Referenční dokumentace pro rozhraní REST API](/rest/api/azure-digitaltwins/).
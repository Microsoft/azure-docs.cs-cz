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
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783808"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Jak používat metodu POST k posílání žádostí do rozhraní API digitálních vláken Azure

[Držitelem je nástroj](https://www.getpostman.com/) pro testování REST, který poskytuje funkce pro požadavky na klíč http v grafickém uživatelském rozhraní (GUI) a v grafickém rozhraní založeném na modulu Můžete ji použít k vytvoření požadavků HTTP a jejich odeslání do [rozhraní REST API pro digitální vlákna Azure](how-to-use-apis-sdks.md).

Tento článek popisuje, jak nakonfigurovat [klienta post REST](https://www.getpostman.com/) pro interakci s rozhraními API digitálních vláken Azure pomocí následujících kroků:

1. Pomocí Azure CLI [**Získejte nosný token**](#get-bearer-token) , který budete používat k provádění požadavků rozhraní API v post.
1. Nastavte si [**kolekci post**](#about-postman-collections) a nakonfigurujte klienta post REST, aby k ověřování používal váš nosný token. Při nastavování kolekce můžete zvolit jednu z těchto možností:
    1. [**Importujte**](#import-collection-of-azure-digital-twins-apis) předem sestavenou kolekci požadavků rozhraní API digitálních vláken Azure.
    1. [**Vytvořte**](#create-your-own-collection) si vlastní kolekci od začátku.
1. [**Přidejte požadavky**](#add-an-individual-request) do nakonfigurované kolekce a odešlete je do rozhraní API digitálních vláken Azure.

Digitální vlákna Azure mají dvě sady rozhraní API, se kterými můžete pracovat: **rovina dat** a **rovina ovládacího prvku**. Další informace o rozdílu mezi těmito sadami rozhraní API naleznete v tématu [*How to: use the Azure Digital Revlákens API and SDKs*](how-to-use-apis-sdks.md). Tento článek obsahuje informace pro obě sady rozhraní API.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete pokračovat v používání metody post pro přístup k rozhraním API digitálních vláken Azure, musíte nastavit instanci digitálních vláken Azure a stáhnout post. Zbytek této části vás provede následujícími kroky.

### <a name="set-up-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Stáhnout příspěvek

Dále si stáhněte desktopovou verzi klienta. Přejděte na [*www.getpostman.com/apps*](https://www.getpostman.com/apps) a postupujte podle pokynů ke stažení aplikace.

## <a name="get-bearer-token"></a>Získat nosný token

Teď, když jste nastavili post a instanci digitálních vláken Azure, budete muset získat nosný token, který můžou žádosti POST použít k autorizaci s rozhraními API digitálních vláken Azure.

Existuje několik možných způsobů, jak tento token získat. Tento článek používá rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) k přihlášení k účtu Azure a získání tokenu tímto způsobem.

Pokud máte rozhraní příkazového řádku Azure [nainstalované lokálně](/cli/azure/install-azure-cli), můžete na svém počítači spustit příkazový řádek a spustit následující příkazy.
V opačném případě můžete otevřít okno [Azure Cloud Shell](https://shell.azure.com) v prohlížeči a spustit příkazy tam.

1. Nejdřív se ujistěte, že jste se k Azure přihlásili pomocí příslušných přihlašovacích údajů, a to spuštěním tohoto příkazu:

    ```azurecli-interactive
    az login
    ```

2. V dalším kroku pomocí příkazu [AZ Account Get-Access-token](/cli/azure/account#az_account_get_access_token) Získejte nosný token s přístupem ke službě digitálního vlákna Azure. V tomto příkazu předáte ID prostředku koncového bodu služby Azure Digital Service, aby bylo možné získat přístupový token, který má přístup k prostředkům Azure Digital probíhat. 

    Požadovaný kontext pro token závisí na tom, kterou sadu rozhraní API používáte. proto pomocí karet níže můžete vybrat mezi [rovinou dat](how-to-use-apis-sdks.md#overview-data-plane-apis) a rozhraními API [řídicí plochy](how-to-use-apis-sdks.md#overview-control-plane-apis) .

    # <a name="data-plane"></a>[Rovina dat](#tab/data-plane)
    
    Pokud chcete získat token pro použití s rozhraními API **roviny dat** , použijte pro kontext tokenu tuto statickou hodnotu: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Toto je ID prostředku pro koncový bod služby Azure Digital Service.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Řídicí rovina](#tab/control-plane)
    
    Chcete-li získat token pro použití s rozhraními API **řídicí plochy** , použijte následující hodnotu pro kontext tokenu: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. Zkopírujte hodnotu `accessToken` ve výsledku a uložte ji pro použití v další části. Toto je **hodnota tokenu** , kterou poskytnete, aby byla vaše žádost autorizována.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Snímek obrazovky konzoly znázorňující výsledek příkazu AZ Account Get-Access-token Zvýrazní se pole accessToken a jeho Ukázková hodnota.":::

>[!TIP]
>Tento token je platný aspoň pět minut a maximálně 60 minut. V případě, že jste nedostali čas přidělený aktuálnímu tokenu, můžete postupovat podle kroků v této části a získat nový.

V dalším kroku nastavíte post pro použití tohoto tokenu k tomu, aby se požadavky rozhraní API využívaly u digitálních vláken Azure.

## <a name="about-postman-collections"></a>O kolekcích po

Žádosti v části post jsou uloženy v **kolekcích** (skupiny požadavků). Když vytvoříte kolekci, která bude seskupovat vaše požadavky, můžete použít společná nastavení na mnoho požadavků najednou. To může výrazně zjednodušit autorizaci, pokud máte v plánu vytvořit více než jednu žádost s rozhraními API digitálních vláken Azure, protože tyto podrobnosti stačí pro celou kolekci nakonfigurovat jenom jednou.

Při práci s digitálními podmnožinami Azure můžete začít tím, že naimportujete [předem sestavenou kolekci všech požadavků digitálních vláken Azure](#import-collection-of-azure-digital-twins-apis). To můžete chtít udělat, pokud zkoumáte rozhraní API a chcete rychle vytvořit projekt s příklady požadavků.

Alternativně můžete také zvolit, že se má začít od začátku, a to [vytvořením vlastní prázdné kolekce](#create-your-own-collection) a naplněním jednotlivých požadavků, které volají pouze rozhraní API, která potřebujete. 

Následující části popisují oba tyto procesy. Zbývající část tohoto článku se provádí v místní aplikaci pro odesílání, takže pokračujte a v počítači teď otevřete aplikaci pro publikování.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Import kolekce rozhraní API digitálních vláken Azure

Rychlý způsob, jak začít s digitálními podprocesy Azure pomocí služby post, je importovat předem sestavenou kolekci žádostí pro rozhraní API digitálních vláken Azure.

### <a name="download-the-collection-file"></a>Stažení souboru kolekce

Prvním krokem při importu sady rozhraní API je stažení kolekce. Zvolte kartu níže pro výběr roviny dat nebo plochy ovládacího prvku, abyste viděli předem připravené možnosti shromažďování.

# <a name="data-plane"></a>[Rovina dat](#tab/data-plane)

V současné době jsou k dispozici dvě kolekce rovin dat digitálních vláken Azure, ze kterých si můžete vybrat:
* [**Kolekce post-The Azure Digital**](https://github.com/microsoft/azure-digital-twins-postman-samples)prokážed: Tato kolekce poskytuje jednoduché prostředí pro zahájení digitálních vláken Azure v příspěvku. Žádosti obsahují ukázková data, takže je můžete spustit s minimálními nároky nutnými pro úpravy. Tuto kolekci vyberte, pokud chcete, aby digestible sada klíčových požadavků rozhraní API, které obsahují ukázkové informace.
    - Chcete-li najít kolekci, přejděte na odkaz úložiště a otevřete soubor s názvem *postman_collection.jsv*.
* Rozhraní **[Swagger pro datové plochy v Azure](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: Toto úložiště obsahuje úplný soubor Swagger pro sadu rozhraní API digitálních vláken Azure, kterou je možné stáhnout a importovat do služby post jako kolekce. Tím se poskytne komplexní sada všech požadavků rozhraní API, ale s prázdnými datovými texty namísto ukázkových dat. Tuto kolekci vyberte, pokud chcete mít přístup ke každému volání rozhraní API a vyplnit všechna data sami.
    - Chcete-li najít kolekci, přejděte na odkaz úložiště a vyberte složku pro nejnovější verzi specifikace. Odsud otevřete soubor s názvem *digitaltwins.js*.

# <a name="control-plane"></a>[Řídicí rovina](#tab/control-plane)

Kolekce, která je aktuálně dostupná pro řídicí plochu, je sada [**Swagger plochy pro řízení digitálního vlákna**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) Toto úložiště obsahuje úplný soubor Swagger pro sadu rozhraní API digitálních vláken Azure, kterou je možné stáhnout a naimportovat do služby post jako kolekce. Tím se poskytne komplexní sada všech požadavků rozhraní API.

Chcete-li najít kolekci, přejděte na odkaz úložiště a vyberte složku pro nejnovější verzi specifikace. Odsud otevřete soubor s názvem *digitaltwins.js*.

---

Tady je postup, jak si na svůj počítač stáhnout zvolenou kolekci, abyste ji mohli naimportovat do služby post.
1. Pomocí výše uvedených odkazů otevřete soubor kolekce v GitHubu v prohlížeči.
1. Kliknutím na tlačítko **nezpracované** otevřete nezpracovaný text souboru.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Snímek obrazovky digitaltwins.jsroviny dat v souboru na GitHubu Kolem nezpracovaného tlačítka se zvýrazní." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Zkopírujte text z okna a vložte ho do nového souboru na vašem počítači.
1. Uložte soubor s příponou *. JSON* (název souboru může být libovolný, který chcete, pokud si ho zapamatujete, abyste soubor našli později).

### <a name="import-the-collection"></a>Import kolekce

Pak importujte kolekci do metody post.

1. V hlavním okně po výběru vyberte tlačítko **Import** .
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Snímek obrazovky s nově otevřeným oknem po otevření. Tlačítko Import je zvýrazněné." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. V následujícím okně importu vyberte **Odeslat soubory** a v počítači, který jste vytvořili dříve, přejděte na soubor kolekce. Vyberte Otevřít.
1. Pro potvrzení klikněte na tlačítko **importovat** .

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Snímek obrazovky tohoto okna pro import, ve kterém se zobrazuje soubor, který se má importovat jako kolekce a tlačítko Import.":::

Nově naimportovaná kolekce se teď může zobrazit v hlavním zobrazení pro odeslání na kartě kolekce.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Snímek obrazovky hlavního okna po odeslání. Nově naimportovaná kolekce je na kartě kolekce zvýrazněna." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Dále pokračujte k další části a přidejte do kolekce nosný token pro autorizaci a připojte ho k instanci digitálních vláken Azure.

### <a name="configure-authorization"></a>Konfigurace autorizace

V dalším kroku upravte kolekci, kterou jste vytvořili pro konfiguraci některých podrobností přístupu. Zvýrazněte kolekci, kterou jste vytvořili, a vyberte ikonu **Zobrazit další akce** , která vyžádá nabídku. Vyberte **Upravit**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Snímek obrazovky. Ikona Zobrazit další akce pro importovanou kolekci je zvýrazněná a možnost upravit je v možnostech zvýrazněna." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Pomocí těchto kroků přidejte do kolekce nosný token pro autorizaci. V tomto případě použijete **hodnotu tokenu** , kterou jste shromáždili v sekci [získat nosný token](#get-bearer-token) , aby ji bylo možné použít pro všechny požadavky rozhraní API v kolekci.

1. V dialogu Upravit pro vaši kolekci se ujistěte, že jste na kartě **autorizace** . 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Snímek obrazovky dialogového okna pro úpravy importované kolekce, na které se zobrazuje karta autorizace" lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Nastavte typ na **OAuth 2,0**, vložte přístupový token do pole přístupový token a vyberte **Save (Uložit**).

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Snímek dialogového okna pro úpravu po importované kolekci na kartě autorizace. Typ je OAuth 2,0 a pole přístupového tokenu je zvýrazněné." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Další konfigurace

# <a name="data-plane"></a>[Rovina dat](#tab/data-plane)

Pokud vytváříte [datovou rovinu dat](how-to-use-apis-sdks.md#overview-data-plane-apis) , můžete ji snadno připojit k prostředkům digitálních vláken Azure pomocí nastavení některých **proměnných** dodaných s kolekcemi. Pokud mnoho požadavků v kolekci vyžaduje stejnou hodnotu (jako je název hostitele instance digitálního vlákna Azure), můžete hodnotu Uložit do proměnné, která se vztahuje na všechny požadavky v kolekci. Obě kolekce ke stažení pro digitální vlákna Azure jsou dodávány s předem vytvořenými proměnnými, které můžete nastavit na úrovni kolekce.

1. Ještě v dialogovém okně Upravit pro vaši kolekci přejděte na kartu **proměnné** .

1. Pomocí **názvu hostitele** vaší instance z oddílu [*předpoklady*](#prerequisites) nastavte pole aktuální hodnota příslušné proměnné. Vyberte **Uložit**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Snímek obrazovky dialogového okna pro úpravy importované kolekce, na které se zobrazuje karta proměnné Pole aktuální hodnota je zvýrazněné." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Pokud má kolekce další proměnné, vyplňte a uložte také tyto hodnoty.

Až budete s výše uvedeným postupem hotovi, dokončili jste konfiguraci kolekce. Pokud chcete, můžete pro kolekci zavřít kartu úpravy.

# <a name="control-plane"></a>[Řídicí rovina](#tab/control-plane)

Pokud vytváříte kolekci [rovin ovládacích prvků](how-to-use-apis-sdks.md#overview-control-plane-apis) , provedete všechno, co potřebujete ke konfiguraci kolekce. Kartu úprav můžete pro kolekci zavřít, pokud chcete, a přejít k další části.

--- 

### <a name="explore-requests"></a>Prozkoumat žádosti

Dále Prozkoumejte požadavky v kolekci rozhraní API digitálních vláken Azure. Kolekci můžete rozbalit a zobrazit tak předem vytvořené požadavky (seřazené podle kategorie operace). 

Různé požadavky vyžadují různé informace o vaší instanci a jejich datech. Pokud chcete zobrazit všechny informace potřebné k vytvoření konkrétní žádosti, vyhledejte podrobnosti žádosti v části [digitální vlákna Azure REST API referenční dokumentaci](/rest/api/azure-digitaltwins/).

Podrobnosti žádosti v kolekci po můžete upravit pomocí těchto kroků:

1. Vyberte ho ze seznamu, abyste si vyčetli jeho upravitelné podrobnosti. 

1. Vyplňte hodnoty pro proměnné uvedené na kartě **params** v části **proměnné cesty**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Snímek obrazovky. Kolekce se rozbalí a zobrazí se žádost. V podrobnostech žádosti se zvýrazní oddíl Path Variables." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Na příslušných kartách zadejte všechny potřebné podrobnosti **záhlaví** nebo **textu** .

Po zadání všech požadovaných podrobností můžete žádost spustit pomocí tlačítka **Odeslat** .

Do kolekce můžete také přidat vlastní požadavky pomocí procesu popsaného v části [*přidat jednotlivé žádosti*](#add-an-individual-request) .

## <a name="create-your-own-collection"></a>Vytvoření vlastní kolekce

Místo importu stávající kolekce všech rozhraní API digitálních vláken Azure můžete také vytvořit vlastní kolekci od začátku. Pak je můžete naplnit jednotlivými požadavky pomocí [REST API Referenční dokumentace Azure Digital](/rest/api/azure-digitaltwins/)requests.

### <a name="create-a-postman-collection"></a>Vytvoření kolekce Postman

1. Chcete-li vytvořit kolekci, vyberte tlačítko **Nový** v hlavním okně pro odeslání.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Snímek obrazovky hlavního okna po odeslání. Tlačítko Nový je zvýrazněné." lightbox="media/how-to-use-postman/postman-new.png":::

    Vyberte typ **kolekce**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Snímek obrazovky s dialogovým oknem vytvořit nový v poli pro odeslání Možnost Collection je zvýrazněna.":::

1. Tím se otevře karta pro vyplňování podrobností o nové kolekci. Vyberte ikonu pro úpravy vedle výchozího názvu kolekce (**Nová kolekce**), abyste ji nahradili vlastní volbou názvu. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Snímek obrazovky dialogového okna pro úpravu nové kolekce v dialogovém okně pro úpravy Ikona pro úpravy vedle názvu ' nová kolekce ' je zvýrazněna." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Dále pokračujte k další části a přidejte do kolekce nosný token pro autorizaci.

### <a name="configure-authorization"></a>Konfigurace autorizace

Pomocí těchto kroků přidejte do kolekce nosný token pro autorizaci. V tomto případě použijete **hodnotu tokenu** , kterou jste shromáždili v sekci [získat nosný token](#get-bearer-token) , aby ji bylo možné použít pro všechny požadavky rozhraní API v kolekci.

1. Pořád v dialogu Upravit pro novou kolekci přejděte na kartu **autorizace** .

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Snímek obrazovky s dialogovým oknem pro úpravy nové kolekce, který zobrazuje kartu autorizace" lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Nastavte typ na **OAuth 2,0**, vložte přístupový token do pole přístupový token a vyberte **Save (Uložit**).

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Snímek obrazovky dialogového okna pro úpravu po vytvoření nové kolekce na kartě autorizace. Typ je OAuth 2,0 a pole přístupového tokenu je zvýrazněné." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Až budete s výše uvedeným postupem hotovi, dokončili jste konfiguraci kolekce. Pokud chcete, můžete pro novou kolekci zavřít kartu upravit.

Nová kolekce se dá zobrazit v hlavním zobrazení pro odeslání na kartě kolekce.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Snímek obrazovky hlavního okna po odeslání. Nově vytvořená vlastní kolekce je na kartě kolekce zvýrazněna." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Přidat jednotlivý požadavek

Teď, když je vaše kolekce nastavená, můžete přidat vlastní žádosti do digitálních rozhraní API Azure.

1. Pokud chcete vytvořit žádost, znovu použijte **nové** tlačítko.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Snímek obrazovky hlavního okna po odeslání. Tlačítko Nový je zvýrazněné." lightbox="media/how-to-use-postman/postman-new.png":::

    Vyberte typ **požadavku**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Snímek obrazovky s dialogovým oknem vytvořit nový v poli pro odeslání Možnost Request je zvýrazněna.":::

1. Tato akce otevře okno Uložit žádost, kde můžete zadat název žádosti, dát mu volitelný popis a vybrat kolekci, do které je součást. Vyplňte podrobnosti a uložte požadavek do kolekce, kterou jste vytvořili dříve.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Snímek obrazovky okna pro uložení žádosti v příspěvku ukazující pole, která jsou popsaná. Je zvýrazněno tlačítko Uložit do kolekce digitálních vláken Azure.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Teď si můžete svůj požadavek zobrazit v kolekci a vybrat ho, abyste si vyžádali jeho upravitelné podrobnosti.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Snímek obrazovky. Kolekce digitálních vláken Azure je rozbalená a zobrazuje podrobnosti žádosti." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Nastavit podrobnosti žádosti

Pokud chcete poslat žádost o odeslání do jedné z rozhraní API digitálních vláken Azure, budete potřebovat adresu URL rozhraní API a informace o tom, jaké podrobnosti vyžaduje. Tyto informace najdete v [referenční dokumentaci ke službě Azure Digital Revlákens REST API](/rest/api/azure-digitaltwins/).

Chcete-li pokračovat s příkladem dotazu, bude tento článek používat rozhraní API pro dotazy (a jeho [referenční dokumentaci](/rest/api/digital-twins/dataplane/query/querytwins)) k dotazování na všechny digitální vlákna v instanci.

1. Získejte adresu URL a typ žádosti z referenční dokumentace. Pro rozhraní API pro dotazování je aktuálně *post `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. V poli PSČ nastavte typ pro požadavek a zadejte adresu URL požadavku, podle potřeby vyplňte zástupné symboly v adrese URL. Tady budete používat **název hostitele** vaší instance z oddílu [*požadavky*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Snímek obrazovky s podrobnostmi o novém požadavku v příspěvku. Adresa URL dotazu z referenční dokumentace byla vyplněna do pole Adresa URL žádosti." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Ověřte, že parametry zobrazené pro požadavek na kartě **params** se shodují s hodnotami popsanými v referenční dokumentaci. Pro tento požadavek v poli `api-version` byl parametr automaticky vyplněn při zadání adresy URL požadavku v předchozím kroku. V případě rozhraní API pro dotazy se jedná o jediný požadovaný parametr, proto je tento krok hotový.
1. Na kartě **autorizace** nastavte typ pro **dědění ověřování z nadřazené položky**. To znamená, že tato žádost bude používat autorizaci, kterou jste nastavili dříve pro celou kolekci.
1. Ověřte, že hlavičky zobrazené pro požadavek na kartě **hlavičky** odpovídají názvům popsaným v referenční dokumentaci. Pro tento požadavek byly automaticky vyplněny několik záhlaví. V případě rozhraní API pro dotazy není nutné provádět žádné možnosti záhlaví, proto je tento krok hotový.
1. Ověřte, že text zobrazený pro požadavek na kartě **tělo** odpovídá potřebám popsaným v referenční dokumentaci. Pro rozhraní API pro dotazy je k zadání textu dotazu potřeba tělo JSON. Tady je příklad těla této žádosti, který se dotazuje na všechny digitální vlákna v instanci:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Snímek obrazovky s podrobnostmi o novém požadavku v příspěvku na kartě text Obsahuje nezpracované tělo JSON s dotazem SELECT * FROM DIGITALTWINS." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Další informace o tom, jak vytvářet dotazy digitálních vláken Azure, najdete v tématu [*Postup: dotazování na dvojitou graf*](how-to-query-graph.md).

1. Podívejte se na referenční dokumentaci pro všechna další pole, která se můžou vyžadovat pro váš typ požadavku. V případě rozhraní API pro dotazování jsou nyní všechny požadavky splněny v žádosti post, takže tento krok je dokončen.
1. K odeslání dokončené žádosti použijte tlačítko **Odeslat** .
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Snímek obrazovky příspěvku s podrobnostmi o novém požadavku. Tlačítko Odeslat je zvýrazněno." lightbox="media/how-to-use-postman/postman-request-send.png":::

Po odeslání žádosti se v okně po žádosti zobrazí podrobnosti o odpovědi. Můžete zobrazit stavový kód odpovědi a jakýkoliv hlavní text.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Snímek obrazovky odeslané žádosti v příspěvku Pod podrobnostmi žádosti se zobrazí odpověď. Stav je 200 OK a tělo zobrazuje výsledky dotazu." lightbox="media/how-to-use-postman/postman-request-response.png":::

Můžete také porovnat odpověď na očekávaná data odpovědi, která jsou uvedena v referenční dokumentaci, a ověřit výsledek nebo se dozvědět více o chybách, ke kterým dojde.

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních API digitálních vláken najdete v tématu [*Postupy: použití rozhraní API a sad SDK pro digitální vlákna Azure*](how-to-use-apis-sdks.md)nebo zobrazení [Referenční dokumentace pro rozhraní REST API](/rest/api/azure-digitaltwins/).
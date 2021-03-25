---
title: Konfigurace indexeru SharePoint Online (Preview)
titleSuffix: Azure Cognitive Search
description: Nastavte indexer SharePointu Online pro automatizaci indexování obsahu knihovny dokumentů v Azure Kognitivní hledání.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 5888a7cc8aa58d1c6edab191e1243ebc60000fd6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048863"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Jak nakonfigurovat indexování SharePointu Online v Kognitivní hledání (Preview)

> [!IMPORTANT] 
> Podpora SharePointu Online je aktuálně ve **ověřované veřejné verzi Preview**. Vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview)můžete požádat o přístup ke službě gated Preview.
>
> Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> Tato funkce poskytuje [REST API verze 2020-06-30-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora portálu nebo sady SDK.

> [!NOTE]
> SharePoint Online podporuje model podrobného autorizace, který určuje přístup pro jednotlivé uživatele na úrovni dokumentu. Indexer služby SharePoint Online nezískává tato oprávnění do indexu vyhledávání a Kognitivní hledání nepodporuje autorizaci na úrovni dokumentu. Když je dokument indexován ze SharePointu Online do vyhledávací služby, je obsah k dispozici všem uživatelům, kteří mají k indexu přístup pro čtení. Pokud požadujete oprávnění na úrovni dokumentu, měli byste prozkoumat filtry zabezpečení a oříznout výsledky neoprávněného obsahu. Další informace najdete v tématu [oříznutí zabezpečení pomocí identit služby Active Directory](search-security-trimming-for-azure-search-with-aad.md).

Tento článek popisuje, jak použít Azure Kognitivní hledání k indexování dokumentů (například souborů PDF, systém Microsoft Office dokumentů a několika dalších běžných formátů) uložených v knihovnách dokumentů SharePointu Online do indexu služby Azure Kognitivní hledání. Nejprve vysvětluje základy nastavení a konfigurace indexeru. Potom nabízí hlubší průzkum chování a scénářů, se kterými se pravděpodobně setkáte.

## <a name="functionality"></a>Funkce
Indexer v Azure Kognitivní hledání je prohledávací modul, který extrahuje hledaná data a metadata ze zdroje dat. Indexer SharePointu Online se připojí k webu SharePointu Online a rejstřík dokumentů z jedné nebo více knihoven dokumentů. Indexer nabízí následující funkce:
+ Indexuje obsah z jedné nebo více knihoven dokumentů SharePointu Online.
+ Indexujte obsah z knihoven dokumentů SharePointu Online, které jsou ve stejném tenantovi jako vaše služba Azure Kognitivní hledání. Indexer nebude fungovat s weby SharePointu, které jsou v jiném tenantovi než vaše služba Azure Kognitivní hledání. 
+ Indexer bude podporovat přírůstkové indexování, což znamená, že identifikuje, který obsah v knihovně dokumentů se změnil a indexuje pouze aktualizovaný obsah při budoucím spuštění indexování. Pokud je například 5 souborů PDF původně indexováno indexerem, pak 1 je aktualizováno, indexer se znovu spustí, indexer bude naindexovat pouze 1 PDF, který byl aktualizován.
+ Text a normalizované obrázky budou ve výchozím nastavení extrahovány z indexovaných dokumentů. Volitelně můžete do kanálu přidat dovednosti pro další obohacení obsahu. Další informace o dovednosti najdete v článku [Koncepty dovednosti v Azure kognitivní hledání](cognitive-search-working-with-skillsets.md).

## <a name="supported-document-formats"></a>Podporované formáty dokumentů

Indexer Azure Kognitivní hledání SharePoint Online může extrahovat text z následujících formátů dokumentů:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Přírůstkové indexování a odstraňování duplicit
Ve výchozím nastavení indexovací služba SharePoint Online podporuje přírůstkové indexování, což znamená, že identifikuje, který obsah v knihovně dokumentů se změnil a indexuje pouze aktualizovaný obsah při budoucím spuštění indexování. Například pokud je v indexeru původně indexovány 5 dokumentů aplikace Word, pak se aktualizuje 1, indexer se znovu spustí, indexer bude znovu indexovat 1 dokument aplikace Word, který byl aktualizován.

Ve výchozím nastavení se podporuje taky zjišťování odstranění. To znamená, že pokud se dokument odstraní z knihovny dokumentů SharePointu Online, indexer detekuje odstranění během budoucího spuštění indexeru a odebere dokument z indexu.

## <a name="setting-up-sharepoint-online-indexing"></a>Nastavení indexování SharePointu Online
Chcete-li nastavit indexer služby SharePoint Online, bude nutné provést některé akce v Azure Portal a některé akce používají REST API verze Preview. Sada SDK tuto verzi Preview nepodporuje.

### <a name="step-1-enable-system-assigned-managed-identity"></a>Krok 1: povolení spravované identity přiřazené systémem
Když je povolená spravovaná identita přiřazená systémem, Azure vytvoří identitu pro vaši vyhledávací službu, kterou může indexer použít.

![Povolit spravovanou identitu přiřazenou systémem](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Povolit spravovanou identitu přiřazenou systémem")

Po výběru možnosti **Uložit** se zobrazí ID objektu, které bylo přiřazeno k vaší vyhledávací službě.

![Spravovaná identita přiřazená systémem](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Spravovaná identita přiřazená systémem")

### <a name="step-2-create-an-aad-application"></a>Krok 2: Vytvoření aplikace AAD
Indexer SharePointu Online bude tuto aplikaci AAD používat pro ověřování. 

1.  Přejděte na [Azure Portal](https://portal.azure.com/).

1.  Otevřete nabídku na levé straně hlavní stránky a vyberte **Azure Active Directory** vyberte možnost **Registrace aplikací**. Vyberte **+ Nová registrace**.
    1.  Zadejte název vaší aplikace.
    2.  Vyberte **jednoho tenanta**.
    3.  Není vyžadován identifikátor URI pro přesměrování.
    4.  Vybrat **registraci**

1.  V nabídce vlevo vyberte **oprávnění rozhraní API** , pak **přidejte oprávnění** a pak **Microsoft Graph** **delegovaná oprávnění**. Přidejte následující oprávnění rozhraní API: 
    1.  **Delegované-soubory. Read. All** 
    2.  **Delegované – websites. Read. All** 
    3.  **Delegovaný uživatel. čtení**

    ![Delegovaná oprávnění API](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Delegovaná oprávnění API")

    Použití delegovaných oprávnění znamená, že indexer bude mít přístup k webu služby SharePoint v kontextu uživatele. Takže když spustíte indexer, bude mít přístup jenom k obsahu, ke kterému má přihlášený uživatel přístup. Při vytváření indexeru nebo aktualizaci zdroje dat dojde k přihlášení uživatele. Přihlašovací krok je popsán dále v tomto článku.

1.  Vyberte kartu **ověřování** . Nastavte možnost **povoluje tok veřejných klientů** na **Ano** a pak vyberte **Uložit**.

1.  Vyberte **+ Přidat platformu**, **mobilní a desktopové aplikace a** pak zkontrolujte a `https://login.microsoftonline.com/common/oauth2/nativeclient` pak **nakonfigurujte**.

    ![Konfigurace ověřování aplikace AAD](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "Konfigurace ověřování aplikace AAD")

1.  Poskytněte souhlas správce (vyžaduje se jenom pro některé klienty).

    Někteří klienti jsou zamčeni takovým způsobem, že se pro tato oprávnění delegovaného rozhraní API vyžaduje souhlas správce. Pokud je to tento případ, budete muset před vytvořením indexeru pro tuto aplikaci AAD udělit souhlas správce. 

    Vzhledem k tomu, že tento požadavek není u všech tenantů k dispozici, doporučujeme nejprve tento krok přeskočit a pokračovat podle pokynů. Pokud při vytváření indexeru potřebujete udělit souhlas správce, ověření se nepovede a oznámí vám, že ke schválení ověřování potřebujete správce. V takovém případě je třeba udělit souhlas správce tenanta pomocí tlačítka níže.

    ![Souhlas správce udělit aplikaci AAD](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "Souhlas správce udělit aplikaci AAD")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>Krok 3: vytvoření zdroje dat
> [!IMPORTANT] 
> Počínaje v této části musíte pro zbývající kroky použít REST API verze Preview. Pokud nejste obeznámeni s Azure Kognitivní hledání REST API, doporučujeme si proniknout v tomto [rychlém](search-get-started-rest.md)startu.

Zdroj dat určuje, která data se mají indexovat, přihlašovací údaje potřebné pro přístup k datům a zásady pro efektivní identifikaci změn dat (nové, změněné nebo odstraněné řádky). Zdroj dat může použít více indexerů ve stejné vyhledávací službě.

Pro indexování SharePointu musí mít zdroj dat následující požadované vlastnosti:
+ **název** je jedinečný název zdroje dat v rámci vyhledávací služby.
+ **typ** musí být "SharePoint". Rozlišují se malá a velká písmena.
+ **přihlašovací údaje** poskytují koncový bod SharePointu Online a ID aplikace AAD (klienta). Příkladem koncového bodu SharePointu Online je `https://microsoft.sharepoint.com/teams/MySharePointSite` . Koncový bod SharePointu Online můžete získat tak, že přejdete na domovskou stránku SharePointového webu a zkopírujete adresu URL z prohlížeče.
+ **kontejner** určuje, která knihovna dokumentů se má indexovat. Další informace o vytvoření kontejneru najdete v části [řízení indexovaných dokumentů](#controlling-which-documents-are-indexed) v tomto dokumentu.

Vytvoření zdroje dat:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>Krok 4: vytvoření indexu
Index určuje pole v dokumentu, atributech a dalších konstrukcích, které prohledají možnosti vyhledávání.

Tady je postup vytvoření indexu s polem s možností prohledávání obsahu pro uložení textu extrahovaého z dokumentů v knihovně dokumentů:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Další informace najdete v tématu [vytvoření indexu (REST API)](/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>Krok 5: vytvoření indexeru
Indexer připojuje zdroj dat k cílovému vyhledávacímu indexu a poskytuje plán pro automatizaci aktualizace dat. Po vytvoření indexu a zdroje dat jste připraveni vytvořit indexer.

V této části budete požádáni o přihlášení s přihlašovacími údaji vaší organizace, které mají přístup k webu služby SharePoint. Pokud je to možné, doporučujeme vytvořit nový uživatelský účet organizace a udělit tak novému uživateli přesná oprávnění, která má indexer mít.

Existuje několik kroků k vytvoření indexeru:

1.  Odešlete žádost o vytvoření indexeru.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  Při prvním vytváření indexeru se nezdaří a zobrazí se následující chyba. Přejít na odkaz v chybové zprávě. Pokud nepřejdete na odkaz během 10 minut, vyprší platnost kódu a budete muset [zdroj dat](#create-data-source)znovu vytvořit.

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Zadejte kód, který byl zadán v chybové zprávě.

    ![Zadejte kód zařízení.](media/search-howto-index-sharepoint-online/enter-device-code.png "Zadejte kód zařízení.")

1.  Indexer služby SharePoint bude k obsahu služby SharePoint přistupovat jako přihlášený uživatel. Uživatel, který se přihlásí během tohoto kroku, bude tímto přihlášeným uživatelem. Takže pokud se přihlásíte pomocí uživatelského účtu, který nemá přístup k dokumentu v knihovně dokumentů, kterou chcete indexovat, indexer nebude mít k tomuto dokumentu přístup.

    Pokud je to možné, doporučujeme vytvořit nový uživatelský účet a udělit tak novému uživateli přesná oprávnění, která má indexer mít.

1.  Schválí oprávnění, která jsou požadována.

    ![Schválení oprávnění API](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "Schválení oprávnění API")

1.  Znovu odešlete žádost indexeru vytvořit. Tentokrát by požadavek měl být úspěšný. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>Krok 6: ověření stavu indexeru
Po vytvoření indexeru můžete zjistit stav indexeru tím, že provedete následující požadavek.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Další informace o stavu indexeru najdete tady: [získání stavu indexeru](/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Aktualizace zdroje dat
Pokud nejsou k dispozici žádné aktualizace objektu zdroje dat, indexer může běžet podle plánu bez zásahu uživatele. Pokaždé, když se aktualizuje objekt zdroje dat Kognitivní hledání Azure, budete se muset znovu přihlásit, aby se indexer spustil. Pokud například změníte dotaz na zdroj dat, budete se muset znovu přihlásit pomocí `https://microsoft.com/devicelogin` a nového kódu.

Po aktualizaci zdroje dat postupujte podle následujících kroků:

1.  Ručně vypíná spuštění indexeru.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Další informace o žádosti o spuštění indexeru najdete tady: [Spustit indexer](/rest/api/searchservice/run-indexer).

1.  Podívejte se na stav indexeru. Pokud poslední spuštění indexeru obsahuje chybu, která vás upozorní na to `https://microsoft.com/devicelogin` , přejít na tuto stránku a zadat nový kód. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Další informace o stavu indexeru najdete tady: [získání stavu indexeru](/rest/api/searchservice/get-indexer-status).

1.  Přihlásit

1.  Ručně spusťte indexer znovu a podívejte se na stav indexeru. Tentokrát by se spuštění indexeru mělo úspěšně spustit.

## <a name="indexing-document-metadata"></a>Indexování metadat dokumentu
Pokud jste indexer nastavili tak, aby indexoval metadata dokumentu, budou k dispozici následující metadata.

> [!NOTE]
> Vlastní metadata nejsou součástí aktuální verze Preview.

| Identifikátor | Typ | Description | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | Kombinace klíče ID webu, ID knihovny a ID položky, která jednoznačně identifikuje položku v knihovně dokumentů pro lokalitu. |
| metadata_spo_site_id | Edm.String | ID webu SharePointu Online |
| metadata_spo_library_id | Edm.String | ID knihovny dokumentů |
| metadata_spo_item_id | Edm.String | ID položky (dokumentu) v knihovně. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Datum a čas poslední změny položky (UTC). |
| metadata_spo_item_name | Edm.String | Název položky. |
| metadata_spo_item_size | Edm.Int64 | Velikost položky (v bajtech). | 
| metadata_spo_item_content_type | Edm.String | Typ obsahu položky | 
| metadata_spo_item_extension | Edm.String | Rozšíření položky |
| metadata_spo_item_weburi | Edm.String | Identifikátor URI položky |
| metadata_spo_item_path | Edm.String | Kombinace nadřazené cesty a názvu položky | 

Indexer SharePointu Online také podporuje metadata specifická pro každý typ dokumentu. Další informace najdete ve [vlastnostech metadat obsahu používaných v Azure kognitivní hledání](search-blob-metadata-properties.md).

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Řízení indexovaných dokumentů
Jeden indexer služby SharePoint Online může indexovat obsah z jedné nebo více knihoven dokumentů. Použijte parametr *Container* při vytváření zdroje dat k označení knihoven dokumentů, které chcete indexovat. *Kontejner* zdroje dat má dvě vlastnosti: *název* a *dotaz*. 

### <a name="name"></a>Name
Vlastnost *Name* je povinná a musí mít jednu ze tří hodnot:
+ *defaultSiteLibrary*
    + Indexujte veškerý obsah z výchozí knihovny dokumentů pro weby.
+   *allSiteLibraries*
    + Indexuje veškerý obsah ze všech knihoven dokumentů v lokalitě. Tato akce neindexuje knihovny dokumentů z podřízené lokality. Ty mohou být v *dotazu* zadány, i když.
+   *useQuery*
    + V *dotazu* je definován pouze indexový obsah.

### <a name="query"></a>Dotaz
Vlastnost *dotazu* se skládá z párů klíčová slova/hodnota. Níže jsou uvedená klíčová slova, která lze použít. Hodnoty jsou buď adresy URL webu, nebo adresy URL knihoven dokumentů.

> [!NOTE]
> Chcete-li získat hodnotu pro konkrétní klíčové slovo, doporučujeme otevřít SharePoint Online v prohlížeči, přejít do knihovny dokumentů, kterou se pokoušíte zahrnout nebo vyloučit a zkopírovat identifikátor URI z prohlížeče. Toto je nejjednodušší způsob, jak získat hodnotu pro použití s klíčovým slovem v dotazu.

| Klíčové slovo | Popis dotazu | Příklad |
| ------------- | -------------- | ----------- |
| null | Pokud je hodnota null nebo prázdná, Indexujte buď výchozí knihovnu dokumentů, nebo všechny knihovny dokumentů v závislosti na názvu kontejneru. | Indexovat veškerý obsah z výchozí knihovny webu: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| includeLibrariesInSite | Indexuje obsah ze všech knihoven v definované lokalitě v připojovacím řetězci. Tyto jsou omezené na podřízené weby vašeho webu. <br><br> Hodnota *dotazu* tohoto klíčového slova by měla být identifikátor URI webu nebo podřízeného webu. | Indexujte veškerý obsah ze všech knihoven dokumentů na webu. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| includeLibrary | Indexuje obsah z této knihovny. <br><br> Hodnota *dotazu* tohoto klíčového slova by měla být v jednom z následujících formátů: <br><br> Příklad 1: <br><br> *includeLibrary = [lokalita nebo dílčí web]/[knihovna dokumentů]* <br><br> Příklad 2: <br><br> Identifikátor URI zkopírovaný z prohlížeče | Indexovat veškerý obsah z MyDocumentLibrary: <br><br> Příklad 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Příklad 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Neindexovat obsah z této knihovny. <br><br> Hodnota *dotazu* tohoto klíčového slova by měla být v jednom z následujících formátů: <br><br> Příklad 1: <br><br> *excludeLibrary = [identifikátor URI lokality nebo podřízeného webu]/[knihovna dokumentů]* <br><br> Příklad 2: <br><br> Identifikátor URI zkopírovaný z prohlížeče | Indexovat veškerý obsah ze všech mých knihoven kromě MyDocumentLibrary: <br><br> Příklad 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Příklad 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Index podle typu souboru
Můžete určit, které dokumenty budou indexovány a které se přeskočí.

### <a name="include-documents-having-specific-file-extensions"></a>Zahrnout dokumenty s konkrétními příponami souborů
Pomocí parametru konfigurace indexeru můžete indexovat jenom dokumenty s příponami názvů souborů, které zadáte `indexedFileNameExtensions` . Hodnota je řetězec obsahující čárkami oddělený seznam přípon souborů (s počáteční tečkou). Například chcete-li indexovat pouze. Soubory PDF a. Soubory DOCX:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Vyloučení dokumentů s konkrétními příponami souborů
Pomocí parametru konfigurace můžete z indexování vyloučit dokumenty s určitými příponami názvů souborů `excludedFileNameExtensions` . Hodnota je řetězec obsahující čárkami oddělený seznam přípon souborů (s počáteční tečkou). Například pro indexování veškerého obsahu s výjimkou těch s. PNG a. Rozšíření JPEG:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Pokud `indexedFileNameExtensions` `excludedFileNameExtensions` jsou přítomny parametry i a, Azure kognitivní hledání nejprve vyhledá `indexedFileNameExtensions` , a potom na `excludedFileNameExtensions` . To znamená, že pokud se stejná Přípona souboru nachází v obou seznamech, bude vyloučena z indexování.

## <a name="handling-errors"></a>Zpracování chyb
Ve výchozím nastavení se indexer služby SharePoint Online zastaví, jakmile dojde k dokumentu s nepodporovaným typem obsahu (například obrázku). Můžete samozřejmě použít `excludedFileNameExtensions` parametr k přeskočení určitých typů obsahu. Je ale možné, že budete muset indexovat dokumenty bez znalosti všech možných typů obsahu předem. Pokud chcete pokračovat v indexování při zjištění nepodporovaného typu obsahu, nastavte `failOnUnsupportedContentType` parametr konfigurace na false:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

V případě některých dokumentů Azure Kognitivní hledání nedokáže určit typ obsahu nebo nemůže zpracovat dokument jiného podporovaného typu obsahu. Chcete-li tento režim selhání ignorovat, nastavte `failOnUnprocessableDocument` parametr konfigurace na hodnotu false:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Kognitivní hledání omezuje velikost indexovaných dokumentů. Tato omezení jsou popsaná v [omezení služby v Azure kognitivní hledání](./search-limits-quotas-capacity.md). Ve výchozím nastavení se u dokumentů s větším množstvím standardně považují chyby. I když nastavíte `indexStorageMetadataOnlyForOversizedDocuments` parametr konfigurace na hodnotu true, pořád ale můžete indexovat metadata úložiště u dokumentů s více velikostmi:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Můžete také pokračovat v indexování, pokud dojde k chybám v jakémkoli okamžiku zpracování, a to při analýze dokumentů nebo při přidávání dokumentů do indexu. Chcete-li ignorovat určitý počet chyb, nastavte `maxFailedItems` `maxFailedItemsPerBatch` parametry konfigurace a na požadované hodnoty. Například:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Viz také
+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Vlastnosti metadat obsahu používané v Azure Kognitivní hledání](search-blob-metadata-properties.md)
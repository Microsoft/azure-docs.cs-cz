---
title: Řešení běžných potíží s indexerem vyhledávání
titleSuffix: Azure Cognitive Search
description: Opravte chyby a běžné problémy s indexery ve službě Azure Kognitivní hledání, včetně připojení ke zdroji dat, brány firewall a chybějících dokumentů.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740051"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Řešení běžných potíží indexerů v Azure Kognitivní hledání

Indexery můžou být spuštěné v množství problémů při indexování dat do Azure Kognitivní hledání. Mezi hlavní kategorie selhání patří:

* [Připojení ke zdroji dat nebo jiným prostředkům](#connection-errors)
* [Zpracování dokumentů](#document-processing-errors)
* [Přijímání dokumentů do indexu](#index-errors)

## <a name="connection-errors"></a>Chyby připojení

> [!NOTE]
> Indexery mají omezené podpory pro přístup ke zdrojům dat a dalším prostředkům zabezpečeným mechanismy zabezpečení sítě Azure. V současné době můžou indexery přistupovat pouze ke zdrojům dat prostřednictvím odpovídajících mechanismů omezení rozsahu IP adres nebo pravidel NSG. Podrobnosti o přístupu ke každému podporovanému zdroji dat najdete níže.
>
> IP adresu vaší vyhledávací služby můžete zjistit tak, že otestujete jeho plně kvalifikovaný název domény (např., `<your-search-service-name>.search.windows.net` ).
>
> Rozsah IP adres `AzureCognitiveSearch` [značky služby](../virtual-network/service-tags-overview.md#available-service-tags) můžete zjistit buď pomocí [souborů JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) , nebo přes [rozhraní API pro zjišťování značek služby](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). Rozsah IP adres se aktualizuje týdně.

### <a name="configure-firewall-rules"></a>Konfigurace pravidel brány firewall

Azure Storage, CosmosDB a Azure SQL poskytují konfigurovatelnou bránu firewall. V případě povolení brány firewall není k dispozici žádná konkrétní chybová zpráva. Chyby brány firewall jsou obvykle obecné a vypadají jako `The remote server returned an error: (403) Forbidden` nebo `Credentials provided in the connection string are invalid or have expired` .

Existují dvě možnosti, jak povolit indexerům přístup k těmto prostředkům v takové instanci:

* Bránu firewall zakažte tak, že povolíte přístup ze **všech sítí** (Pokud je to možné).
* Případně můžete `AzureCognitiveSearch` v pravidlech brány firewall prostředku (omezení rozsahu IP adres) dovolit přístup pro IP [adresu služby Search](../virtual-network/service-tags-overview.md#available-service-tags) a rozsah IP adres.

Podrobnosti o konfiguraci omezení rozsahu IP adres pro každý typ zdroje dat najdete na následujících odkazech:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Databáze Cosmos](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Omezení**: jak je uvedeno v dokumentaci výše pro Azure Storage, omezení rozsahu IP adres budou fungovat jenom v případě, že vaše vyhledávací služba a váš účet úložiště jsou v různých oblastech.

Funkce Azure Functions (které se dají použít jako [vlastní dovednosti webového rozhraní API](cognitive-search-custom-skill-web-api.md)) podporují také [omezení IP adres](../azure-functions/ip-addresses.md#ip-address-restrictions). Seznam IP adres, které se mají konfigurovat, by představoval IP adresu vaší vyhledávací služby a rozsah IP adres `AzureCognitiveSearch` značky služby.

[Tady](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) jsou uvedené podrobnosti o přístupu k datům na SQL serveru na virtuálním počítači Azure.

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurace pravidel skupiny zabezpečení sítě (NSG)

Při přístupu k datům ve spravované instanci SQL nebo při použití virtuálního počítače Azure jako identifikátoru URI webové služby pro [vlastní dovednosti webového rozhraní API](cognitive-search-custom-skill-web-api.md)se zákazníkům nemusí zabývat konkrétními IP adresami.

V takových případech se virtuální počítač Azure nebo spravovaná instance SQL dají nakonfigurovat tak, aby se nacházely v rámci virtuální sítě. Pak se skupina zabezpečení sítě dá nakonfigurovat tak, aby se vyfiltroval typ síťového provozu, který může přecházet do podsítí a síťových rozhraní virtuální sítě.

`AzureCognitiveSearch`Tag služby se dá přímo použít v příchozích [pravidlech NSG](../virtual-network/manage-network-security-group.md#work-with-security-rules) , aniž byste museli hledat jeho rozsah IP adres.

Podrobnější informace o přístupu k datům ve spravované instanci SQL jsou uvedené [tady](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md) .

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "indexování" není povoleno

Azure Kognitivní hledání má implicitní závislost na Cosmos DB indexování. Pokud automatické indexování v Cosmos DB vypnete, Azure Kognitivní hledání vrátí úspěšný stav, ale index obsahu kontejneru se nezdařil. Pokyny, jak kontrolovat nastavení a zapnout indexování, najdete v tématu [Správa indexování v Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

### <a name="sharepoint-online-conditional-access-policies"></a>Zásady podmíněného přístupu pro SharePoint Online

Při vytváření indexeru SharePointu Online provedete krok, který po poskytnutí kódu zařízení vyžaduje přihlášení k aplikaci AAD. Pokud se zobrazí zpráva s oznámením, že vaše přihlášení bylo úspěšné, ale váš správce vyžaduje, aby zařízení vyžadovalo správu přístupového, indexer se pravděpodobně zablokuje přístup k knihovně dokumentů SharePointu Online z důvodu zásad [podmíněného přístupu](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview) .

Pokud chcete aktualizovat zásadu tak, aby umožňovala přístupu indexeru k knihovně dokumentů, postupujte podle následujících kroků:

1. Otevřete Azure Portal a vyhledejte **podmíněný přístup Azure AD** a pak v nabídce vlevo vyberte **zásady** . Pokud nemáte přístup k zobrazení této stránky, budete potřebovat buď najít někoho, kdo má přístup nebo získat přístup.

1. Určete, které zásady blokují službě SharePoint Online indexer, aby se přistupovala k knihovně dokumentů. Zásady, které by mohly blokovat indexer, budou zahrnovat uživatelský účet, který jste použili k ověření během kroku vytvoření indexeru v části **Uživatelé a skupiny** . Zásady můžou mít taky **podmínky** , které:
    * Omezte platformy **Windows** .
    * Omezte **mobilní aplikace a klienty klasické pracovní plochy**.
    * Má **stav zařízení** nastavenou **hodnotu Ano**.

1. Jakmile ověříte, že je k dispozici zásada blokující indexer, je nutné vytvořit výjimku pro indexer. Načtěte IP adresu vyhledávací služby.

    1. Získejte plně kvalifikovaný název domény (FQDN) vaší vyhledávací služby. Bude vypadat nějak takto `<search-service-name>.search.windows.net` : Plně kvalifikovaný název domény můžete najít vyhledáním vyhledávací služby na Azure Portal.

   ![Získat plně kvalifikovaný název domény služby](media\search-indexer-howto-secure-access\search-service-portal.png "Získat plně kvalifikovaný název domény služby")

    IP adresu vyhledávací služby lze získat pomocí `nslookup` (nebo `ping` ) plně kvalifikovaného názvu domény (FQDN). V následujícím příkladu byste přidali "150.0.0.1" do příchozího pravidla v bráně firewall Azure Storage. Může trvat až 15 minut, než se aktualizuje nastavení brány firewall pro indexer vyhledávací služby, aby mohl získat přístup k Azure Storagemu účtu.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Získejte rozsahy IP adres pro spouštěcí prostředí indexeru pro vaši oblast.

    Pro požadavky, které pocházejí z [prostředí pro spouštění s více klienty](search-indexer-securing-resources.md#indexer-execution-environment)indexeru, se používají další IP adresy. Tento rozsah IP adres můžete získat ze značky služby.

    Rozsahy IP adres pro `AzureCognitiveSearch` značku služby lze získat buď prostřednictvím [rozhraní Discovery API (Preview)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) , nebo [souboru JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    Pro tento návod za předpokladu, že je vyhledávací služba ve veřejném cloudu Azure, je potřeba stáhnout [veřejný soubor JSON pro Azure](https://www.microsoft.com/download/details.aspx?id=56519) .

   ![Stáhnout soubor JSON](media\search-indexer-troubleshooting\service-tag.png "Stáhnout soubor JSON")

    V souboru JSON, za předpokladu, že je služba vyhledávání v Středozápadní USA, seznam IP adres pro prostředí pro spuštění indexeru pro více tenantů je uvedený níže.

    ```json
        {
          "name": "AzureCognitiveSearch.WestCentralUS",
          "id": "AzureCognitiveSearch.WestCentralUS",
          "properties": {
            "changeNumber": 1,
            "region": "westcentralus",
            "platform": "Azure",
            "systemService": "AzureCognitiveSearch",
            "addressPrefixes": [
              "52.150.139.0/26",
              "52.253.133.74/32"
            ]
          }
        }
    ```

1. Zpátky na stránce podmíněný přístup v Azure Portal v nabídce vlevo vyberte **pojmenovaná umístění** a pak vyberte **+ umístění rozsahů IP adres**. Dejte novému pojmenovanému umístění název a přidejte rozsahy IP adres pro prostředí služby Search a spuštění indexeru, které jste shromáždili v posledních dvou krocích.
    * Pro IP adresu vyhledávací služby možná budete muset na konec IP adresy přidat "/32", protože akceptuje jenom platné rozsahy IP adres.
    * Pamatujte na to, že pro rozsahy IP adres prostředí indexeru je třeba přidat rozsahy IP adres pro oblast, ve které je vaše vyhledávací služba.

1. Vylučte z zásad nové pojmenované umístění. 
    1. V nabídce vlevo vyberte **zásady** . 
    1. Vyberte zásadu blokující indexer.
    1. Vyberte **podmínky**.
    1. Vyberte **umístění**.
    1. Vyberte **vyloučit** a pak přidejte nové pojmenované umístění.
    1. **Uložte** změny.

1. Počkejte několik minut, než se zásada aktualizuje a vynutila nová pravidla zásad.

1. Pokuste se znovu vytvořit indexer.
    1. Odešlete žádost o aktualizaci objektu zdroje dat, který jste vytvořili.
    1. Znovu odešlete žádost indexeru vytvořit. Pomocí nového kódu se přihlaste a potom po úspěšném přihlášení odešlete další požadavek na vytvoření indexeru.

## <a name="document-processing-errors"></a>Chyby zpracování dokumentu

### <a name="unprocessable-or-unsupported-documents"></a>Nezpracované nebo nepodporované dokumenty

Indexer objektu BLOB [dokumentuje explicitně podporované formáty dokumentů.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) V některých případech kontejner úložiště objektů BLOB obsahuje nepodporované dokumenty. Jindy může dojít k problematickým dokumentům. Můžete se vyhnout zastavení indexeru na těchto dokumentech [změnou možností konfigurace](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Chybějící obsah dokumentu

Indexer objektů BLOB [vyhledá a extrahuje text z objektů BLOB v kontejneru](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Mezi problémy s extrahováním textu patří:

* Dokument obsahuje pouze naskenované obrázky. Objekty blob ve formátu PDF, které obsahují netextový obsah, jako jsou například naskenované obrázky (JPGs), nepřinesí výsledky standardní kanál indexování objektů BLOB. Pokud máte obsah obrázků s textovými prvky, můžete k vyhledání a extrakci textu použít [hledání rozpoznávání](cognitive-search-concept-image-scenarios.md) .
* Indexer objektů BLOB je nakonfigurovaný jenom na metadata indexu. Pro extrakci obsahu musí být indexer objektů BLOB nakonfigurovaný tak, aby mohl [extrahovat obsah i metadata](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Chyby indexu

### <a name="missing-documents"></a>Chybějící dokumenty

Indexery hledají dokumenty ze [zdroje dat](/rest/api/searchservice/create-data-source). V některých případech se zdá, že v indexu chybí dokument ze zdroje dat, který by měl být indexován. K těmto chybám může dojít z několika běžných důvodů:

* Dokument nebyl indexován. Na portálu vyhledejte úspěšné spuštění indexeru.
* Ověřte hodnotu [sledování změn](/rest/api/searchservice/create-data-source#data-change-detection-policies) . Pokud je hodnota horní meze nastavená na budoucí čas, pak se v indexeru přeskočí všechny dokumenty, které mají datum menší než toto. Stav sledování změn indexeru můžete pochopit pomocí polí ' initialTrackingState ' a ' finalTrackingState ' ve [stavu indexeru](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Po spuštění indexeru se dokument aktualizoval. Pokud je indexer podle [plánu](/rest/api/searchservice/create-indexer#indexer-schedule), bude ho nakonec znovu spustit a vybrat.
* [Dotaz](/rest/api/searchservice/create-data-source) zadaný ve zdroji dat vylučuje dokument. Indexery nemůžou indexovat dokumenty, které nejsou součástí zdroje dat.
* [Mapování polí](/rest/api/searchservice/create-indexer#fieldmappings) nebo [rozšíření AI](./cognitive-search-concept-intro.md) změnilo dokument a vypadá jinak, než očekáváte.
* K vyhledání dokumentu použijte [rozhraní API pro vyhledávání v dokumentu](/rest/api/searchservice/lookup-document) .
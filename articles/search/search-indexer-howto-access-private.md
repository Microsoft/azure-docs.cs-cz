---
title: Připojení indexeru prostřednictvím privátního koncového bodu
titleSuffix: Azure Cognitive Search
description: Nakonfigurujte připojení indexeru pro přístup k obsahu z jiných prostředků Azure, které jsou chráněné prostřednictvím privátního koncového bodu.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcacd6c2e1353c71d8e4e25c95ee2b563e7b3fba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150423"
---
# <a name="indexer-connections-through-a-private-endpoint-azure-cognitive-search"></a>Připojení indexeru prostřednictvím privátního koncového bodu (Azure Kognitivní hledání)

Mnoho prostředků Azure (například účtů Azure Storage) je možné nakonfigurovat tak, aby přijímalo připojení z konkrétního seznamu virtuálních sítí, a odmítání mimo připojení pocházející z veřejné sítě. Pokud k indexování dat v Azure Kognitivní hledání používáte indexer a váš zdroj dat je v privátní síti, můžete pro přístup k datům vytvořit (odchozí) [připojení privátního koncového bodu](../private-link/private-endpoint-overview.md) .

Chcete-li použít tuto metodu připojení indexeru, existují dva požadavky:

+ Prostředek Azure poskytující obsah nebo kód musí být dřív zaregistrovaný ve [službě Azure Private Link](https://azure.microsoft.com/services/private-link/).

+ Služba Azure Kognitivní hledání musí být Basic nebo vyšší (není dostupná na úrovni Free). Kromě toho pro indexery, které mají dovednosti, musí být vyhledávací služba S2 nebo vyšší. Další informace najdete v tématu [omezení služby](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Rozhraní API pro správu sdílených prostředků privátního propojení

Privátní koncové body zabezpečených prostředků, které jsou vytvořené prostřednictvím rozhraní API služby Azure Kognitivní hledání, se označují jako *sdílené prostředky privátního propojení* , protože sdílíte přístup k prostředku (například k účtu úložiště), který byl připojen ke [službě privátního propojení Azure](https://azure.microsoft.com/services/private-link/).

Prostřednictvím REST API správy poskytuje Azure Kognitivní hledání operaci [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) , kterou můžete použít ke konfiguraci přístupu ze služby Azure kognitivní hledání indexer.

Připojení privátního koncového bodu k některým prostředkům se dá vytvořit jenom s verzí Preview rozhraní API pro správu vyhledávání ( `2020-08-01-Preview` nebo novější), která je v následující tabulce označená značkou Preview. Prostředky bez příznaku "Preview" lze vytvořit pomocí verze Preview nebo všeobecně dostupné verze rozhraní API ( `2020-08-01` nebo novější).

Níže najdete seznam prostředků Azure, ve kterých je možné vytvořit odchozí privátní koncové body z Azure Kognitivní hledání. `groupId`Hodnoty uvedené v následující tabulce se musí v rozhraní API použít přesně jako zapsané (velká a malá písmena), aby se vytvořil sdílený prostředek privátního propojení.

| Prostředek Azure | ID skupiny |
| --- | --- |
| Azure Storage – objekt BLOB (nebo) ADLS Gen 2 | `blob`|
| Azure Storage – tabulky | `table`|
| Azure Cosmos DB – rozhraní API SQL | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (Preview) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (Preview) | `sites` |

Seznam prostředků Azure, pro které se podporují odchozí připojení privátních koncových bodů, se taky dá dotázat pomocí [seznamu podporovaného rozhraní API](/rest/api/searchmanagement/privatelinkresources/listsupported).

Ve zbývající části tohoto článku se k předvedení REST API volání používají kombinace [ARMClient](https://github.com/projectkudu/ARMClient) a [Poster](https://www.postman.com/) .

> [!NOTE]
> V celém tomto článku Předpokládejme, že název vyhledávací služby je __Contoso-Search__ , který existuje ve skupině prostředků __Contoso__ PŘEDplatného s ID předplatného __00000000-0000-0000-0000-000000000000__. ID prostředku této vyhledávací služby bude `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

Zbývající příklady vám ukáže, jak se dá nakonfigurovat služba __společnosti Contoso-Search__ , aby její indexovací indexery měly přístup k datům z zabezpečeného účtu úložiště. `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Zabezpečení účtu úložiště

Nakonfigurujte účet úložiště tak, aby [povoloval přístup jenom z konkrétních podsítí](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Pokud v Azure Portal zaškrtnete tuto možnost a necháte sadu prázdnou, znamená to, že není povolen žádný provoz z žádné virtuální sítě.

   ![Přístup k Virtual Network](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Přístup k Virtual Network")

> [!NOTE]
> [Přístup k důvěryhodné službě Microsoftu](../storage/common/storage-network-security.md#trusted-microsoft-services) se dá použít k obcházení omezení virtuální sítě nebo IP adresy na takovém účtu úložiště a umožňuje službě vyhledávání přístup k datům v účtu úložiště, jak je popsáno v tématu [přístup k Azure Storage pomocí výjimky důvěryhodné služby ](search-indexer-howto-access-trusted-service-exception.md). Při použití tohoto přístupu ke komunikaci mezi Azure Kognitivní hledání a účtem úložiště se ale přes zabezpečenou páteřní síť Microsoftu stane přes veřejnou IP adresu účtu úložiště.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Krok 1: vytvoření sdíleného prostředku privátního propojení s účtem úložiště

Provést následující volání rozhraní API pro vyžádání služby Azure Kognitivní hledání k vytvoření odchozího připojení privátního koncového bodu k účtu úložiště

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Obsah `create-pe.json` souboru (který představuje tělo žádosti do rozhraní API):

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Po `202 Accepted` úspěchu se vrátí odpověď – proces vytvoření odchozího privátního koncového bodu je dlouhodobá operace (asynchronní). Zahrnuje nasazení následujících prostředků –

1. Privátní koncový bod přidělený privátní IP adresou ve `"Pending"` stavu. Privátní IP adresa se získává z adresního prostoru, který je přidělen virtuální síti prostředí pro spuštění privátního indexeru specifického pro službu Search. Po schválení privátního koncového bodu pocházela jakákoli komunikace z Azure Kognitivní hledání k účtu úložiště z privátní IP adresy a zabezpečeného kanálu privátního propojení.
2. Soukromá zóna DNS pro typ prostředku založená na `groupId` . Tím se zajistí, že jakékoli vyhledání DNS k soukromému prostředku využije IP adresu přidruženou k privátnímu koncovému bodu.

Ujistěte se, že jste zadali správný `groupId` typ prostředku, pro který vytváříte soukromý koncový bod. Jakákoli neshoda bude mít za následek zprávu o neúspěšné odpovědi.

Stejně jako všechny asynchronní operace Azure `PUT` vrátí volání `Azure-AsyncOperation` hodnotu hlavičky, která bude vypadat takto:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Tento identifikátor URI se dá pravidelně dotazovat, aby se získal stav operace. Než budete pokračovat, doporučujeme počkat, dokud stav operace prostředku sdíleného privátního propojení nedosáhne stavu terminálu (tj `succeeded` .).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Krok 2a: schválení připojení privátního koncového bodu pro účet úložiště

> [!NOTE]
> Tato část používá Azure Portal k Projděte si tok schválení privátního koncového bodu do úložiště. Místo toho se dá použít taky [REST API](/rest/api/storagerp/privateendpointconnections) k dispozici prostřednictvím poskytovatele prostředků úložiště (RP).
>
> Jiní poskytovatelé, jako je CosmosDB, Azure SQL Server atd., nabízejí také podobná rozhraní RP API pro správu připojení privátních koncových bodů.

Přejděte na kartu**připojení privátního koncového bodu**účtu úložiště v Azure Portal. Měla by existovat žádost o připojení privátního koncového bodu se zprávou požadavku z předchozího volání rozhraní API (po __úspěšném__dokončení asynchronní operace).

   ![Schválení privátního koncového bodu](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Schválení privátního koncového bodu")

Vyberte privátní koncový bod, který vytvořila služba Azure Kognitivní hledání (pomocí sloupce "privátní koncový bod" Identifikujte připojení privátního koncového bodu pomocí názvu zadaného v předchozím rozhraní API) a zvolte schválit s odpovídající zprávou (zpráva není významná). Ujistěte se, že se připojení k privátnímu koncovému bodu zobrazuje takto (na portálu se může nacházet kdykoli od 1-2 minut, než se stav aktualizuje na portálu).

![Soukromý koncový bod schválen](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Soukromý koncový bod schválen")

Po schválení žádosti o připojení privátního koncového bodu to znamená, že provoz se *může* přesměrovat prostřednictvím privátního koncového bodu. Po schválení privátního koncového bodu Azure Kognitivní hledání vytvoří potřebná mapování zón DNS v zóně DNS, která je pro ni vytvořená.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Krok 2b: dotaz na stav sdíleného prostředku privátního propojení

 Pokud chcete ověřit, že se prostředek sdíleného privátního propojení aktualizoval po schválení, získejte jeho stav pomocí [rozhraní Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Pokud je v `properties.provisioningState` prostředku `Succeeded` a je to `properties.status` , znamená to, `Approved` že sdílený prostředek privátního propojení je funkční a indexery je možné nakonfigurovat tak, aby komunikovaly s privátním koncovým bodem.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Krok 3: Konfigurace indexeru pro spuštění v privátním prostředí

> [!NOTE]
> Tento krok lze provést dokonce ještě před schválením připojení privátního koncového bodu. Dokud nebude připojení privátního koncového bodu schváleno, bude se libovolný indexer, který se pokusí o komunikaci se zabezpečeným prostředkem (například s účtem úložiště), ukončit v přechodném stavu selhání. Nové indexery se nedaří vytvořit. Jakmile je připojení privátního koncového bodu schváleno, indexery budou moci získat přístup k účtu privátního úložiště.

1. [Vytvořte zdroj dat](/rest/api/searchservice/create-data-source) , který odkazuje na účet zabezpečeného úložiště a příslušný kontejner v rámci účtu úložiště. Následující příklad ukazuje tuto žádost v příspěvku.
![Vytvoření zdroje dat](media\search-indexer-howto-secure-access\create-ds.png "Vytvoření zdroje dat")

1. Podobně [vytvořte index](/rest/api/searchservice/create-index) a volitelně [vytvořte dovednosti](/rest/api/searchservice/create-skillset) pomocí REST API.

1. [Vytvořte indexer](/rest/api/searchservice/create-indexer) , který odkazuje na zdroj dat, index a dovednosti vytvořené výše. Kromě toho vynuťte, aby indexer běžel v prostředí pro privátní spouštění, nastavením vlastnosti konfigurace indexeru `executionEnvironment` na `"Private"` .
![Vytvořit indexer](media\search-indexer-howto-secure-access\create-idr.png "Vytvoření indexeru")

Indexer by měl být úspěšně vytvořen a měl by se vytvořit průběh – indexování obsahu z účtu úložiště přes připojení k privátnímu koncovému bodu. Stav indexeru se dá monitorovat pomocí [rozhraní API stavu indexeru](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Pokud již máte indexery, můžete je jednoduše aktualizovat prostřednictvím [rozhraní API Put](/rest/api/searchservice/create-indexer) a nastavit na `executionEnvironment` `"Private"` .

## <a name="troubleshooting-issues"></a>Řešení potíží

- Pokud při vytváření indexeru dojde k chybě s chybovou zprávou podobnou "přihlašovací údaje zdroje dat jsou neplatné", znamená to, že buď připojení privátního koncového bodu nebylo *schváleno* , nebo není funkční.
Získejte stav sdíleného prostředku privátního propojení pomocí [rozhraní Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get). Pokud byla *schválena* , Projděte si `properties.provisioningState` prostředek. Pokud tomu tak je `Incomplete` , znamená to, že některé základní závislosti prostředku se nepodařilo zřídit – znovu vydejte `PUT` požadavek na opětovné vytvoření sdíleného prostředku privátního propojení, který by měl problém vyřešit. Opětovné schválení může být nezbytné – zkontrolujte stav prostředku znovu a ověřte ho.
- Pokud indexer vytvoříte bez nastavování `executionEnvironment` , může být vytvoření indexeru úspěšné, ale jeho historie spuštění bude zobrazovat, že indexery se neúspěšně spustí. Indexer byste měli [aktualizovat](/rest/api/searchservice/update-indexer) tak, aby určoval spouštěcí prostředí.
- Pokud se indexer vytvoří bez nastavení `executionEnvironment` a úspěšně se spustí, znamená to, že Azure kognitivní hledání rozhodl, že jeho spouštěcí prostředí je konkrétní "soukromé" prostředí služby Search. To se ale může změnit v závislosti na nejrůznějších faktorech (prostředky spotřebované indexerem, zatížení služby vyhledávání atd.) a v pozdějších případech se může zdařit, ale důrazně doporučujeme, abyste si nastavili `executionEnvironment` jako, aby se `"Private"` zajistilo, že v budoucnu nebude chyba.
- [Kvóty a omezení](search-limits-quotas-capacity.md) určují, kolik sdílených prostředků privátních propojení lze vytvořit a závisí na SKU vyhledávací služby.

## <a name="next-steps"></a>Další kroky

Další informace o privátních koncových bodech:

- [Co jsou privátní koncové body?](../private-link/private-endpoint-overview.md)
- [Konfigurace DNS nutné pro privátní koncové body](../private-link/private-endpoint-dns.md)
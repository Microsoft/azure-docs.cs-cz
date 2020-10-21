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
ms.openlocfilehash: ff8aa6688d8a838fa2e06d2eef546025cdd9213f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340049"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Vytvoření připojení indexeru prostřednictvím privátního koncového bodu

Mnoho prostředků Azure, třeba účtů úložiště Azure, je možné nakonfigurovat tak, aby přijímalo připojení ze seznamu virtuálních sítí a odmítání mimo připojení pocházející z veřejné sítě. Pokud k indexování dat v Azure Kognitivní hledání používáte indexer a váš zdroj dat je v privátní síti, můžete vytvořit připojení k odchozímu [privátnímu koncovému bodu](../private-link/private-endpoint-overview.md) pro přístup k datům.

Tato metoda připojení indexeru je předmětem následujících dvou požadavků:

+ Prostředek Azure, který poskytuje obsah nebo kód, musí být dřív zaregistrovaný ve [službě Azure Private Link](https://azure.microsoft.com/services/private-link/).

+ Služba Azure Kognitivní hledání musí být na úrovni Basic nebo vyšší. Tato funkce není na úrovni Free k dispozici. Kromě toho, pokud má indexer dovednosti, musí mít úroveň Standard 2 (S2) nebo vyšší. Další informace najdete v tématu [omezení služby](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Rozhraní API pro správu sdílených prostředků privátního propojení

Soukromé koncové body zabezpečených prostředků, které jsou vytvořené prostřednictvím rozhraní API služby Azure Kognitivní hledání, se označují jako *sdílené prostředky privátního propojení*. Důvodem je to, že sdílíte přístup k prostředku, třeba k účtu úložiště, který je integrovaný do [služby Azure Private Link](https://azure.microsoft.com/services/private-link/).

Prostřednictvím REST API správy poskytuje Azure Kognitivní hledání operaci [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) , kterou můžete použít ke konfiguraci přístupu ze služby Azure kognitivní hledání indexer.

Pomocí verze Preview rozhraní API pro správu vyhledávání (verze *2020-08-01-Preview* nebo novější), který je určen pro *Náhled* v následující tabulce, můžete vytvořit připojení privátního koncového bodu k některým prostředkům. Prostředky bez označení verze *Preview* je možné vytvořit pomocí verze Preview nebo všeobecně dostupné verze rozhraní API (*2020-08-01* nebo novější).

V následující tabulce jsou uvedeny prostředky Azure, pro které můžete vytvořit odchozí soukromé koncové body z Azure Kognitivní hledání. Pokud chcete vytvořit sdílený prostředek privátního propojení, zadejte hodnoty **ID skupiny** přesně tak, jak jsou napsány v rozhraní API. V hodnotách se rozlišují malá a velká písmena.

| Prostředek Azure | ID skupiny |
| --- | --- |
| Azure Storage – objekt BLOB (nebo) ADLS Gen 2 | `blob`|
| Azure Storage – tabulky | `table`|
| Azure Cosmos DB – rozhraní API SQL | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (Preview) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (Preview) | `sites` |

Pomocí [seznamu podporovaných rozhraní API](/rest/api/searchmanagement/privatelinkresources/listsupported)můžete také zadat dotaz na prostředky Azure, pro které jsou připojení odchozího koncového bodu podporovaná.

Ve zbývající části tohoto článku se k předvedení REST API volání používají kombinace rozhraní API pro [ARMClient](https://github.com/projectkudu/ARMClient) a [post](https://www.postman.com/) .

> [!NOTE]
> Příklady v tomto článku jsou založené na následujících předpokladech:
> * Název vyhledávací služby je _Contoso-Search_, který existuje ve skupině prostředků _Contoso_ PŘEDplatného s ID předplatného _00000000-0000-0000-0000-000000000000_. 
> * ID prostředku této vyhledávací služby je _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Search/searchServices/contoso-Search_.

Zbývající příklady ukazují, jak se dá nakonfigurovat služba _společnosti Contoso-Search_ , aby její indexovací indexery měly přístup k datům z zabezpečeného účtu úložiště _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Storage/storageAccounts/contoso-Storage_.

## <a name="secure-your-storage-account"></a>Zabezpečení účtu úložiště

Nakonfigurujte účet úložiště tak, aby [povoloval přístup jenom z konkrétních podsítí](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Pokud v Azure Portal vyberete tuto možnost a necháte sadu prázdnou, znamená to, že není povolen žádný provoz z virtuálních sítí.

   ![Snímek obrazovky s podoknem brány firewall a virtuální sítě, ve kterém je zobrazená možnost pro povolení přístupu k vybraným sítím. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> Přístup k virtuální síti nebo omezení IP adres v účtu úložiště můžete obejít pomocí [důvěryhodného přístupu ke službě Microsoftu](../storage/common/storage-network-security.md#trusted-microsoft-services) . Službu vyhledávání můžete také povolit pro přístup k datům v účtu úložiště. Provedete to tak, že v části [přístup indexeru Azure Storage s výjimkou důvěryhodné služby](search-indexer-howto-access-trusted-service-exception.md). 
>
> Pokud však použijete tento přístup, bude komunikace mezi službou Azure Kognitivní hledání a účtem úložiště prostřednictvím veřejné IP adresy účtu úložiště zajištěna přes zabezpečenou páteřní síť Microsoft.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Krok 1: vytvoření sdíleného prostředku privátního propojení s účtem úložiště

Pokud chcete pro Azure Kognitivní hledání požádat o vytvoření odchozího připojení privátního koncového bodu k účtu úložiště, udělejte toto volání rozhraní API: 

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Obsah *create-pe.jsv* souboru, který představuje tělo požadavku rozhraní API, jsou následující:

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

Po `202 Accepted` úspěchu se vrátí odpověď. Proces vytvoření odchozího privátního koncového bodu je dlouhodobě běžící (asynchronní) operace. Zahrnuje nasazení následujících prostředků:

* Privátní koncový bod přidělený privátní IP adresou ve `"Pending"` stavu. Privátní IP adresa se získává z adresního prostoru, který je přidělený virtuální síti spouštěcího prostředí pro privátního indexeru specifického pro službu Search. Po schválení privátního koncového bodu pocházela jakákoli komunikace z Azure Kognitivní hledání k účtu úložiště z privátní IP adresy a zabezpečeného kanálu privátního propojení.

* Soukromá zóna DNS pro typ prostředku založená na `groupId` . Nasazením tohoto prostředku zajistíte, aby jakékoli vyhledávání DNS k soukromému prostředku využívalo IP adresu, která je přidružená k privátnímu koncovému bodu.

Nezapomeňte zadat správný `groupId` typ prostředku, pro který vytváříte soukromý koncový bod. Jakákoli neshoda bude mít za následek zprávu o neúspěšné odpovědi.

Stejně jako u všech asynchronních operací Azure `PUT` vrátí volání `Azure-AsyncOperation` hodnotu hlavičky, která vypadá takto:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Tento identifikátor URI můžete pravidelně dotazovat, aby se získal stav operace. Než budete pokračovat, doporučujeme počkat, dokud stav operace sdíleného privátního propojení nedosáhne stavu terminálu (to znamená, že stav operace je *úspěšný*).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Krok 2a: schválení připojení privátního koncového bodu pro účet úložiště

> [!NOTE]
> V této části použijete Azure Portal k procházení toku schvalování privátního koncového bodu do úložiště. Alternativně můžete použít [REST API](/rest/api/storagerp/privateendpointconnections) , která je k dispozici prostřednictvím poskytovatele prostředků úložiště.
>
> Jiní poskytovatelé, jako je Azure Cosmos DB nebo Azure SQL Server, nabízejí podobná rozhraní API poskytovatele prostředků úložiště pro správu připojení privátních koncových bodů.

1. V Azure Portal vyberte kartu **připojení privátního koncového bodu** účtu úložiště. Po úspěšném dokončení asynchronní operace by měla být žádost o připojení privátního koncového bodu se zprávou požadavku z předchozího volání rozhraní API.

   ![Snímek obrazovky Azure Portal se zobrazením okna připojení privátního koncového bodu.](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Vyberte privátní koncový bod, který služba Azure Kognitivní hledání vytvořila. Ve sloupci **privátní koncový bod** Identifikujte připojení privátního koncového bodu názvem zadaným v předchozím rozhraní API, vyberte **schválit**a pak zadejte příslušnou zprávu. Obsah zprávy není významný. 

   Zajistěte, aby se připojení privátního koncového bodu zobrazovalo na následujícím snímku obrazovky. Aktualizace stavu na portálu může trvat jednu až dvě minuty.

   ![Snímek obrazovky Azure Portal se zobrazeným stavem "Schváleno" v podokně "připojení privátních koncových bodů".](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Po schválení žádosti o připojení privátního koncového bodu se provoz *může* přesměrovat prostřednictvím privátního koncového bodu. Po schválení privátního koncového bodu vytvoří služba Azure Kognitivní hledání potřebná mapování zón DNS v zóně DNS, která je pro ni vytvořená.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Krok 2b: dotaz na stav sdíleného prostředku privátního propojení

Pokud chcete ověřit, že se prostředek sdíleného privátního propojení aktualizoval po schválení, získejte jeho stav pomocí [rozhraní Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Pokud je v `properties.provisioningState` prostředku `Succeeded` a je to `properties.status` , znamená to, `Approved` že sdílený prostředek privátního propojení je funkční a indexer je možné nakonfigurovat tak, aby komunikoval s privátním koncovým bodem.

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

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Krok 3: Konfigurace indexeru pro spuštění v privátním prostředí

> [!NOTE]
> Tento krok můžete provést před schválením připojení privátního koncového bodu. Dokud nebude připojení privátního koncového bodu schváleno, bude se libovolný indexer, který se pokusí o komunikaci se zabezpečeným prostředkem (například s účtem úložiště), ukončit v přechodném stavu selhání. Nové indexery se nedaří vytvořit. Jakmile je připojení privátního koncového bodu schváleno, mohou indexery získat přístup k účtu privátního úložiště.

1. [Vytvořte zdroj dat](/rest/api/searchservice/create-data-source) , který odkazuje na účet zabezpečeného úložiště a příslušný kontejner v rámci účtu úložiště. Následující snímek obrazovky ukazuje tento požadavek v nástroji post.

   ![Snímek obrazovky znázorňující vytvoření zdroje dat v uživatelském rozhraní pro odeslání.](media\search-indexer-howto-secure-access\create-ds.png )

1. Podobně [vytvořte index](/rest/api/searchservice/create-index) a volitelně [vytvořte dovednosti](/rest/api/searchservice/create-skillset) pomocí REST API.

1. [Vytvořte indexer](/rest/api/searchservice/create-indexer) , který odkazuje na zdroj dat, index a dovednosti, které jste vytvořili v předchozím kroku. Kromě toho vynuťte, aby indexer běžel v prostředí pro spuštění privátního spouštění nastavením vlastnosti konfigurace indexeru `executionEnvironment` na `private` .

   ![Snímek obrazovky znázorňující vytvoření indexeru na uživatelském rozhraní pro odeslání](media\search-indexer-howto-secure-access\create-idr.png)

   Po úspěšném vytvoření indexeru by měl začít indexovat obsah z účtu úložiště přes připojení privátního koncového bodu. Stav indexeru můžete monitorovat pomocí [rozhraní API stavu indexeru](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Pokud již existují indexery, můžete je aktualizovat prostřednictvím [rozhraní API Put](/rest/api/searchservice/create-indexer) nastavením na `executionEnvironment` `private` .

## <a name="troubleshooting"></a>Řešení potíží

- Pokud se vytvoření indexeru nepovede a zobrazí se chybová zpráva, například "přihlašovací údaje zdroje dat jsou neplatné", znamená to, že buď není stav připojení privátního koncového bodu dosud *schváleno* , nebo není připojení funkční. Chcete-li tento problém vyřešit: 
  * Získejte stav sdíleného prostředku privátního propojení pomocí [rozhraní Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get). Pokud je stav *schváleno*, obraťte se na `properties.provisioningState` prostředek. Pokud je tady stav `Incomplete` , znamená to, že se některé z podkladových závislostí prostředku nepodařilo nastavit. `PUT`Nové vydání žádosti o opětovné vytvoření sdíleného prostředku privátního propojení by mělo problém vyřešit. Může být nutné provést znovu schválení. Opětovným zkontrolováním stavu prostředku ověřte, zda je problém vyřešen.

- Pokud vytvoříte indexer bez nastavení jeho `executionEnvironment` vlastnosti, vytvoření může být úspěšné, ale jeho historie provádění zobrazí, že indexer běží neúspěšně. Chcete-li tento problém vyřešit:
   * [Aktualizujte indexer](/rest/api/searchservice/update-indexer) a určete spouštěcí prostředí.

- Pokud jste indexer vytvořili bez nastavování `executionEnvironment` vlastnosti a úspěšně se spustí, znamená to, že služba Azure kognitivní hledání rozhodla, že se jedná o příslušné prostředí pro spuštění, *private* které je specifické pro vyhledávací službu. To se může změnit v závislosti na prostředcích spotřebovaných indexerem, zatížení služby vyhledávání a dalších faktorech a může selhat později. Chcete-li tento problém vyřešit:
  * Důrazně doporučujeme nastavit `executionEnvironment` vlastnost na, aby `private` se zajistilo, že v budoucnu nebude úspěšná.

[Kvóty a omezení](search-limits-quotas-capacity.md) určují, kolik sdílených prostředků privátních propojení lze vytvořit a závisí na SKU vyhledávací služby.

## <a name="next-steps"></a>Další kroky

Další informace o privátních koncových bodech:

- [Co jsou privátní koncové body?](../private-link/private-endpoint-overview.md)
- [Konfigurace DNS nutné pro privátní koncové body](../private-link/private-endpoint-dns.md)

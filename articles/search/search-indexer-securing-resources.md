---
title: Zabezpečený přístup k prostředkům indexeru
titleSuffix: Azure Cognitive Search
description: Koncepční přehled možností zabezpečení na úrovni sítě pro Azure Data Access pomocí indexerů v Azure Kognitivní hledání.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 5075c4858f9584cb19442e19d9009d46d0e00ff8
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89463549"
---
# <a name="indexer-access-to-data-sources-using-azure-network-security-features"></a>Přístup indexeru ke zdrojům dat pomocí funkcí zabezpečení sítě Azure

Indexery Azure Kognitivní hledání můžou během provádění uskutečnit odchozí volání do různých prostředků Azure. Tento článek vysvětluje Princip přístupu indexeru k prostředkům, pokud jsou tyto prostředky chráněny pomocí bran firewall protokolu IP, privátních koncových bodů a dalších mechanismů zabezpečení na úrovni sítě. Možné typy prostředků, ke kterým může indexer přistupovat v typickém běhu, jsou uvedeny v následující tabulce.

| Prostředek | Účel v rámci spuštění indexeru |
| --- | --- |
| Azure Storage (objekty blob, tabulky, ADLS Gen 2) | Zdroj dat |
| Azure Storage (objekty blob, tabulky) | Dovednosti (ukládání obohacených dokumentů do mezipaměti a ukládání projekce pro znalostní bázi Store) |
| Azure Cosmos DB (různá rozhraní API) | Zdroj dat |
| Azure SQL Database | Zdroj dat |
| SQL Server na virtuálních počítačích Azure s IaaS | Zdroj dat |
| Spravované instance SQL | Zdroj dat |
| Azure Functions | Hostitel pro vlastní dovednosti webového rozhraní API |
| Cognitive Services | Připojeno k dovednosti, která se bude používat k vyúčtování nad rámec 20 omezených dokumentů |

> [!NOTE]
> Prostředek služby vnímání, který je připojen k dovednosti, se používá pro fakturaci na základě provedených rozšíření a zapsaných do indexu vyhledávání. Nepoužívá se pro přístup k rozhraní API služeb Cognitive Services. Přístup z kanálu rozšíření indexeru k rozhraní API služeb Cognitive Services dochází přes zabezpečený komunikační kanál, ve kterém se data silně šifrují při přenosu a nikdy se neukládají v klidovém stavu.

Zákazníci můžou tyto prostředky zabezpečit prostřednictvím několika mechanismů izolace sítě, které nabízí Azure. S výjimkou prostředku služby vnímání má indexer omezená možnost přístupu ke všem dalším prostředkům, i když se jedná o izolaci sítě, která je popsána v následující tabulce.

| Prostředek | Omezení IP adresy | Privátní koncový bod |
| --- | --- | ---- |
| Azure Storage (objekty blob, tabulky, ADLS Gen 2) | Podporuje se jenom v případě, že je účet úložiště a služba vyhledávání v různých oblastech. | Podporováno |
| Azure Cosmos DB – rozhraní API SQL | Podporováno | Podporováno |
| Rozhraní API pro Azure Cosmos DB Cassandra, Mongo a Gremlin | Podporováno | Nepodporované |
| Azure SQL Database | Podporováno | Podporováno |
| SQL Server na virtuálních počítačích Azure s IaaS | Podporováno | – |
| Spravované instance SQL | Podporováno | – |
| Azure Functions | Podporováno | Podporováno, jenom pro určité SKU Azure Functions |

> [!NOTE]
> Kromě výše uvedených možností můžou zákazníci využít skutečnost, že pro účty úložiště Azure v síti jsou k disKognitivní hledání [důvěryhodné služby společnosti Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services). To znamená, že konkrétní vyhledávací služba může obejít omezení virtuální sítě nebo IP adresy v účtu úložiště a získat přístup k datům v účtu úložiště, pokud je v účtu úložiště povolené příslušné řízení přístupu na základě rolí. Podrobnosti jsou k dispozici v [Průvodci postupy](search-indexer-howto-access-trusted-service-exception.md). Tato možnost se dá využít místo trasy omezení IP adres, a to v případě, že účet úložiště nebo vyhledávací službu nemůžete přesunout do jiné oblasti.

Při volbě mechanismu zabezpečeného přístupu, který by měl indexer používat, zvažte následující omezení:

- [Koncové body služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) se nepodporují pro žádný prostředek Azure.
- Vyhledávací službu nelze zřídit do konkrétní virtuální sítě – služba Azure Kognitivní hledání tuto funkci nebude nabízet.
- Když indexery využívají (odchozí) privátní koncové body pro přístup k prostředkům, můžou se [účtovat další poplatky za privátní propojení](https://azure.microsoft.com/pricing/details/search/) .

## <a name="indexer-execution-environment"></a>Spouštěcí prostředí indexeru

Indexery služby Azure Kognitivní hledání umožňují efektivně extrahovat obsah ze zdrojů dat, přidávat obohacení do extrahovaný obsah, volitelně generovat projekce před zápisem výsledků do indexu vyhledávání. V závislosti na počtu odpovědností přiřazených k indexeru může běžet v jednom ze dvou prostředí:

- Prostředí, které je privátní pro konkrétní vyhledávací službu. Indexery spuštěné v těchto prostředích sdílejí prostředky s jinými úlohami (jako je třeba jiný zákazník zahájil indexování nebo dotazování na úlohy). Obvykle pouze indexery, které nevyžadují mnoho prostředků (například nepoužívejte dovednosti) spuštěné v tomto prostředí.
- Víceklientské prostředí, které hostuje indexery, které jsou náročné prostředků, jako jsou ty s dovednosti. Prostředky náročné prostředků se spouštějí v tomto prostředí, aby poskytovaly optimální výkon a zároveň zajistili, že jsou k dispozici prostředky služby Search pro jiné úlohy. Toto víceklientské prostředí je spravované a zabezpečené pomocí Azure Kognitivní hledání, a to bez dalších nákladů na zákazníka.

Pro všechny spuštěné indexery určí Azure Kognitivní hledání nejlepší prostředí, ve kterém se indexer spustí.

## <a name="granting-access-to-indexer-ip-ranges"></a>Udělení přístupu k rozsahům IP adres indexeru

Pokud se prostředek, ke kterému indexer snaží získat přístup, omezuje jenom na určitou sadu rozsahů IP adres, musíte rozšířit sadu tak, aby zahrnovala možné rozsahy IP adres, ze kterých může požadavek indexeru nacházet. Jak je uvedeno výše, existují dvě možná prostředí, ve kterých se indexery spouštějí a ze kterých můžou pocházet požadavky na přístup. K práci s indexerem bude nutné přidat IP adresy __obou__ prostředí.

- Pro získání IP adresy konkrétního privátního prostředí služby Search `nslookup` (neboli `ping` ) plně kvalifikovaného názvu domény (FQDN) vaší vyhledávací služby. Plně kvalifikovaný název domény služby vyhledávání ve veřejném cloudu, například, by byl `<service-name>.search.windows.net` . Tyto informace jsou k dispozici na Azure Portal.
- IP adresy více tenantů prostředí jsou k dispozici prostřednictvím `AzureCognitiveSearch` značky služby. [Značky služeb Azure](https://docs.microsoft.com/azure/virtual-network/service-tags-overview) mají pro každou službu PUBLIKOVANÝ rozsah IP adres – to je dostupné prostřednictvím [rozhraní Discovery API (Preview)](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#use-the-service-tag-discovery-api-public-preview) nebo [souboru JSON ke stažení](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#discover-service-tags-by-using-downloadable-json-files). V obou případech jsou rozsahy IP adres rozdělené podle oblastí – můžete vybrat jenom rozsahy IP adres přiřazené pro oblast, ve které je vaše vyhledávací služba zřízená.

U některých zdrojů dat se samotné označení služby dá použít přímo místo výčtu seznamu rozsahů IP adres (IP adresa vyhledávací služby se pořád musí používat explicitně). Tyto zdroje dat omezují přístup tím, že nastaví [pravidlo skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview), které nativně podporuje přidání značky služby, na rozdíl od pravidel protokolu IP, jako jsou ta, která nabízí Azure Storage, CosmosDB, Azure SQL atd. zdroje dat, které podporují schopnost používat `AzureCognitiveSearch` tag služby přímo kromě IP adresy vyhledávací služby:

- [SQL Server na virtuálních počítačích s IaaS](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers#restrict-access-to-the-azure-cognitive-search)

- [Spravované instance SQL](https://docs.microsoft.com/azure/search/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers#verify-nsg-rules)

Podrobnosti jsou popsány v tématu [Průvodce postupy](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Udělení přístupu prostřednictvím privátních koncových bodů

Indexery můžou využívat [soukromé koncové body](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) pro přístup k prostředkům, přístup k nim se uzamkne buď pro výběr virtuálních sítí, nebo nemáte povolené žádné veřejné přístupy.
Tato funkce je dostupná jenom pro placené služby s omezením počtu privátních koncových bodů, které se vytvoří. Podrobnosti o limitech jsou popsány na [stránce omezení Azure Search](search-limits-quotas-capacity.md).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Krok 1: Vytvoření privátního koncového bodu pro zabezpečený prostředek

Zákazníci by měli zavolat operaci správy vyhledávání, [vytvořit nebo aktualizovat *sdílené rozhraní API prostředků privátního propojení* ](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) , aby bylo možné vytvořit připojení privátního koncového bodu ke svému zabezpečenému prostředku (například účtu úložiště). Přenosy, které přecházejí z tohoto (odchozího) připojení privátního koncového bodu, pocházejí jenom z virtuální sítě, která je ve spouštěcím prostředí indexeru specifického pro službu Search.

Azure Kognitivní hledání ověří, že volající tohoto rozhraní API mají oprávnění schvalovat požadavky na připojení privátního koncového bodu na zabezpečený prostředek. Pokud třeba požadujete připojení privátního koncového bodu k účtu úložiště, ke kterému nemáte přístup, toto volání se odmítne.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Krok 2: schválení připojení privátního koncového bodu

Když se dokončí operace (asynchronní), která vytvoří sdílený prostředek privátního propojení, vytvoří se připojení privátního koncového bodu ve stavu čeká na vyřízení. Ještě neexistují žádné přenosy dat přes připojení.
Očekává se, že zákazník tuto žádost najde na svém zabezpečeném prostředku a schválí ji. Obvykle to lze provést prostřednictvím portálu nebo prostřednictvím [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

### <a name="step-3-force-indexers-to-run-in-the-private-environment"></a>Krok 3: vynucení spuštění indexerů v prostředí "Private"

Schválený soukromý koncový bod umožňuje odchozí volání ze služby vyhledávání do prostředku, který má určitou formu omezení přístupu na úrovni sítě (například zdroj dat účtu úložiště, který je nakonfigurován tak, aby se k němu přistupuje jenom z určitých virtuálních sítí), aby byl úspěšný.
To znamená, že libovolný indexer, který se může spojit s takovým zdrojem dat přes soukromý koncový bod, bude úspěšný.
Pokud soukromý koncový bod není schválený nebo pokud indexer nevyužívá připojení privátního koncového bodu, bude spuštění indexeru ukončeno `transientFailure` .

Chcete-li povolit indexerům přístup k prostředkům prostřednictvím připojení privátních koncových bodů, je nutné nastavit `executionEnvironment` indexer na `"Private"` , aby se zajistilo, že všechny spuštěné indexery budou moci využívat soukromý koncový bod. Důvodem je to, že privátní koncové body se zřídí v rámci konkrétního prostředí specifického pro službu vyhledávání.

```json
    {
      "name" : "myindexer",
      ... other indexer properties
      "parameters" : {
          ... other parameters
          "configuration" : {
            ... other configuration properties
            "executionEnvironment": "Private"
          }
        }
    }
```

Tyto kroky jsou podrobněji popsané v tématu [Průvodce postupy](search-indexer-howto-access-private.md).
Jakmile máte ke zdroji schválený privátní koncový bod, indexery, které jsou nastavené jako *soukromé* , se pokusí získat přístup prostřednictvím připojení k privátnímu koncovému bodu.

### <a name="limits"></a>Omezení

Pro zajištění optimálního výkonu a stability vyhledávací služby se zavedla omezení (podle skladové položky služby Search) na následujících dimenzích:

- Druhy indexerů, které lze nastavit jako *soukromé*.
- Počet sdílených prostředků privátního propojení, které lze vytvořit.
- Počet různých typů prostředků, pro které lze vytvořit sdílené prostředky privátního propojení.

Tato omezení jsou popsaná v [omezeních služeb](search-limits-quotas-capacity.md).

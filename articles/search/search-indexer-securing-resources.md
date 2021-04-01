---
title: Přístup indexeru k chráněným prostředkům
titleSuffix: Azure Cognitive Search
description: Koncepční přehled možností zabezpečení na úrovni sítě pro Azure Data Access pomocí indexerů v Azure Kognitivní hledání.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcb6e91bba367363385214806077146b1a24fe7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92503483"
---
# <a name="indexer-access-to-content-protected-by-azure-network-security-features-azure-cognitive-search"></a>Přístup indexeru k obsahu chráněnému funkcemi zabezpečení sítě Azure (Azure Kognitivní hledání)

Indexery Azure Kognitivní hledání můžou během provádění uskutečnit odchozí volání do různých prostředků Azure. Tento článek vysvětluje principy přístupu indexeru k obsahu, který je chráněný branami firewall protokolu IP, soukromými koncovými body nebo jinými mechanismy zabezpečení na úrovni sítě Azure. Indexer provádí odchozí volání ve dvou situacích: připojení ke zdrojům dat během indexování a připojení k zapouzdřenému kódu prostřednictvím dovednosti. Seznam všech možných typů prostředků, ke kterým může indexer přistupovat v typickém běhu, je uveden v následující tabulce.

| Prostředek | Účel v rámci spuštění indexeru |
| --- | --- |
| Azure Storage (objekty blob, tabulky, ADLS Gen 2) | Zdroj dat |
| Azure Storage (objekty blob, tabulky) | Dovednosti (ukládání obohacených dokumentů do mezipaměti a ukládání projekce pro znalostní bázi Store) |
| Azure Cosmos DB (různá rozhraní API) | Zdroj dat |
| Azure SQL Database | Zdroj dat |
| SQL Server na virtuálním počítači Azure | Zdroj dat |
| Spravovaná instance SQL | Zdroj dat |
| Azure Functions | Hostitel pro vlastní dovednosti webového rozhraní API |
| Cognitive Services | Připojeno k dovednosti, která se bude používat k vyúčtování nad rámec 20 omezených dokumentů |

> [!NOTE]
> Prostředek služby vnímání, který je připojen k dovednosti, se používá pro fakturaci na základě provedených rozšíření a zapsaných do indexu vyhledávání. Nepoužívá se pro přístup k rozhraní API služeb Cognitive Services. Přístup z kanálu rozšíření indexeru k rozhraní API služeb Cognitive Services dochází prostřednictvím interního zabezpečeného komunikačního kanálu, ve kterém jsou data při přenosu silně zašifrovaná a nikdy se neukládají v klidovém stavu.

Zákazníci můžou tyto prostředky zabezpečit prostřednictvím několika mechanismů izolace sítě, které nabízí Azure. S výjimkou prostředku služby vnímání mají indexery omezené možnosti přístupu k všem dalším prostředkům, i když jsou izolované v síti, které jsou uvedené v následující tabulce.

| Prostředek | Omezení IP adresy | Privátní koncový bod |
| --- | --- | ---- |
| Azure Storage (objekty blob, tabulky, ADLS Gen 2) | Podporuje se jenom v případě, že je účet úložiště a služba vyhledávání v různých oblastech. | Podporováno |
| Azure Cosmos DB – rozhraní API SQL | Podporováno | Podporováno |
| Rozhraní API pro Azure Cosmos DB Cassandra, Mongo a Gremlin | Podporováno | Nepodporované |
| Azure SQL Database | Podporováno | Podporováno |
| SQL Server na virtuálním počítači Azure | Podporováno | – |
| Spravovaná instance SQL | Podporováno | – |
| Azure Functions | Podporováno | Podporováno, pouze pro určité úrovně služby Azure Functions |

> [!NOTE]
> Kromě Azure Storage výše uvedených možností můžou zákazníci využít skutečnost, že služba Azure Kognitivní hledání je [důvěryhodnou službou Microsoftu](../storage/common/storage-network-security.md#trusted-microsoft-services). To znamená, že konkrétní vyhledávací služba může obejít omezení virtuální sítě nebo IP adresy v účtu úložiště a získat přístup k datům v účtu úložiště, pokud je v účtu úložiště povolené příslušné řízení přístupu na základě role. Další informace najdete v tématu [připojení indexeru pomocí výjimky důvěryhodné služby](search-indexer-howto-access-trusted-service-exception.md). Tato možnost se dá využít místo trasy omezení IP adres, a to v případě, že účet úložiště nebo vyhledávací službu nemůžete přesunout do jiné oblasti.

Při volbě mechanismu zabezpečeného přístupu, který by měl indexer používat, zvažte následující omezení:

- Indexer se nemůže připojit ke [koncovému bodu služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md). Veřejné koncové body s přihlašovacími údaji, privátními koncovými body, důvěryhodnými službami a adresováním IP jsou jedinou podporovanou metodologií pro připojení indexeru.
- Vyhledávací službu nelze zřídit do konkrétní virtuální sítě, která je spuštěna nativně na virtuálním počítači. Tato funkce nebude nabízena službou Azure Kognitivní hledání.
- Když indexery využívají (odchozí) privátní koncové body pro přístup k prostředkům, můžou se [účtovat další poplatky za privátní propojení](https://azure.microsoft.com/pricing/details/search/) .

## <a name="indexer-execution-environment"></a>Spouštěcí prostředí indexeru

Indexery služby Azure Kognitivní hledání umožňují efektivně extrahovat obsah ze zdrojů dat, přidávat obohacení do extrahovaný obsah, volitelně generovat projekce před zápisem výsledků do indexu vyhledávání. V závislosti na počtu odpovědností přiřazených k indexeru může běžet v jednom ze dvou prostředí:

- Prostředí, které je privátní pro konkrétní vyhledávací službu. Indexery spuštěné v těchto prostředích sdílejí prostředky s jinými úlohami (například s dalšími zákazníky zahájenými při indexování nebo dotazování úloh). Obvykle se v tomto prostředí spouštějí pouze indexery, které provádějí indexování založené na textu (například nepoužívají dovednosti).
- Víceklientské prostředí hostující indexery, které jsou náročné na prostředky, jako jsou například pomocí dovednosti. Toto prostředí slouží k přesměrování výpočetně náročného zpracování, takže jsou k dispozici prostředky specifické pro službu, které jsou dostupné pro běžné operace. Toto víceklientské prostředí je spravované a zabezpečené Microsoftem bez dalších poplatků zákazníkům.

Pro všechny spuštěné indexery určí Azure Kognitivní hledání nejlepší prostředí, ve kterém se indexer spustí. Pokud k řízení přístupu k prostředkům Azure používáte bránu firewall protokolu IP, získáte informace o prostředích pro spouštění, které vám pomůžou nastavit rozsah IP adres, který je včetně obou.

## <a name="granting-access-to-indexer-ip-ranges"></a>Udělení přístupu k rozsahům IP adres indexeru

Pokud se prostředek, ke kterému indexer snaží získat přístup, omezuje jenom na určitou sadu rozsahů IP adres, musíte rozšířit sadu tak, aby zahrnovala možné rozsahy IP adres, ze kterých může požadavek indexeru nacházet. Jak je uvedeno výše, existují dvě možná prostředí, ve kterých se indexery spouštějí a ze kterých můžou pocházet požadavky na přístup. K práci s indexerem bude nutné přidat IP adresy **obou** prostředí.

- Pro získání IP adresy konkrétního privátního prostředí služby Search `nslookup` (neboli `ping` ) plně kvalifikovaného názvu domény (FQDN) vaší vyhledávací služby. Například plně kvalifikovaný název domény služby vyhledávání ve veřejném cloudu by byl `<service-name>.search.windows.net` . Tyto informace jsou k dispozici na Azure Portal.
- IP adresy více tenantů prostředí jsou k dispozici prostřednictvím `AzureCognitiveSearch` značky služby. [Značky služeb Azure](../virtual-network/service-tags-overview.md) mají pro každou službu PUBLIKOVANÝ rozsah IP adres – to je dostupné prostřednictvím [rozhraní Discovery API (Preview)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) nebo [souboru JSON ke stažení](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). V obou případech jsou rozsahy IP adres rozdělené podle oblastí – můžete vybrat jenom rozsahy IP adres přiřazené pro oblast, ve které je vaše vyhledávací služba zřízená.

U některých zdrojů dat se samotné označení služby dá použít přímo místo výčtu seznamu rozsahů IP adres (IP adresa vyhledávací služby se pořád musí používat explicitně). Tyto zdroje dat omezují přístup tím, že nastaví [pravidlo skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md), které nativně podporuje přidání značky služby, na rozdíl od pravidel protokolu IP, jako jsou ta, která nabízí Azure Storage, Cosmos DB, Azure SQL a tak dále. Zdroje dat, které podporují schopnost používat `AzureCognitiveSearch` tag služby přímo kromě IP adresy vyhledávací služby, jsou:

- [SQL Server na virtuálních počítačích Azure](./search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md#restrict-access-to-the-azure-cognitive-search)

- [Spravované instance SQL](./search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md#verify-nsg-rules)

Další informace o této možnosti připojení najdete v tématu [připojení indexeru pomocí brány firewall protokolu IP](search-indexer-howto-access-ip-restricted.md).

## <a name="granting-access-via-private-endpoints"></a>Udělení přístupu prostřednictvím privátních koncových bodů

Indexery můžou využívat [soukromé koncové body](../private-link/private-endpoint-overview.md) pro přístup k prostředkům, přístup k nim se uzamkne buď pro výběr virtuálních sítí, nebo nemáte povolené žádné veřejné přístupy.
Tato funkce je k dispozici pouze v fakturovatelných vyhledávacích službách s omezením počtu privátních koncových bodů, které jsou vytvořeny. Další informace najdete v tématu [omezení služby](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

### <a name="step-1-create-a-private-endpoint-to-the-secure-resource"></a>Krok 1: Vytvoření privátního koncového bodu pro zabezpečený prostředek

Zákazníci by měli volat operaci správy hledání, [rozhraní CreateOrUpdate API](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) na **sdíleném prostředku privátního propojení**, aby bylo možné vytvořit připojení privátního koncového bodu ke svému zabezpečenému prostředku (například účet úložiště). Přenosy, které přecházejí z tohoto (odchozího) připojení privátního koncového bodu, pocházejí jenom z virtuální sítě, která je ve spouštěcím prostředí indexeru specifického pro službu Search.

Azure Kognitivní hledání ověří, jestli volající tohoto rozhraní API mají oprávnění Azure RBAC ke schválení požadavků na připojení privátního koncového bodu na zabezpečený prostředek. Například pokud požadujete připojení privátního koncového bodu k účtu úložiště s oprávněním jen pro čtení, toto volání bude odmítnuto.

### <a name="step-2-approve-the-private-endpoint-connection"></a>Krok 2: schválení připojení privátního koncového bodu

Když se dokončí operace (asynchronní), která vytvoří sdílený prostředek privátního propojení, vytvoří se připojení privátního koncového bodu ve stavu čeká na vyřízení. Ještě neexistují žádné přenosy dat přes připojení.
Očekává se, že zákazník tuto žádost najde na svém zabezpečeném prostředku a schválí ji. Obvykle to lze provést buď prostřednictvím Azure Portal, nebo prostřednictvím [REST API](/rest/api/virtualnetwork/privatelinkservices/updateprivateendpointconnection).

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

Tyto kroky jsou podrobněji popsány v tématu [připojení indexeru prostřednictvím privátního koncového bodu](search-indexer-howto-access-private.md).
Jakmile máte ke zdroji schválený privátní koncový bod, indexery, které jsou nastavené jako *soukromé* , se pokusí získat přístup prostřednictvím připojení k privátnímu koncovému bodu.

### <a name="limits"></a>Omezení

Pro zajištění optimálního výkonu a stability vyhledávací služby se zavedla omezení (podle úrovně služby vyhledávání) na následujících dimenzích:

- Druhy indexerů, které lze nastavit jako *soukromé*.
- Počet sdílených prostředků privátního propojení, které lze vytvořit.
- Počet různých typů prostředků, pro které lze vytvořit sdílené prostředky privátního propojení.

Tato omezení jsou popsaná v [omezeních služeb](search-limits-quotas-capacity.md).

## <a name="next-steps"></a>Další kroky

- [Připojení indexeru prostřednictvím bran IP firewall](search-indexer-howto-access-ip-restricted.md)
- [Připojení indexeru pomocí výjimky důvěryhodné služby](search-indexer-howto-access-trusted-service-exception.md)
- [Připojení indexeru k privátnímu koncovému bodu](search-indexer-howto-access-private.md)
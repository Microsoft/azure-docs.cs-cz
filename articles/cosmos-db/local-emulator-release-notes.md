---
title: Stažení a poznámky k verzi pro Azure Cosmos DB emulátoru
description: Získejte poznámky k verzi Azure Cosmos DB emulátoru pro různé verze a informace ke stažení.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 442fa1c76d4667a54f1c13b411b1c052c9a5b134
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566638"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Azure Cosmos DB emulátor – poznámky k verzi a informace o stažení
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Tento článek ukazuje poznámky k verzi emulátoru Azure Cosmos DB se seznamem aktualizací funkcí, které byly provedeny v jednotlivých verzích. Obsahuje také nejnovější verzi emulátoru, která se má stáhnout a použít.

## <a name="download"></a>Stáhnout

| | Odkaz |
|---------|---------|
|**Stažení MSI**|[Stažení softwaru společnosti Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Začínáme**|[Vývoj místně pomocí emulátoru Azure Cosmos DB](local-emulator.md)|

## <a name="release-notes"></a>Poznámky k verzi

### <a name="21111-22-february-2021"></a>2.11.11 (22. února 2021)

 - Tato verze aktualizuje obsah místní Průzkumník dat na nejnovější verzi Azure Portal.


### <a name="21110-5-january-2021"></a>2.11.10 (5. ledna 2021)

 - Tato verze aktualizuje obsah místního Průzkumník dat na nejnovější verzi Azure Portal a přidá novou možnost Public, "/ExportPemCert", která umožňuje uživateli emulátoru přímo exportovat certifikát veřejného emulátoru jako. Soubor PEM

### <a name="2119-3-december-2020"></a>2.11.9 (3. prosince 2020)

 - Tato verze řeší několik problémů s funkcí emulátoru Azure Cosmos DB kromě obecného aktualizace obsahu, která odráží nejnovější funkce a vylepšení v Azure Cosmos DB:
 * Opravte problém, kde při použití přímého režimu a klientských aplikací Java selže velké požadavky na datovou část dokumentu.
 * Opravte problém s připojením ke koncovému bodu MongoDB verze 3,6, pokud cílíte na aplikace založené na rozhraní .NET.

### <a name="2118-6-november-2020"></a>2.11.8 (6. listopadu 2020)

 - Tato verze zahrnuje aktualizaci emulátoru Cosmos Průzkumník dat a opravuje problém, kdy se klienti TLS 1,3 pokoušejí otevřít Průzkumník dat.

### <a name="2116-6-october-2020"></a>2.11.6 (6. října 2020)

 - Tato verze řeší problém týkající se souběžnosti, kdy může být vytvořeno více kontejnerů současně. V takových případech jsou data emulátoru ponechána v poškozeném stavu a následující požadavky rozhraní API na koncový bod emulátoru by mohly selhat s chybami "služba není dostupná", která vyžaduje restart a resetování místních dat emulátoru.

### <a name="2115-23-august-2020"></a>2.11.5 (23. srpna 2020)

Tato verze přidává dvě nové možnosti spuštění emulátoru Cosmos: 

* "/EnablePreview" – povoluje funkce ve verzi Preview pro emulátor. Funkce verze Preview, které jsou stále ve vývoji, a ke kterým lze přistupovat prostřednictvím CI a psaní vzorků.
* "/EnableAadAuthentication" – umožňuje emulátoru přijímat vlastní tokeny Azure Active Directory jako alternativu k primárním klíčům Azure Cosmos. Tato funkce je stále ve vývoji; konkrétní přiřazení rolí a další nastavení související s oprávněními se aktuálně nepodporují.

### <a name="2112-07-july-2020"></a>2.11.2 (7. července 2020)

- Tato verze mění způsob, jakým se shromažďují trasování ETL při řešení potíží s emulátorem Cosmos. WPR (nástroje modulu runtime výkonu systému Windows) jsou nyní výchozí nástroje pro zachycení trasování založené na ETL, zatímco staré zachycení založené na nástroji LOGMAN je zastaralé. Tato změna je vyžadována částečně, protože nejnovější aktualizace zabezpečení systému Windows měly neočekávaný dopad na to, jak program LOGMAN funguje při spuštění prostřednictvím emulátoru Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10. června 2020)

- Tato verze opravuje několik chyb souvisejících s emulátorem Průzkumník dat. V některých případech při použití emulátoru Průzkumník dat přes webový prohlížeč se nemůže připojit ke koncovému bodu emulátoru Cosmos a všechny související akce, jako je vytváření databáze nebo kontejneru, budou mít za následek chybu. Druhý opravený problém souvisí s vytvořením položky ze souboru JSON pomocí akce Průzkumník dat nahrávání.

### <a name="2110"></a>2.11.0

- Tato verze zavádí podporu pro zajištění propustnosti s podporou automatického škálování. Tyto nové funkce zahrnují možnost nastavit vlastní maximální stanovenou úroveň propustnosti v jednotkách žádosti (RU/s), povolit automatické škálování u stávajících databází a kontejnerů a programovou podporu prostřednictvím Azure Cosmos DB SDK.
- Oprava problému při dotazování přes velký počet dokumentů (více než 1 GB): emulátor selže s kódem stavu vnitřní chyby 500.

### <a name="292"></a>2.9.2

- Tato verze opravuje chybu při povolování podpory pro koncový bod MongoDb verze 3,2. Také přidává podporu pro generování trasování ETL pro účely řešení potíží pomocí WPR namísto programu LOGMAN.

### <a name="291"></a>2.9.1

- Tato verze opravuje několik problémů v podpoře rozhraní API pro dotazy a obnoví kompatibilitu se staršími OSs, jako je Windows Server 2012.

### <a name="290"></a>2.9.0

- Tato verze přidává možnost nastavit konzistenci na konzistentní předponu a zvýšit maximální limity pro uživatele a oprávnění.

### <a name="272"></a>2.7.2

- Tato verze přidá podporu serveru MongoDB verze 3,6 do emulátoru Cosmos. Pokud chcete spustit koncový bod MongoDB, který cílí na verzi 3,6 služby, spusťte emulátor z příkazového řádku správce s možností/EnableMongoDBEndpoint = 3.6.

### <a name="270"></a>2.7.0

- Tato verze opravuje regresi, která brání uživatelům ve spouštění dotazů na účet rozhraní SQL API z emulátoru při použití klientů využívajících rozhraní .NET Core nebo x86 .NET.

### <a name="246"></a>2.4.6

- Tato verze poskytuje paritu s funkcemi ve službě Azure Cosmos od července 2019 s výjimkami popsanými v části [vývoj místně pomocí emulátoru Azure Cosmos DB](local-emulator.md). Opravuje také několik chyb souvisejících s emulátorem vypnout při vyvolání prostřednictvím příkazového řádku a přepsání interních IP adres pro klienty SDK pomocí přímého připojení k režimu.

### <a name="243"></a>2.4.3

- Ve výchozím nastavení zakázáno spouštění služby MongoDB. Jako výchozí je povolený jenom koncový bod SQL. Uživatel musí spustit koncový bod ručně pomocí možnosti příkazového řádku emulátoru "/EnableMongoDbEndpoint". Teď to vypadá stejně jako všechny ostatní koncové body služby, například Gremlin, Cassandra a Table.
- Opravili jsme chybu v emulátoru, když začínáte na "/AllowNetworkAccess", kde koncové body Gremlin, Cassandra a Table nesprávně zpracovávají požadavky od externích klientů.
- Přidejte přímé spojovací porty do nastavení pravidel brány firewall.

### <a name="240"></a>2.4.0

- Opravili jsme problém s tím, že se nepodaří spustit emulátor, když se v hostitelském počítači nacházejí aplikace pro monitorování sítě, třeba Pulse Client.

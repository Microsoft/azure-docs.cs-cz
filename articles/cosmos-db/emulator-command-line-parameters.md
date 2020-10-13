---
title: Reference k příkazovému řádku a prostředí PowerShell pro emulátor Azure Cosmos DB
description: Přečtěte si o parametrech příkazového řádku pro emulátor Azure Cosmos DB, jak řídit emulátor pomocí PowerShellu a jak změnit počet kontejnerů, které můžete v emulátoru vytvořit.
ms.service: cosmos-db
ms.topic: how-to
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: f8bcadf25ac8e001657f2be012f99ddb507e672d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445183"
---
# <a name="command-line-and-powershell-reference-for-azure-cosmos-db-emulator"></a>Reference k příkazovému řádku a prostředí PowerShell pro emulátor Azure Cosmos DB

Emulátor Azure Cosmos poskytuje místní prostředí, které emuluje službu Azure Cosmos DB pro účely místního vývoje. Po [instalaci emulátoru](local-emulator.md)můžete řídit emulátor pomocí příkazového řádku a příkazů prostředí PowerShell. Tento článek popisuje, jak použít příkazy příkazového řádku a prostředí PowerShell ke spuštění a zastavení emulátoru, konfiguraci možností a provádění dalších operací. Je nutné spustit příkazy z umístění instalace.

##  <a name="manage-the-emulator-with-command-line-syntax"></a><a id="command-line"></a>Správa emulátoru pomocí syntaxe příkazového řádku

```cmd
Microsoft.Azure.Cosmos.Emulator.exe [/Shutdown] [/DataPath] [/Port] [/MongoPort] [/DirectPorts] [/Key] [/EnableRateLimiting] [/DisableRateLimiting] [/NoUI] [/NoExplorer] [/EnableMongoDbEndpoint] [/?]
```

Pokud chcete zobrazit seznam možností, na příkazovém řádku zadejte `Microsoft.Azure.Cosmos.Emulator.exe /?`.

|**Možnost** | **Popis** | **Příkaz**| **Argumenty**|
|---|---|---|---|
|[Žádné argumenty] | Spustí emulátor Azure Cosmos s výchozími nastaveními. |Microsoft.Azure.Cosmos.Emulator.exe| |
|[Nápověda] |Zobrazí seznam podporovaných argumentů příkazového řádku.|Microsoft.Azure.Cosmos.Emulator.exe/? | |
| GetStatus |Získá stav emulátoru Azure Cosmos. Stav je indikován ukončovacím kódem: 1 = spouštění, 2 = spuštěno, 3 = zastaveno. Záporný ukončovací kód označuje, že došlo k chybě. Žádný jiný výstup neexistuje. | Microsoft.Azure.Cosmos.Emulator.exe/GetStatus| |
| Vypnutí| Ukončí emulátor Azure Cosmos.| Microsoft.Azure.Cosmos.Emulator.exe/Shutdown | |
|DataPath | Určuje cestu, do které chcete uložit datové soubory. Výchozí hodnota je%LocalAppdata%\CosmosDBEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/DataPath =\<datapath\> | \<datapath\>: Přístupná cesta |
|Port | Určuje číslo portu pro emulátor. Výchozí hodnota je 8081. |Microsoft.Azure.Cosmos.Emulator.exe/port =\<port\> | \<port\>: Jedno číslo portu |
| ComputePort | Určuje číslo portu, které se má použít pro službu COMPUTE Interop Gateway. Port testu koncového bodu HTTP brány se počítá jako ComputePort + 79. Proto musí být ComputePort a ComputePort + 79 otevřené a dostupné. Výchozí hodnota je 8900. | Microsoft.Azure.Cosmos.Emulator.exe/ComputePort =\<computeport\> | \<computeport\>: Jedno číslo portu |
| EnableMongoDbEndpoint = 3.2 | Povolí rozhraní MongoDB API 3,2. | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.2 | |
| EnableMongoDbEndpoint = 3.6 | Povolí rozhraní MongoDB API 3,6. | Microsoft.Azure.Cosmos.Emulator.exe/EnableMongoDbEndpoint = 3.6 | |
| MongoPort | Určuje číslo portu, který chcete použít pro rozhraní API kompatibility MongoDB. Výchozí hodnota je 10255. |Microsoft.Azure.Cosmos.Emulator.exe/MongoPort =\<mongoport\>|\<mongoport\>: Jedno číslo portu|
| EnableCassandraEndpoint | Povolí rozhraní API Cassandra | Microsoft.Azure.Cosmos.Emulator.exe/EnableCassandraEndpoint | |
| CassandraPort | Určuje číslo portu, který se má použít pro koncový bod Cassandra. Výchozí hodnota je 10350. | Microsoft.Azure.Cosmos.Emulator.exe/CassandraPort =\<cassandraport\> | \<cassandraport\>: Jedno číslo portu |
| EnableGremlinEndpoint | Povolí rozhraní Gremlin API. | Microsoft.Azure.Cosmos.Emulator.exe/EnableGremlinEndpoint | |
| GremlinPort | Číslo portu, které se má použít pro koncový bod Gremlin Výchozí hodnota je 8901. | Microsoft.Azure.Cosmos.Emulator.exe/GremlinPort =\<port\> | \<port\>: Jedno číslo portu |
|EnableTableEndpoint | Povolí Azure rozhraní API pro tabulky | Microsoft.Azure.Cosmos.Emulator.exe/EnableTableEndpoint | |
|TablePort | Číslo portu, které se má použít pro koncový bod tabulky Azure Výchozí hodnota je 8902. | Microsoft.Azure.Cosmos.Emulator.exe/TablePort =\<port\> | \<port\>: Jedno číslo portu|
| KeyFile | Načte autorizační klíč ze zadaného souboru. Pro vytvoření souboru klíče použijte možnost/GenKeyFile | Microsoft.Azure.Cosmos.Emulator.exe/KeyFile =\<file_name\> | \<file_name\>: Cesta k souboru |
| ResetDataPath | Rekurzivně odstraní všechny soubory v zadané cestě. Pokud cestu nezadáte, použije se výchozí hodnota%LOCALAPPDATA%\CosmosDbEmulator. | Microsoft.Azure.Cosmos.Emulator.exe/ResetDataPath =\<path> | \<path\>: Cesta k souboru  |
| StartTraces  |  Spusťte shromažďování protokolů trasování ladění pomocí programu LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe/StartTraces | |
| StopTraces     | Zastavte shromažďování protokolů trasování ladění pomocí programu LOGMAN. | Microsoft.Azure.Cosmos.Emulator.exe/StopTraces  | |
| StartWprTraces  |  Spusťte shromažďování protokolů trasování ladění pomocí nástroje pro záznam výkonu systému Windows. | Microsoft.Azure.Cosmos.Emulator.exe/StartWprTraces | |
| StopWprTraces     | Zastavte shromažďování protokolů trasování ladění pomocí nástroje pro záznam výkonu systému Windows. | Microsoft.Azure.Cosmos.Emulator.exe/StopWprTraces  | |
|FailOnSslCertificateNameMismatch | Ve výchozím nastavení emulátor znovu vygeneruje svůj certifikát TLS/SSL podepsaný svým držitelem, pokud síť SAN certifikátu neobsahuje název domény hostitele emulátoru, místní adresu IPv4, localhost a adresu 127.0.0.1. Tato možnost způsobí, že emulátor při spuštění selže. Pak použijte možnost/GenCert k vytvoření a instalaci nového certifikátu TLS/SSL podepsaného svým držitelem. | Microsoft.Azure.Cosmos.Emulator.exe/FailOnSslCertificateNameMismatch  | |
| GenCert | Vygenerujte a nainstalujte nový certifikát TLS/SSL podepsaný svým držitelem. Volitelně můžete zahrnout čárkami oddělený seznam dalších názvů DNS pro přístup k emulátoru přes síť. | Microsoft.Azure.Cosmos.Emulator.exe/GenCert =\<dns-names\> |\<dns-names\>: Volitelný čárkami oddělený seznam dalších názvů DNS  |
| DirectPorts |Určuje porty, které chcete použít pro přímé připojení. Výchozí hodnoty jsou 10251,10252,10253,10254. | Microsoft.Azure.Cosmos.Emulator.exe/DirectPorts:\<directports\> | \<directports\>: Seznam 4 portů oddělených čárkami |
| Klíč |Autorizační klíč pro emulátor. Klíč musí být 64bajtový vektor s kódováním base-64. | Microsoft.Azure.Cosmos.Emulator.exe/Key:\<key\> | \<key\>: Klíč musí být kódování Base-64 pro vektor 64-byte.|
| EnableRateLimiting | Určuje, že je povoleno chování omezující četnost požadavků. |Microsoft.Azure.Cosmos.Emulator.exe/EnableRateLimiting | |
| DisableRateLimiting |Určuje, že je zakázáno chování omezující četnost požadavků. |Microsoft.Azure.Cosmos.Emulator.exe/DisableRateLimiting | |
| NoUI | Nezobrazuje uživatelské rozhraní emulátoru. | Microsoft.Azure.Cosmos.Emulator.exe/NoUI | |
| NoExplorer | Nezobrazuje Průzkumníka dat při spuštění. |Microsoft.Azure.Cosmos.Emulator.exe/NoExplorer | | 
| PartitionCount | Určuje maximální počet kontejnerů rozdělený na oddíly. Další informace najdete v tématu [Změna počtu kontejnerů](#set-partitioncount) . | Microsoft.Azure.Cosmos.Emulator.exe/PartitionCount =\<partitioncount\> | \<partitioncount\>: Maximální počet povolených kontejnerů s jedním oddílem. Výchozí hodnota je 25. Maximální povolený počet je 250.|
| DefaultPartitionCount| Určuje výchozí počet oddílů pro kontejner rozdělený na oddíly. | Microsoft.Azure.Cosmos.Emulator.exe/DefaultPartitionCount =\<defaultpartitioncount\> | \<defaultpartitioncount\> Výchozí hodnota je 25.|
| AllowNetworkAccess | Povolí přístup k emulátoru přes síť. \<key_string\> \<file_name\> Abyste mohli povolit přístup k síti, musíte taky předat/Key = nebo/keyfile =. | Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/Key = \<key_string\> nebo Microsoft.Azure.Cosmos.Emulator.exe/AllowNetworkAccess/keyfile =\<file_name\>| |
| NoFirewall | Neupravujte pravidla brány firewall, pokud se používá možnost/AllowNetworkAccess. |Microsoft.Azure.Cosmos.Emulator.exe/NoFirewall | |
| GenKeyFile | Vygeneruje nový autorizační klíč a uloží ho do zadaného souboru. Generovaný klíč lze použít s možností /Key nebo/KeyFile. | Microsoft.Azure.Cosmos.Emulator.exe/GenKeyFile =\<path to key file\> | |
| Konzistence | Nastaví výchozí úroveň konzistence pro účet. | Microsoft.Azure.Cosmos.Emulator.exe/Consistency =\<consistency\> | \<consistency\>: Hodnota musí být jedna z následujících [úrovní konzistence](consistency-levels.md): Session, Strong, případný nebo BoundedStaleness. Výchozí hodnota je Session. |
| ? | Zobrazí zprávu nápovědy.| | |

## <a name="manage-the-emulator-with-powershell"></a>Správa emulátoru pomocí PowerShellu

Emulátor se dodává s modulem PowerShellu pro spuštění, zastavení, odinstalaci a načtení stavu služby. Spuštěním následující rutiny použijte modul prostředí PowerShell:

```powershell
Import-Module "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules\Microsoft.Azure.CosmosDB.Emulator"
```

nebo umístěte `PSModules` adresář na svůj `PSModulesPath` a naimportujte ho, jak je znázorněno v následujícím příkazu:

```powershell
$env:PSModulesPath += "$env:ProgramFiles\Azure Cosmos DB Emulator\PSModules"
Import-Module Microsoft.Azure.CosmosDB.Emulator
```

Zde je uveden seznam příkazů pro řízení emulátoru z PowerShellu:

### `Get-CosmosDbEmulatorStatus`

**Syntax**

`Get-CosmosDbEmulatorStatus`

**Poznámky**

Vrátí jednu z těchto hodnot ServiceControllerStatus: ServiceControllerStatus.StartPending, ServiceControllerStatus.Running nebo ServiceControllerStatus.Stopped.

### `Start-CosmosDbEmulator`

**Syntax**

`Start-CosmosDbEmulator [-DataPath <string>] [-DefaultPartitionCount <uint16>] [-DirectPort <uint16[]>] [-MongoPort <uint16>] [-NoUI] [-NoWait] [-PartitionCount <uint16>] [-Port <uint16>] [<CommonParameters>]`

**Poznámky**

Spustí emulátor. Ve výchozím nastavení tento příkaz čeká, dokud emulátor nebude připraven přijímat požadavky. Pokud chcete, aby se rutina vrátila, jakmile spustí emulátor, použijte možnost -NoWait.

### `Stop-CosmosDbEmulator`

**Syntax**

 `Stop-CosmosDbEmulator [-NoWait]`

**Poznámky**

Zastaví emulátor. Ve výchozím nastavení tento příkaz čeká, až emulátor je zcela vypnutý. Pokud chcete, aby se rutina vrátila, jakmile se emulátor začne vypínat, použijte možnost -NoWait.

### `Uninstall-CosmosDbEmulator`

**Syntax**

`Uninstall-CosmosDbEmulator [-RemoveData]`

**Poznámky**

Odinstaluje emulátor a volitelně odstraní úplný obsah $env:LOCALAPPDATA\CosmosDbEmulator.
Rutina zajišťuje zastavení emulátoru před jeho odinstalací.

## <a name="change-the-number-of-default-containers"></a><a id="set-partitioncount"></a>Změna počtu výchozích kontejnerů

Ve výchozím nastavení můžete vytvořit až 25 kontejnerů s pevnou velikostí (podporované jenom pomocí sad Azure Cosmos DB SDK) nebo 5 neomezených kontejnerů pomocí emulátoru Azure Cosmos. Změnou hodnoty **PartitionCount** můžete vytvořit až 250 kontejnerů pevné velikosti nebo 50 neomezených kontejnerů, případně jakoukoli kombinaci dvou, která nepřekračuje 250 kontejnerů pevné velikosti (kde jeden neomezený kontejner = 5 kontejnerů pevné velikosti). Nedoporučuje se však nastavit emulátor pro spuštění s více než 200 kontejnery s pevnou velikostí. Z důvodu režie, kterou přidává k diskovým operacím v/v, což vede k nepředvídatelným časovým limitům při použití rozhraní API koncových bodů.

Pokud se pokusíte vytvořit kontejner po překročení aktuálního počtu oddílů, emulátor vyvolá výjimku ServiceUnavailable s následující zprávou.

> Omlouváme se, ale v tuto chvíli máme vysokou poptávku a v tuto chvíli nemůže váš požadavek splnit. Průběžně pracujeme na zajištění více a větší kapacity online a pomůžeme vám to zkusit znovu.
> ActivityId: 12345678-1234-1234-1234-123456789ABC

Pokud chcete změnit počet kontejnerů dostupných v emulátoru Azure Cosmos, spusťte následující postup:

1. Kliknutím pravým tlačítkem na ikonu **emulátoru Azure Cosmos DB** na hlavním panelu a kliknutím na **resetovat data**odstraňte všechna místní data emulátoru Azure Cosmos.

1. Odstraní všechna data emulátoru v této složce `%LOCALAPPDATA%\CosmosDBEmulator` .

1. Ukončete všechny otevřené instance tak, že kliknete pravým tlačítkem myši na ikonu **emulátoru služby Azure Cosmos DB** na hlavním panelu systému a potom kliknete na **Exit** (Konec). Ukončení všech instancí může chvíli trvat.

1. Nainstalujte nejnovější verzi [emulátoru Azure Cosmos](https://aka.ms/cosmosdb-emulator).

1. Spusťte emulátor s příznakem PartitionCount nastaveným na hodnotu < = 250. Například: `C:\Program Files\Azure Cosmos DB Emulator> Microsoft.Azure.Cosmos.Emulator.exe /PartitionCount=100`.
 
## <a name="next-steps"></a>Další kroky

* [Export certifikátů emulátoru Azure Cosmos pro použití s aplikacemi Java, Python a Node.js](local-emulator-export-ssl-certificates.md)
* [Ladění problémů s emulátorem](troubleshoot-local-emulator.md)

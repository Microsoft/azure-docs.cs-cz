---
title: Řešení potíží při použití emulátoru Azure Cosmos DB
description: Naučte se, jak Troubleshot služby při použití emulátoru Azure Cosmos DB nedostupné, certifikáty, šifrování a správy verzí.
ms.service: cosmos-db
ms.topic: troubleshooting
author: markjbrown
ms.author: mjbrown
ms.date: 09/17/2020
ms.custom: contperfq1
ms.openlocfilehash: 83559cc2ab1ca9597cca405333061e53b6f56aa9
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030743"
---
# <a name="troubleshoot-issues-when-using-the-azure-cosmos-db-emulator"></a>Řešení potíží při použití emulátoru Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Emulátor služby Azure Cosmos DB zajistí místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Tipy v tomto článku vám pomůžou při řešení problémů, se kterými se setkáte při instalaci nebo používání emulátoru Azure Cosmos DB. 

Pokud jste nainstalovali novou verzi emulátoru a dochází k chybám, proveďte obnovení dat. Data můžete obnovit tak, že kliknete pravým tlačítkem na ikonu emulátoru Azure Cosmos DB na hlavním panelu systému a potom kliknete na resetovat data.... Pokud to neopraví chyby, můžete emulátor a všechny starší verze emulátoru odinstalovat, odebrat *C:\Program files\Azure Cosmos DB adresář emulátoru* a přeinstalovat emulátor. Pokyny najdete v části [Odinstalace místního emulátoru](local-emulator.md#uninstall). Případně, pokud resetování dat nefunguje, přejděte do `%LOCALAPPDATA%\CosmosDBEmulator` umístění a odstraňte složku.

## <a name="troubleshoot-corrupted-windows-performance-counters"></a>Řešení poškozených čítačů výkonu systému Windows

* Pokud dojde k selhání emulátoru Azure Cosmos DB, shromážděte soubory s výpisem paměti ze `%LOCALAPPDATA%\CrashDumps` složky, Zkomprimujte je a z [Azure Portal](https://portal.azure.com)otevřete lístek podpory.

* Pokud dojde k chybě `Microsoft.Azure.Cosmos.ComputeServiceStartupEntryPoint.exe` , může to být příznak, ve kterém jsou čítače výkonu v poškozeném stavu. Obvykle se při spuštění následujícího příkazu z příkazového řádku správce vyřeší problém:

  ```cmd
  lodctr /R
   ```

## <a name="troubleshoot-connectivity-issues"></a>Řešení potíží s připojením

* Pokud narazíte na problém s připojením, [Shromážděte trasovací soubory](#trace-files), Zkomprimujte je a otevřete lístek podpory v [Azure Portal](https://portal.azure.com).

* Pokud se zobrazí zpráva **Služba není dostupná**, pravděpodobně se emulátoru nedaří inicializovat sadu síťových protokolů. Zkontrolujte, zda máte nainstalovaného klienta Pulse Secure nebo klienta Juniper Networks, protože potíže mohou způsobovat jejich ovladače síťových filtrů. Odinstalace ovladačů síťových filtrů třetích stran obvykle potíže vyřeší. Případně můžete spustit emulátor pomocí/DisableRIO, který přepne síťovou komunikaci emulátoru na normální rozhraní Winsock. 

* Pokud dojde k **chybě "zakázáno", "zpráva": "žádost se zakazuje zakázaným šifrováním v tranzitním protokolu nebo šifrě. Zaškrtněte nastavení minimální povolený protokol SSL/TLS... "** problémy s připojením, to může být způsobeno globálními změnami v operačním systému (například Insider Preview build 20170) nebo nastavením prohlížeče, které povoluje TLS 1,3 jako výchozí. K podobné chybě může dojít při použití sady SDK ke spuštění žádosti o emulátoru Cosmos, například **Microsoft.Azure.Documents.DocumentClientException: požadavek se zakazuje zakázaným šifrováním v tranzitním protokolu nebo šifrě. Ověřte nastavení minimálního povoleného protokolu SSL/TLS účtu**. V současné době se jedná o očekávané chování, protože emulátor služby Cosmos přijímá pouze protokol TLS 1.2 a funguje pouze s ním. Doporučeným řešením je změnit nastavení a výchozí možnost TLS 1,2; například ve Správci služby IIS přejděte na "weby" – > "výchozí weby" a vyhledejte "vazby lokality" pro port 8081 a upravte je tak, aby se protokol TLS 1,3 zakázal. Podobnou operaci je možné provést pro webový prohlížeč přes možnosti Nastavení.

* Pokud emulátor běží, když počítač přechází do režimu spánku nebo instaluje nějaké aktualizace operačního systému, může se zobrazit zpráva, že **služba momentálně není dostupná**. Obnovte data emulátoru tak, že kliknete pravým tlačítkem na ikonu, která se zobrazuje v oznamovacím panelu Windows, a vyberete **resetovat data**.

## <a name="collect-trace-files"></a><a id="trace-files"></a>Shromažďování trasovacích souborů

Pokud chcete shromažďovat trasovací soubory pro ladění, spusťte z příkazového řádku pro správu následující příkazy:

1. Přejděte do cesty, kde je nainstalován emulátor:

   ```bash
   cd /d "%ProgramFiles%\Azure Cosmos DB Emulator"
   ```

1. Vypněte emulátor a sledujte hlavní panel systému, abyste se ujistili, že se program vypnul. Dokončení může trvat několik minut. Můžete také vybrat možnost **ukončit** v uživatelském rozhraní emulátoru Azure Cosmos DB.

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /shutdown
   ```

1. Spusťte protokolování pomocí následujícího příkazu:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /startwprtraces
   ```

1. Spustit emulátor

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe
   ```

1. Reprodukujte problém. Pokud Průzkumník dat nefunguje, stačí počkat, než se v prohlížeči otevře několik sekund, aby se chyba zachytila.

1. Zastavte protokolování pomocí následujícího příkazu:

   ```bash
   Microsoft.Azure.Cosmos.Emulator.exe /stopwprtraces
   ```
   
1. Přejděte na `%ProgramFiles%\Azure Cosmos DB Emulator` cestu a vyhledejte soubor *docdbemulator_000001. ETL* .

1. Otevřete lístek podpory v [Azure Portal](https://portal.azure.com) a přidejte soubor. ETL spolu s postupem reprodukci.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak ladit problémy s místním emulátorem. Teď můžete přejít k dalším článkům:

* [Export certifikátů emulátoru Azure Cosmos DB pro použití s aplikacemi Java, Python a Node.js](local-emulator-export-ssl-certificates.md)
* [Použití parametrů příkazového řádku a příkazů prostředí PowerShell k řízení emulátoru](emulator-command-line-parameters.md)

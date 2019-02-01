---
title: Diagnostika potíží s zařízení StorSimple 8000 | Dokumentace Microsoftu
description: Popisuje režimy zařízení StorSimple a vysvětluje, jak pomocí prostředí Windows PowerShell pro StorSimple Změna režimu zařízení.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5cce4337e3ef95c6407d46d9b8b6401fe4f6600b
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497736"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Použít diagnostický nástroj, který StorSimple k řešení problémů se zařízením řady 8000

## <a name="overview"></a>Přehled

StorSimple diagnostický nástroj, který diagnostikuje problémy související se stav součásti systému, výkonu, sítě a hardwaru pro zařízení StorSimple. Diagnostický nástroj, který lze použít v různých scénářích. Mezi tyto scénáře patří úlohy plánování, nasazení zařízení StorSimple, posuzování síťového prostředí a určení výkonu provozní zařízení. Tento článek poskytuje přehled nástroje pro diagnostiku a popisuje, jak nástroj je možné se zařízením StorSimple.

Diagnostický nástroj, který je určený primárně pro StorSimple 8000 series na místních zařízeních (8100 a 8600).

## <a name="run-diagnostics-tool"></a>Spusťte nástroj pro diagnostiku

Tento nástroj můžete spustit prostřednictvím rozhraní Windows PowerShell vašeho zařízení StorSimple. Existují dva způsoby, jak získat přístup k místní rozhraní zařízení:

* [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Vzdálený přístup k nástroji přes Windows PowerShell pro StorSimple](storsimple-8000-remote-connect.md).

V tomto článku předpokládáme, že jste připojeni ke konzole sériového portu zařízení pomocí klienta PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Spustit diagnostické nástroje

Po připojení zařízení rozhraní prostředí Windows PowerShell proveďte následující kroky pro spuštění rutiny.
1. Přihlaste se k konzole sériového portu zařízení podle postupu v [použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Zadejte následující příkaz:

    `Invoke-HcsDiagnostics`

    Pokud není zadán parametr oboru, spustí rutina diagnostické testy. Tyto testy zahrnují systém, stav hardwarové součásti, síti a výkonu. 
    
    Pokud chcete spustit pouze konkrétní test, zadejte parametr oboru. Například pokud chcete spustit pouze testovací síť, zadejte

    `Invoke-HcsDiagnostics -Scope Network`

3. Vyberte a zkopírujte výstup z okna PuTTY do textového souboru k další analýze.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scénáře použití nástroje Diagnostika

Použijte diagnostický nástroj, který k řešení potíží se stavem sítě, výkon, systém a hardware systému. Tady je několik možných scénářů:

* **Zařízení do offline režimu** – vaše zařízení StorSimple řady 8000 je offline. Nicméně z rozhraní Windows PowerShell zdá se, že oba kontrolery jsou spuštěné.
    * Tento nástroj můžete pak určit stav sítě.
         
         > [!NOTE]
         > Nepoužívejte tento nástroj pro vyhodnocení výkonu a síťových nastavení na zařízení než registrace (nebo konfiguraci prostřednictvím Průvodce nastavením). Platnou IP adresu se přiřadí k zařízení během Průvodce instalací a registrací. Tuto rutinu můžete spustit na zařízení není zaregistrované, stav hardwaru a systému. Použijte parametr oboru, například:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problémy s trvalou zařízení** -dochází k problémům s zařízení, které vypadá to, že k uchování. Například registrace se nedaří. Vám může také být problémy zařízení po zařízení úspěšně zaregistrovaná a provozní dobu.
    * V takovém případě pomocí tohoto nástroje pro předběžné řešení potíží, před odesláním žádosti o službu Microsoft Support. Doporučujeme vám spustit tento nástroj a zachytit výstup tohoto nástroje. Tento výstup jim pak můžou k podpoře urychlit řešení potíží s.
    * Pokud selhání hardwaru komponenta nebo clusteru, by měl protokolu v žádosti o podporu.

* **Výkon zařízení nízká** – StorSimple vaše zařízení je pomalé.
    * V takovém případě spusťte tuto rutinu s parametrem oboru nastaveným na výkon. Analýza výstupu. Získání cloudu latence pro čtení i zápis. Použít oznámených latencí jako maximální dosažitelný cíl, zvážit režijní náklady pro vnitřní zpracování dat a pak nasadit úlohy v systému. Další informace najdete v části [řešení potíží s výkonem zařízení pomocí síťový test](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Testování a ukázka výstupy diagnostiky

### <a name="hardware-test"></a>Test hardwaru

Tento test Určuje stav hardwarových komponent, USM firmwaru a firmwaru disku, které jsou spuštěny v systému.

* Hardwarové součásti hlášené jsou ty součásti, které neprošel testem nebo nejsou k dispozici v systému.
* USM firmwaru a disku verze firmwaru označené pro řadič 0, 1 Kontroleru a sdílených komponent ve vašem systému. Úplný seznam hardwarových komponent přejděte na:

    * [Součásti ve primárního skříň](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Součásti ve skříni EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Pokud test hardwaru hlásí selhání součásti, [protokolu v žádosti o službu pomocí Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Ukázkový výstup hardwaru testovacích běhů v zařízení 8100

Tady je ukázkový výstup ze zařízení StorSimple 8100. V modelu zařízení 8100 není k dispozici EBOD skříň. Komponenty řadiče EBOD proto nezobrazují.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Testovací systém

Tento test vrátí informace o systému, jsou k dispozici aktualizace, informace o clusteru a informace o službě pro vaše zařízení.

* Informace o systému zahrnuje modelu, sériové číslo zařízení, časové pásmo, stav kontroléru a podrobné software verze, která běží v systému. Pokud chcete pochopit různé parametry systému hlášené jako výstup, přejděte na [interpretace systémové informace](#appendix-interpreting-system-information).

* Dostupnost aktualizace hlásí, zda jsou režimy běžných a údržby k dispozici a jejich názvy přidruženého balíčku. Pokud `RegularUpdates` a `MaintenanceModeUpdates` jsou `false`, to znamená, že nejsou k dispozici aktualizace. Vaše zařízení je aktuální.
* Informace o clusteru obsahuje informace o různých logické součásti všech skupin HCS clusteru a jejich příslušné stavy. Pokud se zobrazí skupiny offline clusteru v této části sestavy, [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Informace o službě obsahuje názvy a stavy všech HCS a CIS, používaných služeb běžícího ve vašem zařízení. Tyto informace jsou užitečné pro Microsoft Support při řešení tohoto problému zařízení.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Ukázkový výstup testů systému v zařízení 8100

Tady je ukázkový výstup systému testovacího běhu v zařízení 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Síťový test

Tento test ověřuje stav síťová rozhraní, porty, DNS a NTP připojení k serveru, SSL certifikátů, přihlašovacích údajů účtu úložiště, připojení k serverům Update, a připojení k proxy webu v zařízení StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Ukázkový výstup sítě testování, když je povolena pouze DATA0

Tady je ukázkový výstup zařízení 8100. Zobrazí se ve výstupu, který:
* Pouze DATA 0 a síťová rozhraní DATA 1 jsou povolené a nakonfigurované.
* Na portálu nejsou povolené rozhraní DATA 2 – 5.
* Konfigurace serveru DNS je platný a zařízení se můžete připojit prostřednictvím serveru DNS.
* Připojení k serveru NTP. je také v pořádku.
* Jsou otevřené porty 80 a 443. Nicméně je zablokovaný port 9354. Na základě [síťové požadavky na systém](storsimple-system-requirements.md), budete muset otevřít tento port pro komunikaci služby Service bus.
* Certifikáty SSL je neplatný.
* Zařízení se můžete připojit k účtu úložiště: _myss8000storageacct_.
* Připojení k serverům Update je platná.
* Webový proxy server není nakonfigurovaný na tomto zařízení.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Ukázkový výstup testu sítě, pokud jsou povolené DATA0 a DATA1

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Test výkonnosti

Tento test sestavy výkonu cloudu prostřednictvím latence čtení a zápis cloudu pro vaše zařízení. Tento nástroj umožňuje stanovení základní úrovně výkonu cloudu, které můžete dosáhnout pomocí StorSimple. Kterou nástroj hlásí maximální výkon (nejlepší scénář latence čtení a zápis), který můžete získat pro připojení.

Jak kterou nástroj hlásí maximální výkon dosažitelný, můžeme použít oznámených latencí pro čtení i zápis jako cíle při nasazování úloh.

Test simuluje velikosti objektu blob přidružené typy jiný svazek v zařízení. Standardní vrstvené a zálohování místně připojené svazky velikost objektu blob 64 KB. Vrstvené svazky se zaškrtnutým políčkem archivu použít velikost dat blob 512 KB. Pokud vaše zařízení má místně připnuté a vrstvené svazky nakonfigurováni pouze odpovídající do objektu blob 64 KB, velikost dat je spuštěn test.

Pokud chcete použít tento nástroj, postupujte následovně:

1.  Nejprve vytvořte kombinaci vrstvené svazky a vrstvené svazky se zaškrtnutým políčkem archivovaný. Tím zajistíte, že se nástroj spouští testy pro 64 KB a 512 KB objekty BLOB.

2. Spusťte rutinu po vytvořen a nakonfigurován svazky. Zadejte:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Latence čtení a zápis, kterou nástroj hlásí si poznamenejte. Tento test může trvat několik minut, který bude spuštěn před hlásí výsledky.

4. Pokud latence připojení všechno pod očekávaný rozsah, pak latence, kterou nástroj hlásí slouží jako maximální dosažitelný cíl při nasazování úloh. Zvyšuje zatížení pro vnitřní zpracování dat zvážit.

    Pokud je vysoká latence čtení a zápis hlášených diagnostický nástroj, který:

    1. Konfigurace služby blob Storage Analytics a analyzovat výstup pochopit latence pro účet úložiště Azure. Podrobné pokyny najdete v části [povolit a nakonfigurovat analýzu úložiště](../storage/common/storage-enable-and-view-metrics.md). Pokud tyto latenci, jsou také vysokou a srovnatelný s čísly, který jste dostali od StorSimple diagnostický nástroj, který, budete muset odesláním žádosti o službu Azure storage.

    2. Pokud jsou s nízkou latencí účet úložiště, obraťte se na správce sítě a zjistit všechny problémy s latencí ve vaší síti.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Ukázkový výstup spuštění v zařízení 8100 test výkonnosti

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Dodatek: interpretace systémové informace

Tady je Tabulka popisující, jaký různé parametry prostředí Windows PowerShell na mapě informace o systému. 

| Parametr Powershellu    | Popis  |
|-------------------------|------------------|
| ID instance             | Každý kontroler má jedinečný identifikátor nebo identifikátor GUID s ním spojená.|
| Název                    | Popisný název zařízení nakonfigurované na webu Azure portal během nasazování zařízení. Popisný název výchozí je sériové číslo zařízení. |
| Model                   | Model zařízení řady StorSimple 8000. Model může být 8100 nebo 8600.|
| sériové číslo            | Sériové číslo zařízení přiřazené na objekt pro vytváření a 15 znaků. Například udává 8600 SHX0991003G44HT:<br> 8600 – je model zařízení.<br>TVX – je lokalita výroby.<br> 0991003 - je konkrétní produkt. <br> G44HT-posledních 5 číslic jsou zvětšeny pro vytvoření jedinečnými sériovými čísly. Toto video asi sekvenční sady.|
| Časové pásmo                | Časové pásmo zařízení nakonfigurované na webu Azure Portal během nasazování zařízení.|
| CurrentController       | Kontroler, který jste připojeni přes rozhraní Windows PowerShell vašeho zařízení StorSimple.|
| ActiveController        | Kontroler, který je v zařízení aktivní a je řízení všech síťových a diskových operací. To může být řadič 0 a řadič 1.  |
| Controller0Status       | Stav řadiče 0 na vašem zařízení. Stav kontroleru může být normální, v režimu obnovení nebo nedostupný.|
| Controller1Status       | Stav řadiče 1 na vašem zařízení.  Stav kontroleru může být normální, v režimu obnovení nebo nedostupný.|
| SystemMode              | Celkový stav zařízení StorSimple. Stav zařízení může být normální, údržby nebo vyřazení (odpovídá možnosti deaktivován na webu Azure Portal).|
| FriendlySoftwareVersion | Popisný řetězec, který odpovídá verzi softwaru zařízení. Pro systém spuštěna verze Update 4 verze popisný softwaru by StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | Verze softwaru HCS, která běží na vašem zařízení. Například verze softwaru HCS odpovídající StorSimple 8000 Series Update 4.0 je 6.3.9600.17820. |
| ApiVersion              | Verze softwaru rozhraní API Windows Powershellu HCS zařízení.|
| VhdVersion              | Verze softwaru tovární image, která byla součástí zařízení. Pokud můžete obnovit v zařízení výchozí tovární nastavení, poběží verze tohoto softwaru.|
| OSVersion               | Verze softwaru operačního systému Windows Server běžící na zařízení. Zařízení StorSimple je založená Windows Server 2012 R2, který odpovídá 6.3.9600.|
| CisAgentVersion         | Verze agenta Cis běžící na zařízení StorSimple. Tento agent pomáhá komunikovat se službou StorSimple Manager běží v Azure.|
| MdsAgentVersion         | Verze odpovídá Mds agenta spuštěného v zařízení StorSimple. Tento agent přesune data monitorování a Diagnostika služby (MDS).|
| Lsisas2Version          | Verze odpovídá LSI ovladače zařízení StorSimple.|
| Kapacita                | Celková kapacita zařízení v bajtech.|
| RemoteManagementMode    | Určuje, jestli zařízení můžou vzdáleně spravovat přes rozhraní Windows PowerShell. |
| FipsMode                | Označuje, zda je na zařízení povolený režim USA federální informace o zpracování Standard (FIPS). Standard FIPS 140 definuje schválené pro použití podle federální vlády počítačové systémy pro ochranu citlivých dat kryptografické algoritmy. Pro zařízení s verzí Update 4 nebo novější je ve výchozím nastavení povolen režim FIPS. |

## <a name="next-steps"></a>Další postup

* Další informace [syntaxe rutiny Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Další informace o tom, jak [řešení problémů s nasazením](storsimple-troubleshoot-deployment.md) zařízení StorSimple.

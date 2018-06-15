---
title: Nástroj diagnostiky pro zařízení StorSimple 8000 Poradce při potížích | Microsoft Docs
description: Popisuje režimy zařízení StorSimple a vysvětluje, jak pomocí prostředí Windows PowerShell pro StorSimple ke změně režimu zařízení.
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
ms.openlocfilehash: 7199009553eb7aae31db3f913fe4de87e03d74ba
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27744108"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Nástroj diagnostiky StorSimple potíží pomocí 8000 řady zařízení

## <a name="overview"></a>Přehled

Nástroj diagnostiky StorSimple diagnostikuje problémy související s stav součásti systému, výkonu, sítě a hardwaru pro zařízení StorSimple. Nástroj diagnostiky lze použít v různých situacích. Mezi tyto scénáře patří úlohy plánování, nasazení zařízení StorSimple, vyhodnocovat síťové prostředí a určení výkon provozní zařízení. Tento článek obsahuje přehled nástroje pro diagnostiku a popisuje, jak nástroj lze použít s zařízení StorSimple.

Nástroj diagnostiky je primárně určený pro řadu zařízení StorSimple 8000 místní (8100 a 8600).

## <a name="run-diagnostics-tool"></a>Spustit diagnostické nástroje

Tento nástroj můžete spustit pomocí rozhraní Windows PowerShell zařízení StorSimple. Existují dva způsoby pro přístup k místní rozhraní zařízení:

* [Použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Vzdálený přístup nástroj prostřednictvím Windows Powershellu pro StorSimple](storsimple-8000-remote-connect.md).

V tomto článku předpokládáme, že jste připojení ke konzole sériového portu zařízení prostřednictvím PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Chcete-li spustit nástroj diagnostiky

Jakmile se připojíte k rozhraní Windows PowerShell zařízení, proveďte následující kroky ke spuštění rutiny.
1. Přihlaste se k konzole sériového portu zařízení podle pokynů v [použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Zadejte následující příkaz:

    `Invoke-HcsDiagnostics`

    Pokud není zadán parametr oboru, rutina provede všechny diagnostické testy. Tyto testy zahrnují systému, stav součásti hardwaru, síť a výkonu. 
    
    Pokud chcete spustit pouze konkrétní test, zadejte parametr oboru. Například spusťte pouze test sítě, zadejte

    `Invoke-HcsDiagnostics -Scope Network`

3. Vyberte a zkopírujte výstup z okna PuTTY do textového souboru k další analýze.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scénáře použití nástroje diagnostiky

Použijte nástroj Diagnostika k řešení potíží se stavem sítě, výkon, systém a hardware systému. Tady je několik možných scénářů:

* **Zařízení do offline režimu** -zařízení řady StorSimple 8000 si je offline. Však z rozhraní Windows PowerShell, zdá se, že jsou oběma řadičům spuštěná.
    * Tento nástroj můžete pak určit stav sítě.
         
         > [!NOTE]
         > Nepoužívejte tento nástroj pro vyhodnocení nastavení sítě a výkonu v zařízení než registrace (nebo konfigurace prostřednictvím Průvodce instalací). Platnou IP je přiřazený k zařízení během registrace a Průvodce instalací. Tuto rutinu můžete spustit na zařízení, který není zaregistrován pro stav hardwaru a systému. Používejte parametr oboru, například:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problémy s trvalé zařízení** -dochází k zařízení problémy, které se zdá, že zachovat. Například registrace se nedaří. Můžete také vykazuje problémy zařízení po zařízení je úspěšně registrovaná a funkční nějakou dobu.
    * V takovém případě použijte tento nástroj pro předběžné odstraňování před přihlášením žádost o služby s Microsoft Support. Doporučujeme, abyste tento nástroj spustit a zachycení výstup tohoto nástroje. Potom můžete zadat Tento výstup pro podporu, aby urychlit řešení potíží.
    * Pokud jsou všechny součásti nebo clusteru selhání hardwaru, je zapotřebí přihlásit v žádosti o podporu.

* **Nedostatek výkonu zařízení** -zařízení StorSimple si je pomalé.
    * V takovém případě spusťte tuto rutinu s parametrem oboru nastavena na výkon. Analýza výstupu. Získat cloudu latenci pro čtení a zápis. Používat oznámených latencí jako maximální dosažitelný cíl, zohlednit v některé zatížení pro vnitřní zpracování dat a potom nasaďte úlohy v systému. Další informace, přejděte na [použít testovací sítě k řešení potíží s výkonem zařízení](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Výstupy testů a ukázkové diagnostiky

### <a name="hardware-test"></a>Test na hardwaru

Tento test Určuje stav hardwarové součásti, Seznam USM firmware a firmware disku spuštěny v systému.

* Hardwarové součásti hlášené jsou tyto součásti, které se nezdařily testu nebo nejsou k dispozici v systému.
* Seznam USM verzí firmwaru firmwaru a disku jsou hlášené pro řadič 0, 1 řadiče a sdílené součásti v systému. Úplný seznam hardwarové součásti přejděte na:

    * [Součásti v primární skříň](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Součásti v EBOD skříň](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Pokud test na hardwaru ohlásí selhání součásti, [protokolu v žádosti o služby se Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Ukázkový výstup spustí na zařízení s 8100 test na hardwaru

Tady je ukázkový výstup ze zařízení StorSimple 8100. Skříň EBOD ve model zařízení 8100, není nainstalována. Proto nejsou hlášené komponenty EBOD řadiče.

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

Tento test hlásí informace o systému, dostupné aktualizace, informace o clusteru a informace služby pro vaše zařízení.

* Informace o systému zahrnuje modelu, sériové číslo zařízení, časové pásmo, stav řadiče a verze softwaru podrobné spuštěných v systému. Chcete-li pochopit různé parametry systému hlášené jako výstup, přejděte na [interpretace informace o systému](#appendix-interpreting-system-information).

* Dostupnost aktualizace oznámí, zda režimů regular a údržby nejsou k dispozici a jejich názvy přidruženého balíčku. Pokud `RegularUpdates` a `MaintenanceModeUpdates` jsou `false`, to znamená, že aktualizace nejsou k dispozici. Zařízení je aktuální.
* Informace o clusteru obsahuje informace o různých logických součástí všech skupin HCS clusteru a jejich příslušné stavy. Pokud uvidíte skupinu offline clusteru v této části sestavy, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Informace o službě obsahuje názvy a stavy všech HCS a položek konfigurace služby spuštěné na zařízení. Tyto informace jsou užitečné pro systém Microsoft Support při řešení potíží problém zařízení.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Ukázkový výstup testu systému spustí na zařízení s 8100

Tady je ukázkový výstup testu systému spustí na zařízení s 8100.

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
hcs_management_servic         Online HCS Cluster Group
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

### <a name="network-test"></a>Test sítě

Tento test ověřuje stav síťových rozhraní, porty, DNS a NTP připojení k serveru, SSL certifikátů, přihlašovacích údajů účtu úložiště, připojení k serverům aktualizace, a připojení k proxy serveru webu na zařízení StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Ukázkový výstup sítě testů, pokud je povoleno pouze DATA0

Tady je ukázkový výstup zařízení 8100. Zobrazí se ve výstupu který:
* Pouze DATA 0 a síťová rozhraní DATA 1 jsou povolené a nakonfigurované.
* DATA 2 až 5 nejsou povolené v portálu.
* Konfigurace serveru DNS je platný, a zařízení se můžete připojit pomocí serveru DNS.
* Připojení k serveru NTP je také v pořádku.
* Jsou otevřené porty 80 a 443. Port 9354 však je blokován. Na základě [požadavky na systém sítě](storsimple-system-requirements.md), je třeba otevřít tento port pro komunikaci se service bus.
* Certifikáty SSL je platný.
* Zařízení se může připojit k účtu úložiště: _myss8000storageacct_.
* Připojení k serverům aktualizace je platný.
* Webový proxy server není nakonfigurován na tomto zařízení.

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

Tento test sestavy výkon cloudu prostřednictvím latenci pro čtení a zápis cloudu pro vaše zařízení. Tento nástroj slouží k stanovení základní úrovně výkonu cloudu, můžete dosáhnout s StorSimple. Nástroj hlásí maximální výkon (nejlepší scénář pro latenci pro čtení a zápis), které můžete získat pro připojení.

Jak nástroj sestavy maximální možná výkonu, můžeme použít oznámených latencí pro čtení a zápis jako cíle při nasazování úlohy.

Test simuluje velikosti objektu blob přidružené typy jiný svazek v zařízení. Víceúrovňová Regular a zálohování místně vázaných svazků velikost objektu blob 64 KB. Vrstvené svazky s možností archivu zaškrtnuta možnost použít velikost dat blob 512 KB. Pokud vaše zařízení má vrstvené a místně vázaných svazků konfigurace, pouze test odpovídající do objektu blob 64 KB, velikost dat je spuštěna.

Chcete-li tento nástroj používat, proveďte následující kroky:

1.  Nejprve vytvořte směs vrstvené svazky a vrstvené svazky s archivovaný možnost zaškrtnutí. Tato akce zajistíte, že nástroj spustí testy pro 64 KB a 512 KB velikosti objektu blob.

2. Spusťte rutinu po po vytvoření a konfiguraci svazky. Zadejte:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Poznamenejte si latenci pro čtení a zápis hlášené nástroj. Tento test může trvat několik minut, který bude spuštěn před ohlásí výsledky.

4. Latence připojení jsou pod očekávaný rozsah, pak latence hlášené nástroj lze nastavit jako maximální dosažitelný cíl při nasazování úlohy. Dvoufaktorového v některé zatížení pro vnitřní zpracování dat.

    Pokud jsou vysoké latenci pro čtení a zápis hlášené nástroj diagnostiky:

    1. Konfigurace pro služby blob Storage Analytics a analyzovat výstup pochopit latence pro účet úložiště Azure. Podrobné pokyny najdete v tématu [povolení a konfigurace úložiště analýzy](../storage/common/storage-enable-and-view-metrics.md). Pokud tyto latence jsou také vysokou a porovnatelný z hlediska na čísla, který jste dostali od nástroj diagnostiky StorSimple, budete muset přihlásit žádost o služby s Azure storage.

    2. Pokud jsou nízkou latenci účet úložiště, obraťte se na správce sítě prozkoumat problémy s latencí ve vaší síti.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Ukázkový výstup testu výkonnosti spustí na zařízení s 8100

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

## <a name="appendix-interpreting-system-information"></a>Dodatek: interpretace informace o systému

Zde je popisující, co různé parametry prostředí Windows PowerShell v systémové informace o mapování pro tabulku. 

| Parametr prostředí PowerShell    | Popis  |
|-------------------------|------------------|
| ID instance             | Každý řadič má jedinečný identifikátor nebo identifikátor GUID s ním spojená.|
| Název                    | Popisný název zařízení, jak nakonfigurovat prostřednictvím portálu Azure při nasazení zařízení. Popisný název výchozí je sériové číslo zařízení. |
| Model                   | Model zařízení řady StorSimple 8000. Model může být 8100 nebo 8600.|
| sériové číslo            | Sériové číslo zařízení se přiřadí v objektu pro vytváření a 15 znaků. Například 8600 SHX0991003G44HT určuje:<br> 8600 – je model zařízení.<br>TVX – je místo výroby.<br> 0991003 - je konkrétní produkt. <br> G44HT-, které jsou k vytvoření jedinečné sériová čísla zvýší posledních 5 číslic. Toto nemusí být sekvenční sada.|
| Časové pásmo                | Časové pásmo zařízení podle konfigurace v portálu Azure během nasazování zařízení.|
| CurrentController       | Řadiče, které jste připojeni přes rozhraní prostředí Windows PowerShell zařízení StorSimple.|
| ActiveController        | Na řadič, který je v zařízení aktivní a je řízení všech síťových a diskových operací. To může být řadič 0 nebo 1 řadiče.  |
| Controller0Status       | Stav řadič 0 na vašem zařízení. Stav řadič může být normálního v režimu obnovení, nebo je nedostupný.|
| Controller1Status       | Stav řadič 1 na vašem zařízení.  Stav řadič může být normálního v režimu obnovení, nebo je nedostupný.|
| SystemMode              | Celkový stav zařízení StorSimple. Stav zařízení může být normální údržby, nebo vyřazení (odpovídá deaktivováno na portálu Azure).|
| FriendlySoftwareVersion | Popisný řetězec, který odpovídá verzi softwaru zařízení. U systému, který používá aktualizace 4 verze popisný softwaru bude StorSimple 8000 řady aktualizace 4.0.|
| HcsSoftwareVersion      | Verze softwaru HCS spuštěné na vašem zařízení. Například verze softwaru HCS odpovídající StorSimple 8000 řady aktualizace 4.0 je 6.3.9600.17820. |
| ApiVersion              | Verze softwaru rozhraní API prostředí PowerShell systému Windows HCS zařízení.|
| VhdVersion              | Verze softwaru vytváření bitové kopie, který byl součástí zařízení. Pokud zařízení resetujete do výchozího, pak spustí tato verze softwaru.|
| OSVersion               | Verze softwaru operačního systému Windows Server spuštěné na zařízení. Zařízení StorSimple je založena na Windows Server 2012 R2, která odpovídá 6.3.9600.|
| CisAgentVersion         | Verze pro agenta položek konfigurace pro spuštění v zařízení StorSimple. Tento agent pomáhá komunikovat se službou StorSimple Manager běží v Azure.|
| MdsAgentVersion         | Verze odpovídající Mds agenta spuštěného v zařízení StorSimple. Tento agent přesune data monitorování a Diagnostika služby MDS ().|
| Lsisas2Version          | Verze odpovídající LSI ovladače zařízení StorSimple.|
| Kapacita                | Celkový počet zařízení v bajtech.|
| RemoteManagementMode    | Určuje, zda zařízení můžete vzdáleně spravovat přes jeho rozhraní Windows PowerShell. |
| FipsMode                | Určuje, zda režim Spojených států informace o zpracování Standard FIPS (Federal) povolena na vašem zařízení. Standard FIPS 140 definuje kryptografické algoritmy pro použití schváleno nám Federal government počítačových systémů pro ochranu citlivá data. Pro zařízení se systémem Update 4 nebo novější je ve výchozím nastavení povolen režim FIPS. |

## <a name="next-steps"></a>Další postup

* Další informace [syntaxe rutiny Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Další informace o tom, jak [potíží s nasazením](storsimple-troubleshoot-deployment.md) zařízení StorSimple.

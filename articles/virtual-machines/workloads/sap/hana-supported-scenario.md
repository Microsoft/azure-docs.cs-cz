---
title: Podporované scénáře SAP HANA v Azure (velké instance) | Microsoft Docs
description: Podporované scénáře a jejich podrobnosti architektura pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/25/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b703f6d141005cf3cf29531a0586eebb61693a2
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754446"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Podporované scénáře pro velké instance HANA
Tento dokument popisuje podporované scénáře a jejich podrobnosti architektura pro HANA velké instancí (HLI).

>[!NOTE]
>Pokud zde není uvedeným vámi požadovaného scénáře, kontaktujte tým Microsoft Service Management k vyhodnocení požadavků.
Než budete pokračovat s jednotkou HLI zřizování, ověřte návrh s SAP nebo váš partner implementace služby.

## <a name="terms-and-definitions"></a>Termíny a definice
Projdeme si podmínky a definice použité v dokumentu.

- SID: Identifikátor systému pro systém HANA.
- HLI: Hana velké instance.
- Zotavení po Havárii: Po havárii obnovení Web.
- Zotavení po Havárii normální: nastavení systému s vyhrazený prostředek za účelem zotavení po Havárii použít jenom.
- Zotavení po Havárii Multipurpose: Systému v lokalitě zotavení po Havárii, které jsou nakonfigurované na používání mimo produkční prostředí spolu s provozním instance nakonfigurován pro použití k zotavení po Havárii událostí. 
- Jeden identifikátor SID: Systému se jedna instance nainstalována.
- Více SID: Systém s více instancemi nakonfigurované. Zkratka MCOS prostředí.


## <a name="overview"></a>Přehled
Instance velké HANA podporují řadu architektur plnit požadavky vaší organizace. Následující seznam popisuje scénáře a podrobnosti o jejich konfiguraci. 

Návrh odvozené architektury je čistě z hlediska infrastruktury a pro nasazení HANA musí najdete SAP nebo partnery vaší implementace. Pokud vaše scénáře nejsou uvedeny, obraťte se na týmu účtů Microsoft a zkontrolujte architekturu odvozena řešení pro vás.

**Tyto architektury jsou plně kompatibilní s návrhem TDI (přizpůsobit integraci dat) a podporována SAP.**

Tento dokument popisuje podrobnosti ze dvou komponent v jednotlivé podporované architektury:

- Ethernet
- Úložiště

### <a name="ethernet"></a>Ethernet

Každý server zřízený vybavená předem nakonfigurovaným rozhraním sady sítě ethernet. Tady jsou podrobnosti o ethernet nakonfigurované na každý HLI jednotky.

- **A**: používá se pro/přístup klienta.
- **B**: používá se pro komunikaci se uzel k uzlu. To je nakonfigurovaný na všech serverech (bez ohledu na topologii požadovaný) ale použít jenom pro scénáře škálování.
- **C**: Toto rozhraní se používá pro uzel k připojení úložiště.
- **D**: Toto rozhraní se používá pro uzel k připojení zařízení ISCSI pro STONITH instalační program. Toto rozhraní je nakonfigurovaná, pouze pokud je požadované nastavení HSR.  

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Uzel k uzlu |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | STONITH |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel k uzlu |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | STONITH |

Můžete použít rozhraní založené na topologii nakonfigurované na jednotce HLI. Rozhraní "B" je třeba nastavit pro komunikaci jednoho uzlu do druhého, což je užitečné, když máte topologii Škálováním na více systémů nakonfigurované. V případě konfigurace škálování jednoho uzlu se nepoužije toto rozhraní. Zkontrolujte požadované scénářů (dále v tomto dokumentu) Chcete-li získat další informace o využití rozhraní. 

V případě potřeby můžete definovat další Síťových karet sami. Však lze není změnit konfiguraci na stávající síťové karty.

>[!NOTE]
>Přesto můžete najít další rozhraní, které jsou fyzické rozhraní nebo záruční. Měli byste zvážit výše uvedené rozhraní pro váš případ použít, můžete ignorovat rest / nebo nechcete být zmírněna s.


### <a name="storage"></a>Úložiště
Úložiště je předem nakonfigurován podle topologie požadovaný. Velikosti svazků a přípojný bod lišit v závislosti na počtu serverů, SKU a topologie nakonfigurované. Chcete-li získat další informace zkontrolujte požadované scénářů (dále v tomto dokumentu). Pokud je potřeba další úložiště, si můžete zakoupit jeden přírůstek TB.

>[!NOTE]
>Přípojný bodUSR/sap/<SID> symbolický odkaz na přípojný bod/hana/sdílené.


## <a name="supported-scenarios"></a>Podporované scénáře

V architektuře diagramy pro grafiky používají následující zápisy:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

V následujícím seznamu jsou podporované scénáře:

1. Jeden uzel a jeden identifikátor SID
2. Jeden uzel MCOS
3. Jeden uzel a zotavení po Havárii (normální)
4. Jeden uzel a zotavení po Havárii (Multipurpose)
5. HSR s STONITH
6. HSR s zotavení po Havárii (normální / Multipurpose) 
7. Hostitele automatické převzetí služeb při selhání (1 + 1) 
8. Horizontální navýšení kapacity s pohotovostní režim
9. Horizontální navýšení kapacity bez pohotovostní režim
10. Horizontální navýšení kapacity s zotavení po Havárii



## <a name="1-single-node-with-one-sid"></a>1. Jeden uzel a jeden identifikátor SID

Tato topologie podporuje jeden uzel v rozšiřování škálování využívajících konfigurace s jeden identifikátor SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single uzlu s jeden – SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované ale není v použití |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale není v použití |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/Hana/Shared/SID | Instalace HANA | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory | 
|/Hana/log/SID/mnt00001 | Nainstalujte soubory protokolu | 
|/Hana/logbackups/SID | Znovu protokoly: |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Jeden uzel MCOS

Tato topologie podporuje jeden uzel v rozšiřování škálování využívajících konfigurací s více identifikátorů SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![jedním. uzel mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované ale není v použití |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale není v použití |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/Hana/Shared/SID1 | Instalace HANA SID1 | 
|/Hana/data/SID1/mnt00001 | Nainstalujte datové soubory pro SID1 | 
|/Hana/log/SID1/mnt00001 | Soubory protokolu instalace pro SID1 | 
|/Hana/logbackups/SID1 | Vrátit protokoly pro SID1 |
|/Hana/Shared/SID2 | Instalace HANA SID2 | 
|/Hana/data/SID2/mnt00001 | Nainstalujte datové soubory pro SID2 | 
|/Hana/log/SID2/mnt00001 | Soubory protokolu instalace pro SID2 | 
|/Hana/logbackups/SID2 | Vrátit protokoly pro SID2 |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- Rozdělení velikosti svazku je založena na velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) oddílu se dozvíte, jaké databáze velikosti v paměti jsou podporovány s multisid prostředí.

## <a name="3-single-node-with-dr-normal"></a>3. Jeden uzel a zotavení po Havárii (normální)
 
Tato topologie podporuje jeden uzel v rozšiřování škálování využívajících konfigurace s jeden nebo více identifikátory SID s replikace založené na úložiště k webu zotavení po Havárii pro primární identifikátor SID. V diagramu pouze jeden identifikátor SID je znázorněn v primární lokalitě, ale multisid (MCOS) je také podporována.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované ale není v použití |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale není v použití |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/Hana/Shared/SID | Instalace HANA pro identifikátor zabezpečení SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro identifikátor zabezpečení SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro identifikátor zabezpečení SID | 
|/Hana/logbackups/SID | Vrátit protokoly pro identifikátor zabezpečení SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikost svazku je založena na velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) oddílu se dozvíte, jaké databáze velikosti v paměti jsou podporovány s multisid prostředí.
- Na zotavení po Havárii: svazky a přípojné body jsou nakonfigurované (označené jako "Požadovaných pro instalaci HANA") pro provozní instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označena jako "Replikace úložiště") se replikují přes snímek z pracoviště. Jsou tyto svazky připojené jenom na dobu převzetí služeb při selhání. Odkazovat [převzetí služeb při selhání postupu zotavení po havárii](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Jeden uzel a zotavení po Havárii (Multipurpose)
 
Tato topologie podporuje jeden uzel v rozšiřování škálování využívajících konfigurace s jeden nebo více identifikátory SID s replikace založené na úložiště k webu zotavení po Havárii pro primární identifikátor SID. V diagramu pouze jeden identifikátor SID je znázorněn v primární lokalitě, ale multisid (MCOS) je také podporována. V lokalitě zotavení po Havárii HLI jednotka se používá pro QA instance spuštěného výrobní činnosti z primární lokality. Během převzetí služeb při selhání zotavení po Havárii (nebo testovací převzetí služeb při selhání) je ukončena QA instance v lokalitě zotavení po Havárii.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single uzlu s-zotavení po havárii multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované ale není v použití |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale není v použití |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/Hana/Shared/SID | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**V lokalitě zotavení po Havárii**|
|/Hana/Shared/SID | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/Shared/QA-SID | Instalace HANA pro identifikátor zabezpečení SID QA | 
|/Hana/data/QA-SID/mnt00001 | Nainstalujte datové soubory pro identifikátor zabezpečení SID QA | 
|/Hana/log/QA-SID/mnt00001 | Soubory protokolu instalace pro identifikátor zabezpečení SID QA |
|/Hana/logbackups/QA-SID | Vrátit protokoly pro identifikátor zabezpečení SID QA |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikost svazku je založena na velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) oddílu se dozvíte, jaké databáze velikosti v paměti jsou podporovány s multisid prostředí.
- Na zotavení po Havárii: svazky a přípojné body jsou nakonfigurované (označené jako "Požadovaných pro instalaci HANA") pro provozní instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označena jako "Replikace úložiště") se replikují přes snímek z pracoviště. Jsou tyto svazky připojené jenom na dobu převzetí služeb při selhání. Odkazovat [převzetí služeb při selhání postupu zotavení po havárii](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti. 
- Na zotavení po Havárii: data, logbackups, protokolu, sdílené svazky pro kontrolu kvality (označena jako "QA Instance instalace") jsou nakonfigurované pro instalaci instance QA.

## <a name="5-hsr-with-stonith"></a>5. HSR s STONITH
 
Tato topologie podporují dva uzly pro konfiguraci HANA systému replikace (HSR). 

**Od nyní tato architektura je podporován pouze pro SUSE operační systém.**


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR s STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované ale není v použití |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Použít pro STONITH |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale není v použití |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Použít pro STONITH |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/Hana/Shared/SID | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**Na sekundárním uzlu**|
|/Hana/Shared/SID | HANA instalace pro sekundární SID | 
|/Hana/data/SID/mnt00001 | Datové soubory instalace pro sekundární SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro sekundární SID | 
|/Hana/logbackups/SID | Vrátit protokoly pro sekundární identifikátor zabezpečení SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikost svazku je založena na velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) oddílu se dozvíte, jaké databáze velikosti v paměti jsou podporovány s multisid prostředí.
- STONITH: SBD je nakonfigurován pro instalační program STONITH. Použití STONITH je však volitelné.


## <a name="6-hsr-with-dr"></a>6. HSR s zotavení po Havárii
 
Tato topologie podporují dva uzly pro konfiguraci HANA systému replikace (HSR). Jak běžné a víceúčelových DR je podporována. 

V diagramu víceúčelových scénář je znázorněn tam, kde v lokalitě zotavení po Havárii, HLI jednotka se používá pro QA instance spuštěného výrobní činnosti z primární lokality. Během převzetí služeb při selhání zotavení po Havárii (nebo testovací převzetí služeb při selhání) je ukončena QA instance v lokalitě zotavení po Havárii. 



### <a name="architecture-diagram"></a>Diagram architektury  

![HSR s DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované ale není v použití |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Použít pro STONITH |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale není v použití |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Použít pro STONITH |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu v primární lokalitě**|
|/Hana/Shared/SID | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**Na sekundárním uzlu v primární lokalitě**|
|/Hana/Shared/SID | HANA instalace pro sekundární SID | 
|/Hana/data/SID/mnt00001 | Datové soubory instalace pro sekundární SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro sekundární SID | 
|/Hana/logbackups/SID | Vrátit protokoly pro sekundární identifikátor zabezpečení SID |
|**V lokalitě zotavení po Havárii**|
|/Hana/Shared/SID | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/Shared/QA-SID | Instalace HANA pro identifikátor zabezpečení SID QA | 
|/Hana/data/QA-SID/mnt00001 | Nainstalujte datové soubory pro identifikátor zabezpečení SID QA | 
|/Hana/log/QA-SID/mnt00001 | Soubory protokolu instalace pro identifikátor zabezpečení SID QA |
|/Hana/logbackups/QA-SID | Vrátit protokoly pro identifikátor zabezpečení SID QA |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikost svazku je založena na velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-architecture) oddílu se dozvíte, jaké databáze velikosti v paměti jsou podporovány s multisid prostředí.
- STONITH: SBD je nakonfigurován pro instalační program STONITH. Použití STONITH je však volitelné.
- Na zotavení po Havárii: **dvě sady svazky úložiště jsou požadovány** pro primární a sekundární uzel replikace.
- Na zotavení po Havárii: svazky a přípojné body jsou nakonfigurované (označené jako "Požadovaných pro instalaci HANA") pro provozní instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označena jako "Replikace úložiště") se replikují přes snímek z pracoviště. Jsou tyto svazky připojené jenom na dobu převzetí služeb při selhání. Odkazovat [převzetí služeb při selhání postupu zotavení po havárii](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti. 
- Na zotavení po Havárii: data, logbackups, protokolu, sdílené svazky pro kontrolu kvality (označena jako "QA Instance instalace") jsou nakonfigurované pro instalaci instance QA.


## <a name="7-host-auto-failover-11"></a>7. Hostitele automatické převzetí služeb při selhání (1 + 1)
 
Tato topologie podporuje dva uzly v konfiguraci hostitele automatické převzetí služeb při selhání. Je jeden uzel s role hlavního/pracovního procesu a dalšími jako úsporného režimu. **SAP podporuje tento scénář jenom pro S/4 HANA.** Odkazovat OSS poznámka "[S 2408419 - SAP nebo 4HANA - podpora více uzly](https://launchpad.support.sap.com/#/notes/2408419)" Další podrobnosti.



### <a name="architecture-diagram"></a>Diagram architektury  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Uzel k uzlu |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel k uzlu |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na hlavní a pohotovostní uzly**|
|/ hana/sdílené | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |



### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- Úsporný režim: svazky a přípojné body jsou nakonfigurované (označené jako "Požadovaných pro instalaci HANA") pro instalaci HANA Instance na pohotovostní jednotky.
 

## <a name="8-scale-out-with-standby"></a>8. Horizontální navýšení kapacity s pohotovostní režim
 
Tato topologie podporuje více uzlů v horizontální navýšení kapacity konfigurace. Jako pohotovostní je jeden uzel s role hlavního operačního serveru, jeden nebo více uzlů s role pracovního procesu a jeden nebo více uzlů. Přestože může existovat jenom jeden hlavní uzel libovolném časovém okamžiku.


### <a name="architecture-diagram"></a>Diagram architektury  

![škálování. nm standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Uzel k uzlu |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel k uzlu |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na uzlech master, pracovního procesu a pohotovostní režim**|
|/ hana/sdílené | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |


## <a name="9-scale-out-without-standby"></a>9. Horizontální navýšení kapacity bez pohotovostní režim
 
Tato topologie podporuje více uzlů v horizontální navýšení kapacity konfigurace. Neexistuje jeden uzel s role hlavního operačního serveru a jednu nebo režim uzly s role pracovního procesu. Přestože může existovat jenom jeden hlavní uzel libovolném časovém okamžiku.


### <a name="architecture-diagram"></a>Diagram architektury  

![nm.png škálování.](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Uzel k uzlu |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel k uzlu |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na hlavní a pracovní uzly**|
|/ hana/sdílené | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |


### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Horizontální navýšení kapacity s zotavení po Havárii
 
Tato topologie podporuje více uzlů v horizontální navýšení kapacity s zotavení po Havárii. Běžné a víceúčelových zotavení po Havárii je podporována. Na obrázku je znázorněn pouze jeden účelem zotavení po Havárii. Může požádat o Tato topologie s nebo bez pohotovostní uzlu.


### <a name="architecture-diagram"></a>Diagram architektury  

![škálování s dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Předem konfigurovaných následující rozhraní sítě:

| ROZHRANÍ LOGICKÉ SESKUPOVÁNÍ | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient HLI |
| B | TYP I | eth2.tenant | eno3.tenant | Uzel k uzlu |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované ale není v použití |
| A | TYP II | sítě VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | sítě VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel k uzlu |
| C | TYP II | sítě VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | sítě VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale není v použití |

### <a name="storage"></a>Úložiště
Předem konfigurovaných následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/ hana/sdílené | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**Na uzlu zotavení po Havárii**|
|/ hana/sdílené | HANA instalace pro produkční SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční SID | 


### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
-  Na zotavení po Havárii: svazky a přípojné body jsou nakonfigurované (označené jako "Požadovaných pro instalaci HANA") pro provozní instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označena jako "Replikace úložiště") se replikují přes snímek z pracoviště. Jsou tyto svazky připojené jenom na dobu převzetí služeb při selhání. Odkazovat [převzetí služeb při selhání postupu zotavení po havárii](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti. 


## <a name="next-steps"></a>Další postup
- Odkazovat [infrastruktury a připojení](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) pro HLI
- Odkazovat [vysoké dostupnosti a zotavení po havárii](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) pro HLI
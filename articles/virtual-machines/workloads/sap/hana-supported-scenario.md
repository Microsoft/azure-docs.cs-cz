---
title: Podporované scénáře SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Podporované scénáře a jejich podrobnosti architektury pro SAP HANA v Azure (velké instance)
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
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0e9d57c224150454677a03462368038ed8c63edf
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576489"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Podporované scénáře pro velké instance HANA
Tento dokument popisuje podporované scénáře a jejich podrobnosti architektury pro HANA velké instance (HLI).

>[!NOTE]
>Pokud vámi požadovaného scénáře není uvedené zde, obraťte se na tým Microsoft Service Management k vyhodnocení požadavků.
Než budete pokračovat s zřizování jednotek HLI ověření návrh s použitím SAP nebo váš partner implementace služby.

## <a name="terms-and-definitions"></a>Termíny a definice
Nyní se pokusíme pochopit pojmy a definice použitá v dokumentu.

- SID: Identifikátor systém HANA system.
- HLI: Velké instance Hana.
- Zotavení po Havárii: Lokalitě zotavení po havárii.
- Normální zotavení po Havárii: systém nastavení s vyhrazeným prostředkem pro účely zotavení po Havárii použít pouze.
- Zotavení po Havárii Multipurpose: Systému v lokalitě zotavení po Havárii konfigurován pro použití neprodukčním prostředí, spolu s produkční instance konfigurován pro použití na událost zotavení po Havárii. 
- Jeden identifikátor SID: Systému s jednou instancí, které jsou nainstalované.
- Více identifikátorů SID: Systému s více instancemi nakonfigurované. Zkratka MCOS prostředí.


## <a name="overview"></a>Přehled
Velké instance HANA podporují řadu architektur plnit vaše obchodní požadavky. Následující seznam popisuje scénáře a jejich podrobnosti o konfiguraci. 

Návrh architektury odvozené je výhradně z hlediska infrastruktury a musí konzultovat SAP nebo vaše implementace partnery pro nasazení HANA. Pokud vaše scénáře nejsou uvedené, obraťte se na zákaznický tým Microsoftu na kontrola architektury a získávat z nich řešení za vás.

**Tyto architektury jsou plně kompatibilní s návrhem TDI (přizpůsobené integrace dat) a SAP podporuje.**

Tento dokument popisuje podrobnosti ze dvou částí v jednotlivé podporované architektury:

- Ethernet
- Úložiště

### <a name="ethernet"></a>Ethernet

Každý server zřízené vybavená předem nakonfigurovaným rozhraním sady rozhraní sítě ethernet. Tady jsou podrobnosti o rozhraní sítě ethernet na každou jednotku HLI nakonfigurované.

- **A**: Toto rozhraní se používá pro/přístup klienta.
- **B**: Toto rozhraní se používá ke komunikaci uzlu k uzlu. Toto rozhraní je nakonfigurovaná na všech serverech (bez ohledu na topologii požadovaná), ale používá jenom pro 
- scénáře škálování na víc systémů.
- **C**: Toto rozhraní se používá pro uzel k připojení úložiště.
- **D**: Toto rozhraní se používá pro uzel k připojení zařízení ISCSI využitím techniky STONITH instalace. Toto rozhraní je konfigurovat pouze pokud je požadované nastavení HSR.  

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Uzel k uzlu |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | VYUŽITÍM TECHNIKY STONITH |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel k uzlu |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | VYUŽITÍM TECHNIKY STONITH |

Použijete rozhraní, které jsou založeny na topologii nakonfigurován na jednotce HLI. Rozhraní "B" je třeba nastavit pro komunikace na uzly, což je užitečné, když máte nakonfigurované strukturu horizontální navýšení kapacity. V případě konfigurace vertikálně navýšit kapacitu jednoho uzlu toto rozhraní se nepoužívá. Zkontrolujte požadované scénáře (dále v tomto dokumentu) Chcete-li získat další informace o využití rozhraní. 

V případě potřeby můžete definovat další karty NIC sami. Však můžete nebyl změněn konfiguraci na stávajících síťových karet.

>[!NOTE]
>Můžete stále zjistit další rozhraní, které jsou fyzické rozhraní nebo zárukách. Výše uvedené rozhraní byste měli zvážit pro váš případ používá, můžete ignorovat rest / nebo korigovat v.

Distribuce pro jednotky s dvě IP adresy přiřazené by měl vypadat:

- Ethernet "A" by měl mít IP adresa přiřazená, která je mimo rozsah adres fondu IP serveru, který jste odeslali do Microsoft. Tato IP adresa se použije pro zachování v/etc/hosts na operační systém.

- Ethernet "C" by měl mít IP adresa přiřazená, který se používá ke komunikaci s systému souborů NFS. Proto tyto adresy provést **není** třeba udržovat v etc/hosts, aby bylo možné povolit instancí na instance provoz v rámci tenanta.

V případech nasazení HANA System Replication nebo horizontální navýšení kapacity HANA není konfigurace okna s dvě IP adresy přiřazené vhodná. Pokud máte dvě IP adresy, které jsou přiřazeny pouze a chce nasadit taková konfigurace, obraťte se na SAP HANA v Azure Service Management k získání třetí IP adresy ve třetí přiřazené sítě VLAN. Pro velká Instance HANA jednotky mají tři IP adresy přiřazené na tři portů se síťovými Kartami platí následující pravidla využití:

- Ethernet "A" by měl mít IP adresa přiřazená, která je mimo rozsah adres fondu IP serveru, který jste odeslali do Microsoft. Proto tato IP adresa se nepoužije pro zachování v/etc/hosts na operační systém.

- Ethernet "B" by měla sloužit exkluzivně na webu etc/hosts pro komunikaci mezi různými instancemi. Tyto adresy by také IP adresy, které je třeba udržovat v HANA konfigurací škálování na víc systémů jako IP adresy, které používá HANA pro konfigurace mezi uzly.

- Ethernet "C" by měl mít IP adresa přiřazená, který se používá ke komunikaci s úložiště NFS. Tento typ adresy proto by neměl být udržovány v etc/hosts.

- Ethernet "D" by měla sloužit pouze pro přístup k zařízení využitím techniky STONITH pro pacemaker. Toto rozhraní se ale vyžaduje při konfiguraci HANA System Replication (HSR) a chcete dosáhnout automatického převzetí služeb při selhání na operační systém pomocí SBD na základě zařízení.


### <a name="storage"></a>Úložiště
Úložiště je předem nakonfigurován založeny na topologii požadavku. Velikosti svazku a přípojný bod se liší v závislosti na počet serverů, SKU a topologii nakonfigurována. Zkontrolujte požadované scénáře (dále v tomto dokumentu) Chcete-li získat další informace. Pokud je potřeba větší úložiště, můžete ji zakoupit v TB přírůstcích.

>[!NOTE]
>Přípojnýbod/USR/sap/<SID> symbolický odkaz k přípojnému bodu/hana/sdílené.


## <a name="supported-scenarios"></a>Podporované scénáře

V diagramy architektury následující zápisy se používají pro obrázky:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

Následující seznam uvádí podporované scénáře:

1. Jednoho uzlu se jeden identifikátor SID
2. Jeden uzel MCOS
3. Jeden uzel a zotavení po Havárii (normální)
4. Jeden uzel a zotavení po Havárii (Multipurpose)
5. HSR s využitím techniky STONITH
6. HSR pomocí zotavení po Havárii (normální / Multipurpose) 
7. Hostitel automatické převzetí služeb při selhání (1 + 1) 
8. Horizontální navýšení kapacity s pohotovostním režimem:
9. Horizontální navýšení kapacity bez pohotovostní režim
10. Horizontální navýšení kapacity pomocí zotavení po Havárii



## <a name="1-single-node-with-one-sid"></a>1. Jednoho uzlu se jeden identifikátor SID

Tato topologie podporuje jeden uzel v vertikálního navýšení konfiguraci s jeden identifikátor SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single – uzlu s jeden – SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Nakonfigurované ale nepoužívá |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale nepoužívá |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/Hana/Shared/SID | Instalace HANA | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory | 
|/Hana/log/SID/mnt00001 | Nainstalujte soubory protokolu | 
|/Hana/logbackups/SID | Znovu protokoly |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. Jeden uzel MCOS

Tato topologie podporuje jeden uzel v vertikálního navýšení konfigurací s více identifikátorů SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Nakonfigurované ale nepoužívá |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale nepoužívá |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/Hana/Shared/SID1 | Instalace HANA SID1 | 
|/Hana/data/SID1/mnt00001 | Datové soubory instalace SID1 | 
|/Hana/log/SID1/mnt00001 | Soubory protokolu instalace SID1 | 
|/Hana/logbackups/SID1 | Znovu protokoly pro SID1 |
|/Hana/Shared/SID2 | Instalace HANA SID2 | 
|/Hana/data/SID2/mnt00001 | Datové soubory instalace SID2 | 
|/Hana/log/SID2/mnt00001 | Soubory protokolu instalace SID2 | 
|/Hana/logbackups/SID2 | Znovu protokoly pro SID2 |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- Rozdělení velikosti svazku je založená velikosti databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) multisid prostředí podporuje části se dozvíte, jaké databáze o velikosti v paměti.

## <a name="3-single-node-with-dr-normal"></a>3. Jeden uzel a zotavení po Havárii (normální)
 
Tato topologie podporuje jeden uzel v vertikálního navýšení konfigurace s jeden nebo více identifikátory SID s replikací úložiště k zotavení po Havárii serveru pro primární identifikátor SID. V diagramu pouze jeden identifikátor SID je znázorněn v primární lokalitě, ale multisid (MCOS) je také podporována.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Nakonfigurované ale nepoužívá |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale nepoužívá |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/Hana/Shared/SID | Instalace HANA pro identifikátor zabezpečení SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro identifikátor zabezpečení SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro identifikátor zabezpečení SID | 
|/Hana/logbackups/SID | Znovu protokoly pro identifikátor zabezpečení SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- U MCOS: Distribuce velikost svazku závisí vypnout velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) multisid prostředí podporuje části se dozvíte, jaké databáze o velikosti v paměti.
- Na zotavení po Havárii: svazky a přípojné body se konfigurují (označené jako "Vyžadované pro instalaci HANA") pro produkční prostředí instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označené jako "Replikace úložiště") se replikují pomocí snímků z pracoviště. Jsou tyto svazky připojené během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti.
- Spouštěcí svazek pro **typ SKU můžu třídy** se replikuje do uzlu zotavení po Havárii.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Jeden uzel a zotavení po Havárii (Multipurpose)
 
Tato topologie podporuje jeden uzel v vertikálního navýšení konfigurace s jeden nebo více identifikátory SID s replikací úložiště k zotavení po Havárii serveru pro primární identifikátor SID. V diagramu pouze jeden identifikátor SID je znázorněn v primární lokalitě, ale multisid (MCOS) je také podporována. V lokalitě zotavení po Havárii HLI jednotky se používají QA instancí z primární lokality jsou spuštěné operace produkčního prostředí. Během převzetí služeb při selhání zotavení po Havárii (nebo testovací převzetí služeb při selhání) je odstavit instance dotazů a odpovědí v lokalitě zotavení po Havárii.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single – uzlu s-zotavení po havárii – multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Nakonfigurované ale nepoužívá |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale nepoužívá |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/Hana/Shared/SID | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**V lokalitě zotavení po Havárii**|
|/Hana/Shared/SID | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/Shared/QA-SID | Instalace HANA pro identifikátor zabezpečení SID dotazů a odpovědí | 
|/Hana/data/QA-SID/mnt00001 | Nainstalujte datové soubory pro identifikátor zabezpečení SID dotazů a odpovědí | 
|/Hana/log/QA-SID/mnt00001 | Soubory protokolu instalace pro identifikátor zabezpečení SID dotazů a odpovědí |
|/Hana/logbackups/QA-SID | Znovu protokoly pro identifikátor zabezpečení SID dotazů a odpovědí |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- U MCOS: Distribuce velikost svazku závisí vypnout velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) multisid prostředí podporuje části se dozvíte, jaké databáze o velikosti v paměti.
- Na zotavení po Havárii: svazky a přípojné body se konfigurují (označené jako "Vyžadované pro instalaci HANA") pro produkční prostředí instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označené jako "Replikace úložiště") se replikují pomocí snímků z pracoviště. Jsou tyto svazky připojené během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti. 
- Na zotavení po Havárii: data, logbackups, log, sdílené svazky pro kontrolu kvality (označené jako "QA Instance instalace") jsou nakonfigurované pro instalaci instance dotazů a odpovědí.
- Spouštěcí svazek pro **typ SKU můžu třídy** se replikuje do uzlu zotavení po Havárii.

## <a name="5-hsr-with-stonith"></a>5. HSR s využitím techniky STONITH
 
Tato topologie podporují dva uzly pro konfiguraci HANA System Replication (HSR). Tato konfigurace je podporovaná jenom pro jeden instance HANA na uzlu. Prostředky, MCOS scénáře nejsou podporovány.

**Tato architektura je v současné době podporuje jenom pro SUSE operační systém.**


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR s STONITH.png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Nakonfigurované ale nepoužívá |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Používá pro využitím techniky STONITH |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale nepoužívá |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Používá pro využitím techniky STONITH |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/Hana/Shared/SID | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**Na sekundárním uzlu**|
|/Hana/Shared/SID | HANA instalace pro sekundární SID | 
|/Hana/data/SID/mnt00001 | Datové soubory instalace pro sekundární SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro sekundární SID | 
|/Hana/logbackups/SID | Znovu protokoly pro sekundární SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- U MCOS: Distribuce velikost svazku závisí vypnout velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) multisid prostředí podporuje části se dozvíte, jaké databáze o velikosti v paměti.
- Využitím techniky STONITH: SBD je nakonfigurován pro instalační program využitím techniky STONITH. Nicméně použití využitím techniky STONITH je volitelný.


## <a name="6-hsr-with-dr"></a>6. HSR pomocí zotavení po Havárii
 
Tato topologie podporují dva uzly pro konfiguraci HANA System Replication (HSR). Je podporováno jak běžné a multipurpose zotavení po Havárii. Tyto konfigurace jsou podporovány pouze pro jeden instance HANA na uzlu. Znamená, že se tyto konfigurace se nepodporují MCOS scénáře.

V diagramu multipurpose scénář je znázorněn tam, kde v lokalitě zotavení po Havárii, HLI jednotky se používají QA instancí z primární lokality jsou spuštěné operace produkčního prostředí. Během převzetí služeb při selhání zotavení po Havárii (nebo testovací převzetí služeb při selhání) je odstavit instance dotazů a odpovědí v lokalitě zotavení po Havárii. 



### <a name="architecture-diagram"></a>Diagram architektury  

![HSR s DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Nakonfigurované ale nepoužívá |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Používá pro využitím techniky STONITH |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Nakonfigurované ale nepoužívá |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Používá pro využitím techniky STONITH |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu v primární lokalitě**|
|/Hana/Shared/SID | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**Na sekundárním uzlu v primární lokalitě**|
|/Hana/Shared/SID | HANA instalace pro sekundární SID | 
|/Hana/data/SID/mnt00001 | Datové soubory instalace pro sekundární SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro sekundární SID | 
|/Hana/logbackups/SID | Znovu protokoly pro sekundární SID |
|**V lokalitě zotavení po Havárii**|
|/Hana/Shared/SID | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/Shared/QA-SID | Instalace HANA pro identifikátor zabezpečení SID dotazů a odpovědí | 
|/Hana/data/QA-SID/mnt00001 | Nainstalujte datové soubory pro identifikátor zabezpečení SID dotazů a odpovědí | 
|/Hana/log/QA-SID/mnt00001 | Soubory protokolu instalace pro identifikátor zabezpečení SID dotazů a odpovědí |
|/Hana/logbackups/QA-SID | Znovu protokoly pro identifikátor zabezpečení SID dotazů a odpovědí |

### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- U MCOS: Distribuce velikost svazku závisí vypnout velikost databáze v paměti. Odkazovat [přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) multisid prostředí podporuje části se dozvíte, jaké databáze o velikosti v paměti.
- Využitím techniky STONITH: SBD je nakonfigurován pro instalační program využitím techniky STONITH. Nicméně použití využitím techniky STONITH je volitelný.
- Na zotavení po Havárii: **dvě sady úložiště svazky jsou požadovány** pro replikaci primárního a sekundárního uzlu.
- Na zotavení po Havárii: svazky a přípojné body se konfigurují (označené jako "Vyžadované pro instalaci HANA") pro produkční prostředí instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označené jako "Replikace úložiště") se replikují pomocí snímků z pracoviště. Jsou tyto svazky připojené během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti. 
- Na zotavení po Havárii: data, logbackups, log, sdílené svazky pro kontrolu kvality (označené jako "QA Instance instalace") jsou nakonfigurované pro instalaci instance dotazů a odpovědí.
- Spouštěcí svazek pro **typ SKU můžu třídy** se replikuje do uzlu zotavení po Havárii.


## <a name="7-host-auto-failover-11"></a>7. Hostitel automatické převzetí služeb při selhání (1 + 1)
 
Tato topologie podporuje dva uzly v konfiguraci automatického převzetí služeb při selhání hostitele. Existuje jeden uzel s hlavní server nebo pracovní role a další jako úsporného režimu. **Tento scénář SAP podporuje jenom pro s/4 HANA.** Přečtěte si poznámku OSS "[2408419 - SAP S/4HANA – podpora více uzly](https://launchpad.support.sap.com/#/notes/2408419)" pro více podrobností.



### <a name="architecture-diagram"></a>Diagram architektury  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Uzel má uzel komunikace |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel má uzel komunikace |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na hlavní a pohotovostní uzly**|
|/ hana/sdílené | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |



### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
- V pohotovostním režimu: svazky a přípojné body se konfigurují (označené jako "Vyžadované pro instalaci HANA") pro instalaci HANA Instance na pohotovostní jednotky.
 

## <a name="8-scale-out-with-standby"></a>8. Horizontální navýšení kapacity s pohotovostním režimem:
 
Tato topologie podporuje více uzlů v konfiguraci horizontální navýšení kapacity. Existuje jeden uzel s role hlavního operačního serveru, jeden nebo více uzlů s rolí pracovního procesu a jeden nebo více uzlů jako pohotovostní režim. Však může existovat pouze jeden hlavní uzel libovolném časovém okamžiku.


### <a name="architecture-diagram"></a>Diagram architektury  

![škálování aplikace. nm standby.png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Uzel má uzel komunikace |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel má uzel komunikace |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na uzlech master, pracovních procesů a pohotovostní režim**|
|/ hana/sdílené | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |


## <a name="9-scale-out-without-standby"></a>9. Horizontální navýšení kapacity bez pohotovostní režim
 
Tato topologie podporuje více uzlů v konfiguraci horizontální navýšení kapacity. Existuje jeden uzel s role hlavního operačního serveru a jeden nebo režim uzly s rolí pracovního procesu. Však může existovat pouze jeden hlavní uzel libovolném časovém okamžiku.


### <a name="architecture-diagram"></a>Diagram architektury  

![nm.png horizontálním navýšením kapacity](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Uzel má uzel komunikace |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel má uzel komunikace |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na hlavní server a pracovní uzly**|
|/ hana/sdílené | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |


### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Horizontální navýšení kapacity pomocí zotavení po Havárii
 
Tato topologie podporuje více uzlů v škálování se zotavení po Havárii. Normální a multipurpose zotavení po Havárii je podporována. Na obrázku je znázorněn pouze jedinému účelu zotavení po Havárii. Můžete požádat o Tato topologie s nebo bez něj pohotovostní uzel.


### <a name="architecture-diagram"></a>Diagram architektury  

![škálování s dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Následující síťová rozhraní jsou předem nakonfigurovány:

| LOGICKÁ ROZHRANÍ SÍŤOVÉ KARTY | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | ZADEJTE MI | eth0.tenant | eno1.tenant | Klient HLI |
| B | ZADEJTE MI | eth2.tenant | eno3.tenant | Uzel má uzel komunikace |
| C | ZADEJTE MI | eth1.tenant | eno2.tenant | Uzel úložiště |
| D | ZADEJTE MI | eth4.tenant | eno4.tenant | Nakonfigurované ale nepoužívá |
| A | TYP II | síť VLAN<tenantNo> | team0.tenant | Klient HLI |
| B | TYP II | síť VLAN < tenantNo + 2 > | team0.tenant + 2 | Uzel má uzel komunikace |
| C | TYP II | síť VLAN < tenantNo + 1 > | team0.tenant + 1 | Uzel úložiště |
| D | TYP II | síť VLAN < tenantNo + 3 > | team0.tenant + 3 | Nakonfigurované ale nepoužívá |

### <a name="storage"></a>Úložiště
Je předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/ hana/sdílené | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 
|/Hana/logbackups/SID | Protokoly pro produkční prostředí SID znovu: |
|**V uzlu zotavení po Havárii**|
|/ hana/sdílené | HANA instalaci pro produkční prostředí SID | 
|/Hana/data/SID/mnt00001 | Nainstalujte datové soubory pro produkční prostředí SID | 
|/Hana/log/SID/mnt00001 | Soubory protokolu instalace pro produkční prostředí SID | 


### <a name="key-considerations"></a>Klíčové aspekty
- /USR/SAP/SID je symbolický odkaz na /hana/shared/SID.
-  Na zotavení po Havárii: svazky a přípojné body se konfigurují (označené jako "Vyžadované pro instalaci HANA") pro produkční prostředí instalace HANA Instance na jednotku HLI zotavení po Havárii. 
- Na zotavení po Havárii: data, logbackups a sdílené svazky (označené jako "Replikace úložiště") se replikují pomocí snímků z pracoviště. Jsou tyto svazky připojené během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery#disaster-recovery-failover-procedure) další podrobnosti. 
- Spouštěcí svazek pro **typ SKU můžu třídy** se replikuje do uzlu zotavení po Havárii.


## <a name="next-steps"></a>Další postup
- Přečtěte si [infrastruktura a možnosti připojení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) pro HLI
- Přečtěte si [vysokou dostupnost a zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) pro HLI
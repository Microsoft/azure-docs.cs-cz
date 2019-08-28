---
title: Podporované scénáře SAP HANA v Azure (velké instance) | Microsoft Docs
description: Podporované scénáře a jejich podrobnosti architektury pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/06/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f17e447f26ae4f7573941fc0c578a918ff45a145
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101219"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Podporované scénáře pro velké instance HANA
Tento dokument popisuje podporované scénáře spolu s podrobnostmi jejich architektury pro velké instance HANA (HLI).

>[!NOTE]
>Pokud zde požadovaný scénář není uveden, obraťte se na tým Microsoftu pro správu služeb a vyhodnoťte vaše požadavky.
Než budete pokračovat s zřizováním jednotek HLI, ověřte návrh pomocí SAP nebo partnera pro implementaci služeb.

## <a name="terms-and-definitions"></a>Pojmy a definice
Pojďme pochopit výrazy a definice používané v dokumentu.

- ID Systémový identifikátor systému HANA
- HLI: Velké instance Hana
- DR Lokalita pro zotavení po havárii.
- Normální DR: Nastavení systému se vyhrazeným prostředkem pouze pro účely zotavení po havárii.
- Víceúčelový nástroj DR: Systém na počítači DR je nakonfigurovaný tak, aby používal neprodukční prostředí spolu s produkční instancí nakonfigurovanou pro použití v události zotavení po havárii. 
- Jeden identifikátor SID:  Systém s jednou nainstalovanou instancí.
- Vícenásobné SID: Je nakonfigurovaný systém s více instancemi. Označuje se také jako MCOS prostředí.


## <a name="overview"></a>Přehled
Velké instance HANA podporují nejrůznější architektury pro splnění vašich obchodních požadavků. Následující seznam obsahuje informace o scénářích a jejich podrobnostech o konfiguraci. 

Návrh odvozené architektury je čistě z hlediska infrastruktury a Vy musíte pro nasazení HANA požádat o informace o SAP nebo partnerovi implementace. Pokud vaše scénáře nejsou uvedené, obraťte se na tým účet Microsoft, abyste zkontrolovali architekturu a odvodili řešení za vás.

**Tyto architektury jsou plně kompatibilní s návrhem TDI (přizpůsobením integrace dat) a podporovaném SAP.**

Tento dokument popisuje podrobnosti o těchto dvou součástech v každé podporované architektuře:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Každý zřízený Server je předem nakonfigurovaný se sadami rozhraní Ethernet. Zde jsou uvedeny podrobnosti o rozhraních sítě Ethernet nakonfigurovaných na každé jednotce HLI.

- **A**: Toto rozhraní se používá pro/klientský přístup.
- **B**: Toto rozhraní se používá pro komunikaci mezi uzly. Toto rozhraní je nakonfigurované na všech serverech (bez ohledu na požadovanou topologii), ale používá se jenom pro 
- scénáře škálování na více instancí.
- **C**: Toto rozhraní se používá pro uzel k připojení úložiště.
- **D**: Toto rozhraní se používá pro připojení uzlu k zařízení ISCSI pro instalaci STONITH. Toto rozhraní se nakonfiguruje jenom v případě, že se požaduje instalace HSR.  

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Uzel do uzlu |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | STONITH |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Uzel do uzlu |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | STONITH |

Rozhraní použijete na základě topologie nakonfigurované na HLI jednotce. Například rozhraní "B" je nastavené pro komunikaci mezi uzly, což je užitečné v případě, že je nakonfigurované topologie škálování na více systémů. V případě konfigurace s jedním uzlem bez přípony se toto rozhraní nepoužívá. Přečtěte si požadované scénáře (dále v tomto dokumentu) a získejte další informace o využití rozhraní. 

V případě potřeby můžete definovat další karty síťových adaptérů sami. Konfiguraci existujících síťových adaptérů ale nemůžete změnit.

>[!NOTE]
>Stále můžete najít další rozhraní, která jsou fyzická rozhraní nebo vázání. Měli byste uvažovat o výše zmíněných rozhraních pro použitý případ, REST můžete ignorovat/nebo nemusíte vystavit.

Distribuce jednotek se dvěma přiřazenými IP adresami by měla vypadat takto:

- Síť Ethernet "A" by měla mít přiřazenou IP adresu, která se nachází mimo rozsah adres fondu IP adres, který jste odeslali společnosti Microsoft. Tato IP adresa se používá pro údržbu v/etc/hosts operačního systému.

- Síť Ethernet "C" by měla mít přiřazenou IP adresu, která se používá pro komunikaci se systémem souborů NFS. Proto tyto adresy **není** nutné udržovat v jiných hostitelích, aby bylo možné instanci provozovat v rámci tenanta.

Pro případy nasazení replikace systému HANA nebo škálování HANA na více systémů není konfigurace okna se dvěma přiřazenými IP adresami vhodná. Pokud mají dvě IP adresy přiřazené jenom a chtějí nasazovat takovou konfiguraci, kontaktujte SAP HANA v rámci správy služeb Azure a získejte třetí IP adresu v přiřazené třetí síti VLAN. Pro jednotky velkých instancí HANA, které mají tři IP adresy přiřazené na tři porty síťových adaptérů, platí následující pravidla použití:

- Síť Ethernet "A" by měla mít přiřazenou IP adresu, která se nachází mimo rozsah adres fondu IP adres, který jste odeslali společnosti Microsoft. Proto se tato IP adresa nedá použít k údržbě v/etc/hosts operačního systému.

- Síť Ethernet "B" by se měla používat výhradně k údržbě v hostitelích atd. a ke komunikaci mezi různými instancemi. Tyto adresy by taky představovaly IP adresy, které je potřeba udržovat v konfiguracích se škálováním na více instancí, jako IP adresy HANA používané pro konfiguraci mezi uzly.

- Síť Ethernet "C" by měla mít přiřazenou IP adresu, která se používá ke komunikaci s úložištěm NFS. Proto by tento typ adres neměl být udržován v jiných hostitelích.

- Síť Ethernet "D" by se měla používat výhradně pro přístup ke STONITH zařízení pro Pacemaker. Toto rozhraní se vyžaduje, když konfigurujete replikaci systému HANA (HSR) a chcete v operačním systému dosáhnout automatického převzetí služeb při selhání pomocí zařízení založeného na SBD.


### <a name="storage"></a>Storage
Úložiště je předem nakonfigurované na základě požadované topologie. Velikosti svazků a přípojný bod se liší v závislosti na počtu nakonfigurovaných serverů, SKU a topologie. Pokud chcete získat další informace, Projděte si požadované scénáře (dále v tomto dokumentu). Pokud potřebujete další úložiště, můžete ho koupit v jednom z dalších TB.

>[!NOTE]
>Přípojný bod SID/usr/SAP/\<> je symbolický odkaz na/Hana/Shared přípojný bod.


## <a name="supported-scenarios"></a>Podporované scénáře

V diagramech architektury se pro grafiky použijí tyto zápisy:

![Legends.PNG](media/hana-supported-scenario/Legends.PNG)

V následujícím seznamu jsou uvedeny podporované scénáře:

1. Jeden uzel s jedním identifikátorem SID
2. MCOS jednoho uzlu
3. Jeden uzel se systémem DR (normální)
4. Jeden uzel se systémem DR (víceúčelový)
5. HSR s STONITH
6. HSR s nástrojem DR (normální/víceúčelový) 
7. Automatické převzetí služeb při selhání hostitele (1 + 1) 
8. Horizontální navýšení kapacity pomocí úsporného režimu
9. Horizontální navýšení kapacity bez úsporného režimu
10. Horizontální navýšení kapacity pomocí nástroje DR



## <a name="1-single-node-with-one-sid"></a>1. Jeden uzel s jedním identifikátorem SID

Tato topologie podporuje jeden uzel v konfiguraci škálované nahoru s jedním identifikátorem SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Single-node-with-one-SID.png](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Instalace HANA | 
|/hana/data/SID/mnt00001 | Instalace datových souborů | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu | 
|/hana/logbackups/SID | Protokoly opětovného provedení |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.

## <a name="2-single-node-mcos"></a>2. MCOS jednoho uzlu

Tato topologie podporuje jeden uzel v konfiguraci škálované nahoru s více identifikátory SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![single-node-mcos.png](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|/hana/shared/SID1 | Instalace HANA pro SID1 | 
|/hana/data/SID1/mnt00001 | Instalace datových souborů pro SID1 | 
|/hana/log/SID1/mnt00001 | Instalace souborů protokolu pro SID1 | 
|/hana/logbackups/SID1 | Protokoly znovu pro SID1 |
|/hana/shared/SID2 | Instalace HANA pro SID2 | 
|/hana/data/SID2/mnt00001 | Instalace datových souborů pro SID2 | 
|/hana/log/SID2/mnt00001 | Instalace souborů protokolu pro SID2 | 
|/hana/logbackups/SID2 | Protokoly znovu pro SID2 |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.
- Distribuce velikosti svazku je založena na velikosti databáze v paměti. V části [Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) se dozvíte, jaké velikosti databází jsou v paměti podporované prostředím multisid.

## <a name="3-single-node-with-dr-normal"></a>3. Jeden uzel se systémem DR (normální)
 
Tato topologie podporuje jeden uzel v konfiguraci škálované nahoru s jedním nebo více SID s replikací na základě úložiště na webu DR pro primární identifikátor SID. V diagramu je v primární lokalitě znázorněn pouze jeden identifikátor SID, ale je také podporován multisid (MCOS).

### <a name="architecture-diagram"></a>Diagram architektury  

![Single-node-with-dr.png](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Instalace HANA pro SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro SID | 
|/hana/logbackups/SID | Protokoly znovu pro identifikátor SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. V části [Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) se dozvíte, jaké velikosti databází jsou v paměti podporované prostředím multisid.
- V programu DR: Pro instalaci instancí produkčního HANA v jednotce DR HLI se nakonfigurují svazky a mountpoints (s označením "požadováno pro instalaci HANA"). 
- V programu DR: Data, logbackups a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) .
- Spouštěcí svazek pro **třídu typu SKU I** se replikuje do uzlu Dr.


## <a name="4-single-node-with-dr-multipurpose"></a>4. Jeden uzel se systémem DR (víceúčelový)
 
Tato topologie podporuje jeden uzel v konfiguraci škálované nahoru s jedním nebo více SID s replikací na základě úložiště na webu DR pro primární identifikátor SID. V diagramu je v primární lokalitě znázorněn pouze jeden identifikátor SID, ale je také podporován multisid (MCOS). V lokalitě DR se jednotka HLI používá pro instanci QA, zatímco provozní operace běží z primární lokality. V době, kdy dojde k převzetí služeb při selhání DR (nebo test převzetí služeb při selhání), se instance QA na webu DR vypne.

### <a name="architecture-diagram"></a>Diagram architektury  

![single-node-with-dr-multipurpose.png](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**Na webu DR**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/shared/QA-SID | Instalace HANA pro zabezpečení pro QA | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro kontrolu zabezpečení pro QA | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro kontrolu zabezpečení pro QA |
|/hana/logbackups/QA-SID | Protokoly znovu pro identifikátor SID pro QA |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. V části [Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) se dozvíte, jaké velikosti databází jsou v paměti podporované prostředím multisid.
- V programu DR: Pro instalaci instancí produkčního HANA v jednotce DR HLI se nakonfigurují svazky a mountpoints (s označením "požadováno pro instalaci HANA"). 
- V programu DR: Data, logbackups a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) . 
- V programu DR: Pro instalaci instance QA jsou nakonfigurovaná data, logbackups, log, sdílené svazky pro QA (s označením "instalace instance QA").
- Spouštěcí svazek pro **třídu typu SKU I** se replikuje do uzlu Dr.

## <a name="5-hsr-with-stonith"></a>5. HSR s STONITH
 
Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA (HSR). Tato konfigurace je podporována pouze pro jednotlivé instance HANA na uzlu. To znamená, že scénáře MCOS nejsou podporované.

**Od této chvíle je tato architektura podporovaná jenom pro operační systém SUSE.**


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR-with-STONITH. png](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Používá se pro STONITH |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Používá se pro STONITH |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**Na sekundárním uzlu**|
|/hana/shared/SID | Instalace HANA pro sekundární identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro sekundární identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro sekundární identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro sekundární identifikátor zabezpečení SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. V části [Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) se dozvíte, jaké velikosti databází jsou v paměti podporované prostředím multisid.
- STONITH: SBD je nakonfigurován pro nastavení STONITH. Použití STONITH je však volitelné.


## <a name="6-hsr-with-dr"></a>6. HSR s nástrojem DR
 
Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA (HSR). Normální i víceúčelový DR je podporován. Tyto konfigurace se podporují jenom pro jednotlivé instance HANA na uzlu. Znamená, MCOS scénáře nejsou u těchto konfigurací podporovány.

V diagramu se v multifunkčním scénáři zobrazuje, kde se v lokalitě DR používá HLI jednotka pro instanci QA, zatímco provozní operace běží z primární lokality. V době, kdy dojde k převzetí služeb při selhání DR (nebo test převzetí služeb při selhání), se instance QA na webu DR vypne. 



### <a name="architecture-diagram"></a>Diagram architektury  

![HSR-with-DR.png](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Používá se pro STONITH |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Používá se pro STONITH |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primárním uzlu v primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**V sekundárním uzlu v primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro sekundární identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro sekundární identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro sekundární identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro sekundární identifikátor zabezpečení SID |
|**Na webu DR**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/shared/QA-SID | Instalace HANA pro zabezpečení pro QA | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro kontrolu zabezpečení pro QA | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro kontrolu zabezpečení pro QA |
|/hana/logbackups/QA-SID | Protokoly znovu pro identifikátor SID pro QA |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. V části [Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) se dozvíte, jaké velikosti databází jsou v paměti podporované prostředím multisid.
- STONITH: SBD je nakonfigurován pro nastavení STONITH. Použití STONITH je však volitelné.
- V programu DR: Pro replikaci primárního a sekundárního uzlu **se vyžadují dvě sady svazků úložiště** .
- V programu DR: Pro instalaci instancí produkčního HANA v jednotce DR HLI se nakonfigurují svazky a mountpoints (s označením "požadováno pro instalaci HANA"). 
- V programu DR: Data, logbackups a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) . 
- V programu DR: Pro instalaci instance QA jsou nakonfigurovaná data, logbackups, log, sdílené svazky pro QA (s označením "instalace instance QA").
- Spouštěcí svazek pro **třídu typu SKU I** se replikuje do uzlu Dr.


## <a name="7-host-auto-failover-11"></a>7. Automatické převzetí služeb při selhání hostitele (1 + 1)
 
Tato topologie podporuje dva uzly v konfiguraci automatického převzetí služeb při selhání hostitele. Existuje jeden uzel s rolí hlavního/pracovního procesu a druhý jako pohotovostní. **SAP podporuje tento scénář pouze pro S/4 HANA.** Další podrobnosti najdete v poznámce OSS "[2408419-SAP S/4HANA-multi-node support](https://launchpad.support.sap.com/#/notes/2408419)".



### <a name="architecture-diagram"></a>Diagram architektury  

![SCA](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly a uzlem |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Komunikace mezi uzly a uzlem |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V hlavním uzlu a v pohotovostních uzlech**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |



### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.
- V pohotovostním režimu: Pro instalaci instance HANA v pohotovostní jednotce se nakonfigurují svazky a mountpoints (s označením "požadováno pro instalaci HANA").
 

## <a name="8-scale-out-with-standby"></a>8. Horizontální navýšení kapacity pomocí úsporného režimu
 
Tato topologie podporuje více uzlů v konfiguraci s možností horizontálního rozšíření kapacity. Existuje jeden uzel s rolí hlavního, jeden nebo více uzlů s rolí pracovního procesu a jeden nebo více uzlů jako pohotovostní režim. I když v daném časovém okamžiku může být pouze jeden hlavní uzel.


### <a name="architecture-diagram"></a>Diagram architektury  

![scaleout-nm-Standby. png](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly a uzlem |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Komunikace mezi uzly a uzlem |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V uzlech hlavní, pracovní a pohotovostní**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |


## <a name="9-scale-out-without-standby"></a>9. Horizontální navýšení kapacity bez úsporného režimu
 
Tato topologie podporuje více uzlů v konfiguraci s možností horizontálního rozšíření kapacity. Existuje jeden uzel s rolí hlavního serveru a jeden nebo více uzlů s rolí pracovního procesu. I když v daném časovém okamžiku může být pouze jeden hlavní uzel.


### <a name="architecture-diagram"></a>Diagram architektury  

![scaleout-nm.png](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly a uzlem |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Komunikace mezi uzly a uzlem |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V hlavních a pracovních uzlech**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.

## <a name="10-scale-out-with-dr"></a>10. Horizontální navýšení kapacity pomocí nástroje DR
 
Tato topologie podporuje více uzlů ve škálování na více instancí s použitím nástroje DR. Podpora normálního i víceúčelového DR je podporovaná. V diagramu je znázorněn pouze jeden účel DR. Tuto topologii můžete vyžádat pomocí nebo bez úsporného uzlu.


### <a name="architecture-diagram"></a>Diagram architektury  

![scaleout-with-dr.png](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| LOGICKÁ ROZHRANÍ NIC | TYP SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient na HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly a uzlem |
| C | TYP I | eth1. tenant | eno2. tenant | Uzel do úložiště |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | tenantNo\<> sítě VLAN | team0. tenant | Klient na HLI |
| B | TYP II | síť\<VLAN tenantNo + 2 > | team0. tenant + 2 | Komunikace mezi uzly a uzlem |
| C | TYP II | síť\<VLAN tenantNo + 1 > | team0. tenant + 1 | Uzel do úložiště |
| D | TYP II | síť\<VLAN tenantNo + 3 > | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Předkonfigurované jsou následující mountpoints:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**V uzlu DR**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Nainstalují se datové soubory pro produkční identifikátor SID. | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID.
-  V programu DR: Pro instalaci instancí produkčního HANA v jednotce DR HLI se nakonfigurují svazky a mountpoints (s označením "požadováno pro instalaci HANA"). 
- V programu DR: Data, logbackups a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze během doby převzetí služeb při selhání. Další informace najdete v dokumentu [postup převzetí služeb při selhání pro zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) . 
- Spouštěcí svazek pro **třídu typu SKU I** se replikuje do uzlu Dr.


## <a name="next-steps"></a>Další postup
- Přečtěte si [infrastrukturu a konektivitu](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) pro HLI
- Přečtěte si [vysokou dostupnost a zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) pro HLI.

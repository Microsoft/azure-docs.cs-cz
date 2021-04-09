---
title: Podporované scénáře pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Podporované scénáře a jejich podrobnosti architektury pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a49c55da0bb8018b3faa7c6f70b02e79d9478603
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666679"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Podporované scénáře pro velké instance HANA
Tento článek popisuje podporované scénáře a podrobnosti architektury pro velké instance HANA (HLI).

>[!NOTE]
>Pokud se požadovaný scénář nezmiňuje v tomto článku, obraťte se na tým Microsoftu pro správu služeb a vyhodnoťte vaše požadavky.
Před nastavením jednotky HLI ověřte návrh pomocí SAP nebo partnera pro implementaci služeb.

## <a name="terms-and-definitions"></a>Pojmy a definice
Pojďme pochopit pojmy a definice, které se používají v tomto článku:

- **SID**: systémový identifikátor systému Hana
- **HLI**: velké instance Hana
- **Dr**: zotavení po havárii
- **Normální Dr**: nastavení systému s vyhrazeným prostředkem pouze pro účely zotavení po havárii
- **Víceúčelový Dr**: systém Dr-site, který je nakonfigurovaný tak, aby používal neprodukční prostředí spolu s produkční instancí nakonfigurovanou pro událost zotavení po havárii. 
- **Single-SID**: systém s jednou nainstalovanou instancí
- **Multi-SID**: systém s nakonfigurovanými více instancemi; označuje se taky jako MCOS prostředí.
- **HSR**: replikace systému SAP HANA

## <a name="overview"></a>Přehled
Velké instance HANA podporují nejrůznější architektury, které vám pomůžou dosáhnout vašich obchodních požadavků. Následující části obsahují informace o scénářích architektury a jejich podrobnostech o konfiguraci. 

Návrh odvozené architektury je čistě z hlediska infrastruktury a Vy musíte pro nasazení HANA konzultovat informace o SAP nebo partnerovi implementace. Pokud vaše scénáře nejsou uvedené v tomto článku, obraťte se na tým účet Microsoft, kde si můžete projít architekturu a odvodit pro vás řešení.

> [!NOTE]
> Tyto architektury jsou plně kompatibilní s návrhem rozhraní TDI (Standard Integration Data Integration) a podporovaném SAP.

Tento článek popisuje podrobné informace o těchto dvou součástech v každé podporované architektuře:

- Ethernet
- Storage

### <a name="ethernet"></a>Ethernet

Každý zřízený Server obsahuje předem nakonfigurované sady rozhraní Ethernet. Rozhraní sítě Ethernet konfigurovaná u každé jednotky HLI jsou rozdělená do čtyř typů:

- Odpověď: používá **se** pro nebo klientský přístup.
- **B**: používá se pro komunikaci mezi uzly. Toto rozhraní je nakonfigurované na všech serverech (bez ohledu na požadovanou topologii), ale používá se jenom pro scénáře škálování na více instancí.
- **C**: používá se pro připojení mezi uzly.
- **D**: pro instalaci STONITH se používá pro připojení zařízení mezi uzly a iSCSI. Toto rozhraní je konfigurováno pouze v případě, že je požadován instalační program HSR.  

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Uzel do uzlu|
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | STONITH |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Uzel do uzlu|
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | STONITH |

Zvolíte rozhraní na základě topologie, která je nakonfigurovaná na HLI jednotce. Například rozhraní "B" je nastaveno pro komunikaci mezi uzly, což je užitečné v případě, že máte nakonfigurované topologie škálování na více instancí. Toto rozhraní se nepoužívá pro jeden uzel, konfigurace pro horizontální navýšení kapacity. Další informace o použití rozhraní najdete v požadovaných scénářích (dále v tomto článku). 

V případě potřeby můžete definovat další karty síťových adaptérů sami. Konfigurace stávajících síťových karet se ale *nedají* změnit.

>[!NOTE]
>Můžete najít další rozhraní, která jsou fyzická rozhraní nebo vázání. Měli byste zvážit pouze dříve uvedená rozhraní pro váš případ použití. Jakékoli další mohou být ignorovány.

Rozdělení jednotek se dvěma přiřazenými IP adresami by mělo vypadat takto:

- Síť Ethernet "A" by měla mít přiřazenou IP adresu v rozsahu adres fondu IP adres, který jste odeslali do Microsoftu. Tato IP adresa by měla být zachována v adresáři */etc/hosts* operačního systému.

- Síť Ethernet "C" by měla mít přiřazenou IP adresu, která se používá pro komunikaci se systémem souborů NFS. Tuto adresu *není* nutné udržovat v adresáři *etc/hosts* , aby bylo možné provozovat instance v rámci tenanta.

Pro replikaci systému HANA nebo nasazení s více instancemi v HANA není konfigurace okna se dvěma přiřazenými IP adresami vhodná. Pokud máte jenom dvě přiřazené IP adresy a chcete takovou konfiguraci nasadit, obraťte se SAP HANA na správu služeb Azure. Můžou vám přiřadit třetí IP adresu ve třetí síti VLAN. Pro jednotky velkých instancí HANA se třemi přiřazenými IP adresami na tři porty síťových adaptérů platí následující pravidla použití:

- Síť Ethernet "A" by měla mít přiřazenou IP adresu mimo rozsah adres fondu IP adres, který jste odeslali společnosti Microsoft. Tato IP adresa by neměla být zachována v adresáři *etc/hostitelů* operačního systému.

- Síť Ethernet "B" by měla být zachována výhradně v adresáři *etc/hosts* pro komunikaci mezi různými instancemi. Toto jsou IP adresy, které se mají zachovat v konfiguracích se škálováním na více instancí, jako IP adresy, které HANA používá pro konfiguraci mezi uzly.

- Síť Ethernet "C" by měla mít přiřazenou IP adresu, která se používá ke komunikaci s úložištěm NFS. Tento typ adresy by neměl být udržován v adresáři *etc/hosts* .

- Síť Ethernet "D" by se měla používat výhradně pro přístup k zařízením STONITH pro Pacemaker. Toto rozhraní se vyžaduje při konfiguraci replikace systému HANA a k zajištění automatického převzetí služeb při selhání operačního systému pomocí zařízení založeného na SBD.


### <a name="storage"></a>Storage
Úložiště je předem nakonfigurované na základě požadované topologie. Velikosti svazků a přípojné body se liší v závislosti na počtu serverů, počtu SKU a nakonfigurované topologii. Další informace najdete v požadovaných scénářích (dále v tomto článku). Pokud potřebujete víc úložiště, můžete si ho koupit v přírůstcích po 1 TB.

>[!NOTE]
>Přípojný bod/usr/SAP/ \<SID> je symbolický odkaz na přípojný bod/Hana/Shared.


## <a name="supported-scenarios"></a>Podporované scénáře

Diagramy architektury v dalších částech používají následující zápisy:

[![Tabulka diagramů architektury](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Tady jsou podporované scénáře:

* Jeden uzel s jedním identifikátorem SID
* MCOS jednoho uzlu
* Jeden uzel se systémem DR (normální)
* Jeden uzel se systémem DR (víceúčelový)
* HSR s STONITH
* HSR s nástrojem DR (normální/víceúčelový) 
* Automatické převzetí služeb při selhání hostitele (1 + 1) 
* Horizontální navýšení kapacity pomocí úsporného režimu
* Horizontální navýšení kapacity bez úsporného režimu
* Horizontální navýšení kapacity pomocí nástroje DR

## <a name="single-node-with-one-sid"></a>Jeden uzel s jedním identifikátorem SID

Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním identifikátorem SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s jedním identifikátorem SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Instalace HANA | 
|/hana/data/SID/mnt00001 | Instalace datových souborů | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu | 
|/hana/logbackups/SID | Protokoly opětovného provedení |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..

## <a name="single-node-mcos"></a>MCOS jednoho uzlu

Tato topologie podporuje jeden uzel v konfiguraci škálování s více identifikátory SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![MCOS jednoho uzlu](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

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
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databází jsou v paměti podporované v prostředí s více identifikátory SID, najdete v tématu [Přehled a architektura](./hana-overview-architecture.md).

## <a name="single-node-with-dr-using-storage-replication"></a>Jeden uzel se systémem DR pomocí replikace úložiště
 
Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním nebo více identifikátory SID s replikací založenými na úložištích na webu DR pro primární identifikátor SID. V diagramu se v primární lokalitě znázorňuje jenom systém s jedním identifikátorem SID, ale podporují se i systémy MCOS.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel se systémem DR pomocí replikace úložiště](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Instalace HANA pro SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro identifikátor SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- MCOS: distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databází jsou v paměti podporované v prostředí s více identifikátory SID, najdete v tématu [Přehled a architektura](./hana-overview-architecture.md).
- Na webu DR: jsou nakonfigurované svazky a přípojné body (u instalace s označením "požadováno pro instalaci HANA") pro instalaci instance provozní HANA v HLI jednotce DR. 
- Na webu DR: data, zálohy protokolů a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace najdete v tématu [postup převzetí služeb při selhání při zotavení po havárii](./hana-overview-high-availability-disaster-recovery.md).
- Spouštěcí svazek pro *třídu typu SKU I* se replikuje do uzlu Dr.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Jeden uzel se systémem DR (víceúčelový) pomocí replikace úložiště
 
Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním nebo více identifikátory SID s replikací založenými na úložištích na webu DR pro primární identifikátor SID. V diagramu se v primární lokalitě znázorňuje jenom systém s jedním identifikátorem SID, ale podporují se i systémy s více identifikátory SID (MCOS). V lokalitě DR se jednotka HLI používá pro instanci QA, zatímco provozní operace běží z primární lokality. Během převzetí služeb při selhání (nebo testu převzetí služeb při selhání) se vypíná instance QA na webu DR.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel se systémem DR (víceúčelový) pomocí replikace úložiště](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**Na webu DR**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/shared/QA-SID | Instalace HANA pro identifikátor SID pro QA | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro kontrolu zabezpečení pro QA | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro kontrolu zabezpečení pro QA |
|/hana/logbackups/QA-SID | Protokoly znovu pro identifikátor SID pro QA |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- MCOS: distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databází jsou v paměti podporované v prostředí s více identifikátory SID, najdete v tématu [Přehled a architektura](./hana-overview-architecture.md).
- Na webu DR: jsou nakonfigurované svazky a přípojné body (u instalace s označením "požadováno pro instalaci HANA") pro instalaci instance provozní HANA v HLI jednotce DR. 
- Na webu DR: data, zálohy protokolů a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace najdete v tématu [postup převzetí služeb při selhání při zotavení po havárii](./hana-overview-high-availability-disaster-recovery.md). 
- Na webu DR: jsou nakonfigurovaná data, zálohy protokolů, protokoly a sdílené svazky pro QA (s označením "instalace instance QA") pro instalaci instance QA.
- Spouštěcí svazek pro *třídu typu SKU I* se replikuje do uzlu Dr.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR s STONITH pro vysokou dostupnost
 
Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA. Tato konfigurace je podporovaná jenom pro jednotlivé instance HANA na uzlu. To znamená, že scénáře MCOS *nejsou podporovány.*

> [!NOTE]
> Od prosince 2019 je tato architektura podporovaná jenom pro operační systém SUSE.


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR s STONITH pro vysokou dostupnost](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Používá se pro STONITH |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Používá se pro STONITH |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**Na sekundárním uzlu**|
|/hana/shared/SID | Instalace HANA pro sekundární identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro sekundární identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro sekundární identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro sekundární identifikátor zabezpečení SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- MCOS: distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databází jsou v paměti podporované v prostředí s více identifikátory SID, najdete v tématu [Přehled a architektura](./hana-overview-architecture.md).
- STONITH: SBD je nakonfigurovaný pro nastavení STONITH. Použití STONITH je však volitelné.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Vysoká dostupnost pomocí HSR a zotavení po havárii s využitím replikace úložiště
 
Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA. Podporují se normální i víceúčelové DRs. Tyto konfigurace se podporují jenom pro jednotlivé instance HANA na uzlu. To znamená, že MCOS scénáře *nejsou u těchto konfigurací podporovány.*

V diagramu je víceúčelový scénář znázorněný na webu DR, kde se jednotka HLI používá pro instanci QA, zatímco provozní operace běží z primární lokality. Během převzetí služeb při selhání (nebo testu převzetí služeb při selhání) se vypíná instance QA na webu DR. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Vysoká dostupnost pomocí HSR a zotavení po havárii s využitím replikace úložiště](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Používá se pro STONITH |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Používá se pro STONITH |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primárním uzlu v primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**V sekundárním uzlu v primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro sekundární identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro sekundární identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro sekundární identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro sekundární identifikátor zabezpečení SID |
|**Na webu DR**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/shared/QA-SID | Instalace HANA pro identifikátor SID pro QA | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro kontrolu zabezpečení pro QA | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro kontrolu zabezpečení pro QA |
|/hana/logbackups/QA-SID | Protokoly znovu pro identifikátor SID pro QA |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- MCOS: distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databází jsou v paměti podporované v prostředí s více identifikátory SID, najdete v tématu [Přehled a architektura](./hana-overview-architecture.md).
- STONITH: SBD je nakonfigurovaný pro nastavení STONITH. Použití STONITH je však volitelné.
- Na webu DR: pro replikaci primárního a sekundárního uzlu *jsou vyžadovány dvě sady svazků úložiště* .
- Na webu DR: jsou nakonfigurované svazky a přípojné body (u instalace s označením "požadováno pro instalaci HANA") pro instalaci instance provozní HANA v HLI jednotce DR. 
- Na webu DR: data, zálohy protokolů a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace najdete v tématu [postup převzetí služeb při selhání při zotavení po havárii](./hana-overview-high-availability-disaster-recovery.md). 
- Na webu DR: jsou nakonfigurovaná data, zálohy protokolů, protokoly a sdílené svazky pro QA (s označením "instalace instance QA") pro instalaci instance QA.
- Spouštěcí svazek pro *třídu typu SKU I* se replikuje do uzlu Dr.


## <a name="host-auto-failover-11"></a>Automatické převzetí služeb při selhání hostitele (1 + 1)
 
Tato topologie podporuje dva uzly v konfiguraci automatického převzetí služeb při selhání hostitele. Existuje jeden uzel s rolí hlavního/pracovního procesu a druhý jako pohotovostní. *SAP podporuje tento scénář pouze pro S/4 HANA.* Další informace najdete v tématu standard [OSS note 2408419-SAP S/4HANA-multi-node support](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagram architektury  

![Automatické převzetí služeb při selhání hostitele (1 + 1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet&quot;></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Komunikace mezi uzly |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name=&quot;storage&quot;></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V hlavním uzlu a v pohotovostních uzlech**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |



### <a name=&quot;key-considerations&quot;></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- V pohotovostním režimu: pro instalaci instance HANA v pohotovostní jednotce se nakonfigurují svazky a přípojné body (s označením &quot;požadováno pro instalaci HANA").
 

## <a name="scale-out-with-standby"></a>Horizontální navýšení kapacity pomocí úsporného režimu
 
Tato topologie podporuje více uzlů v konfiguraci s možností horizontálního rozšíření kapacity. Existuje jeden uzel s rolí hlavního serveru, jeden nebo více uzlů s rolí pracovního procesu a jeden nebo více uzlů jako pohotovostní režim. V jednom okamžiku ale může být pouze jeden hlavní uzel.


### <a name="architecture-diagram"></a>Diagram architektury  

![Horizontální navýšení kapacity pomocí úsporného režimu](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Komunikace mezi uzly |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V uzlech hlavní, pracovní a pohotovostní**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |


## <a name="scale-out-without-standby"></a>Horizontální navýšení kapacity bez úsporného režimu
 
Tato topologie podporuje více uzlů v konfiguraci s možností horizontálního rozšíření kapacity. Existuje jeden uzel s rolí hlavního serveru a jeden nebo více uzlů s rolí pracovního procesu. V jednom okamžiku ale může být pouze jeden hlavní uzel.


### <a name="architecture-diagram"></a>Diagram architektury  

![Horizontální navýšení kapacity bez úsporného režimu](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Komunikace mezi uzly |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V hlavních a pracovních uzlech**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..

## <a name="scale-out-with-dr-using-storage-replication"></a>Horizontální navýšení kapacity pomocí replikace úložiště v systému DR
 
Tato topologie podporuje více uzlů ve škálování na více instancí s použitím nástroje DR. Podporují se normální i víceúčelové DRs. V diagramu je znázorněn pouze jeden účel DR. Tuto topologii můžete vyžádat pomocí nebo bez úsporného uzlu.


### <a name="architecture-diagram"></a>Diagram architektury  

![Horizontální navýšení kapacity pomocí replikace úložiště v systému DR](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet&quot;></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-HLI |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-HLI |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Komunikace mezi uzly |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name=&quot;storage&quot;></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**V uzlu DR**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 


### <a name=&quot;key-considerations&quot;></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
-  Na webu DR: jsou nakonfigurované svazky a přípojné body (u instalace s označením &quot;požadováno pro instalaci HANA") pro instalaci instance provozní HANA v HLI jednotce DR. 
- Na webu DR: data, zálohy protokolů a sdílené svazky (označené jako replikace úložiště) se replikují prostřednictvím snímku z produkčního webu. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace najdete v tématu [postup převzetí služeb při selhání při zotavení po havárii](./hana-overview-high-availability-disaster-recovery.md). 
- Spouštěcí svazek pro *třídu typu SKU I* se replikuje do uzlu Dr.


## <a name="single-node-with-dr-using-hsr"></a>Jeden uzel s DR pomocí HSR
 
Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním identifikátorem zabezpečení (SID) s replikací systému HANA na lokalitu DR pro primární identifikátor SID. V diagramu se v primární lokalitě znázorňuje jenom systém s jedním identifikátorem SID, ale podporují se i systémy s více identifikátory SID (MCOS).

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s DR pomocí HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-to-HLI/HSR |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-to-HLI/HSR |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
V jednotkách HLI (primárních i DR) jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Instalace HANA pro SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro identifikátor SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- MCOS: distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databází jsou v paměti podporované v prostředí s více identifikátory SID, najdete v tématu [Přehled a architektura](./hana-overview-architecture.md).
- Primární uzel se synchronizuje s uzlem DR pomocí replikace systému HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) slouží k propojení okruhů ExpressRoute a vytvoření privátní sítě mezi vašimi regionálními sítěmi.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>HSR jednoho uzlu do DR (náklady optimalizované) 
 
 Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním identifikátorem zabezpečení (SID) s replikací systému HANA na lokalitu DR pro primární identifikátor SID. V diagramu se v primární lokalitě znázorňuje jenom systém s jedním identifikátorem SID, ale podporují se i systémy s více identifikátory SID (MCOS). V lokalitě DR se jednotka HLI používá pro instanci QA, zatímco provozní operace běží z primární lokality. Během převzetí služeb při selhání (nebo testu převzetí služeb při selhání) se vypíná instance QA na webu DR.

### <a name="architecture-diagram"></a>Diagram architektury  

![HSR jednoho uzlu do DR (náklady optimalizované)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-to-HLI/HSR |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-to-HLI/HSR |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**Na webu DR**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|/hana/shared/QA-SID | Instalace HANA pro identifikátor SID pro QA | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro kontrolu zabezpečení pro QA | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro kontrolu zabezpečení pro QA |
|/hana/logbackups/QA-SID | Protokoly znovu pro identifikátor SID pro QA |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- MCOS: distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databází jsou v paměti podporované v prostředí s více identifikátory SID, najdete v tématu [Přehled a architektura](./hana-overview-architecture.md).
- Na serveru DR: jsou nakonfigurované svazky a přípojné body (označené jako "výrobní instance v lokalitě DR") pro instalaci instance provozní HANA v HLI jednotce DR. 
- Na webu DR: jsou nakonfigurovaná data, zálohy protokolů, protokoly a sdílené svazky pro QA (s označením "instalace instance QA") pro instalaci instance QA.
- Primární uzel se synchronizuje s uzlem DR pomocí replikace systému HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) slouží k propojení okruhů ExpressRoute a vytvoření privátní sítě mezi vašimi regionálními sítěmi.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Vysoká dostupnost a zotavení po havárii pomocí HSR 
 
 Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA pro vysokou dostupnost místní oblasti. V případě zotavení po havárii se třetí uzel v oblasti DR synchronizuje s primární lokalitou pomocí HSR (asynchronní režim). 

### <a name="architecture-diagram"></a>Diagram architektury  

![Vysoká dostupnost a zotavení po havárii pomocí HSR](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet&quot;></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-to-HLI/HSR |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-to-HLI/HSR |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name=&quot;storage&quot;></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**Na webu DR**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |


### <a name=&quot;key-considerations&quot;></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- Na serveru DR: jsou nakonfigurované svazky a přípojné body (označené jako &quot;výr DR instance") pro instalaci instance v produkčním prostředí HANA na HLI jednotce DR. 
- Uzel primární lokality se synchronizuje s uzlem DR pomocí replikace systému HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) slouží k propojení okruhů ExpressRoute a vytvoření privátní sítě mezi vašimi regionálními sítěmi.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Vysoká dostupnost a zotavení po havárii s HSR (náklady optimalizované)
 
 Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA pro vysokou dostupnost místní oblasti. V případě DR se třetí uzel v oblasti DR synchronizuje s primární lokalitou pomocí HSR (asynchronní režim), zatímco jiná instance (například QA) už je spuštěná z uzlu DR. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Vysoká dostupnost a zotavení po havárii s HSR (náklady optimalizované)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet&quot;></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-to-HLI/HSR |
| B | TYP I | ETH2. tenant | eno3. tenant | Nakonfigurováno, ale nepoužívá se |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-to-HLI/HSR |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Nakonfigurováno, ale nepoužívá se |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name=&quot;storage&quot;></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**Na webu DR**|
|/hana/shared/SID | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|/hana/shared/QA-SID | Instalace HANA pro identifikátor SID pro QA | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro kontrolu zabezpečení pro QA | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro kontrolu zabezpečení pro QA |
|/hana/logbackups/QA-SID | Protokoly znovu pro identifikátor SID pro QA |

### <a name=&quot;key-considerations&quot;></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- Na serveru DR: jsou nakonfigurované svazky a přípojné body (označené jako &quot;výr DR instance") pro instalaci instance v produkčním prostředí HANA na HLI jednotce DR. 
- Na webu DR: jsou nakonfigurovaná data, zálohy protokolů, protokoly a sdílené svazky pro QA (s označením "instalace instance QA") pro instalaci instance QA.
- Uzel primární lokality se synchronizuje s uzlem DR pomocí replikace systému HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) slouží k propojení okruhů ExpressRoute a vytvoření privátní sítě mezi vašimi regionálními sítěmi.

## <a name="scale-out-with-dr-using-hsr"></a>Horizontální navýšení kapacity pomocí HSR
 
Tato topologie podporuje více uzlů ve škálování na více instancí s použitím nástroje DR. Tuto topologii můžete vyžádat pomocí nebo bez úsporného uzlu. Uzel primární lokality se synchronizuje s uzlem webu DR pomocí replikace systému HANA (asynchronní režim).


### <a name="architecture-diagram"></a>Diagram architektury  

[Horizontální navýšení ![ kapacity pomocí HSR](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Ethernet
Jsou předem nakonfigurované následující síťová rozhraní:

| Logické rozhraní NIC | Typ SKU | Název s operačním systémem SUSE | Název s operačním systémem RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0. tenant | eno1. tenant | Klient-to-HLI/HSR |
| B | TYP I | ETH2. tenant | eno3. tenant | Komunikace mezi uzly |
| C | TYP I | eth1. tenant | eno2. tenant | Mezi uzly a úložištěm |
| D | TYP I | eth4. tenant | eno4. tenant | Nakonfigurováno, ale nepoužívá se |
| A | TYP II | režim\<tenantNo> | team0. tenant | Klient-to-HLI/HSR |
| B | TYP II | režim\<tenantNo+2> | team0. tenant + 2 | Komunikace mezi uzly |
| C | TYP II | režim\<tenantNo+1> | team0. tenant + 1 | Mezi uzly a úložištěm |
| D | TYP II | režim\<tenantNo+3> | team0. tenant + 3 | Nakonfigurováno, ale nepoužívá se |

### <a name="storage"></a>Storage
Jsou předem nakonfigurované následující přípojné body:

| Přípojný bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |
|**V uzlu DR**|
|/hana/shared | Instalace HANA pro produkční identifikátor SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční identifikátor SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční identifikátor SID | 
|/hana/logbackups/SID | Protokoly znovu pro produkční identifikátor SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na/hana/shared/SID..
- Na serveru DR: svazky a přípojné body jsou konfigurovány pro instalaci instance provozní HANA v HLI jednotce DR. 
- Uzel primární lokality se synchronizuje s uzlem DR pomocí replikace systému HANA. 
- [Global REACH](../../../expressroute/expressroute-global-reach.md) slouží k propojení okruhů ExpressRoute a vytvoření privátní sítě mezi vašimi regionálními sítěmi.


## <a name="next-steps"></a>Další kroky
- [Infrastruktura a konektivita](./hana-overview-infrastructure-connectivity.md) pro velké instance Hana
- [Vysoká dostupnost a zotavení po havárii](./hana-overview-high-availability-disaster-recovery.md) pro velké instance Hana

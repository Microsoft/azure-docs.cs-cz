---
title: Podporované scénáře pro SAP HANA v Azure (velké instance)| Dokumenty společnosti Microsoft
description: Podporované scénáře a podrobnosti o jejich architektuře pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 019f462d4264d19bcc4806d91223029a95f9d819
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617171"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>Podporované scénáře pro velké instance HANA
Tento článek popisuje podporované scénáře a podrobnosti o architektuře pro velké instance HANA (HLI).

>[!NOTE]
>Pokud váš požadovaný scénář není uveden v tomto článku, obraťte se na tým microsoft service management posoudit vaše požadavky.
Před nastavením jednotky HLI ověřte návrh se společností SAP nebo partnerem implementace služby.

## <a name="terms-and-definitions"></a>Pojmy a definice
Pojďme pochopit pojmy a definice, které se používají v tomto článku:

- **SID**: Identifikátor systému pro systém HANA
- **HLI**: Velké instance Hana
- **DR**: Zotavení po havárii
- **Normální DR:** Nastavení systému s vyhrazeným zdrojem pouze pro účely zotavení po havárii
- **Víceúčelová dr. Dr.** Dr. Systém lokality, který je nakonfigurován pro použití neprodukčního prostředí vedle produkční instance, která je nakonfigurovaná pro událost zotavení po Havárii 
- **Single-SID**: Systém s nainstalovanou jednou instancí
- **Multi-SID**: Systém s více instancemi nakonfigurován; také nazývá mcos prostředí
- **HSR**: Replikace systému SAP HANA

## <a name="overview"></a>Přehled
Velké instance HANA podporují celou řadu architektur, které vám pomohou splnit vaše obchodní požadavky. Následující části popisují architektonické scénáře a podrobnosti jejich konfigurace. 

Návrh odvozené architektury je čistě z hlediska infrastruktury a je nutné konzultovat SAP nebo vaše implementační partnery pro nasazení HANA. Pokud vaše scénáře nejsou uvedeny v tomto článku, obraťte se na tým účtu Microsoft zkontrolovat architekturu a odvodit řešení pro vás.

> [!NOTE]
> Tyto architektury jsou plně kompatibilní s návrhem tdi (Tailored Data Integration) a podporované sapem.

Tento článek popisuje podrobnosti o dvě součásti v každé podporované architektury:

- Síť Ethernet
- Úložiště

### <a name="ethernet"></a>Síť Ethernet

Každý zřízený server je dodáván s předkonfigurovanými sadami ethernetových rozhraní. Ethernetová rozhraní nakonfigurovaná na každé jednotce HLI jsou rozdělena do čtyř typů:

- **A**: Používá se pro nebo přístupem klienta.
- **B**: Používá se pro komunikaci mezi uzlami. Toto rozhraní je konfigurováno na všech serverech (bez ohledu na požadovanou topologii), ale používá se pouze pro scénáře horizontálního navýšení kapacity.
- **C**: Používá se pro připojení mezi uznami do úložiště.
- **D**: Používá se pro připojení zařízení mezi uzly k iSCSI pro nastavení STONITH. Toto rozhraní je konfigurováno pouze v případě, že je požadováno nastavení HSR.  

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Uzel-uzel|
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | STONITH |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Uzel-uzel|
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

Můžete zvolit rozhraní na základě topologie, která je nakonfigurována na jednotce HLI. Například rozhraní "B" je nastavenpro komunikaci mezi uzlem, což je užitečné, když máte nakonfigurovanou topologii horizontálního navýšení kapacity. Toto rozhraní se nepoužívá pro konfigurace s jedním uzly, které jsou nastupnice. Další informace o využití rozhraní, zkontrolujte požadované scénáře (dále v tomto článku). 

V případě potřeby můžete definovat další karty NIC sami. Konfigurace existujících nic však *nelze* změnit.

>[!NOTE]
>Můžete najít další rozhraní, která jsou fyzické rozhraní nebo lepení. Měli byste zvážit pouze výše uvedené rozhraní pro váš případ použití. Ostatní mohou být ignorovány.

Distribuce pro jednotky se dvěma přiřazenými adresami IP by měla vypadat takto:

- Ethernet "A" by měl mít přiřazenou IP adresu, která je v rozsahu adres fondu IP serveru, který jste odeslali společnosti Microsoft. Tato IP adresa by měla být udržována v adresáři */etc/hosts* operačního systému.

- Ethernet "C" by měl mít přiřazenou IP adresu, která se používá pro komunikaci se službou NFS. Tato adresa *nemusí* být udržována v adresáři *etc/hosts,* aby byl povolen provoz instance v rámci klienta.

Pro nasazení replikace systému HANA nebo nasazení horizontálního navýšení kapacity HANA není vhodná konfigurace okna se dvěma přiřazenými adresami IP. Pokud máte jenom dvě přiřazené IP adresy a chcete nasadit takovou konfiguraci, obraťte se na SAP HANA ve službě Azure Service Management. Mohou vám přiřadit třetí IP adresu ve třetí síti VLAN. Pro jednotky velkých instancí HANA se třemi přiřazenými adresami IP na třech portech nic platí následující pravidla použití:

- Ethernet "A" by měl mít přiřazenou IP adresu, která je mimo rozsah adres fondu IP serveru, který jste odeslali společnosti Microsoft. Tato IP adresa by neměla být udržována v adresáři *etc/hosts* operačního systému.

- Ethernet "B" by měl být udržován výhradně v adresáři *etc/hosts* pro komunikaci mezi různými instancemi. Jedná se o IP adresy, které mají být udržovány v konfiguracích HANA s horizontálním navýšením kapacity jako adresy IP, které HANA používá pro konfiguraci mezi uzly.

- Ethernet "C" by měl mít přiřazenou IP adresu, která se používá pro komunikaci s úložištěm NFS. Tento typ adresy by neměl být udržován v adresáři *etc/hosts.*

- Ethernet "D" by měl být používán výhradně pro přístup k zařízením STONITH pro kardiostimulátor. Toto rozhraní je vyžadováno při konfiguraci replikace systému HANA a chcete dosáhnout automatického převzetí služeb při selhání operačního systému pomocí zařízení založeného na Protokolu SBD.


### <a name="storage"></a>Úložiště
Úložiště je předkonfigurováno na základě požadované topologie. Velikostsvazku a přípojné body se liší v závislosti na počtu serverů, počtu skutových částem a nakonfigurované topologii. Další informace naleznete v požadovaných scénářích (dále v tomto článku). Pokud požadujete další úložiště, můžete si ho zakoupit po krocích po 1 TB.

>[!NOTE]
>Přípojný bod /usr/sap/\<SID> je symbolický odkaz na /hana/shared mount point.


## <a name="supported-scenarios"></a>Podporované scénáře

Diagramy architektury v následujících částech používají následující zápisy:

[![Tabulka diagramů architektury](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

Zde jsou podporované scénáře:

* Jeden uzel s jedním SID
* Jeden uzel MCOS
* Jeden uzel s DR (normální)
* Jeden uzel s DR (víceúčelový)
* HSR se stonitthem
* HSR s DR (normální/víceúčelové) 
* Automatické převzetí služeb při selhání hostitele (1+1) 
* Horizontální navýšení kapacity s pohotovostním režimem
* Horizontální navýšení kapacity bez pohotovostního režimu
* Horizontální navýšení kapacity pomocí zotavení po havárii

## <a name="single-node-with-one-sid"></a>Jeden uzel s jedním SID

Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s jedním SID](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Instalace HANA | 
|/hana/data/SID/mnt00001 | Instalace datových souborů | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu | 
|/hana/logbackups/SID | Znovu protokoly |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.

## <a name="single-node-mcos"></a>Jeden uzel MCOS

Tato topologie podporuje jeden uzel v konfiguraci škálování s více SID.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|/hana/shared/SID1 | Instalace HANA pro SID1 | 
|/hana/data/SID1/mnt00001 | Instalace datových souborů pro SID1 | 
|/hana/log/SID1/mnt00001 | Instalace souborů protokolu pro SID1 | 
|/hana/logbackups/SID1 | Znovu protokoly pro SID1 |
|/hana/shared/SID2 | Hana instalace pro SID2 | 
|/hana/data/SID2/mnt00001 | Instalace datových souborů pro SID2 | 
|/hana/log/SID2/mnt00001 | Instalace souborů protokolu pro SID2 | 
|/hana/logbackups/SID2 | Znovu protokoly pro SID2 |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databáze v paměti jsou podporovány v prostředí s více sid, naleznete v [tématu Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).

## <a name="single-node-with-dr-using-storage-replication"></a>Jeden uzel s zotavenípo uhlazenou pomocí replikace úložiště
 
Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním nebo více SID, s replikací na základě úložiště do lokality ZOTAVENÍ pro primární SID. V diagramu je v primární lokalitě zobrazen pouze systém jednoho SID, ale podporovány jsou také systémy MCOS.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s zotavenípo uhlazenou pomocí replikace úložiště](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Hana instalace pro SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro SID | 
|/hana/logbackups/SID | Znovu protokoly pro SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databáze v paměti jsou podporovány v prostředí s více sid, naleznete v [tématu Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- V lokalitě zotavení po Havárii: Svazky a přípojné body jsou konfigurovány (označeny jako "Povinné pro instalaci HANA") pro instalaci instance HANA v jednotce DR HLI. 
- V lokalitě zotavení po Havárii: Data, zálohprotokolu a sdílené svazky (označené jako "Replikace úložiště") jsou replikovány prostřednictvím snímku z produkčního místa. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace naleznete v [tématu Postup převzetí služeb při selhání zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery).
- Spouštěcí svazek pro *třídu SKU typu I* je replikován do uzlu zotavení po havárii.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>Jeden uzel s DR (víceúčelový) pomocí replikace úložiště
 
Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním nebo více SID, s replikací na základě úložiště do lokality ZOTAVENÍ pro primární SID. V diagramu je v primární lokalitě zobrazen pouze systém jednoho SID, ale jsou podporovány také systémy mod (MCOS). V lokalitě ZOTAVENÍ po Havárii se jednotka HLI používá pro instanci QA, zatímco produkční operace jsou spuštěny z primární lokality. Během převzetí služeb při selhání (nebo test převzetí služeb při selhání) se instance QA v lokalitě zotavení po Havárii vypne.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s DR (víceúčelový) pomocí replikace úložiště](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**Na stránkách zotavení po Havárii**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/shared/QA-SID | Hana instalace pro QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro qa sid |
|/hana/logbackups/QA-SID | Znovu protokoly pro QA SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databáze v paměti jsou podporovány v prostředí s více sid, naleznete v [tématu Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- V lokalitě zotavení po Havárii: Svazky a přípojné body jsou konfigurovány (označeny jako "Povinné pro instalaci HANA") pro instalaci instance HANA v jednotce DR HLI. 
- V lokalitě zotavení po Havárii: Data, zálohprotokolu a sdílené svazky (označené jako "Replikace úložiště") jsou replikovány prostřednictvím snímku z produkčního místa. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace naleznete v [tématu Postup převzetí služeb při selhání zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Na lokalitě zotavení po Havárii: Data, zálohprotokolu, protokola, protokola a sdílené svazky pro QA (označené jako "Instalace instance QA") jsou konfigurovány pro instalaci instance QA.
- Spouštěcí svazek pro *třídu SKU typu I* je replikován do uzlu zotavení po havárii.

## <a name="hsr-with-stonith-for-high-availability"></a>HSR s STONITH pro vysokou dostupnost
 
Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA. Tato konfigurace je podporována pouze pro jednotlivé instance HANA v uzlu. To znamená, že scénáře MCOS *nejsou* podporovány.

> [!NOTE]
> Od prosince 2019 je tato architektura podporována pouze pro operační systém SUSE.


### <a name="architecture-diagram"></a>Diagram architektury  

![HSR s STONITH pro vysokou dostupnost](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Používá se pro STONITH |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Používá se pro STONITH |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**Na sekundárním uzlu**|
|/hana/shared/SID | Hana instalace pro sekundární SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro sekundární SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro sekundární sid | 
|/hana/logbackups/SID | Znovu protokoly pro sekundární SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databáze v paměti jsou podporovány v prostředí s více sid, naleznete v [tématu Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: SBD je nakonfigurován pro nastavení STONITH. Použití stonithu je však volitelné.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>Vysoká dostupnost s HSR a DR s replikací úložiště
 
Tato topologie podporuje dva uzly pro konfiguraci replikace systému HANA. Jsou podporovány normální i víceúčelové drs. Tyto konfigurace jsou podporovány pouze pro jednotlivé instance HANA v uzlu. To znamená, že scénáře MCOS *nejsou* podporovány s těmito konfiguracemi.

V diagramu je víceúčelový scénář zobrazen v lokalitě zotavení po Havárii, kde se jednotka HLI používá pro instanci QA, zatímco produkční operace jsou spuštěny z primární lokality. Během převzetí služeb při selhání (nebo test převzetí služeb při selhání) se instance QA v lokalitě zotavení po Havárii vypne. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Vysoká dostupnost s HSR a DR s replikací úložiště](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Používá se pro STONITH |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Používá se pro STONITH |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu v primární lokalitě**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**Na sekundárním uzlu v primární lokalitě**|
|/hana/shared/SID | Hana instalace pro sekundární SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro sekundární SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro sekundární sid | 
|/hana/logbackups/SID | Znovu protokoly pro sekundární SID |
|**Na stránkách zotavení po Havárii**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/shared/QA-SID | Hana instalace pro QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro qa sid |
|/hana/logbackups/QA-SID | Znovu protokoly pro QA SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databáze v paměti jsou podporovány v prostředí s více sid, naleznete v [tématu Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- STONITH: SBD je nakonfigurován pro nastavení STONITH. Použití stonithu je však volitelné.
- V lokalitě zotavení po Havárii: Pro replikaci primárního a sekundárního uzlu *jsou vyžadovány dvě sady svazků úložiště.*
- V lokalitě zotavení po Havárii: Svazky a přípojné body jsou konfigurovány (označeny jako "Povinné pro instalaci HANA") pro instalaci instance HANA v jednotce DR HLI. 
- V lokalitě zotavení po Havárii: Data, zálohprotokolu a sdílené svazky (označené jako "Replikace úložiště") jsou replikovány prostřednictvím snímku z produkčního místa. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace naleznete v [tématu Postup převzetí služeb při selhání zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Na lokalitě zotavení po Havárii: Data, zálohprotokolu, protokola, protokola a sdílené svazky pro QA (označené jako "Instalace instance QA") jsou konfigurovány pro instalaci instance QA.
- Spouštěcí svazek pro *třídu SKU typu I* je replikován do uzlu zotavení po havárii.


## <a name="host-auto-failover-11"></a>Automatické převzetí služeb při selhání hostitele (1+1)
 
Tato topologie podporuje dva uzly v konfiguraci automatického převzetí služeb při selhání hostitele. Existuje jeden uzel s rolí hlavního serveru a pracovního procesu a druhý jako pohotovostní režim. *SAP podporuje tento scénář pouze pro S/4 HANA.* Další informace naleznete [v poznámce OSS 2408419 - SAP S/4HANA - Podpora více uzlů](https://launchpad.support.sap.com/#/notes/2408419).



### <a name="architecture-diagram"></a>Diagram architektury  

![Automatické převzetí služeb při selhání hostitele (1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Komunikace mezi uzlužněmi |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Komunikace mezi uzlužněmi |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**Na hlavním serveru a pohotovostních uzlech**|
|/hana/shared | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |



### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- V pohotovostním režimu: Svazky a přípojné body jsou konfigurovány (označeny jako "Povinné pro instalaci HANA") pro instalaci instance HANA v pohotovostní jednotce.
 

## <a name="scale-out-with-standby"></a>Horizontální navýšení kapacity s pohotovostním režimem
 
Tato topologie podporuje více uzlů v konfiguraci horizontálnínavýšení kapacity. Existuje jeden uzel s hlavní rolí, jeden nebo více uzlů s rolí pracovního procesu a jeden nebo více uzlů jako pohotovostního režimu. Však může být pouze jeden hlavní uzel v libovolném bodě v čase.


### <a name="architecture-diagram"></a>Diagram architektury  

![Horizontální navýšení kapacity s pohotovostním režimem](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Komunikace mezi uzlužněmi |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Komunikace mezi uzlužněmi |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**Na hlavním, pracovním a pohotovostním uzlu**|
|/hana/shared | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |


## <a name="scale-out-without-standby"></a>Horizontální navýšení kapacity bez pohotovostního režimu
 
Tato topologie podporuje více uzlů v konfiguraci horizontálnínavýšení kapacity. Existuje jeden uzel s hlavní rolí a jeden nebo více uzlů s rolí pracovního procesu. Však může být pouze jeden hlavní uzel v libovolném bodě v čase.


### <a name="architecture-diagram"></a>Diagram architektury  

![Horizontální navýšení kapacity bez pohotovostního režimu](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Komunikace mezi uzlužněmi |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Komunikace mezi uzlužněmi |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**Na uzlech hlavního serveru a pracovního procesu**|
|/hana/shared | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.

## <a name="scale-out-with-dr-using-storage-replication"></a>Horizontální navýšení kapacity pomocí zotavení po havárii pomocí replikace úložiště
 
Tato topologie podporuje více uzlů v horizontálním navýšení kapacity s dr. Jsou podporovány normální i víceúčelové drs. V diagramu je zobrazenpouze jeden účel DR. Tuto topologii můžete požádat s pohotovostním uzlem nebo bez něj.


### <a name="architecture-diagram"></a>Diagram architektury  

![Horizontální navýšení kapacity pomocí zotavení po havárii pomocí replikace úložiště](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-hli |
| B | TYP I | eth2.tenant | eno3.tenant | Komunikace mezi uzlužněmi |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-hli |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Komunikace mezi uzlužněmi |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**V uzlu zotavení po havárii**|
|/hana/shared | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
-  V lokalitě zotavení po Havárii: Svazky a přípojné body jsou konfigurovány (označeny jako "Povinné pro instalaci HANA") pro instalaci instance HANA v jednotce DR HLI. 
- V lokalitě zotavení po Havárii: Data, zálohprotokolu a sdílené svazky (označené jako "Replikace úložiště") jsou replikovány prostřednictvím snímku z produkčního místa. Tyto svazky jsou připojeny pouze při převzetí služeb při selhání. Další informace naleznete v [tématu Postup převzetí služeb při selhání zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery). 
- Spouštěcí svazek pro *třídu SKU typu I* je replikován do uzlu zotavení po havárii.


## <a name="single-node-with-dr-using-hsr"></a>Jeden uzel s DR pomocí HSR
 
Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním SID, s HANA systémová replikace do lokality ZOTAVENÍ pro primární SID. V diagramu je v primární lokalitě zobrazen pouze systém jednoho SID, ale jsou podporovány také systémy mod (MCOS).

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel s DR pomocí HSR](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-HLI/HSR |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány jak na jednotkách HLI (primární, tak na zotavení po havárii):

| Přípojkový bod | Případ použití | 
| --- | --- |
|/hana/shared/SID | Hana instalace pro SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro SID | 
|/hana/logbackups/SID | Znovu protokoly pro SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databáze v paměti jsou podporovány v prostředí s více sid, naleznete v [tématu Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- Primární uzel se synchronizuje s uzlou dr. pomocí replikace systému HANA. 
- [Globální dosah](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) se používá k propojení okruhů ExpressRoute dohromady a vytvoření privátní sítě mezi místními sítěmi.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>Jeden uzel HSR do DR (optimalizované pro náklady) 
 
 Tato topologie podporuje jeden uzel v konfiguraci škálování s jedním SID, s HANA systémová replikace do lokality ZOTAVENÍ pro primární SID. V diagramu je v primární lokalitě zobrazen pouze systém jednoho SID, ale jsou podporovány také systémy mod (MCOS). V lokalitě ZOTAVENÍ po Havárii se jednotka HLI používá pro instanci QA, zatímco produkční operace jsou spuštěny z primární lokality. Během převzetí služeb při selhání (nebo test převzetí služeb při selhání) se instance QA v lokalitě zotavení po Havárii vypne.

### <a name="architecture-diagram"></a>Diagram architektury  

![Jeden uzel HSR do DR (optimalizované pro náklady)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-HLI/HSR |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**Na stránkách zotavení po Havárii**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|/hana/shared/QA-SID | Hana instalace pro QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro qa sid |
|/hana/logbackups/QA-SID | Znovu protokoly pro QA SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- Pro MCOS: Distribuce velikosti svazku je založena na velikosti databáze v paměti. Informace o tom, jaké velikosti databáze v paměti jsou podporovány v prostředí s více sid, naleznete v [tématu Přehled a architektura](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).
- V lokalitě zotavení po Havárii: Svazky a přípojné body jsou konfigurovány (označeny jako "INSTANCE PROD v lokalitě ZOTAVENÍ) pro instalaci produkční instance HANA v jednotce DR HLI. 
- Na lokalitě zotavení po Havárii: Data, zálohprotokolu, protokola, protokola a sdílené svazky pro QA (označené jako "Instalace instance QA") jsou konfigurovány pro instalaci instance QA.
- Primární uzel se synchronizuje s uzlou dr. pomocí replikace systému HANA. 
- [Globální dosah](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) se používá k propojení okruhů ExpressRoute dohromady a vytvoření privátní sítě mezi místními sítěmi.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>Vysoká dostupnost a zotavení po havárii pomocí hsr 
 
 Tato topologie podporuje dva uzly pro konfiguraci hana systémové replikace pro vysokou dostupnost místních oblastí. Pro zotavení po havárii se třetí uzel v oblasti zotavení po Havárii synchronizuje s primární lokalitou pomocí funkce HSR (asynchronní režim). 

### <a name="architecture-diagram"></a>Diagram architektury  

![Vysoká dostupnost a zotavení po havárii pomocí hsr](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-HLI/HSR |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**Na stránkách zotavení po Havárii**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- V lokalitě zotavení po Havárii: Svazky a přípojné body jsou konfigurovány (označeny jako "INSTANCE PROD DR" pro instalaci instance HANA v jednotce DR HLI. 
- Primární uzel lokality se synchronizuje s uzlou dr. pomocí replikace systému HANA. 
- [Globální dosah](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) se používá k propojení okruhů ExpressRoute dohromady a vytvoření privátní sítě mezi místními sítěmi.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>Vysoká dostupnost a zotavení po havárii pomocí hsr (optimalizované pro náklady)
 
 Tato topologie podporuje dva uzly pro konfiguraci hana systémové replikace pro vysokou dostupnost místních oblastí. Pro zotavení po havárii třetí uzel v oblasti zotavení po Havárii synchronizuje s primární lokalitou pomocí HSR (asynchronní režim), zatímco jiné instance (například QA) je již běží z uzlu ZOTAVENÍ po Havárii. 

### <a name="architecture-diagram"></a>Diagram architektury  

![Vysoká dostupnost a zotavení po havárii pomocí hsr (optimalizované pro náklady)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Nakonfigurované, ale nepoužívají |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-HLI/HSR |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Nakonfigurované, ale nepoužívají |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**V primární lokalitě**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**Na stránkách zotavení po Havárii**|
|/hana/shared/SID | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|/hana/shared/QA-SID | Hana instalace pro QA SID | 
|/hana/data/QA-SID/mnt00001 | Instalace datových souborů pro QA SID | 
|/hana/log/QA-SID/mnt00001 | Instalace souborů protokolu pro qa sid |
|/hana/logbackups/QA-SID | Znovu protokoly pro QA SID |

### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- V lokalitě zotavení po Havárii: Svazky a přípojné body jsou konfigurovány (označeny jako "INSTANCE PROD DR" pro instalaci instance HANA v jednotce DR HLI. 
- Na lokalitě zotavení po Havárii: Data, zálohprotokolu, protokola, protokola a sdílené svazky pro QA (označené jako "Instalace instance QA") jsou konfigurovány pro instalaci instance QA.
- Primární uzel lokality se synchronizuje s uzlou dr. pomocí replikace systému HANA. 
- [Globální dosah](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) se používá k propojení okruhů ExpressRoute dohromady a vytvoření privátní sítě mezi místními sítěmi.

## <a name="scale-out-with-dr-using-hsr"></a>Horizontální navýšení kapacity pomocí zotavení po havárii pomocí hsr
 
Tato topologie podporuje více uzlů v horizontálním navýšení kapacity s dr. Tuto topologii můžete požádat s pohotovostním uzlem nebo bez něj. Primární uzel lokality se synchronizuje s uzlou lokalitou zotavení po Havárii pomocí hana systémové replikace (asynchronní režim).


### <a name="architecture-diagram"></a>Diagram architektury  

[![Horizontální navýšení kapacity pomocí zotavení po havárii pomocí hsr](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>Síť Ethernet
Následující síťová rozhraní jsou předkonfigurována:

| Logické rozhraní síťové rozhraní | Typ skladové položky | Název s os SUSE | Název s os RHEL | Případ použití|
| --- | --- | --- | --- | --- |
| A | TYP I | eth0.tenant | eno1.tenant | Klient-HLI/HSR |
| B | TYP I | eth2.tenant | eno3.tenant | Komunikace mezi uzlužněmi |
| C | TYP I | eth1.tenant | eno2.tenant | Uzel-úložiště |
| D | TYP I | eth4.tenant | eno4.tenant | Nakonfigurované, ale nepoužívají |
| A | TYP II | vlan\<tenantNo> | team0.tenant | Klient-HLI/HSR |
| B | TYP II | vlan\<nájemceNe+2> | team0.tenant+2 | Komunikace mezi uzlužněmi |
| C | TYP II | vlan\<nájemceNo+1> | team0.tenant+1 | Uzel-úložiště |
| D | TYP II | vlan\<tenantNo+3> | team0.tenant+3 | Nakonfigurované, ale nepoužívají |

### <a name="storage"></a>Úložiště
Následující přípojné body jsou předkonfigurovány:

| Přípojkový bod | Případ použití | 
| --- | --- |
|**Na primárním uzlu**|
|/hana/shared | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |
|**V uzlu zotavení po havárii**|
|/hana/shared | HANA instalace pro výrobu SID | 
|/hana/data/SID/mnt00001 | Instalace datových souborů pro produkční SID | 
|/hana/log/SID/mnt00001 | Instalace souborů protokolu pro produkční sid | 
|/hana/logbackups/SID | Znovu protokoly pro produkční SID |


### <a name="key-considerations"></a>Klíčové aspekty
- /usr/sap/SID je symbolický odkaz na /hana/shared/SID.
- V lokalitě ZOTAVENÍ po Havárii: Svazky a přípojné body jsou konfigurovány pro instalaci instance HANA v jednotce DR HLI. 
- Primární uzel lokality se synchronizuje s uzlou dr. pomocí replikace systému HANA. 
- [Globální dosah](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) se používá k propojení okruhů ExpressRoute dohromady a vytvoření privátní sítě mezi místními sítěmi.


## <a name="next-steps"></a>Další kroky
- [Infrastruktura a připojení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity) pro velké instance HANA
- [Vysoká dostupnost a zotavení po havárii](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery) pro velké instance HANA

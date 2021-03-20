---
title: Komponenty VMware privátního cloudu
titleSuffix: Azure VMware Solution by CloudSimple
description: Přečtěte si, jak služba CloudSimple umožňuje nasadit VMware nativně v umístěních Azure. Privátní cloudy jsou integrované se zbytkem cloudu Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88142498"
---
# <a name="private-cloud-vmware-components"></a>Komponenty VMware privátního cloudu

Privátní cloud je izolovaný systém VMware Stack (ESXi hosts, vCenter, síti vSAN a NSX), který je spravovaný serverem vCenter v doméně pro správu.  Služba CloudSimple umožňuje nasadit VMware nativně na holé infrastruktuře Azure v umístěních Azure.  Privátní cloudy jsou integrované se zbytkem cloudu Azure.  Privátní cloud se nasazuje s následujícími komponentami zásobníku VMware:

* **VMware ESXi –** Hypervisor na vyhrazených uzlech Azure
* **VMware vCenter –** Zařízení pro centralizovanou správu prostředí vSphere privátního cloudu
* **VMware síti vSAN –** Vysoce sblížené řešení infrastruktury technologie Hyper-v
* **Datové centrum VMware NSX –** Virtualizace sítě a bezpečnostní software  

## <a name="vmware-component-versions"></a>Verze komponent VMware

Zásobník VMware privátního cloudu je nasazený s následující verzí softwaru.

| Součást | Verze | Licencovaná verze |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Enterprise Plus |
| vCenter | 6.7 U2 | vCenter úrovně Standard |
| Síti vSAN | 6.7 | Enterprise |
| Datové centrum NSX | 2.4.1 | Pokročilý |

## <a name="esxi"></a>ESXi

VMware ESXi se na zřízených uzlech CloudSimple nainstaluje při vytváření privátního cloudu.  ESXi poskytuje hypervisor pro nasazení virtuálních počítačů s úlohou.  Uzly poskytují infrastrukturu (výpočetní výkon a úložiště) s podporou technologie Hyper-v privátním cloudu.  Uzly jsou součástí clusteru vSphere v privátním cloudu.  Každý uzel má čtyři rozhraní fyzické sítě připojená k Underlay síti.  Dvě fyzická síťová rozhraní se používají k vytvoření **vSphere distribuovaného přepínače (VDS)** na vCenter a dva se používají k vytvoření **virtuálního distribuovaného přepínače spravovaného NSX (N-VDS)**.  Síťová rozhraní jsou nakonfigurovaná v režimu aktivní-aktivní pro zajištění vysoké dostupnosti.

Další informace o VMware ESXi

## <a name="vcenter-server-appliance"></a>zařízení vCenter Server

zařízení vCenter Server (VCSA) poskytuje funkce ověřování, správy a orchestrace pro řešení VMware podle CloudSimple. VCSA se službou Embedded Platform Controller (PSC) se nasadí při vytváření privátního cloudu.  VCSA je nasazený v clusteru vSphere, který se vytvoří při nasazení privátního cloudu.  Každý privátní cloud má vlastní VCSA.  Rozšíření privátního cloudu přidá uzly do VCSA v privátním cloudu.

### <a name="vcenter-single-sign-on"></a>jednotné přihlašování vCenter

Služba Embedded Platform Service Controller na VCSA je přidružená k řadiči **vCenter s jednou Sign-On doménou**.  Název domény je **cloudsimple. Local**.  **CloudOwner@cloudsimple.com** Pro přístup k serveru vCenter se vytvoří výchozí uživatel.  Pro vCenter můžete přidat své místní nebo [zdroje identit](set-vcenter-identity.md)služby Azure Active Directory.

## <a name="vsan-storage"></a>úložiště síti vSAN

Privátní cloudy se vytvářejí s plně nakonfigurovaným úložištěm síti vSAN, které je místní pro cluster.  K vytvoření clusteru vSphere s úložištěm dat síti vSAN se vyžadují minimální tři uzly stejné SKU.  Ve výchozím nastavení jsou v úložišti dat síti vSAN povoleny pouze duplicity a komprese.  V každém uzlu clusteru vSphere se vytvoří dvě skupiny disků. Každá skupina disků obsahuje jeden disk mezipaměti a tři disky s kapacitou.

V clusteru vSphere se vytvoří výchozí zásada úložiště síti vSAN a použije se pro úložiště dat síti vSAN.  Tato zásada určuje, jak se zřídí objekty úložiště virtuálního počítače a které se přidělují v úložišti dat, aby se zaručila požadovaná úroveň služby.  Zásady úložiště definují **selhání pro tolerovat (FTT)** a **metodu odolnosti proti chybám**.  Můžete vytvořit nové zásady úložiště a použít je na virtuální počítače. Aby bylo možné udržovat smlouvu SLA, musí být v úložišti síti vSAN DataStore udržována 25% volná kapacita.  

### <a name="default-vsan-storage-policy"></a>Výchozí zásady úložiště síti vSAN

Následující tabulka ukazuje výchozí parametry zásad úložiště síti vSAN.

| Počet uzlů v clusteru vSphere | FTT | Metoda odolnosti proti chybám |
|------------------------------------|-----|--------------------------|
| 3 a 4 uzly | 1 | RAID 1 (zrcadlení) – vytvoří 2 kopie. |
| 5 až 16 uzlů | 2 | RAID 1 (zrcadlení) – vytvoří 3 kopie. |

## <a name="nsx-data-center"></a>Datové centrum NSX

Datové centrum NSX poskytuje virtualizaci sítě, mikrosegmentaci a možnosti zabezpečení sítě ve vašem privátním cloudu.  Prostřednictvím NSX můžete nakonfigurovat všechny služby podporované datacentrem NSX v privátním cloudu.  Při vytváření privátního cloudu jsou nainstalované a nakonfigurované následující komponenty NSX.

* NSXT Manager
* Dopravní zóny
* Profil pro odchozí připojení hostitele a Edge
* Logický přepínač pro přenos hran, Ext1 a ext2
* Fond IP adres pro uzel přenosu ESXi
* Fond IP adres pro uzel hraničního přenosu
* Hraniční uzly
* Pravidlo ochrany DRS proti spřažení pro virtuální počítače s kontrolérem a hraničními zařízeními
* Směrovač vrstvy 0
* Povolit protokol BGP ve směrovači Tier0

## <a name="vsphere-cluster"></a>cluster vSphere

Hostitelé ESXi jsou nakonfigurováni jako cluster, aby zajistili vysokou dostupnost privátního cloudu.  Při vytváření privátního cloudu jsou součásti pro správu nástroje vSphere nasazeny do prvního clusteru.  Pro součásti pro správu je vytvořen fond zdrojů a všechny virtuální počítače pro správu jsou nasazeny v tomto fondu zdrojů. První cluster se nedá odstranit, aby se zmenšil jeho privátní cloud.  cluster vSphere poskytuje vysokou dostupnost pro virtuální počítače s využitím **VSPHERE ha**.  Selhání k tolerování vycházejí z počtu dostupných uzlů v clusteru.  Můžete použít vzorec, ```Number of nodes = 2N+1``` ve kterém ```N``` je počet nedovolených odtolerovatcích.

### <a name="vsphere-cluster-limits"></a>omezení clusteru vSphere

| Prostředek | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu (první cluster vSphere) | 3 |
| Maximální počet uzlů v clusteru vSphere v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Maximální počet clusterů vSphere v privátním cloudu | 21 |
| Minimální počet uzlů v novém clusteru vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Údržba infrastruktury VMware

V některých případech je potřeba provést změny v konfiguraci infrastruktury VMware. V současné době se tyto intervaly můžou vyskytnout každých 1-2 měsíců, ale frekvence se očekává, že se v průběhu času odmítne. Tento typ údržby se obvykle dá provést bez přerušení běžné spotřeby služeb CloudSimple Services. Během intervalu údržby VMware budou následující služby fungovat bez dopadu:

* Rovina správy VMware a aplikace
* přístup vCenter
* Všechny sítě a úložiště
* Veškerý provoz Azure

## <a name="updates-and-upgrades"></a>Aktualizace a upgrady

CloudSimple zodpovídá za správu životního cyklu softwaru VMware (ESXi, vCenter, PSC a NSX) v privátním cloudu.

Mezi aktualizace softwaru patří:

* **Opravy**. Opravy zabezpečení nebo opravy chyb vydané VMware.
* **Aktualizace**. Dílčí verze změny komponenty zásobníku VMware.
* **Upgrady**. Hlavní změna verze komponenty zásobníku VMware.

CloudSimple testuje kritickou opravu zabezpečení, jakmile bude k dispozici z VMware. V rámci smlouvy SLA CloudSimple zavede opravu zabezpečení do prostředí privátního cloudu za týden.

CloudSimple poskytuje čtvrtletní aktualizace pro softwarové komponenty VMware. Když je k dispozici nová hlavní verze softwaru VMware, CloudSimple spolupracuje se zákazníky na koordinaci vhodného časového období údržby pro upgrade.  

## <a name="next-steps"></a>Další kroky

* [Údržba a aktualizace CloudSimple](cloudsimple-maintenance-updates.md)

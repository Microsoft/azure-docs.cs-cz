---
title: Privátní cloudové komponenty VMware
titleSuffix: Azure VMware Solution by CloudSimple
description: Popisuje, jak jsou komponenty VMware nainstalovány v privátním cloudu.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9c9b80cd4d8a7a7ac5597d10bbb87095564bd461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279504"
---
# <a name="private-cloud-vmware-components"></a>Komponenty Private Cloud VMware

Privátní cloud je izolované prostředí vmware (hostitelé ESXi, vCenter, vSAN a NSX) spravované serverem vCenter v doméně pro správu.  Služba CloudSimple umožňuje nasadit VMware nativně na infrastrukturu Azure holé kovové v umístění Azure.  Privátní cloudy jsou integrované se zbytkem Azure Cloudu.  Privátní cloud se nasazuje s následujícími součástmi zásobníku VMware:

* **Společnost VMware ESXi -** Hypervisor na vyhrazených uzlech Azure
* **VMware vCenter -** Zařízení pro centralizovanou správu prostředí private cloud vSphere
* **VMware vSAN -** Řešení hyperkonvergované infrastruktury
* **Datové centrum VMware NSX -** Software pro virtualizaci a zabezpečení sítě  

## <a name="vmware-component-versions"></a>Verze komponent VMware

Privátní cloudový zásobník VMware je nasazen s následující verzí softwaru.

| Komponenta | Version | Licencovaná verze |
|-----------|---------|------------------|
| Esxi | 6,7U2 | Podnik Plus |
| vCenter | 6,7U2 | Standard vCenter |
| vSAN | 6.7 | Enterprise |
| Datové centrum NSX | 2.4.1 | Upřesnit |

## <a name="esxi"></a>Esxi

VMware ESXi se instaluje na zřízených uzlech CloudSimple při vytváření privátního cloudu.  ESXi poskytuje hypervisor pro nasazení virtuálních počítačů (VM).  Uzly poskytují hyperkonvergované infrastruktury (výpočetní prostředky a úložiště) ve vašem privátním cloudu.  Uzly jsou součástí clusteru vSphere v privátním cloudu.  Každý uzel má čtyři rozhraní fyzické sítě připojené k podložení sítě.  Dvě fyzická síťová rozhraní se používají k vytvoření **vSphere Distributed Switch (VDS)** na vCenter a dvě se používají k vytvoření **virtuálního distribuovaného přepínače spravovaného NSX (N-VDS).**  Síťová rozhraní jsou konfigurována v aktivním aktivním režimu pro vysokou dostupnost.

Další informace o společnosti VMware ESXi

## <a name="vcenter-server-appliance"></a>Zařízení serveru vCenter

Zařízení vCenter server (VCSA) poskytuje funkce ověřování, správy a orchestrace pro řešení VMware od CloudSimple. VCSA s integrovaným řadičem služby platformy (PSC) se nasazuje při vytváření privátního cloudu.  VCSA se nasazuje v clusteru vSphere, který se vytvoří při nasazení privátního cloudu.  Každý privátní cloud má svůj vlastní VCSA.  Rozšíření privátního cloudu přidává uzly do VCSA v privátním cloudu.

### <a name="vcenter-single-sign-on"></a>vCenter jednotné přihlašování

Integrovaný řadič služby platformy ve službě VCSA je přidružen k **doméně jednotného přihlašování vCenter**.  Název domény je **cloudsimple.local**.  Pro přístup **CloudOwner@cloudsimple.com** k programu vCenter je vytvořen výchozí uživatel.  Pro vCenter můžete přidat místní [zdroje identit](set-vcenter-identity.md)y adresáře Azure/Azure.

## <a name="vsan-storage"></a>úložiště vSAN

Privátní cloudy jsou vytvářeny s plně nakonfigurovaným all-flash vSAN úložištěm, místním clusteru.  K vytvoření clusteru vSphere s úložištěm dat vSAN jsou vyžadovány minimálně tři uzly stejné skladové položky.  De-duplikace a komprese jsou povoleny na vsan úložiště dat ve výchozím nastavení.  V každém uzlu clusteru vSphere jsou vytvořeny dvě skupiny disků. Každá skupina disků obsahuje jeden disk mezipaměti a tři disky s kapacitou.

Ve clusteru vSphere se vytvoří výchozí zásada úložiště vSAN a použije se na úložiště dat vSAN.  Tato zásada určuje, jak jsou zřízeny a přiděleny objekty úložiště virtuálních zařízení v rámci úložiště dat zaručit požadovanou úroveň služeb.  Zásady úložiště definuje **selhání tolerovat (FTT)** a **selhání tolerance metody**.  Můžete vytvořit nové zásady úložiště a použít je na virtuální chod. Chcete-li zachovat sla, 25% volná kapacita musí být zachována na vsan úložiště dat.  

### <a name="default-vsan-storage-policy"></a>Výchozí zásady úložiště vSAN

Níže uvedená tabulka ukazuje výchozí parametry zásad úložiště vSAN.

| Počet uzlů v clusteru vSphere | FTT | Metoda tolerance selhání |
|------------------------------------|-----|--------------------------|
| 3 a 4 uzly | 1 | RAID 1 (zrcadlení) - vytvoří 2 kopie |
| 5 až 16 uzlů | 2 | RAID 1 (zrcadlení) - vytvoří 3 kopie |

## <a name="nsx-data-center"></a>Datové centrum NSX

NSX Data Center poskytuje v privátním cloudu možnosti virtualizace sítě, mikrosegmentace a zabezpečení sítě.  Všechny služby podporované datovým centrem NSX můžete konfigurovat ve vašem soukromém cloudu prostřednictvím nsx.  Při vytváření privátního cloudu jsou nainstalovány a nakonfigurovány následující součásti NSX.

* Správce NSXT
* Dopravní zóny
* Profil hostitele a propojení okrajů
* Logický přepínač pro přenos okrajů, Ext1 a Ext2
* Fond IP pro přenosový uzel ESXi
* Fond IP pro uzel přenosu okrajů
* Uzly okrajů
* Pravidlo ochrany proti spřažení DRS pro řadič a virtuální virtuální zařízení Edge
* Směrovač úrovně 0
* Povolení protokolu BGP na směrovači Tier0

## <a name="vsphere-cluster"></a>cluster vSphere

Hostitelé ESXi jsou konfigurováni jako cluster, aby zajistili vysokou dostupnost privátního cloudu.  Když vytvoříte privátní cloud, součásti správy vSphere se nasazují do prvního clusteru.  Fond prostředků se vytvoří pro součásti správy a všechny virtuální ms správy jsou nasazeny v tomto fondu prostředků. První cluster nelze odstranit zmenšit privátní cloud.  Cluster vSphere poskytuje vysokou dostupnost pro virtuální společnosti pomocí **vSphere HA**.  Selhání tolerovat jsou založeny na počtu dostupných uzlů v clusteru.  Můžete použít ```Number of nodes = 2N+1``` vzorec, ```N``` kde je počet selhání tolerovat.

### <a name="vsphere-cluster-limits"></a>omezení clusteru vSphere

| Prostředek | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu (první cluster vSphere) | 3 |
| Maximální počet uzlů v clusteru vSphere v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Maximální počet clusterů vSphere v privátním cloudu | 21 |
| Minimální počet uzlů v novém clusteru vSphere | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Údržba infrastruktury společnosti VMware

V některých proto je nutné provést změny konfigurace infrastruktury VMware. V současné době se tyto intervaly mohou vyskytovat každé 1-2 měsíce, ale očekává se, že frekvence v průběhu času klesá. Tento typ údržby lze obvykle provést bez přerušení normální spotřeby cloudsimple služeb. Během intervalu údržby společnosti VMware fungují bez jakéhokoli vtípku bez dopadu následující služby:

* Letadlo a aplikace pro správu společnosti VMware
* vCenter přístup
* Všechny sítě a úložiště
* Veškerý provoz Azure

## <a name="updates-and-upgrades"></a>Aktualizace a upgrady

CloudSimple je zodpovědný za správu životního cyklu softwaru VMware (ESXi, vCenter, PSC a NSX) v privátním cloudu.

Aktualizace softwaru zahrnují:

* **Záplaty**. Opravy zabezpečení nebo opravy chyb vydané společností VMware.
* **Aktualizace**. Dílčí verze změny součásti zásobníku VMware.
* **Inovace**. Hlavní změna verze součásti zásobníku VMware.

CloudSimple testuje kritickou opravu zabezpečení, jakmile bude k dispozici od společnosti VMware. Podle sla, CloudSimple zavádí opravy zabezpečení do privátního cloudu prostředí během týdne.

CloudSimple poskytuje čtvrtletní aktualizace údržby softwarových komponent VMware. Když je k dispozici nová hlavní verze softwaru VMware, CloudSimple spolupracuje se zákazníky na koordinaci vhodného okna údržby pro upgrade.  

## <a name="next-steps"></a>Další kroky

* [CloudJednoduchá údržba a aktualizace](cloudsimple-maintenance-updates.md)

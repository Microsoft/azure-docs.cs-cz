---
title: Řešení Azure VMware podle CloudSimple – VMware součásti privátního cloudu
description: Popisuje, jak v privátním cloudu nejsou nainstalované součásti VMware
author: sharaths-cs
ms.author: dikamath
ms.date: 04/30/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e6548a5a04e32b374a8a9c29d2ca5f89fd65c78
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160182"
---
# <a name="private-cloud-vmware-components"></a>Součásti VMware privátního cloudu

Privátní cloud je izolované zásobníku VMware (hostitele ESXi, vCenter, sítě vSAN a NSX) prostředí spravované pomocí vCenter serveru v doméně pro správu.  Služba CloudSimple umožňuje nasadit VMware nativně na holé počítače infrastrukturu Azure v umístění Azure.  Privátní cloudy jsou integrovány se zbytkem cloudu Azure.  Privátní cloud se nasazuje s následujícími součástmi zásobníku VMware:

* **VMware ESXi -** hypervisoru v Azure vyhrazené uzly
* **VMware vCenter -** zařízení pro centralizovanou správu vSphere prostředí privátního cloudu
* **Síť vSAN VMware –** řešení infrastruktury konvergované na Hyper v
* **Datové centrum VMware NSX -** síťové virtualizace a Software pro zabezpečení  

## <a name="vmware-component-versions"></a>Verze součástí VMware

Zásobníku VMware privátní cloud se nasazuje s následující verze softwaru.

| Komponenta | Version | Licencovanou verzi |
|-----------|---------|------------------|
| ESXi | 6.7U1 | Enterprise Plus |
| vCenter | 6.7U1 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.3 | Upřesňující |

## <a name="esxi"></a>ESXi

VMware ESXi je nainstalován na uzlech zakoupené CloudSimple při vytvoření privátního cloudu.  ESXi poskytuje hypervisor nasazení úloh virtuálních počítačů (VM).  Uzly poskytují infrastrukturu konvergované na hyper v (výpočetní výkon a úložiště) v privátním cloudu.  Uzly jsou součástí clusteru vSphere do privátního cloudu.  Každý uzel má čtyři fyzické sítě rozhraní připojené k underlay sítě.  Dvě fyzická síťová rozhraní, které se používají k vytváření **vSphere distribuované přepnout (VDS)** na serveru vCenter a dvě slouží k vytvoření **NSX spravované virtuální distribuovaný přepínač (N-VDS)**.  Síťová rozhraní jsou nakonfigurované v režimu aktivní aktivní pro zajištění vysoké dostupnosti.

Další informace o VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter server zařízení

vCenter server zařízení (VCSA) poskytuje funkce ověřování, Správa a Orchestrace pro řešení VMware podle CloudSimple. VCSA s vložený platformy služby kontroler (PSC) je nasazen při vytváření privátního cloudu.  VCSA nasazený na clusteru vSphere, který je vytvořen při nasazení privátního cloudu.  Každý privátní cloud má vlastní VCSA.  Rozšíření privátního cloudu přidá uzly VCSA do privátního cloudu.

### <a name="vcenter-single-sign-on"></a>vCenter jednotného přihlašování

Vložené řadič služby platformy na VCSA je přidružen **vCenter Single Sign-On domény**.  Název domény je **cloudsimple.local**.  Výchozí uživatel **CloudOwner@cloudsimple.com** se pro přístup k serveru vCenter.  Můžete přidat typu místní nebo Azure active directory [identitu zdroje pro vCenter](https://docs.azure.cloudsimple.com/set-vcenter-identity/).

## <a name="vsan-storage"></a>síť vSAN úložiště

Privátní cloudy jsou vytvářeny s úložištěm sítě vSAN plně nakonfigurovaného typu flash, místní cluster.  Minimální tři uzly se stejným SKU jsou nutné k vytvoření clusteru vSphere pomocí úložiště dat sítě vSAN.  Deduplikace a komprese jsou ve výchozím nastavení povolená v úložišti dat sítě vSAN.  Na každém uzlu clusteru vSphere se vytvoří dvě skupiny disku. Každá skupina disk obsahuje jeden disk mezipaměti a tři disky kapacity.

Výchozí zásady sítě vSAN úložiště je vytvořen na clusteru vSphere a použít na úložiště dat sítě vSAN.  Tato zásada určuje, jak jsou objekty úložiště virtuálních počítačů zřízené a přidělená v rámci úložiště dat zajistit vyžadovanou úroveň služeb.  Definuje zásadu úložiště **Tolerované chyby (FTT)** a **selhání tolerance metoda**.  Můžete vytvářet nové zásady úložiště a použít je k virtuálním počítačům. Pokud chcete zachovat smlouvu SLA, musí být udržovány 25 % rezervní kapacity v úložišti dat sítě vSAN.  

### <a name="default-vsan-storage-policy"></a>Výchozí zásady úložiště sítě vSAN

Následující tabulka ukazuje výchozí parametry zásad sítě vSAN úložiště.

| Počet uzlů v clusteru vSphere | FTT | Tolerance failure – metoda |
|------------------------------------|-----|--------------------------|
| 3 a 4 uzly | 1 | RAID 1 (zrcadlení) - vytvoří 2 kopie |
| 5 až 16 uzlů | 2 | RAID 1 (zrcadlení) - vytvoří 3 kopiemi |

## <a name="nsx-data-center"></a>NSX Data Center

NSX datové centrum poskytuje možnosti zabezpečení sítě, micro segmentace a virtualizace sítě v privátním cloudu.  Můžete nakonfigurovat všechny služeb podporovaných službou NSX datového centra v privátním cloudu prostřednictvím NSX.  Při vytváření privátního cloudu jsou následující komponenty NSX nainstalovaný a nakonfigurovaný.

* NSXT Manager
* Přenos zóny
* Hostitel a Edge pro odesílání
* Logický přepínač pro přenos Edge, Ext1 a Ext2
* Fond IP adres pro uzel ESXi přenosu
* Fond IP adres pro hraniční uzel přenosu
* Hraniční uzly
* Pravidlo DRS proti spřažení pro virtuální počítače Edge a kontroler
* Směrovač vrstvy 0
* Povolit protokol BGP na směrovači Tier0

## <a name="vsphere-cluster"></a>vSphere clusteru

Hostitelé ESXi jsou nakonfigurované jako cluster k zajištění vysoké dostupnosti privátního cloudu.  Při vytváření privátního cloudu komponentami pro správu nástroje vSphere jsou nasazeny v prvním clusteru.  Vytvoření fondu zdrojů pro součásti správy a všechny virtuální počítače pro správu jsou nasazené v tomto fondu zdrojů. První cluster nejde odstranit, zmenšení privátního cloudu.  vSphere clusteru poskytuje vysokou dostupnost pro virtuální počítače pomocí **vSphere HA**.  Tolerované chyby jsou založeny na počtu dostupných uzlů v clusteru.  Můžete použít vzorec ```Number of nodes = 2N+1``` kde ```N``` je počet tolerované chyby.

### <a name="vsphere-cluster-limits"></a>omezení clusteru vSphere

| Resource | Omezení |
|----------|-------|
| Minimální počet uzlů pro vytvoření privátního cloudu (první vSphere clusteru) | 3 |
| Maximální počet uzlů v vSphere clusteru v privátním cloudu | 16 |
| Maximální počet uzlů v privátním cloudu | 64 |
| Maximální počet vSphere clustery v privátním cloudu | 21 |
| Minimální počet uzlů na nové vSphere clusteru | 3 |

## <a name="vmware-infrastructure-maintenance"></a>Údržba infrastruktury VMware

Někdy je potřeba provést změny v konfiguraci infrastruktury VMware. V současné době tyto intervaly může dojít, každé 1-2 měsíce, ale očekává se frekvence tak, aby odmítal v čase. Tento typ údržby obvykle provést bez přerušení normálního využití CloudSimple služeb. Během intervalu údržby VMware tyto služby nadále schopné fungovat bez jakéhokoli dopadu:

* Rovina správy VMware a aplikací
* přístup k serveru vCenter
* Všechny sítě a úložiště
* Všem službám Azure

## <a name="updates-and-upgrades"></a>Aktualizace a upgrady

CloudSimple zodpovídá za správu životního cyklu softwaru VMware (ESXi, vCenter, PSČ a NSX) v privátním cloudu.

Softwarové aktualizace zahrnují:

* **Opravy**. Opravy zabezpečení nebo opravy chyb vydání ve VMware.
* **Aktualizace**. Vedlejší verze aktualizace změnit součásti zásobníku VMware.
* **Upgrady**. Změna hlavní verze součásti zásobníku VMware.

CloudSimple testuje opravy zabezpečení, jakmile bude k dispozici z VMware. Podle smlouvy SLA CloudSimple zahrnuje navýšení kapacity na úroveň opravy zabezpečení pro prostředí privátních cloudů v rámci jednoho týdne.

CloudSimple poskytuje čtvrtletní údržby aktualizace softwarových součástí VMware. Pokud je dostupná nová hlavní verze softwaru VMware, CloudSimple funguje se zákazníky ke koordinaci časového období údržby vhodné k upgradu.  

## <a name="next-steps"></a>Další postup

* [CloudSimple Údržba a aktualizace](cloudsimple-maintenance-updates.md)
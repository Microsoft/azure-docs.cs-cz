---
title: Koncepty – privátní cloudy a clustery
description: Seznamte se s klíčovými možnostmi pro datové centra a vSphere clustery definovaná na základě softwaru řešení Azure VMware.
ms.topic: conceptual
ms.date: 03/13/2021
ms.openlocfilehash: d1837ae7cf01fcb9642e0cafe4e0430e403b9899
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462507"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Koncepce privátního cloudu a clusteru řešení Azure VMware

Řešení Azure VMware poskytuje privátní cloudy založené na VMware v Azure. Privátní cloudy obsahují clustery vytvořené pomocí vyhrazených a holých hostitelů Azure. Nasazují a spravují prostřednictvím Azure Portal, CLI nebo PowerShellu.  Clustery zřízené v privátních cloudech zahrnují VMware vSphere, vCenter, síti vSAN a NSX software. Nasazení hardwaru a softwaru privátního cloudu řešení Azure VMware jsou v Azure plně integrovaná a automatizovaná.

Mezi předplatnými Azure, privátními cloudy Azure VMware, clustery síti vSAN a hostiteli jsou logické vztahy. Diagram zobrazuje jedno předplatné Azure se dvěma privátními cloudy, které reprezentují vývojové a produkční prostředí.  V každém z těchto privátních cloudů jsou dva clustery. 

Tento článek popisuje všechny tyto koncepce.

![Obrázek dvou privátních cloudů v rámci zákaznického předplatného](./media/hosts-clusters-private-clouds-final.png)


## <a name="private-clouds"></a>Privátní cloudy

Privátní cloudy obsahují clustery síti vSAN vytvořené pomocí vyhrazených a holých hostitelů Azure. Každý privátní cloud může mít několik clusterů spravovaných stejným serverem vCenter a správcem NSX-T. Privátní cloudy můžete nasadit a spravovat v portálu, rozhraní příkazového řádku nebo PowerShellu. 

Stejně jako u jiných prostředků jsou privátní cloudy nainstalované a spravované v rámci předplatného Azure. Počet privátních cloudů v rámci předplatného je škálovatelný. Zpočátku existuje omezení jednoho privátního cloudu na jedno předplatné.

## <a name="clusters"></a>Clustery
U každého vytvořeného privátního cloudu je ve výchozím nastavení k dispozici jeden cluster síti vSAN. Clustery můžete přidávat, odstraňovat a škálovat pomocí Azure Portal nebo přes rozhraní API.  Všechny clustery mají výchozí velikost 3 hostitelů a můžou škálovat až 16 hostitelů. Můžete mít až čtyři clustery na jeden privátní cloud.

Zkušební clustery jsou k dispozici pro vyhodnocení a omezená na tři hostitele. Existuje jeden zkušební cluster na jeden privátní cloud. Během zkušebního období můžete škálovat zkušební cluster jediným hostitelem.

Správce vSphere a NSX-T můžete použít ke správě většiny dalších aspektů konfigurace clusteru nebo operace. Všechna místní úložiště každého hostitele v clusteru jsou pod kontrolou síti vSAN.

## <a name="hosts"></a>Hostitelé

Clustery řešení Azure VMware jsou založené na holé infrastruktuře Hyper-holý. V následující tabulce jsou uvedeny kapacity paměti RAM, procesoru a disku hostitele.

| Typ hostitele              |             Procesor             |   RAM (GB)   |  Síti vSAN NVMe cache úrovně (TB, RAW)  |  úroveň kapacity síti vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| AVS36          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

Hostitelé, kteří používají k sestavení nebo škálování clusterů, pocházejí z izolovaného fondu hostitelů. Tito hostitelé prošli hardwarovými testy a bezpečně odstranili všechna data. 

## <a name="vmware-software-versions"></a>Verze softwaru VMware

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="host-maintenance-and-lifecycle-management"></a>Údržba hostitelů a správa životního cyklu

Údržba hostitelů a správa životního cyklu nemají žádný vliv na kapacitu a výkon clusterů privátního cloudu.  Mezi příklady automatizované údržby hostitele patří upgrady firmwaru a oprava hardwaru nebo náhrada.

Společnost Microsoft zodpovídá za správu životního cyklu zařízení NSX-T, jako je NSX-T Manager a NSX-T Edge. Společnost Microsoft zodpovídá za zavedení konfigurace sítě, například vytvoření brány vrstvy 0 a povolení směrování North-South. Zodpovídáte za konfiguraci NSX-T SDN. Například segmenty sítě, pravidla pro distribuované brány firewall, brány 1 a nástroje pro vyrovnávání zatížení.

## <a name="backup-and-restoration"></a>Zálohování a obnovení

Konfigurace privátního cloudu vCenter a NSX-T jsou v plánu hodinového zálohování.  Zálohování se uchovává po dobu tří dní. Pokud potřebujete obnovení ze zálohy, otevřete [žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support) v Azure Portal pro vyžádání obnovení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s koncepty privátního cloudu řešení Azure VMware, se můžete seznámit s těmito postupy: 

- [Sítě řešení Azure VMware a koncepty vzájemné propojení](concepts-networking.md)
- [Koncepty úložiště řešení Azure VMware](concepts-storage.md).
- [Jak povolit prostředek řešení Azure VMware](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->
[concepts-networking]: ./concepts-networking.md

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753


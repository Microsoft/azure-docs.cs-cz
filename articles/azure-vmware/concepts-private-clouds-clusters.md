---
title: Koncepty – privátní cloudy a clustery
description: Seznamte se s klíčovými možnostmi pro datové centra a vSphere clustery definovaná na základě softwaru řešení Azure VMware.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 7f8508abed3446c58a683454bc49ec7dbb49d051
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825070"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Koncepce privátního cloudu a clusteru řešení Azure VMware

Řešení Azure VMware poskytuje privátní cloudy založené na VMware v Azure. Privátní cloudy obsahují clustery vytvořené pomocí vyhrazených a holých hostitelů Azure. Nasazují a spravují prostřednictvím Azure Portal, CLI nebo PowerShellu.  Clustery zřízené v privátních cloudech zahrnují VMware vSphere, vCenter, síti vSAN a NSX software. Nasazení hardwaru a softwaru privátního cloudu řešení Azure VMware jsou v Azure plně integrovaná a automatizovaná.

Mezi předplatnými Azure, privátními cloudy Azure VMware, clustery síti vSAN a hostiteli jsou logické vztahy. Diagram zobrazuje jedno předplatné Azure se dvěma privátními cloudy, které reprezentují vývojové a produkční prostředí.  V každém z těchto privátních cloudů jsou dva clustery. 

Tento článek popisuje všechny tyto koncepce.

![Obrázek dvou privátních cloudů v rámci zákaznického předplatného](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>Z důvodu nižších možných potřeb vývojového prostředí používejte menší clustery s menšími hostiteli kapacity. 

## <a name="private-clouds"></a>Privátní cloudy

Privátní cloudy obsahují clustery síti vSAN vytvořené pomocí vyhrazených a holých hostitelů Azure. Každý privátní cloud může mít několik clusterů spravovaných stejným serverem vCenter a správcem NSX-T. Privátní cloudy můžete nasadit a spravovat v portálu, rozhraní příkazového řádku nebo PowerShellu. 

Stejně jako u jiných prostředků jsou privátní cloudy nainstalované a spravované v rámci předplatného Azure. Počet privátních cloudů v rámci předplatného je škálovatelný. Zpočátku existuje omezení jednoho privátního cloudu na jedno předplatné.

## <a name="clusters"></a>Clustery
U každého vytvořeného privátního cloudu je ve výchozím nastavení k dispozici jeden cluster síti vSAN. Clustery můžete přidávat, odstraňovat a škálovat pomocí Azure Portal nebo přes rozhraní API.  Všechny clustery mají výchozí velikost 3 hostitelů a můžou škálovat až 16 hostitelů.  Hostitelé, kteří se používají v clusteru, musí být stejný typ hostitele.

Zkušební clustery jsou k dispozici pro vyhodnocení a omezená na tři hostitele. Existuje jeden zkušební cluster na jeden privátní cloud. Během zkušebního období můžete škálovat zkušební cluster jediným hostitelem.

Správce vSphere a NSX-T můžete použít ke správě většiny dalších aspektů konfigurace clusteru nebo operace. Všechna místní úložiště každého hostitele v clusteru jsou pod kontrolou síti vSAN.

## <a name="hosts"></a>Hostitelé

Clustery privátního cloudu řešení Azure VMware využívají hostitele infrastruktury sblížené s použitím technologie Hyper-v holých počítačů. V následující tabulce jsou uvedeny kapacity paměti RAM, procesoru a disku hostitele. 

| Typ hostitele              |             Procesor             |   RAM (GB)   |  Síti vSAN NVMe cache úrovně (TB, RAW)  |  úroveň kapacity síti vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (HE)          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

Hostitelé, kteří používají k sestavení nebo škálování clusterů, pocházejí z izolovaného fondu hostitelů. Tito hostitelé prošli hardwarovými testy a bezpečně odstranili všechna data. 

## <a name="vmware-software-versions"></a>Verze softwaru VMware

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="host-maintenance-and-lifecycle-management"></a>Údržba hostitelů a správa životního cyklu

Údržba hostitelů a správa životního cyklu nemají žádný vliv na kapacitu a výkon clusterů privátního cloudu.  Mezi příklady automatizované údržby hostitele patří upgrady firmwaru a oprava hardwaru nebo náhrada.

Společnost Microsoft zodpovídá za správu životního cyklu zařízení NSX-T, jako je NSX-T Manager a NSX-T Edge. Zodpovídá taky za zavádění konfigurace sítě, jako je vytvoření brány vrstvy 0 a povolení směrování North-South. Zodpovídáte za konfiguraci NSX-T SDN. Například segmenty sítě, pravidla pro distribuované brány firewall, brány 1 a nástroje pro vyrovnávání zatížení.

> [!IMPORTANT]
> Neměňte konfiguraci brány NSX-T Edge nebo vrstvy 0, protože to může způsobit ztrátu služby.

## <a name="backup-and-restoration"></a>Zálohování a obnovení

Konfigurace privátního cloudu vCenter a NSX-T jsou v plánu hodinového zálohování.  Zálohování se uchovává po dobu tří dní. Pokud potřebujete obnovení ze zálohy, otevřete [žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support) v Azure Portal pro vyžádání obnovení.

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte o [sítích a vzájemné propojení konceptech](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753


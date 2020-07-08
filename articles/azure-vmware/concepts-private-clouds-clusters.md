---
title: Koncepty – privátní cloudy a clustery
description: Seznamte se s klíčovými možnostmi pro služby Azure VMware Software-Defined data Centers a vSphere v řešení VMware v Azure pomocí VMware.
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 09e1fd45b1dd873509f942ef8b524783acfed4ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906985"
---
# <a name="azure-vmware-solution-avs-preview-private-cloud-and-cluster-concepts"></a>Koncept řešení Azure VMware (AVS) ve verzi Preview pro privátní cloud a clustery

Řešení Azure VMware (AVS) poskytuje privátní cloudy založené na VMware v Azure. Privátní cloudy jsou sestavené z clusterů vyhrazených hostitelů holých počítačů a nasazují a spravují prostřednictvím Azure Portal. Clustery v privátních cloudech se zřídí pomocí softwaru VMware vSphere, vCenter, síti vSAN a NSX. Nasazení hardwaru a softwaru privátního cloudu služby AVS jsou plně integrovaná a automatizovaná v Azure.

Mezi předplatnými Azure, soukromými cloudy služby AVS, clustery síti vSAN a hostiteli existuje logický vztah. V diagramu se zobrazí dva privátní cloudy v jednom předplatném Azure. Privátní cloudy reprezentují vývojové a produkční prostředí, z nichž každý má vlastní privátní cloud. V každém z těchto privátních cloudů existují dva clustery. Pro zobrazení nižších možných potřeb vývojového prostředí se používají menší clustery s nižšími hostiteli kapacity. Všechny tyto koncepce jsou popsány v následujících částech.

![Obrázek dvou privátních cloudů v rámci zákaznického předplatného](./media/hosts-clusters-private-clouds-final.png)

## <a name="private-clouds"></a>Privátní cloudy

Privátní cloudy obsahují síti vSAN clustery, které jsou vytvořené pomocí vyhrazených a holéch hostitelů Azure. Každý privátní cloud může mít několik clusterů, které spravuje stejný server vCenter a správce NSX-T. Privátní cloudy můžete nasadit a spravovat v portálu, rozhraní příkazového řádku nebo PowerShellu. Stejně jako u jiných prostředků jsou privátní cloudy nainstalované a spravované v rámci předplatného Azure.

Počet privátních cloudů v rámci předplatného je škálovatelný. Zpočátku existuje omezení jednoho privátního cloudu na jedno předplatné.

## <a name="clusters"></a>Clustery

V každém privátním cloudu vytvoříte aspoň jeden cluster síti vSAN. Při vytváření privátního cloudu je ve výchozím nastavení k dispozici jeden cluster. Do privátního cloudu můžete přidat další clustery pomocí Azure Portal nebo přes rozhraní API. Všechny clustery mají výchozí velikost tří hostitelů a je možné je škálovat od 3 do 16 hostitelů. Typ hostitelů používaných v clusteru musí být stejného typu. Typy hostitelů jsou popsány v následující části.

Zkušební clustery jsou k dispozici pro vyhodnocení a jsou omezeny na tři hostitele a jeden zkušební cluster na jeden privátní cloud. Během zkušebního období můžete škálovat zkušební cluster jediným hostitelem.

Clustery můžete vytvářet, odstraňovat a škálovat prostřednictvím portálu nebo rozhraní API. Ke správě většiny dalších aspektů konfigurace nebo operace clusteru stále používáte Správce vSphere a NSX-T. Všechna místní úložiště každého hostitele v clusteru se řídí síti vSAN.

## <a name="hosts"></a>Hostitelé

V clusterech se zabezpečením v privátním cloudu pro funkci AVS se používají neúplné uzly infrastruktury s použitím technologie Hyper-v. Kapacita paměti RAM, procesoru a disku hostitele je uvedena v následující tabulce. 

| Typ hostitele              |             Procesor             |   RAM (GB)   |  Síti vSAN NVMe cache úrovně (TB, RAW)  |  úroveň kapacity síti vSAN SSD (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| Horní – konec (HE)          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

Hostitelé, kteří se používají k sestavení nebo škálování clusterů, se získávají z izolovaného fondu hostitelů. Tito hostitelé prošli hardwarovými testy a museli jste bezpečně odstranit všechna data z disků Flash. Když odeberete hostitele z clusteru, interní disky se bezpečně vymažou a hostitelé se umístí do izolovaného fondu hostitelů. Když přidáte hostitele do clusteru, použije se upravený hostitel z izolovaného fondu.

## <a name="vmware-software-versions"></a>Verze softwaru VMware

Aktuální verze softwaru VMware softwaru používané v clusterech privátních cloudů pro funkci AVS jsou:

| Software              |    Verze   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U2    | 
| ESXi                  |    6,7 U2    | 
| Síti vSAN                  |    6,7 U2    |
| NSX-T                 |      2.5     |

U všech nových clusterů v privátním cloudu bude verze softwaru odpovídat obsahu, který aktuálně běží v privátním cloudu. Pro všechny nové privátní cloudy v rámci zákaznického předplatného je nainstalovaná nejnovější verze softwarového zásobníku.

Obecné zásady upgradu a procesy pro software pro platformu AVS jsou popsány v dokumentu koncepty upgrady.

## <a name="host-maintenance-and-lifecycle-management"></a>Údržba hostitelů a správa životního cyklu

Údržba hostitelů a správa životního cyklu se provádí bez dopadu na kapacitu nebo výkon clusterů privátního cloudu. Mezi příklady automatizované údržby hostitele patří upgrady firmwaru a oprava hardwaru nebo náhrada.

Společnost Microsoft zodpovídá za správu životního cyklu zařízení NSX-T, jako je NSX-T Manager a NSX-T Edge. Společnost Microsoft je také zodpovědná za zavedení konfigurace sítě, například vytvoření brány vrstvy 0 a povolení směrování na sever-jih. Jako správce privátního cloudu služby AVS zodpovídáte za NSX konfiguraci SDN, jako jsou segmenty sítě, distribuovaná pravidla brány firewall, brány 1 a nástroje pro vyrovnávání zatížení.

> [!IMPORTANT]
> Správce služby AVS nesmí měnit konfiguraci brány NSX-T Edge nebo vrstvy 0. To může mít za následek ztrátu služby.

## <a name="backup-and-restoration"></a>Zálohování a obnovení

Konfigurace privátních cloudů vCenter a NSX-T se zálohuje každou hodinu. Zálohování se uchovává po dobu tří dní. Obnovení ze zálohy je požadováno prostřednictvím žádosti o služby v Azure Portal.

## <a name="next-steps"></a>Další kroky

Dalším krokem je seznámit se s [Koncepty sítí a mezi připojeními](concepts-networking.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753


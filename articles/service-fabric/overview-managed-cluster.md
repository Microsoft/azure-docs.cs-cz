---
title: Service Fabric spravované clustery (Preview)
description: Service Fabric spravované clustery představují vývoj modelu prostředků clusteru Azure Service Fabric, který zjednodušuje správu nasazení a clusterů.
ms.topic: overview
ms.date: 09/28/2020
ms.openlocfilehash: 3d26a92126491662d5c51b3c4e8900ffa547f830
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91410482"
---
# <a name="service-fabric-managed-clusters-preview"></a>Service Fabric spravované clustery (Preview)

Service Fabric spravované clustery představují vývoj modelu prostředků clusteru Azure Service Fabric, který zjednodušuje možnosti správy nasazení a clusterů.

Šablona modelu Azure Resource model (ARM) pro tradiční Service Fabric clustery vyžaduje, abyste nadefinovali prostředek clusteru spolu s několika podpůrnými prostředky, které musí být "kabelové" správně (při nasazení a v celém životním cyklu clusteru), aby cluster a služby fungovaly správně. Model zapouzdření pro Service Fabric spravované clustery se naopak skládá z jednoho *Service Fabric spravovaného prostředku clusteru* . Všechny základní prostředky pro cluster se odříznout a spravuje Azure vaším jménem.

**Service Fabric tradičního modelu clusteru** 
 ![ Service Fabric tradičního modelu clusteru][sf-composition]

**Service Fabric model** 
 ![ spravovaného clusteru Service Fabric zapouzdřeného modelu clusteru][sf-encapsulation]

V souvislosti s velikostí a složitostí je šablona ARM pro Service Fabric spravovaný cluster přibližně 100 řádků JSON, přičemž některé 1000 řádky vyžadovaly definování typického clusteru Service Fabric:

| Prostředky služby Service Fabric | Service Fabric prostředky spravovaného clusteru |
|----------|-----------|
| Cluster Service Fabric | Service Fabric spravovaný cluster |
| Sady škálování virtuálních počítačů | |
| Nástroj pro vyrovnávání zatížení | |
| Veřejná IP adresa | |
| Účty úložiště | |
| Virtuální síť | |

Service Fabric spravované clustery poskytují řadu výhod oproti tradičním clusterům:

**Zjednodušené nasazení a Správa clusteru**
- Nasazení a správa jednoho prostředku Azure
- Správa certifikátů a automatické otočení
- Zjednodušené operace škálování

**Zabránit provozním chybám**
- Zabránit neshodě konfigurace s podkladovým prostředkům
- Blokovat nebezpečné operace (například odstranění uzlu počáteční hodnoty)

**Osvědčené postupy ve výchozím nastavení**
- Zjednodušená nastavení spolehlivosti a odolnosti

Za Service Fabric spravované clustery se neúčtují žádné další poplatky nad rámec nákladů na podkladové prostředky, které pro cluster potřebujete.

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric SKU spravovaného clusteru

Service Fabric spravované clustery jsou k dispozici v jednotkách Basic a Standard.

| Funkce | Basic | Standard |
| ------- | ----- | -------- |
| Síťový prostředek (SKU pro [Load Balancer](../load-balancer/skus.md), [Veřejná IP adresa](../virtual-network/public-ip-addresses.md)) | Basic | Standard |
| Minimální počet uzlů (instance virtuálního počítače) | 3 | 5 |
| Maximální počet uzlů na typ uzlu | 100 | 100 |
| Maximální počet typů uzlů | 1 | 20 |
| Přidat nebo odebrat typy uzlů | Ne | Ano |
| Zónová redundance | Ne | Ano |

## <a name="service-fabric-managed-cluster-feature-roadmap"></a>Service Fabric plán funkcí spravovaného clusteru
Protože se jedná o předběžnou verzi Preview Service Fabric spravovaných clusterů, je potřeba mít na paměti několik nedostatků. Tyto funkce budou k dispozici v budoucích verzích. 

* Publikování aplikací do clusterů přímo ze sady Visual Studio
* Spravované identity 
* Nasazení aplikací ARM 
* Zóny dostupnosti 
* Reverzní proxy 
* Automatické škálování 
* Aktualizují se pravidla NSG 
* Automatické upgrady operačního systému

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Service Fabric spravovanými clustery, vyzkoušejte si rychlý Start:

> [!div class="nextstepaction"]
> [Vytvoření spravovaného clusteru Service Fabric (Preview)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png
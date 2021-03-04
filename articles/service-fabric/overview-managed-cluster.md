---
title: Service Fabric spravované clustery (Preview)
description: Service Fabric spravované clustery představují vývoj modelu prostředků clusteru Azure Service Fabric, který zjednodušuje správu nasazení a clusterů.
ms.topic: overview
ms.date: 02/15/2021
ms.openlocfilehash: 271852214097ee96ba6b10de7a94904981cd8ef8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041225"
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

## <a name="whats-new-for-service-fabric-managed-clusters"></a>Co je nového u Service Fabric spravovaných clusterů

Nejnovější funkce pro Service Fabric spravované clustery ve verzi Preview zahrnují podporu pro:

* [Nasazení aplikací pomocí šablon ARM](how-to-managed-cluster-app-deployment-template.md)
* [Automatické upgrady operačního systému](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [Šifrování disku](how-to-enable-managed-cluster-disk-encryption.md)
* [Použití pravidel NSG](how-to-managed-cluster-networking.md)

Mezi funkce, které se mají přidat v nadcházejících verzích, patří:

* Nasazení aplikací pomocí sady Visual Studio
* Podpora spravovaných identit
* Zóny dostupnosti
* Reverzní proxy
* Automatické škálování

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s Service Fabric spravovanými clustery, vyzkoušejte si rychlý Start:

> [!div class="nextstepaction"]
> [Vytvoření spravovaného clusteru Service Fabric (Preview)](quickstart-managed-cluster-template.md)


[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png
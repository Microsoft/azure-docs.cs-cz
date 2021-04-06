---
title: Běžné otázky týkající se Service Fabric spravovaných clusterů
description: Nejčastější dotazy týkající se Service Fabric spravovaných clusterů, včetně možností, případů použití a běžných scénářů.
ms.topic: troubleshooting
ms.author: pepogors
author: peterpogorski
ms.date: 02/15/2021
ms.custom: references_regions
ms.openlocfilehash: aa77896ba88d0ffd0a6f94a84603b5f4a1803357
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633083"
---
# <a name="service-fabric-managed-clusters-frequently-asked-questions"></a>Nejčastější dotazy k Service Fabric spravovaných clusterů

Tady jsou některé nejčastější dotazy a odpovědi na Service Fabric spravované clustery (Preview).

## <a name="general"></a>Obecné

### <a name="what-are-service-fabric-managed-clusters"></a>Co jsou spravované clustery Service Fabric?

Service Fabric spravované clustery představují vývoj modelu prostředků Service Fabric clusteru, který je navržený tak, aby usnadnil nasazení a správu clusterů. Service Fabric spravovaný cluster používá model Azure Resource Manager zapouzdření, aby uživatel musel definovat a nasadit jenom jeden prostředek clusteru v porovnání s mnoha nezávislými prostředky, které musí nasazovat dnes (Virtual Machine Scale set, Load Balancer, IP a další).

### <a name="what-regions-are-supported-in-the-preview"></a>Jaké oblasti podporuje verze Preview?

Podporované oblasti pro Service Fabric spravované clustery ve verzi Preview zahrnují `centraluseuap` ,,,, `eastus2euap` `eastasia` `northeurope` `westcentralus` a `eastus2` .

### <a name="can-i-do-an-in-place-migration-of-my-existing-service-fabric-cluster-to-a-managed-cluster-resource"></a>Můžu provést místní migraci stávajícího Service Fabric clusteru do prostředku spravovaného clusteru?

No. V tuto chvíli byste museli vytvořit nový prostředek clusteru Service Fabric, abyste mohli použít nový typ prostředku Service Fabric spravovaného clusteru.

### <a name="is-there-an-additional-cost-for-service-fabric-managed-clusters"></a>Existují pro Service Fabric spravované clustery další náklady?

No. K Service Fabric spravovanému clusteru se nevztahují žádné další poplatky nad rámec nákladů na základní výpočetní prostředky, úložiště a síťové prostředky, které jsou pro cluster nutné.

### <a name="is-there-a-new-sla-introduced-by-the-service-fabric-managed-cluster-resource"></a>Zavádí prostředek spravovaného clusteru Service Fabric novou smlouvu SLA?

Smlouva SLA se nezměnila z modelu prostředků aktuální Service Fabric.

### <a name="what-is-the-difference-between-a-basic-and-standard-sku-cluster"></a>Jaký je rozdíl mezi základním a standardním clusterem SKU?

Základní cluster SKU znamená, že většina konfigurací poskytuje Service Fabric poskytovatel prostředků. Clustery základních SKU jsou určeny k použití pro testování a předprodukční prostředí. Standardní cluster SKU umožňuje uživatelům nakonfigurovat cluster tak, aby splňoval požadavky přímo. Další informace najdete v tématu [Service Fabric spravovaných SKU clusteru](./overview-managed-cluster.md#service-fabric-managed-cluster-skus).

## <a name="cluster-deployment-and-management"></a>Nasazení a Správa clusteru

### <a name="i-run-custom-script-extensions-on-my-virtual-machine-scale-set-can-i-continue-to-do-that-with-a-managed-service-fabric-resource"></a>Spouštím rozšíření vlastních skriptů na mém virtuálním počítači s měřítkem, můžu s spravovaným prostředkem Service Fabric pokračovat?

Ano, můžete zadat rozšíření virtuálních počítačů na spravovaných typech uzlů clusteru. Další informace najdete v tématu [Přidání rozšíření sady škálování na Service Fabric typ uzlu spravovaného clusteru](how-to-managed-cluster-vmss-extension.md).

### <a name="i-want-to-have-an-internal-only-load-balancer-is-that-possible"></a>Chci mít jenom interní nástroj pro vyrovnávání zatížení, je to možné?

No. V současné době není možné mít nástroj pro vyrovnávání zatížení jen interní. Doporučuje se uzamknout pravidla skupiny zabezpečení sítě (NSG), která zablokuje libovolný nepožadovaný příchozí a odchozí provoz.

### <a name="can-i-autoscale-my-cluster"></a>Můžu cluster znovu škálovat?

Automatické škálování není aktuálně k dispozici ve verzi Preview.

### <a name="can-i-deploy-my-cluster-across-availability-zones"></a>Můžu cluster nasadit napříč zónami dostupnosti?

Clustery zón pro více dostupnosti nejsou aktuálně k dispozici ve verzi Preview.

### <a name="can-i-select-between-automatic-and-manual-upgrades-for-my-cluster-runtime"></a>Můžu si vybrat mezi automatickými a ručními upgrady pro modul runtime clusteru?

V rámci verze Preview budou všechny upgrady modulu runtime dokončeny automaticky.

## <a name="applications"></a>Aplikace

### <a name="is-there-a-local-development-experience-for-service-fabric-managed-clusters"></a>Je pro Service Fabric spravovaných clusterů k dispozici místní vývojové prostředí?

Místní vývojové prostředí zůstane beze změny ze stávajících Service Fabric clusterů. Další informace najdete v tématu [Nastavení vývojového prostředí](./service-fabric-get-started.md) pro další informace o místním prostředí pro vývoj.

### <a name="can-i-deploy-my-applications-as-an-azure-resource-manager-resource"></a>Můžu své aplikace nasadit jako prostředek Azure Resource Manager?

Ano. Přidala se podpora pro nasazení aplikací jako prostředku Azure Resource Manager (kromě nasazení pomocí PowerShellu a rozhraní příkazového řádku). Informace o tom, jak začít, najdete v tématu [nasazení aplikace spravovaného clusteru Service Fabric pomocí šablony ARM](how-to-managed-cluster-app-deployment-template.md).

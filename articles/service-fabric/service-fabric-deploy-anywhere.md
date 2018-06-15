---
title: Vytvořit clustery se Azure Service Fabric na serveru Windows a Linux | Microsoft Docs
description: Spusťte na serveru Windows a Linux, tzn., budete moci nasadit a hostování aplikací Service Fabric kdekoli clusterů Service Fabric můžete spustit systém Windows Server nebo Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/28/2018
ms.author: dekapur
ms.openlocfilehash: 3d427d99f6919991c29fc5947ebe0082670a1cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34213125"
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Vytvoření clusterů Service Fabric na Windows Server nebo Linux
Cluster služby Azure Service Fabric je sada virtuální nebo fyzické počítače, do kterých jsou nasazené a spravovat vaše mikroslužeb připojené k síti. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzlem clusteru. Clustery můžete škálovat na tisících uzlů. Pokud přidáte nové uzly do clusteru, Service Fabric znovu vytvoří rovnováhu repliky oddílu služby a instance napříč vyšší počet uzlů. Celkové zlepšuje výkon aplikace a snižuje kolize pro přístup do paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric opakujte znovu vytvoří rovnováhu repliky oddílu a instance napříč ke snížení počtu uzlů zajistit lepší využití hardwaru na každém uzlu.

Service Fabric umožňuje vytváření clusterů Service Fabric na všechny virtuální počítače nebo počítače se systémem Windows Server nebo Linux. To znamená, že budete moci nasadit a spouštět aplikace Service Fabric v jakémkoli prostředí, kde máte sadu Windows Server nebo Linux počítače, které jsou vzájemně provázány, je jej na místních počítačích Microsoft Azure nebo kteréhokoli poskytovatele cloudových služeb.

## <a name="create-service-fabric-clusters-on-azure"></a>Vytvoření clusterů Service Fabric v Azure
Vytvoření clusteru v Azure se provádí buď prostřednictvím šablonu Model prostředků nebo [portál Azure](https://portal.azure.com). Čtení [vytvořit cluster Service Fabric pomocí šablony Resource Manageru](service-fabric-cluster-creation-via-arm.md) nebo [z portálu Azure vytvořit cluster Service Fabric](service-fabric-cluster-creation-via-portal.md) Další informace.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Podporované operační systémy pro clustery v Azure
Budete moci vytvořit clustery se u virtuálních počítačů s těmito operačními systémy:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> Pokud se rozhodnete nasadit Service Fabric na Windows Server. 1709, Upozorňujeme, že (1) není dlouhou dobu údržby větve, takže možná budete muset v budoucnu přesunout verze a (2) Pokud nasadíte kontejnery, kontejnery postavené na systému Windows Server 2016 nejsou funkční v systému Windows Server  . 1709 a naopak (budete muset znovu vytvořit, je jejich nasazení).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Vytvořit samostatný Service Fabric clustery v místě nebo kteréhokoli poskytovatele cloudových služeb
Service Fabric nabízí balíček instalace vytvořit samostatnou Service Fabric clustery místně nebo na všechny poskytovatele cloudových služeb.

Další informace o nastavení samostatné Service Fabric clusterů v systému Windows Server, přečtěte si [vytváření clusteru Service Fabric pro systém Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Samostatné clustery nejsou aktuálně podporovány pro Linux. Linux je podporována v jedné pole pro vývoj a clustery pro víc počítačů Azure Linux.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Všechna nasazení cloudu a místních nasazení
Proces vytvoření místní cluster Service Fabric je podobný procesu vytváření clusteru s podporou na všechny cloudové zvoleného sadu virtuálních počítačů. Počáteční kroky ke zřízení virtuálních počítačů se řídí poskytovatele cloudové nebo místní prostředí, který používáte. Jakmile máte sadu virtuálních počítačů s připojením k síti povolena mezi nimi, pak postup nastavte balíček Service Fabric, upravit nastavení clusteru a spusťte vytvoření clusteru a správy skriptů jsou identické. To zajišťuje, že znalosti a zkušenosti provoz a správu clusterů Service Fabric převoditelné když zvolíte cíle nové hostitelských prostředích.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Výhody vytvoření samostatných clusterů Service Fabric
* Jste volné vyberte všechny poskytovatele cloudu k hostování clusteru.
* Aplikace Service Fabric, jednou napsané, lze spustit v prostředí s více hostitelských s minimální a žádné změny.
* Informace o vytváření aplikací Service Fabric představuje z jednoho hostování prostředí do druhého.
* Provozní prostředí, jak spouštět a spravovat Service Fabric clusterů má u sebe přes z jednoho prostředí do druhého.
* Široká zákazníka reach je bez vazby hostování prostředí omezení.
* Další úroveň spolehlivosti a ochranu proti výpadkům rozšířeným existuje, protože můžete přesunout službu do jiného prostředí nasazení Pokud zprostředkovatele dat centra nebo cloudu přerušení spojení.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Podporované operační systémy pro samostatné clustery
Budete moci vytvořit clustery se na počítače s těmito operačními systémy (Linux ještě nepodporuje.) nebo virtuální počítače:

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Vytvořit místní výhod clusterů Service Fabric v Azure přes samostatnou bitovou clusterů Service Fabric
Clusterů Service Fabric systémem Azure poskytuje možnost výhod oproti místní, takže pokud nemáte konkrétní potřeby kterém spouštíte clustery, pak doporučujeme vám spustit v Azure. V Azure poskytujeme integrace s dalšími funkce Azure a službami, které usnadňuje operace a Správa clusteru jednodušší a spolehlivější.

* **Portál Azure:** portál Azure umožňuje snadno vytvářet a spravovat clustery.
* **Azure Resource Manager:** pomocí nástroje Azure Resource Manager umožňuje snadnou správu všechny prostředky používané clusteru jako jednotku a zjednodušuje sledování nákladů a fakturace.
* **Cluster Service Fabric jako prostředek Azure** cluster A Service Fabric je prostředek služby Azure, abyste mohli ho stejně jako ostatní prostředky v Azure.
* **Integrace s infrastruktury Azure** Service Fabric koordinuje s základní infrastrukturu Azure pro operační systém, sítě a jiných upgrady ke zlepšení dostupnosti a spolehlivosti aplikací.  
* **Diagnostika:** v Azure, poskytujeme integraci s Azure diagnostiku a analýzy protokolů.
* **Automatické škálování:** pro clustery v Azure, poskytujeme integrovanou funkci automatického škálování z důvodu sady škálování virtuálního počítače. V jiných prostředích cloudu a místní budete muset vytvořit vlastní automatické škálování funkce nebo škálování ručně pomocí rozhraní API, která zveřejňuje Service Fabric pro škálování clusterů.

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru s podporou na virtuální počítače nebo počítače se systémem Windows Server: [vytváření clusteru Service Fabric pro systém Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru s podporou na virtuální počítače nebo počítače se systémem Linux: [vytvoření clusteru s podporou systému Linux](service-fabric-cluster-creation-via-portal.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)


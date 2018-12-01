---
title: Vytvoření clusterů Service Fabric ve Windows serveru a Linuxu | Dokumentace Microsoftu
description: Clustery Service Fabricu spouštět na Windows Server a Linux, což znamená, že budete mít k nasazení a hostování aplikací Service Fabric kdekoli můžete spustit systém Windows Server nebo Linux.
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
ms.date: 11/28/2018
ms.author: dekapur
ms.openlocfilehash: e4540076b29cf3cd51f03239a1868e18a41781d9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726521"
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Vytvoření clusterů Service Fabric ve Windows serveru nebo Linuxu
Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Clustery je možné škálovat na tisíce uzlů. Pokud přidáte nové uzly do clusteru Service Fabric znovu vytvoří rovnováhu replik oddílů služby a instance napříč zvýšeného počtu uzlů. Celkově zlepšuje výkon aplikací a snižuje kolize pro přístup k paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu znovu vytvoří rovnováhu replik oddílů a instancí napříč snížený počet uzlů, abyste měli lepší využití hardwaru na každém uzlu.

Service Fabric umožňuje vytvářet clustery Service Fabric na všechny virtuální počítače nebo počítače se systémem Windows Server nebo Linux. To znamená, že budete moct nasadit a spouštět aplikace Service Fabric v jakémkoli prostředí, kam máte sadu Windows Server nebo Linux počítačů, které jsou propojeny, jde o Microsoft Azure v místním nebo jakýkoli jiný poskytovatel cloudu.

## <a name="create-service-fabric-clusters-on-azure"></a>Vytvoření clusterů Service Fabric v Azure
Vytvoření clusteru v Azure se provádí buď prostřednictvím modelu prostředků šablony nebo [webu Azure portal](https://portal.azure.com). Čtení [vytvořit cluster Service Fabric pomocí šablony Resource Manageru](service-fabric-cluster-creation-via-arm.md) nebo [vytvořit cluster Service Fabric z portálu Azure portal](service-fabric-cluster-creation-via-portal.md) Další informace.

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Podporované operační systémy pro clustery v Azure
Budete moct vytvářet clustery na virtuálních počítačích s těmito operačními systémy:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (podpora ve verzi preview)

> [!NOTE]
> Pokud se rozhodnete nasadit Service Fabric ve Windows serveru 1709, mějte prosím na paměti, že (1) není dlouhodobém horizontu obslužná větev, takže možná budete muset přesunout verze v budoucnu a (2) Pokud nasazujete kontejnery, kontejnery, založená na Windows serveru 2016 nefungují na Windows serveru  1709 a naopak (budete muset znovu nasadit).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Vytvoření samostatného Service Fabric clustery v místním nebo jakýkoli jiný poskytovatel cloudu
Service Fabric nabízí instalační balíček si můžete vytvořit samostatné Service Fabric clustery v místním nebo jakýkoli jiný poskytovatel cloudu.

Další informace o nastavení samostatného Service Fabric clusterů v systému Windows Server, přečtěte si [vytváření clusteru Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Samostatné clustery nejsou aktuálně podporované pro Linux. Linux je podporována v jedné pole pro vývoj a clustery s více počítači Azure s Linuxem.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Všechna nasazení cloudových a místních nasazení
Proces vytvoření v místním clusteru Service Fabric je podobný procesu vytváření clusteru v libovolném cloudu podle vašeho výběru s sadu virtuálních počítačů. První kroky ke zřízení virtuálních počítačů se řídí poskytovatele cloudu nebo v místním prostředí, kterou používáte. Až budete mít sadu virtuálních počítačů s připojením k síti povolena mezi nimi, potom postup nastavení balíčku Service Fabric, upravit nastavení clusteru a spuštění vytvoření clusteru a skripty pro správu jsou identické. Tím se zajistí, že vaše znalosti a zkušenosti s provoz a správu clusterů Service Fabric je nepřenosná při výběru cílit na nová hostitelská prostředí.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Výhody vytvoření samostatných clusterů Service Fabric
* Můžete libovolně vybrat jakýkoli jiný poskytovatel cloudu pro hostování vašeho clusteru.
* Aplikace Service Fabric, jednou zapsat, lze spustit ve více hostitelských prostředích s minimálním pro žádné změny.
* Informace o vytváření aplikací Service Fabric se přenesou z jednoho hostování prostředí do jiného.
* Provozní prostředí provoz a správu Service Fabric clusterů mělo přes z jednoho prostředí do druhého.
* Dosah široké zákazníka, je bez vazby hostování prostředí omezení.
* Další úroveň spolehlivosti a ochrany proti rozšířených výpadky existuje, protože můžete přesouvat služby do jiného prostředí nasazení Pokud zprostředkovatele datového centra nebo cloudu se nedostupnosti.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Podporované operační systémy pro samostatné clustery
Budete moct vytvářet clustery na virtuální počítače nebo počítače s těmito operačními systémy (Linux se ještě nepodporuje):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Místně vytvořenou výhod clusterů Service Fabric v Azure přes samostatnou bitovou clusterů Service Fabric
Clustery Service Fabric běžící v Azure nabízí možnost výhod oproti místní, takže pokud nemáte konkrétní požadavky pro místo, kde spouštíte vašich clusterů, pak doporučujeme ji spustit v Azure. V Azure zajišťuje integraci s jinými funkce Azure a služby, které provádí operace a Správa clusteru jednodušší a spolehlivější.

* **Azure portal:** webu Azure portal umožňuje snadno vytvářet a spravovat clustery.
* **Azure Resource Manageru:** umožňuje snadnou správu všechny prostředky používané položkou clusteru jako celek pomocí Azure Resource Manageru a zjednodušuje sledování nákladů a fakturace.
* **Cluster Service Fabric jako prostředek Azure** clusteru A Service Fabric je prostředek Azure, takže lze modelovat, stejně jako ostatní prostředky v Azure.
* **Integrace s infrastrukturou Azure** koordinuje Service Fabric se základní infrastrukturou Azure pro operační systém, sítě a jiné upgradu zvýšit dostupnost a spolehlivost vašich aplikací.  
* **Diagnostika:** v Azure, zajišťuje integraci s diagnostikou Azure a službou Log Analytics.
* **Automatické škálování:** pro clustery v Azure, poskytujeme integrované funkce automatického škálování z důvodu škálovací sady virtuálních počítačů. V místní a dalších cloudových prostředích budete muset sestavit vlastní automatické škálování funkce nebo určený počet číslic ručně pomocí rozhraní API, která poskytuje Service Fabric pro škálování clusterů.

## <a name="next-steps"></a>Další postup

* Vytvoření clusteru na virtuální počítače nebo počítače se systémem Windows Server: [vytváření clusteru Service Fabric pro Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Vytvoření clusteru na počítače s Linuxem nebo virtuální počítače: [vytvoření clusteru s Linuxem](service-fabric-cluster-creation-via-portal.md)
* Informace o [možnostech podpory pro Service Fabric](service-fabric-support.md)


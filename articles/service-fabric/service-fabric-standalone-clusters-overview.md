---
title: Service Fabric samostatné clustery – přehled | Dokumentace Microsoftu
description: Clustery Service Fabricu spouštět na Windows Server a Linux, což znamená, že budete mít k nasazení a hostování aplikací Service Fabric kdekoli můžete spustit systém Windows Server nebo Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 5d3421c4d48a6bf8416a774c4b4e5e7852a83f06
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967191"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Přehled Service Fabric samostatné clustery

Cluster Service Fabric je síťově propojená sada virtuálních nebo fyzických počítačů, do které se nasazují a spravují mikroslužby. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Clustery je možné škálovat na tisíce uzlů. Pokud přidáte nové uzly do clusteru Service Fabric znovu vytvoří rovnováhu replik oddílů služby a instance napříč zvýšeného počtu uzlů. Celkově zlepšuje výkon aplikací a snižuje kolize pro přístup k paměti. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu znovu vytvoří rovnováhu replik oddílů a instancí napříč snížený počet uzlů, abyste měli lepší využití hardwaru na každém uzlu.

Typ uzlu definuje velikost, počet a vlastnosti pro sadu uzlů v clusteru. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Typy uzlů se používají k definování rolí pro sadu uzlů clusteru, například „front-end“ nebo „back-end“. Cluster může mít více než jeden typ uzlu, ale v případě produkčních clusterů musí existovat alespoň pět virtuálních počítačů primárního typu (nebo minimálně tři virtuální počítače v případě testovacích clusterů). V uzlech primárního typu jsou umístěny [systémové služby Service Fabric](service-fabric-technical-overview.md#system-services).

Proces vytvoření v místním clusteru Service Fabric je podobný procesu vytváření clusteru v libovolném cloudu podle vašeho výběru s sadu virtuálních počítačů. První kroky ke zřízení virtuálních počítačů se řídí poskytovatele cloudu nebo v místním prostředí, kterou používáte. Až budete mít sadu virtuálních počítačů s připojením k síti povolena mezi nimi, potom postup nastavení balíčku Service Fabric, upravit nastavení clusteru a spuštění vytvoření clusteru a skripty pro správu jsou identické. Tím se zajistí, že vaše znalosti a zkušenosti s provoz a správu clusterů Service Fabric je nepřenosná při výběru cílit na nová hostitelská prostředí.

## <a name="cluster-security"></a>Zabezpečení clusteru
Cluster Service Fabric je prostředek, který vlastníte.  Je vaší odpovědností zajistit clusterům pomáhá zabránit neoprávněným uživatelům v připojení k nim. Zabezpečení clusteru je obzvláště důležité při spouštění úloh v produkčním prostředí v clusteru.

### <a name="node-to-node-security"></a>Zabezpečení mezi uzly
Zabezpečení mezi uzly zabezpečuje komunikaci mezi virtuálními počítači nebo počítačích v clusteru. Tento scénář zabezpečení zajistíte, že jenom počítače, které mají oprávnění k připojení clusteru mohl podílet na hostování aplikací a služeb v clusteru. Service Fabric pomocí certifikátů X.509 zabezpečení clusteru a poskytuje funkce pro zabezpečení aplikací.  Certifikát clusteru je potřeba zabezpečit provoz clusteru a clusteru a serverem ověřování.  Vlastní certifikáty podepsané lze použít pro testovací clustery, ale certifikát od důvěryhodné certifikační autority by měla sloužit k zabezpečené produkční clustery.

Zabezpečení Windows může být povolená i u samostatného clusteru Windows. Pokud máte systém Windows Server 2012 R2 a Windows Active Directory, doporučujeme použít zabezpečení Windows s skupinových účtů spravované služby. S účty Windows, použijte zabezpečení Windows.

Další informace najdete v článku [zabezpečení mezi uzly](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Uzel klienta zabezpečení
Uzel klienta zabezpečení ověřuje klienty a pomáhá zajistit komunikaci mezi klientem a jednotlivým uzlům v clusteru. Tento typ zabezpečení pomáhá zajistit, že jenom Autorizovaní uživatelé přístup ke clusteru a aplikace, které jsou nasazené na clusteru. Klienti jsou jednoznačně identifikované pomocí kteréhokoliv svoje přihlašovací údaje zabezpečení certifikátu X.509. Libovolný počet volitelné klientské certifikáty lze použít k ověřování klientů správce nebo uživatel s clusterem.

Kromě klientských certifikátů je Azure Active Directory také nakonfigurovat k ověřování klientů s clusterem.

Další informace najdete v článku [uzel klienta zabezpečení](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Service Fabric také podporuje řízení přístupu k omezení přístupu k určité operace clusteru pro různé skupiny uživatelů. To pomáhá lépe zabezpečit clusteru. Klienti připojující se ke clusteru podporují dva typy ovládacích prvků přístupu: Role správce a role uživatele.  

Další informace najdete v článku [řízení přístupu na základě Role (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Škálování

Požadavky na aplikace v průběhu času měnit. Budete muset zvýšit prostředky clusteru k zajištění provozu aplikace vyšší zatížení nebo sítě nebo omezovat prostředky clusteru, jakmile poptávka poklesne. Po vytvoření clusteru Service Fabric, je možné škálovat cluster vodorovně (změnit počet uzlů), nebo svisle (změnit prostředky uzly). Je možné škálovat cluster v okamžiku, i když spouštění úloh v clusteru. Škálování clusteru, vaše aplikace automaticky škálovat směrem také.

Další informace najdete v článku [škálování samostatné clustery](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Upgrade

Samostatný cluster je prostředek, které zcela vlastní. Zodpovídáte za použití dílčích oprav základního operačního systému a inicializaci upgrady prostředků infrastruktury. Můžete nastavit cluster pro příjem upgradů automatické modulu runtime, když společnost Microsoft vydává nové verze, nebo vybrat podporovaná verze modulu runtime, který chcete. Kromě upgrady prostředků infrastruktury můžete také opravy operačního systému a aktualizovat konfiguraci clusteru, například certifikáty nebo porty aplikací. 

Další informace najdete v článku [upgrade samostatné clustery](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy
Budete moct vytvářet clustery na virtuální počítače nebo počítače s těmito operačními systémy (Linux se ještě nepodporuje):

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="next-steps"></a>Další postup
Další informace o [zabezpečení](service-fabric-cluster-security.md), [škálování](service-fabric-cluster-scaling-standalone.md), a [upgrade](service-fabric-cluster-upgrade-standalone.md) samostatné clustery.

Další informace o [možnosti podpory pro Service Fabric](service-fabric-support.md).
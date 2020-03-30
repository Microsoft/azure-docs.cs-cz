---
title: Přehled clusterů Samostatné služby Fabric
description: Clustery Service Fabric běží na Windows Server a Linux, což znamená, že budete moci nasadit a hostit service fabric aplikace kdekoli můžete spustit Windows Server nebo Linux.
author: dkkapur
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: e8912ef5bc0fd6009443b736031fc9af57ab6c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465638"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Přehled samostatných clusterů Service Fabric

Cluster Service Fabric je síťová sada virtuálních nebo fyzických počítačů, do kterých jsou vaše mikroslužby nasazeny a spravovány. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel clusteru. Clustery lze škálovat na tisíce uzlů. Pokud do clusteru přidáte nové uzly, service fabric znovu vyvažuje repliky a instance oddílů služby napříč zvýšeným počtem uzlů. Celkový výkon aplikace zlepšuje a tvrzení o přístupu k paměti snižuje. Pokud uzly v clusteru nejsou používány efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyvažuje repliky oddílů a instance přes snížený počet uzlů, aby bylo možné lépe využít hardware v každém uzlu.

Typ uzlu definuje velikost, číslo a vlastnosti pro sadu uzlů v clusteru. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Typy uzlů se používají k definování rolí pro sadu uzlů clusteru, například „front-end“ nebo „back-end“. Cluster může mít více než jeden typ uzlu, ale v případě produkčních clusterů musí existovat alespoň pět virtuálních počítačů primárního typu (nebo minimálně tři virtuální počítače v případě testovacích clusterů). V uzlech primárního typu jsou umístěny [systémové služby Service Fabric](service-fabric-technical-overview.md#system-services).

Proces vytváření clusteru Service Fabric v místním prostředí je podobný procesu vytváření clusteru v libovolném cloudu podle vašeho výběru se sadou virtuálních počítačů. Počáteční kroky ke zřízení virtuálních počítačů se řídí poskytovatelem cloudu nebo místní prostředí, které používáte. Jakmile máte mezi sebou povolenou sadu virtuálních připojení s povoleným síťovým připojením, kroky k nastavení balíčku Service Fabric, úpravě nastavení clusteru a spuštění skriptů pro vytváření a správu clusteru jsou identické. Tím je zajištěno, že vaše znalosti a zkušenosti s provozem a správou clusterů Service Fabric jsou přenositelné, pokud se rozhodnete cílit na nová hostitelská prostředí.

## <a name="cluster-security"></a>Zabezpečení clusteru
Cluster Service Fabric je prostředek, který vlastníte.  Je vaší odpovědností zabezpečit clustery, abyste zabránili neoprávněným uživatelům v připojení k nim. Zabezpečený cluster je obzvláště důležitý, pokud v clusteru spouštěte produkční úlohy.

### <a name="node-to-node-security"></a>Zabezpečení mezi uzlužinami
Zabezpečení mezi uzlem zabezpečuje komunikaci mezi virtuálními počítači nebo počítači v clusteru. Tento scénář zabezpečení zajišťuje, že pouze počítače, které jsou oprávněny připojit se ke clusteru, se mohou účastnit hostování aplikací a služeb v clusteru. Service Fabric používá certifikáty X.509 k zabezpečení clusteru a poskytování funkcí zabezpečení aplikací.  K zabezpečení přenosu clusteru a zajištění ověřování clusteru a serveru je vyžadován certifikát clusteru.  Vlastní podepsané certifikáty lze použít pro testovací clustery, ale k zabezpečení produkčních clusterů by měl být použit certifikát od důvěryhodné certifikační autority.

Zabezpečení systému Windows lze povolit také pro samostatný cluster systému Windows. Pokud máte Windows Server 2012 R2 a Windows Active Directory, doporučujeme použít zabezpečení systému Windows se skupinovými účty spravovaných služeb. V opačném případě použijte zabezpečení systému Windows s účty systému Windows.

Další informace naleznete v oblasti [zabezpečení mezi uzny](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Zabezpečení klient-uzel
Zabezpečení klient-uzel ověřuje klienty a pomáhá zabezpečit komunikaci mezi klientem a jednotlivými uzly v clusteru. Tento typ zabezpečení pomáhá zajistit, aby ke clusteru a aplikacím nasazeným v clusteru měli přístup pouze oprávnění uživatelé. Klienti jsou jednoznačně identifikováni pomocí svých pověření zabezpečení certifikátu X.509. Libovolný počet volitelných klientských certifikátů lze použít k ověření klientských nebo uživatelských klientů pomocí clusteru.

Kromě klientských certifikátů lze službu Azure Active Directory také nakonfigurovat k ověřování klientů pomocí clusteru.

Další informace naleznete v [seznamu Zabezpečení klient-uzel](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Service Fabric také podporuje řízení přístupu k omezení přístupu k určitým operacím clusteru pro různé skupiny uživatelů. To pomáhá zvýšit zabezpečení clusteru. Pro klienty, kteří se připojují ke clusteru, jsou podporovány dva typy řízení přístupu: role správce a role uživatele.  

Další informace naleznete [v části Řízení přístupu na základě rolí (RBAC).](service-fabric-cluster-security.md#role-based-access-control-rbac)

## <a name="scaling"></a>Škálování

Požadavky aplikací se v průběhu času mění. Možná budete muset zvýšit prostředky clusteru, aby bylo možné splnit zvýšené zatížení aplikací nebo síťový provoz nebo snížit prostředky clusteru při poklesu poptávky. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo svisle (změnit prostředky uzlů). Cluster můžete kdykoli škálovat, i když jsou v clusteru spuštěny úlohy. Při škálování clusteru se automaticky škálují také vaše aplikace.

Další informace naleznete v [informacích o změně velikosti samostatných clusterů](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Inovace

Samostatný cluster je prostředek, který zcela vlastníte. Jste zodpovědní za opravu základníoperační systém a zahájení upgrady prostředků infrastruktury. Cluster můžete nastavit tak, aby přijímali automatické inovace za běhu, když společnost Microsoft vydá novou verzi, nebo můžete vybrat požadovanou podporovanou verzi modulu runtime. Kromě upgradů prostředků infrastruktury můžete také opravit konfiguraci operačního systému a aktualizovat konfiguraci clusteru, jako jsou certifikáty nebo porty aplikací. 

Další informace naleznete [v článek Inovace samostatných clusterů](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy
Clustery můžete vytvářet na virtuálních počítačích nebo v počítačích s těmito operačními systémy (Linux ještě není podporovaný):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [zabezpečení](service-fabric-cluster-security.md), [škálování](service-fabric-cluster-scaling-standalone.md)a [upgradování](service-fabric-cluster-upgrade-standalone.md) samostatných clusterů.

Další informace o [možnostech podpory service fabric](service-fabric-support.md).

---
title: Přehled samostatných clusterů Service Fabric
description: Clustery Service Fabric běží na Windows serveru a Linux, což znamená, že budete moct nasazovat a hostovat Service Fabric aplikace kdekoli, kde můžete používat Windows Server nebo Linux.
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: c57d1ef6db295400ad986adadb804fb942a723e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91841063"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Přehled samostatných clusterů Service Fabric

Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se označuje jako uzel clusteru. Clustery se můžou škálovat na tisíce uzlů. Pokud do clusteru přidáte nové uzly, Service Fabric rebilance repliky oddílů služby a instance napříč rostoucím počtem uzlů. Celkový výkon aplikace vylepšuje a kolizí pro přístup k snížení velikosti paměti. Pokud se uzly v clusteru nepoužívají efektivně, můžete snížit počet uzlů v clusteru. Service Fabric znovu vyrovnává repliky oddílů a instance napříč sníženým počtem uzlů, aby bylo možné lépe využívat hardware na jednotlivých uzlech.

Typ uzlu definuje velikost, číslo a vlastnosti pro sadu uzlů v clusteru. Pro každý typ uzlu je pak možné nezávislé vertikální navyšování nebo snižování kapacity, otevírání různých sad portů a používání různých metrik kapacity. Typy uzlů se používají k definování rolí pro sadu uzlů clusteru, například „front-end“ nebo „back-end“. Cluster může mít více než jeden typ uzlu, ale v případě produkčních clusterů musí existovat alespoň pět virtuálních počítačů primárního typu (nebo minimálně tři virtuální počítače v případě testovacích clusterů). V uzlech primárního typu jsou umístěny [systémové služby Service Fabric](service-fabric-technical-overview.md#system-services).

Proces vytvoření místního clusteru Service Fabric se podobá procesu vytvoření clusteru v jakémkoli cloudu podle vašeho výběru se sadou virtuálních počítačů. Úvodní kroky pro zřízení virtuálních počítačů se řídí poskytovatelem cloudu nebo místním prostředím, které používáte. Jakmile budete mít k dispozici sadu virtuálních počítačů s povoleným připojením k síti, pak kroky pro nastavení balíčku Service Fabric, úpravě nastavení clusteru a spuštění skriptů pro vytváření a správu clusteru jsou identické. Tím zajistíte, že vaše znalosti a zkušenosti s provozem a správou Service Fabric clusterů jsou převoditelné, pokud se rozhodnete cílit na nová hostující prostředí.

## <a name="cluster-security"></a>Zabezpečení clusteru

Cluster Service Fabric je prostředek, který vlastníte.  Je vaše zodpovědnost za zabezpečení clusterů, aby se zabránilo neoprávněným uživatelům v jejich připojení. Zabezpečený cluster je obzvláště důležitý při spuštění produkčních úloh v clusteru.

> [!NOTE]
> Ověřování systému Windows je založené na protokolu Kerberos. Protokol NTLM není podporován jako typ ověřování.
>
> Kdykoli je to možné, použijte pro Service Fabric clusterů ověřování pomocí certifikátu X. 509.

### <a name="node-to-node-security"></a>Zabezpečení mezi uzly

Zabezpečení mezi uzly zabezpečuje komunikaci mezi virtuálními počítači nebo počítači v clusteru. Tento scénář zabezpečení zajišťuje, že se můžou účastnit hostování aplikací a služeb v clusteru jenom počítače, které jsou autorizované pro připojení ke clusteru. Service Fabric k zabezpečení clusteru a poskytování funkcí zabezpečení aplikací používá certifikáty X. 509.  Certifikát clusteru je nutný k zabezpečení provozu clusteru a k zajištění ověřování clusteru a serveru.  Certifikáty podepsané svým držitelem se dají použít pro testovací clustery, ale k zabezpečení produkčních clusterů by se měly použít certifikát od důvěryhodné certifikační autority.

Zabezpečení systému Windows lze také povolit pro samostatný cluster systému Windows. Pokud máte Windows Server 2012 R2 a Windows Active Directory, doporučujeme použít zabezpečení systému Windows se skupinovými účty spravované služby. V opačném případě použijte zabezpečení systému Windows s účty systému Windows.

Další informace najdete v článku [zabezpečení mezi](service-fabric-cluster-security.md#node-to-node-security) uzly.

### <a name="client-to-node-security"></a>Zabezpečení klient-uzel

Zabezpečení typu klient-uzel ověřuje klienty a pomáhá zabezpečit komunikaci mezi klientem a jednotlivými uzly v clusteru. Tento typ zabezpečení pomáhá zajistit, že ke clusteru a aplikacím nasazeným v clusteru mají přístup jenom autorizovaní uživatelé. Klienti se jednoznačně identifikují pomocí přihlašovacích údajů zabezpečení certifikátů X. 509. K ověřování klientů správce nebo uživatelů s clusterem lze použít libovolný počet volitelných klientských certifikátů.

Kromě klientských certifikátů je možné Azure Active Directory taky nakonfigurovat na ověřování klientů pomocí clusteru.

Další informace najdete v článku [zabezpečení mezi klienty a uzly](service-fabric-cluster-security.md#client-to-node-security) .

### <a name="role-based-access-control-rbac"></a>Řízení přístupu na základě role (RBAC)
Service Fabric také podporuje řízení přístupu pro omezení přístupu k určitým operacím clusteru pro různé skupiny uživatelů. To pomáhá zvýšit zabezpečení clusteru. Pro klienty, kteří se připojují ke clusteru, jsou podporovány dva typy řízení přístupu: role správce a role uživatele.  

Další informace najdete v [Access Control na základě rolí (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

## <a name="scaling"></a>Škálování

Aplikace vyžaduje změnu v průběhu času. Možná budete muset zvýšit prostředky clusteru tak, aby splňovaly zvýšené zatížení aplikace nebo síťový provoz nebo snížili prostředky clusteru, když na vyžádání klesne požadavek. Po vytvoření clusteru Service Fabric můžete škálovat cluster vodorovně (změnit počet uzlů) nebo vertikálně (změnit prostředky uzlů). Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru. I když se cluster škáluje, vaše aplikace se automaticky škálují.

Další informace najdete v článku [škálování samostatných clusterů](service-fabric-cluster-scaling-standalone.md).

## <a name="upgrading"></a>Inovován

Samostatný cluster je prostředek, který zcela vlastníte. Zodpovídáte za opravy základního operačního systému a zahájení upgradu prostředků infrastruktury. Cluster můžete nastavit tak, aby přijímal automatické upgrady za běhu, když společnost Microsoft vydává novou verzi, nebo vybrat podporovanou verzi modulu runtime, kterou požadujete. Kromě upgradů prostředků infrastruktury můžete také opravovat operační systém a aktualizovat konfiguraci clusteru, jako jsou certifikáty nebo porty aplikací. 

Další informace najdete v článku [upgrade samostatných clusterů](service-fabric-cluster-upgrade-standalone.md).

## <a name="supported-operating-systems"></a>Podporované operační systémy

Můžete vytvářet clustery na virtuálních počítačích nebo počítačích, na kterých běží tyto operační systémy (Linux zatím není podporovaný):

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [zabezpečení](service-fabric-cluster-security.md), [škálování](service-fabric-cluster-scaling-standalone.md)a [upgradu](service-fabric-cluster-upgrade-standalone.md) samostatných clusterů.

Přečtěte si o [možnostech podpory Service Fabric](service-fabric-support.md).

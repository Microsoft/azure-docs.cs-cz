---
title: Omezení pro fondy uzlů Windows serveru ve službě Azure Kubernetes Service (AKS)
description: Přečtěte si o známých omezeních při spouštění fondů uzlů Windows serveru a úloh aplikací ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 65c62324a27e8377a1cc9833595b15cf08c6c820
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298168"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuální omezení pro fondy uzlů Windows serveru a úlohy aplikací ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) můžete vytvořit fond uzlů, na kterém běží Windows Server jako hostovaný operační systém na uzlech. Tyto uzly můžou spouštět nativní aplikace typu kontejner pro Windows, jako jsou ty, které jsou postavené na .NET Framework. Vzhledem k zásadním rozdílům v tom, jak operační systémy Linux a Windows poskytují podporu kontejnerů, nejsou aktuálně pro fondy uzlů Windows k dispozici některé běžné funkce související s Kubernetes a pod.

Tento článek popisuje některá omezení a koncepty operačního systému pro uzly Windows serveru v AKS. Fondy uzlů pro Windows Server jsou momentálně ve verzi Preview.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Které operační systémy Windows jsou podporovány?

AKS používá systém Windows Server 2019 jako verzi hostitelského operačního systému a podporuje pouze izolaci procesů. Image kontejneru vytvořené pomocí jiných verzí Windows serveru se nepodporují. [Kompatibilita Windows kontejneru verze][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Je Kubernetes v systému Windows a Linux odlišná?

Podpora fondu uzlů serveru oken zahrnuje některá omezení, která jsou součástí nadřazeného Windows serveru v projektu Kubernetes. Tato omezení nejsou specifická pro AKS. Další informace o této nadřazené podpoře pro Windows Server v Kubernetes naleznete v části [podporované funkce a omezení][upstream-limitations] [v článku Úvod do podpory Windows v dokumentu Kubernetes][intro-windows] , z projektu Kubernetes.

Kubernetes je historicky zaměřený na Linux. Mnohé příklady, které se používají na webu [Kubernetes.IO][kubernetes] pro odesílání dat, jsou určené pro použití v uzlech se systémem Linux. Při vytváření nasazení, která používají kontejnery Windows serveru, platí následující požadavky na úrovni operačního systému:

- **Identita** – Linux identifikuje uživatele pomocí celočíselného identifikátoru (UID). Uživatel má také alfanumerické uživatelské jméno pro přihlášení, které Linux překládá na UID uživatele. Podobně Linux identifikuje skupinu uživatelů pomocí identifikátoru celočíselné skupiny (GID) a překládá název skupiny na odpovídající GID.
    - Windows Server používá větší binární identifikátor zabezpečení (SID), který je uložený v databázi SAM (Windows Security Access Manager). Tato databáze není sdílená mezi hostitelem a kontejnery nebo mezi kontejnery.
- **Oprávnění k souborům** – Windows Server používá seznam řízení přístupu založený na identifikátorech SID, nikoli bitovou masku oprávnění a UID + GID.
- **Cesty k souborům** – konvence ve Windows serveru slouží jako místo/.
    - V části pod specifikacemi, které připojovat svazky, zadejte cestu pro kontejnery Windows serveru správně. Například místo přípojného bodu */mnt/Volume* v kontejneru Linux zadejte písmeno jednotky a umístění, jako je například */K/Volume* , které chcete připojit jako jednotku *k:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Jaký druh disků se podporuje pro Windows?

Disky Azure a soubory Azure jsou podporované typy svazků, ke kterým se dostanete jako svazky NTFS v kontejneru Windows serveru.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Můžu v AKS spouštět clustery jenom pro Windows?

Hlavní uzly (Řídicí rovina) v clusteru AKS jsou hostované AKS služby, nebudete k dispozici pro operační systém uzlů hostujících hlavní součásti. Všechny clustery AKS se vytvoří s výchozím prvním fondem uzlů, který je založený na systému Linux. Tento fond uzlů obsahuje systémové služby, které jsou potřeba ke fungování clusteru. Doporučujeme, abyste spustili aspoň dva uzly v prvním fondu uzlů, abyste zajistili spolehlivost clusteru a možnost provádět operace s clustery. První fond uzlů se systémem Linux nejde odstranit, pokud se neodstraní samotný cluster AKS.

## <a name="what-network-plug-ins-are-supported"></a>Jaké síťové moduly plug-in jsou podporované?

Clustery AKS s fondy uzlů Windows musí používat síťový model Azure CNI (rozšířené). Kubenet (základní) sítě se nepodporují. Další informace o rozdílech v síťových modelech najdete v tématu [Koncepty sítě pro aplikace v AKS][azure-network-models]. – Model sítě Azure CNI vyžaduje další plánování a předpoklady pro správu IP adres. Další informace o plánování a implementaci Azure CNI najdete v tématu [Konfigurace sítě Azure CNI v AKS][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Můžu změnit maximum. počet lusků na uzel?

V současné době je potřeba nastavit hodnotu na maximálně 30 lusků, aby se zajistila spolehlivost vašich clusterů.

## <a name="how-do-patch-my-windows-nodes"></a>Jak mám opravit uzly Windows?

Uzly Windows serveru v AKS se musí *upgradovat* , aby se získaly nejnovější opravy a aktualizace oprav. Aktualizace systému Windows nejsou na uzlech v AKS povoleny. AKS uvolní nové image fondu uzlů, jakmile jsou k dispozici opravy, je zákazníkům zodpovědný za upgrade fondů uzlů, aby zůstal aktuální na opravách a opravách hotfix. To platí také pro použitou verzi Kubernetes. Poznámky k verzi AKS budou označovat, že jsou k dispozici nové verze. Další informace o upgradu fondu uzlů Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

> [!NOTE]
> Aktualizovaná image Windows serveru se použije jenom v případě, že se před upgradem fondu uzlů provede upgrade clusteru (upgrade roviny ovládacího prvku).
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Návody otočit instanční objekt pro fond uzlů Windows?

Ve verzi Preview fondy uzlů Windows nepodporují otočení instančního objektu jako omezení verze Preview. Pokud chcete aktualizovat instanční objekt, vytvořte nový fond uzlů Windows a migrujte své lusky ze staršího fondu do nového. Až to dokončíte, odstraňte starší fond uzlů.

## <a name="how-many-node-pools-can-i-create"></a>Kolik fondů uzlů lze vytvořit?

Cluster AKS může mít maximálně 10 fondů uzlů. V těchto fondech uzlů můžete mít maximálně 1000 uzlů. [Omezení fondu uzlů][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Jak můžu pojmenovat fondy uzlů Windows?

Název musí být delší než 6 (šest) znaků. Toto je aktuální omezení AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Jsou všechny funkce podporované uzly Windows?

Zásady sítě a kubenet se v tuto chvíli nepodporují s uzly Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Můžu spustit řadiče příchozího přenosu dat v uzlech Windows?

Ano, řadič příchozího přenosu dat, který podporuje kontejnery Windows serveru, se může spouštět na uzlech Windows v AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Můžu použít Azure Dev Spaces s uzly Windows?

Azure Dev Spaces je aktuálně k dispozici pouze pro fondy uzlů se systémem Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Můžou kontejnery Windows serveru používat gMSA?

Podpora skupinových účtů spravované služby (gMSA) není v současné době k dispozici v AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Můžu použít Azure Monitor pro kontejnery s uzly a kontejnery Windows?

Ano, můžete ale Azure Monitor neshromažďuje protokoly (stdout) z kontejnerů Windows. Můžete se stále připojit k živému streamu protokolů stdout z kontejneru Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Co když potřebuji funkci, která není podporovaná?

V AKS pracujeme na zprovoznění všech funkcí, které v systému Windows potřebujete, ale pokud se setkáte, je open source projekt [AKS-Engine][aks-engine] jednoduchý a plně přizpůsobitelný způsob, jak spustit Kubernetes v Azure, včetně podpory Windows. Ujistěte se prosím, že máte přehled o funkcích, které připravujeme pro [AKS][aks-roadmap].

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s kontejnery Windows serveru v AKS, [vytvořte fond uzlů, na kterém běží Windows Server v AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909

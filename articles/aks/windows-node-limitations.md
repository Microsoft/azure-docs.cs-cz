---
title: Omezení fondů uzlů systému Windows Server
titleSuffix: Azure Kubernetes Service
description: Informace o známých omezeních při spuštění fondů uzlů Windows Server a úloh aplikací ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: 934acf06a779c1c3b0b13e74b196b174dd944e66
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886666"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Aktuální omezení pro fondy uzlů Windows Server a úlohy aplikací ve službě Azure Kubernetes Service (AKS)

Ve službě Azure Kubernetes Service (AKS) můžete vytvořit fond uzlů, který spouští Windows Server jako hostovaný operační systém na uzlech. Tyto uzly můžete spustit nativní aplikace kontejneru systému Windows, například ty, které jsou postaveny na rozhraní .NET Framework. Vzhledem k tomu, že existují velké rozdíly v tom, jak operační systém Linux a Windows poskytuje podporu kontejnerů, některé běžné funkce Kubernetes a pod související nejsou v současné době k dispozici pro fondy uzlů systému Windows.

Tento článek popisuje některá omezení a koncepty operačního systému pro uzly windows serveru v AKS. Fondy uzlů pro windows server jsou aktuálně ve verzi Preview.

> [!IMPORTANT]
> Funkce AKS preview jsou samoobslužné opt-in. Náhledy jsou poskytovány "tak, jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS Previews jsou částečně pokryty zákaznickou podporou na základě maximálního úsilí. Jako takové tyto funkce nejsou určeny pro produkční použití. Další informace naleznete v následujících článcích podpory:
>
> * [Zásady podpory AKS][aks-support-policies]
> * [Nejčastější dotazy k podpoře Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Které operační systémy Windows jsou podporovány?

AKS používá Windows Server 2019 jako verzi hostitelského operačního systému a podporuje pouze izolaci procesů. Image kontejnerů vytvořené pomocí jiných verzí systému Windows Server nejsou podporovány. [Kompatibilita verzí kontejneru systému Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Liší se Kubernetes ve Windows a Linuxu?

Podpora fondu uzlů Windows Server zahrnuje některá omezení, která jsou součástí projektu windows server v kubernetes. Tato omezení nejsou specifická pro AKS. Další informace o této upstream podporu pro Windows Server v Kubernetes, naleznete v části [podporované funkce a omezení][upstream-limitations] intro na windows podporu v dokumentu [Kubernetes,][intro-windows] z projektu Kubernetes.

Kubernetes je historicky zaměřen na Linux. Mnoho příkladů používaných v upstream [Kubernetes.io][kubernetes] webové stránky jsou určeny pro použití na linuxových uzlech. Při vytváření nasazení, která používají kontejnery Windows Server, platí následující důležité informace na úrovni operačního systému:

- **Identita** – Linux identifikuje uživatele podle identifikátoru uživatele celéčíslo (UID). Uživatel má také alfanumerické uživatelské jméno pro přihlášení, které Linux překládá na uživatelské rozhraní uživatele. Podobně Linux identifikuje skupinu uživatelů podle identifikátoru celé skupiny (GID) a převede název skupiny na odpovídající GID.
    - Systém Windows Server používá větší binární identifikátor zabezpečení (SID), který je uložen v databázi správce přístupu k zabezpečení systému Windows (SAM). Tato databáze není sdílena mezi hostitelem a kontejnery nebo mezi kontejnery.
- **Oprávnění k souborům** – systém Windows Server používá seznam řízení přístupu založený na sid, nikoli bitovou masku oprávnění a UID+GID
- **Cesty k souborům** – konvence v systému Windows Server je použití \ místo /.
    - Ve specifikacích podu, které připevňují svazky, zadejte správnou cestu pro kontejnery windows serveru. Například místo přípojného bodu */mnt/volume* v kontejneru Linuxu zadejte písmeno jednotky a umístění, například */K/Volume,* které chcete připojit jako jednotku *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Jaký druh disků jsou podporovány pro Windows?

Disky Azure a soubory Azure jsou podporované typy svazků, ke které se přistupuje jako svazky NTFS v kontejneru Windows Serveru.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Lze spustit pouze clustery Systému Windows v AKS?

Hlavní uzly (rovina řízení) v clusteru AKS jsou hostovány službou AKS, nebudete vystaveni operačnímu systému uzlů hostujících hlavní součásti. Všechny clustery AKS jsou vytvořeny s výchozím fondem prvních uzlů, který je založen na Linuxu. Tento fond uzlů obsahuje systémové služby, které jsou potřebné pro fungování clusteru. Doporučujeme spustit alespoň dva uzly v prvním fondu uzlů, aby byla zajištěna spolehlivost clusteru a schopnost operací clusteru. První fond uzlů založený na Linuxu nelze odstranit, dokud není odstraněn samotný cluster AKS.

## <a name="what-network-plug-ins-are-supported"></a>Jaké síťové moduly plug-in jsou podporovány?

Clustery AKS s fondy uzlů Windows musí používat model sítě Azure CNI (advanced). Kubenet (základní) síť není podporována. Další informace o rozdílech v síťových modelech naleznete v [tématu Síťové koncepty pro aplikace v AKS][azure-network-models]. - Model sítě Azure CNI vyžaduje další plánování a aspekty pro správu IP adres. Další informace o plánování a implementaci Azure CNI najdete [v tématu Konfigurace azure cni sítě v AKS][configure-azure-cni].

## <a name="can-i-change-the-max--of-pods-per-node"></a>Mohu změnit max. # lusků na uzel?

Ano. Důsledky a možnosti, které jsou k dispozici, naleznete [v tématu Maximální počet podů][maximum-number-of-pods].

## <a name="how-do-patch-my-windows-nodes"></a>Jak opravuje uzly systému Windows?

Uzly systému Windows Server v Systému AKS musí být *upgradovány,* aby bylo možné získat nejnovější opravy a aktualizace. Aktualizace systému Windows nejsou povoleny na uzlech v AKS. AKS vydává nové image fondu uzlů, jakmile jsou k dispozici opravy, je odpovědností zákazníků upgradovat fondy uzlů, aby zůstali aktuální na opravách oprav a oprav hotfix. To platí i pro používanou verzi Kubernetes. Poznámky k verzi AKS budou označovat, kdy jsou k dispozici nové verze. Další informace o inovaci fondu uzlů systému Windows Server naleznete [v tématu Upgrade fondu uzlů v AKS][nodepool-upgrade].

> [!NOTE]
> Aktualizovaná bitová kopie systému Windows Server bude použita pouze v případě, že byl před upgradem fondu uzlů proveden upgrade clusteru (upgrade roviny ovládacího prvku).
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Jak lze otočit instanční objekt pro fond uzlů systému Windows?

Během náhledu fondy uzlů systému Windows nepodporují střídání instancí služby jako omezení náhledu. Chcete-li aktualizovat instanční objekt, vytvořte nový fond uzlů systému Windows a migrujte pody ze staršího fondu do nového. Po dokončení odstraňte fond starších uzlů.

## <a name="how-many-node-pools-can-i-create"></a>Kolik fondů uzlů mohu vytvořit?

Cluster AKS může mít maximálně 10 fondů uzlů. Můžete mít maximálně 1000 uzlů v rámci těchto fondů uzlů. [Omezení fondu uzlů][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Jak mohu pojmenovat fondy uzlů systému Windows?

Název je třeba zachovat maximálně na 6 (šest) znaků. Toto je aktuální omezení AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Jsou všechny funkce podporovány uzly systému Windows?

Síťové zásady a kubenet nejsou aktuálně podporovány uzly systému Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Můžu v uzlech Windows spustit řadiče příchozího přenosu dat?

Ano, řadič příchozího přenosu dat, který podporuje kontejnery systému Windows Server, může být spuštěn v uzlech systému Windows v systému AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Můžu používat Azure Dev Spaces s uzly Windows?

Azure Dev Spaces je momentálně dostupná jenom pro fondy uzlů založených na Linuxu.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Mohou kontejnery Windows Serveru používat gMSA?

Podpora skupinových účtů spravovaných služeb (gMSA) není v současné době v AKS k dispozici.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Můžu azure monitor použít pro kontejnery s uzly a kontejnery Windows?

Ano, můžete, ale Azure Monitor neshromažďuje protokoly (stdout) z kontejnerů windows. Stále můžete připojit k živému proudu protokolů stdout z kontejneru systému Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Co když potřebuji funkci, která není podporována?

Usilovně pracujeme na tom, abychom do Windows v AKS přinesli všechny funkce, které potřebujete, ale pokud narazíte na mezery, projekt open source a upstream [aks-engine][aks-engine] poskytuje snadný a plně přizpůsobitelný způsob, jak provozovat Kubernetes v Azure, včetně podpory Windows. Prosím, ujistěte se, podívejte se na náš plán funkcí přichází [AKS plán][aks-roadmap].

## <a name="next-steps"></a>Další kroky

Chcete-li začít s kontejnery Windows Server v AKS, [vytvořte fond uzlů, který spouští systém Windows Server v AKS][windows-node-cli].

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
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node

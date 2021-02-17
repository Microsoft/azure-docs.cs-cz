---
title: Nejčastější dotazy týkající se fondů uzlů Windows serveru
titleSuffix: Azure Kubernetes Service
description: Na nejčastějších dotazech se můžete podívat, když ve službě Azure Kubernetes Service (AKS) spouštíte fondy uzlů Windows serveru a úlohy aplikací.
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: b20ebe82556bb4db6844511ec0953f4d4e75f383
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574728"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Nejčastější dotazy týkající se fondů uzlů Windows serveru v AKS

Ve službě Azure Kubernetes Service (AKS) můžete vytvořit fond uzlů, na kterém běží Windows Server jako hostovaný operační systém na uzlech. Tyto uzly můžou spouštět nativní aplikace typu kontejner pro Windows, jako jsou ty, které jsou postavené na .NET Framework. Existují rozdíly v tom, jak operační systémy Linux a Windows poskytují podporu kontejnerů. Některé běžné funkce pro Linux Kubernetes a pod ním nejsou aktuálně k dispozici pro fondy uzlů Windows.

Tento článek popisuje některé z nejčastějších dotazů a konceptů operačního systému pro uzly Windows serveru v AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Které operační systémy Windows jsou podporovány?

AKS používá systém Windows Server 2019 jako verzi hostitelského operačního systému a podporuje pouze izolaci procesů. Image kontejneru vytvořené pomocí jiných verzí Windows serveru se nepodporují. Další informace najdete v tématu [Kompatibilita verzí kontejnerů Windows][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Je Kubernetes v systému Windows a Linux odlišná?

Podpora fondu uzlů serveru oken zahrnuje některá omezení, která jsou součástí nadřazeného Windows serveru v projektu Kubernetes. Tato omezení nejsou specifická pro AKS. Další informace o této nadřazené podpoře pro Windows Server v Kubernetes naleznete v části [podporované funkce a omezení][upstream-limitations] [v článku Úvod do podpory Windows v dokumentu Kubernetes][intro-windows] , z projektu Kubernetes.

Kubernetes je historicky zaměřený na Linux. Mnohé příklady, které se používají na webu [Kubernetes.IO][kubernetes] pro odesílání dat, jsou určené pro použití v uzlech se systémem Linux. Při vytváření nasazení, která používají kontejnery Windows serveru, platí následující požadavky na úrovni operačního systému:

- **Identita** – Linux identifikuje uživatele pomocí celočíselného identifikátoru (UID). Uživatel má také alfanumerické uživatelské jméno pro přihlášení, které Linux překládá na UID uživatele. Podobně Linux identifikuje skupinu uživatelů pomocí identifikátoru celočíselné skupiny (GID) a překládá název skupiny na odpovídající GID.
    - Windows Server používá větší binární identifikátor zabezpečení (SID), který je uložený v databázi SAM (Windows Security Access Manager). Tato databáze není sdílená mezi hostitelem a kontejnery nebo mezi kontejnery.
- **Oprávnění k souborům** – Windows Server používá seznam řízení přístupu založený na identifikátorech SID, nikoli bitovou masku oprávnění a UID + GID.
- **Cesty k souborům** – konvence ve Windows serveru slouží jako místo/.
    - V části pod specifikacemi, které připojovat svazky, zadejte cestu pro kontejnery Windows serveru správně. Například místo přípojného bodu */mnt/Volume* v kontejneru Linux zadejte písmeno jednotky a umístění, jako je například */K/Volume* , které chcete připojit jako jednotku *k:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Jaký druh disků se podporuje pro Windows?

Disky Azure a soubory Azure jsou podporované typy svazků. K těmto souborům se dostanete v kontejneru Windows serveru jako svazky NTFS.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Můžu v AKS spouštět clustery jenom pro Windows?

Hlavní uzly (Řídicí rovina) v clusteru AKS jsou hostované AKS služby, nebudete k dispozici pro operační systém uzlů hostujících hlavní součásti. Všechny clustery AKS se vytvářejí s výchozím prvním fondem uzlů, který je založený na systému Linux. Tento fond uzlů obsahuje systémové služby, které jsou potřeba ke fungování clusteru. Doporučujeme, abyste spustili aspoň dva uzly v prvním fondu uzlů, abyste zajistili spolehlivost clusteru a možnost provádět operace s clustery. První fond uzlů se systémem Linux nejde odstranit, pokud se neodstraní samotný cluster AKS.

## <a name="how-do-i-patch-my-windows-nodes"></a>Návody opravit moje uzly Windows?

Chcete-li získat nejnovější opravy pro uzly systému Windows, můžete buď [upgradovat fond uzlů][nodepool-upgrade] , nebo [upgradovat bitovou kopii uzlu][upgrade-node-image]. Aktualizace systému Windows nejsou na uzlech v AKS povoleny. AKS uvolní nové image fondu uzlů, jakmile jsou k dispozici opravy, a je zodpovědností uživatele při upgradu fondů uzlů, aby zůstal aktuální na opravách a opravách hotfix. To platí také pro použitou verzi Kubernetes. [Poznámky k verzi AKS][aks-release-notes] označují, že jsou k dispozici nové verze. Další informace o upgradu celého fondu uzlů Windows serveru najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade]. Pokud vás zajímá pouze aktualizace image uzlu, přečtěte si téma [upgrady imagí uzlu AKS][upgrade-node-image].

> [!NOTE]
> Aktualizovaná bitová kopie systému Windows Server bude použita pouze v případě, že byl proveden upgrade clusteru (upgrade plochy ovládacího prvku) před upgradem fondu uzlů.
>

## <a name="what-network-plug-ins-are-supported"></a>Jaké síťové moduly plug-in jsou podporované?

Clustery AKS s fondy uzlů Windows musí používat síťový model Azure CNI (rozšířené). Kubenet (základní) sítě se nepodporují. Další informace o rozdílech v síťových modelech najdete v tématu [Koncepty sítě pro aplikace v AKS][azure-network-models]. Model sítě Azure CNI vyžaduje další plánování a předpoklady pro správu IP adres. Další informace o plánování a implementaci Azure CNI najdete v tématu [Konfigurace sítě Azure CNI v AKS][configure-azure-cni].

## <a name="is-preserving-the-client-source-ip-supported"></a>Zachovává se podpora zdrojové IP adresy klienta?

V současné době [zachování IP adresy zdrojového klienta][client-source-ip] není u uzlů Windows podporováno.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Můžu změnit max. # lusků na uzel?

Ano. Důsledky a možnosti, které jsou k dispozici, najdete v části [maximální počet lusků][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Proč se mi při pokusu o vytvoření nového fondu agentů Windows zobrazuje chyba?

Pokud jste cluster vytvořili před únorem 2020 a nikdy jste neudělali žádné operace upgradu clusteru, cluster stále používá starou bitovou kopii systému Windows. Možná jste viděli chybu, která se podobá:

"Následující seznam imagí, na které odkazuje šablona nasazení, nebyl nalezen: Publisher: MicrosoftWindowsServer, nabídka: WindowsServer, SKU: 2019-Datacenter-Core-smalldisk-2004, verze: nejnovější. https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimagePokyny k hledání dostupných imagí najdete v tématu.

Oprava této chyby:

1. Upgradujte [rovinu řízení clusteru][upgrade-cluster-cp] , aby se aktualizovala nabídka Image a vydavatel.
1. Vytvořte nové fondy agentů Windows.
1. Přesuňte Windows lusky z existujících fondů agentů Windows na nové fondy agentů Windows.
1. Odstraňte staré fondy agentů Windows.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Návody otočit instanční objekt pro fond uzlů Windows?

Fondy uzlů Windows nepodporují otočení objektu Service. Pokud chcete aktualizovat instanční objekt, vytvořte nový fond uzlů Windows a migrujte své lusky ze staršího fondu do nového. Až to dokončíte, odstraňte starší fond uzlů.

Místo toho používejte spravované identity, které jsou v podstatě obálkami objektů služby. Další informace najdete v tématu [použití spravovaných identit ve službě Azure Kubernetes][managed-identity].

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

Ano, můžete ale Azure Monitor ve verzi Public Preview pro shromažďování protokolů (stdout, stderr) a metriky z kontejnerů Windows. Můžete se také připojit k živému streamu protokolů stdout z kontejneru Windows.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Existují nějaká omezení počtu služeb v clusteru s uzly Windows?

Cluster s uzly Windows může mít přibližně 500 služeb před tím, než dojde k vyčerpání portů.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Můžu použít Zvýhodněné hybridní využití Azure s uzly Windows?

Ano. Zvýhodněné hybridní využití Azure pro Windows Server snižuje provozní náklady tím, že vám umožní převést místní licenci Windows serveru na AKS uzly Windows.

Zvýhodněné hybridní využití Azure lze použít na celém clusteru AKS nebo na jednotlivých uzlech. Pro jednotlivé uzly je potřeba přejít do [skupiny prostředků uzlu][resource-groups] a použít zvýhodněné hybridní využití Azure na uzly přímo. Další informace o použití Zvýhodněné hybridní využití Azure u jednotlivých uzlů najdete v tématu [zvýhodněné hybridní využití Azure pro Windows Server][hybrid-vms]. 

Pokud chcete použít Zvýhodněné hybridní využití Azure na novém clusteru AKS, použijte `--enable-ahub` argument.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Pokud chcete použít Zvýhodněné hybridní využití Azure v existujícím clusteru AKS, aktualizujte cluster pomocí `--enable-ahub` argumentu.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Chcete-li zjistit, zda je v clusteru nastavena Zvýhodněné hybridní využití Azure, použijte následující příkaz:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Pokud je cluster Zvýhodněné hybridní využití Azure povolený, výstup `az vmss show` bude podobný následujícímu:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Můžu použít webový řídicí panel Kubernetes s kontejnery Windows?

Ano, pomocí [webového řídicího panelu Kubernetes][kubernetes-dashboard] můžete získat přístup k informacím o kontejnerech Windows, ale v tuto chvíli nemůžete spustit *kubectl exec* do běžícího kontejneru Windows přímo z webového řídicího panelu Kubernetes. Další podrobnosti o připojení ke spuštěnému kontejneru Windows najdete v tématu [připojení pomocí protokolu RDP ke službě Azure Kubernetes (AKS) clustery Windows serveru pro účely údržby nebo řešení potíží][windows-rdp].

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>Co když potřebuji funkci, která není podporovaná?

V AKS pracujeme na zprovoznění všech funkcí, které v systému Windows potřebujete, ale pokud se setkáte, je open source projekt [AKS-Engine][aks-engine] jednoduchý a plně přizpůsobitelný způsob, jak spustit Kubernetes v Azure, včetně podpory Windows. Ujistěte se, že máte přehled o funkcích, které připravujeme pro [AKS][aks-roadmap].

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s kontejnery Windows serveru v AKS, [vytvořte fond uzlů, na kterém běží Windows Server v AKS][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks

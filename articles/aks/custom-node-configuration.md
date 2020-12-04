---
title: Přizpůsobení konfigurace uzlu pro fondy uzlů Azure Kubernetes Service (AKS) (Preview)
description: Naučte se, jak přizpůsobit konfiguraci v uzlech clusteru Azure Kubernetes Service (AKS) a fondech uzlů.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: f1e9d65baacb9c712b92ef6f00abda169031b47e
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581869"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Přizpůsobení konfigurace uzlů pro fondy uzlů Azure Kubernetes Service (AKS) (Preview)

Přizpůsobení konfigurace uzlu umožňuje nakonfigurovat nebo ladit nastavení operačního systému (OS) nebo parametry kubelet tak, aby odpovídaly potřebám zatížení. Když vytvoříte cluster AKS nebo do svého clusteru přidáte fond uzlů, můžete přizpůsobit podmnožinu běžně používaných nastavení operačního systému a kubelet. Pokud chcete nakonfigurovat nastavení nad rámec této podmnožiny, [použijte k přizpůsobení potřebných konfigurací sadu démonů, aniž byste ztrátě AKS podporu pro vaše uzly](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registrace `CustomNodeConfigPreview` funkce Preview

Pokud chcete vytvořit cluster AKS nebo fond uzlů, který může přizpůsobit parametry kubelet nebo nastavení operačního systému, musíte `CustomNodeConfigPreview` u svého předplatného povolit příznak funkce.

Příznak funkce Zaregistrujte `CustomNodeConfigPreview` pomocí příkazu [AZ Feature Register][az-feature-register] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Zobrazení stavu v *registraci* trvá několik minut. Pomocí příkazu [AZ Feature list][az-feature-list] ověřte stav registrace:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Až budete připraveni, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Instalace rozšíření rozhraní příkazového řádku aks-preview

Pokud chcete vytvořit cluster AKS nebo fond uzlů, který může přizpůsobit parametry kubelet nebo nastavení operačního systému, budete potřebovat nejnovější rozšíření Azure CLI *AKS Preview* . Nainstalujte rozšíření Azure CLI *AKS-Preview* pomocí příkazu [AZ Extension Add][az-extension-add] . Nebo nainstalujte jakékoli dostupné aktualizace pomocí příkazu [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Použít vlastní konfiguraci uzlu

### <a name="kubelet-custom-configuration"></a>Vlastní konfigurace Kubelet

Podporované parametry Kubelet a přijaté hodnoty jsou uvedeny níže.

| Parametr | Povolené hodnoty/interval | Výchozí | Popis |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | žádná, statická | Žádná | Statické zásady umožňují kontejnerům v [garantovaných luskech](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) s CELOČÍSELNým procesorem požádá o přístup k exkluzivním procesorům na uzlu. |
| `cpuCfsQuota` | true, false | true |  Povolí nebo zakáže vynucení kvót CFS procesoru pro kontejnery, které určují omezení procesoru. | 
| `cpuCfsQuotaPeriod` | Interval v milisekundách (MS) | `100ms` | Nastaví hodnotu CFS období kvóty procesoru. | 
| `imageGcHighThreshold` | 0-100 | 85 | Procento využití disku, po kterém je vždy spuštěno uvolňování paměti imagí. Minimální využití **disku, které spustí** uvolňování paměti. Pro zakázání uvolňování paměti imagí nastavte na 100. | 
| `imageGcLowThreshold` | 0-100, není vyšší než `imageGcHighThreshold` | 80 | Procento využití disku před tím, než se nespustí image pro uvolnění paměti. Minimální využití disku, které **může** aktivovat uvolňování paměti. |
| `topologyManagerPolicy` | žádné, nejvyšší úsilí, omezení, jeden uzel NUMA-Node | Žádná | Optimalizace zarovnání uzlů NUMA, další informace najdete [tady](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Pouze Kubernetes v 1.18 +. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Žádné | Povolený seznam nebezpečných sysctls nebo nebezpečných vzorů sysctl. | 

### <a name="linux-os-custom-configuration"></a>Vlastní konfigurace operačního systému Linux

Podporovaná nastavení operačního systému a přijaté hodnoty jsou uvedené níže.

#### <a name="file-handle-limits"></a>Omezení popisovačů souborů

Když obsluhuje spoustu provozu, je běžné, že provoz, který zadáváte, pochází z velkého počtu místních souborů. Můžete vylepšit níže uvedená nastavení jádra a předdefinovaná omezení, která vám umožní zvládnout víc za cenu některé systémové paměti.

| Nastavení | Povolené hodnoty/interval | Výchozí | Popis |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 – 12000500 | 709620 | Maximální počet souborů – popisovačů, které bude přidělovat jádro systému Linux, zvýšením této hodnoty můžete zvýšit maximální povolený počet otevřených souborů. |
| `fs.inotify.max_user_watches` | 781250 – 2097152 | 1048576 | Maximální počet hodinky souborů povolených systémem. Každé *sledování* je zhruba 90 bajtů v 32 jádru a zhruba 160 bajtů v 64 případě 16bitového jádra. | 
| `fs.aio-max-nr` | 65536 – 6553500 | 65536 | AIO-Nr zobrazuje aktuální počet asynchronních vstupně-výstupních požadavků v rámci systému. AIO-Max-Nr umožňuje změnit maximální hodnotu AIO-NR, která může růst. |
| `fs.nr_open` | 8192 – 20000500 | 1048576 | Maximální počet souborových popisovačů, které může proces přidělit. |


#### <a name="socket-and-network-tuning"></a>Vyladění soketu a sítě

U uzlů agentů, u kterých se očekává, že budou zpracovávat velmi velký počet souběžných relací, můžete použít podmnožinu možností TCP a sítě níže, kterou můžete upravit na jeden fond uzlů. 

| Nastavení | Povolené hodnoty/interval | Výchozí | Popis |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 – 3240000 | 16384 | Maximální počet žádostí o připojení, které se dají zařadit do fronty pro kterýkoli příslušný naslouchací soket. Horní limit hodnoty parametru nevyřízených položek předaných funkci [naslouchání (2)](http://man7.org/linux/man-pages/man2/listen.2.html) . Pokud je argument nevyřízených položek větší než `somaxconn` , pak se tiše zkrátí na tento limit.
| `net.core.netdev_max_backlog` | 1000 – 3240000 | 1000 | Maximální počet paketů zařazených do fronty na vstupní straně, když rozhraní přijímá pakety rychleji než jádra, může je zpracovat. |
| `net.core.rmem_max` | 212992 – 134217728 | 212992 | Maximální velikost vyrovnávací paměti soketu pro příjem v bajtech |
| `net.core.wmem_max` | 212992 – 134217728 | 212992 | Maximální velikost vyrovnávací paměti soketu pro odesílání v bajtech | 
| `net.core.optmem_max` | 20480 – 4194304 | 20480 | Maximální velikost pomocné vyrovnávací paměti (možnost vyrovnávací paměti možností) povolená na jeden soket. Paměť možností soketu se v několika případech používá k ukládání dalších struktur, které se týkají využití soketu. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 – 3240000 | 16384 | Maximální počet požadavků na připojení zařazených do fronty, které ještě neobdržely potvrzení od připojujícího se klienta. Pokud se toto číslo překročí, jádro zahájí vyřazení požadavků. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 – 1440000 | 32768 | Maximální počet `timewait` soketů, které systém uchovává současně. Pokud je toto číslo překročeno, je čas-čekací soket okamžitě zničen a je vytištěno upozornění. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Doba, po kterou osamocené připojení (už na něj neodkazuje žádné aplikace) zůstane ve stavu FIN_WAIT_2, než se ukončí na místním konci. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Jak často protokol TCP posílá `keepalive` zprávy `keepalive` , když je povolený. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Počet testů `keepalive` , které TCP odesílá, dokud nerozhodnete, že je připojení přerušeno. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Jak často se sondy odesílají. Vynásobeno tím je `tcp_keepalive_probes` čas ukončit připojení, které nereaguje, po spuštění testu. | 
| `net.ipv4.tcp_tw_reuse` | 0 nebo 1 | 0 | Umožňuje znovu použít `TIME-WAIT` sokety pro nová připojení, když je bezpečná z hlediska protokolu. | 
| `net.ipv4.ip_local_port_range` | First: 1024-60999 a poslední: 32768-65000] | První: 32768 a poslední: 60999 | Rozsah místních portů, který používají přenosy TCP a UDP k výběru místního portu. Skládá se ze dvou čísel: první číslo je první místní port povolený pro přenosy TCP a UDP v uzlu agent, druhým je poslední číslo místního portu. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 – 80000 | 4 096 | Minimální počet položek, které mohou být v mezipaměti ARP. Uvolňování paměti se neaktivuje, pokud je počet položek pod tímto nastavením. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 – 90000 | 8192 | Částečný maximální počet položek, které mohou být v mezipaměti ARP. Toto nastavení je pravděpodobně nejdůležitější, protože po dosažení tohoto softwarového maxima bude shromažďování paměti protokolu ARP po dobu 5 sekund aktivované. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 – 100000 | 16384 | Maximální počet položek v mezipaměti protokolu ARP. |
| `net.netfilter.nf_conntrack_max` | 131072 – 589824 | 131072 | `nf_conntrack` je modul, který sleduje položky připojení pro překlad adres (NAT) v systému Linux. `nf_conntrack`Modul používá tabulku hash k záznamu *vytvořeného* záznamu o připojení protokolu TCP. `nf_conntrack_max` je maximální počet uzlů v zatřiďovací tabulce, tedy maximální počet připojení, který modul podporuje, `nf_conntrack` nebo velikost tabulky pro sledování připojení. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 – 147456 | 65536 | `nf_conntrack` je modul, který sleduje položky připojení pro překlad adres (NAT) v systému Linux. `nf_conntrack`Modul používá tabulku hash k záznamu *vytvořeného* záznamu o připojení protokolu TCP. `nf_conntrack_buckets` je velikost zatřiďovací tabulky. | 

#### <a name="worker-limits"></a>Omezení pracovních procesů

Podobně jako u omezení deskriptorů souborů je počet pracovních procesů nebo vláken, které proces může vytvořit, omezený nastavením jádra i omezeními uživatele. Omezení počtu uživatelů na AKS je neomezené. 

| Nastavení | Povolené hodnoty/interval | Výchozí | Popis |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Procesy mohou aktivovat pracovní vlákna. Maximální počet všech vláken, která lze vytvořit, je nastaven s nastavením jádra `kernel.threads-max` . | 

#### <a name="virtual-memory"></a>Virtuální paměť

Níže uvedená nastavení lze použít k ladění provozu subsystému virtuálního počítače v jádru Linux a nezměněných `writeout` dat na disk.

| Nastavení | Povolené hodnoty/interval | Výchozí | Popis |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 – 262144 | 65530 | Tento soubor obsahuje maximální počet oblastí rozvržení paměti, které může proces mít. Oblasti mapy paměti se používají jako vedlejší účinky volání `malloc` , přímo pomocí, a a `mmap` `mprotect` `madvise` také při načítání sdílených knihoven. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Tato procentuální hodnota určuje, jakou má jádro jádra uvolnit paměť, která se používá pro ukládání objektů Directory a inode do mezipaměti. |
| `vm.swappiness` | 0 - 100 | 60 | Tento ovládací prvek slouží k definování způsobu, jakým bude jádro měnit paměťové stránky. Vyšší hodnoty zvyšují agresivní, nižší hodnoty snižují množství swapu. Hodnota 0 vydá pokyn k tomu, aby jádro nezahájilo prohození, dokud není množství volných stránek a stránkovacího souboru menší než horní značka v zóně. | 
| `swapFileSizeMB` | 1 MB-velikost [dočasného disku](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Žádné | SwapFileSizeMB určuje velikost odkládacího souboru v MEGABAJTech, které se vytvoří v uzlech agenta z tohoto fondu uzlů. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparentní Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) je funkce jádra pro Linux, která má za cíl zlepšit výkon tím, že efektivně využívá hardware pro mapování paměti vašeho procesoru. Pokud je tato možnost povolená, pokusí se jádro přidělit `hugepages` , kdykoli je to možné, a pokud je tato `mmap` oblast 2 MB, bude mít na 2 MB stránky. V některých případech `hugepages` , kdy je povolený systém, můžou aplikace ukončit přidělování více paměťových prostředků. Aplikace může `mmap` mít velkou oblast, ale jenom 1 bajt, v takovém případě může místo stránky 4k přidělit stránku 2 MB, a to z nějakého důvodu. V tomto scénáři je možné zakázat `hugepages` systém nebo ho pouze v `MADV_HUGEPAGE madvise` oblastech. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Tato hodnota určuje, jestli se má jádro vydávat s lepším využitím komprimace paměti, aby se zajistilo větší `hugepages` dostupnost. | 

> [!IMPORTANT]
> Pro usnadnění vyhledávání a čitelnosti se nastavení operačního systému zobrazí v tomto dokumentu podle názvu, ale měla by být přidána do konfiguračního souboru JSON nebo do rozhraní AKS API pomocí [konvence CamelCase pro psaní velkých písmen](https://docs.microsoft.com/dotnet/standard/design-guidelines/capitalization-conventions).

Vytvořte `kubeletconfig.json` soubor s následujícím obsahem:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Vytvořte `linuxosconfig.json` soubor s následujícím obsahem:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Vytvořte nový cluster s kublet a konfiguracemi operačního systému pomocí souborů JSON, které jste vytvořili v předchozím kroku. 

> [!NOTE]
> Při vytváření clusteru můžete zadat konfiguraci kubelet, konfiguraci operačního systému nebo obojí. Pokud určíte konfiguraci při vytváření clusteru, bude tato konfigurace použita pouze u uzlů v počátečním fondu uzlů. Všechna nastavení, která nejsou nakonfigurovaná v souboru JSON, budou uchovávat výchozí hodnotu.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Přidejte nový fond uzlů s určením parametrů Kubelet pomocí souboru JSON, který jste vytvořili.

> [!NOTE]
> Když přidáte fond uzlů do existujícího clusteru, můžete zadat konfiguraci kubelet, konfiguraci operačního systému nebo obojí. Pokud určíte konfiguraci při přidávání fondu uzlů, bude tato konfigurace použita pouze u uzlů v novém fondu uzlů. Všechna nastavení, která nejsou nakonfigurovaná v souboru JSON, budou uchovávat výchozí hodnotu.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si, [jak nakonfigurovat cluster AKS](cluster-configuration.md).
- Přečtěte si, jak [upgradovat image uzlů](node-image-upgrade.md) v clusteru.
- Přečtěte si téma [upgrade clusteru Azure Kubernetes Service (AKS)](upgrade-cluster.md) , kde se dozvíte, jak upgradovat cluster na nejnovější verzi Kubernetes.
- V seznamu [nejčastějších dotazů k AKS](faq.md) najdete odpovědi na některé běžné otázky týkající se AKS.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
---
title: ZASTARALÉ Azure Container Service – Nejčastější dotazy
description: Odpovědi na nejčastější dotazy týkající se Azure Container Service, služby, která zjednodušuje vytváření, konfiguraci a správu clusterů virtuálních počítačů pro spouštění aplikací typu kontejner Docker.
services: container-service
author: dlepow
manager: gwallace
ms.service: container-service
ms.topic: article
ms.date: 08/30/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f031f55bbff534181459bb1bf7469f315aee27cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75887780"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>ZASTARALÉ Nejčastější dotazy ke službě kontejneru

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

## <a name="orchestrators"></a>Orchestrátory

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Jaké orchestrátory kontejneru podporujete ve službě Azure Container Service? 

Podporuje se open source DC/OS, Docker Swarm a Kubernetes. Další informace najdete v tématu [Přehled](container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Podporujete režim Docker Swarm? 

Režim Swarm v současné době není podporován, ale do budoucna se plánuje. 

### <a name="does-azure-container-service-support-windows-containers"></a>Podporuje služba Azure Container Service kontejnery Windows?  

Aktuálně jsou podporovány kontejnery Linux se všemi orchestrátory. Podpora pro Windows kontejnery s Kubernetes je ve verzi Preview.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Doporučujete ve službě Azure Container Service použití konkrétního orchestrátoru? 
Obecně nedoporučujeme konkrétní orchestrator. Pokud máte zkušenosti s některým z podporovaných orchestrátorů, můžete je využít ve službě Azure Container Service. Trendy v datech nicméně naznačují, že systém DC/OS se v produkčním prostředí osvědčil pro úlohy s velkým objemem dat a úlohy IoT, Kubernetes se skvěle hodí pro úlohy nativní pro cloud a Docker Swarm je známý svou integrací s nástroji Dockeru a jednoduchostí osvojování.

V závislosti na scénáři můžete také vytvořit a spravovat vlastní řešení kontejnerů pomocí dalších služeb Azure. Mezi tyto služby patří [Virtual Machines](../../virtual-machines/linux/overview.md), [Service Fabric](../../service-fabric/service-fabric-overview.md), [Web Apps](../../app-service/overview.md) a [Batch](../../batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Jaký je rozdíl mezi službou Azure Container Service a modulem ACS? 
Azure Container Service je služba Azure, na kterou se vztahuje smlouva SLA, s funkcemi na webu Azure Portal, nástroji příkazového řádku Azure a rozhraními API Azure. Služba umožňuje rychlou implementaci a správu clusterů se standardními nástroji pro orchestraci kontejnerů a poskytuje poměrně málo možností konfigurace. 

[Modul ACS](https://github.com/Azure/acs-engine) je open source projekt, který umožňuje zkušeným uživatelům přizpůsobení konfigurace clusteru na všech úrovních. Možnost změny konfigurace infrastruktury i softwaru znamená, že pro modul ACS nenabízíme žádnou smlouvu SLA. Podpora probíhá prostřednictvím open source projektu na GitHubu, nikoli přes oficiální kanály Microsoftu. 

Další podrobnosti najdete v našich [zásadách podpory pro kontejnery](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers).

## <a name="cluster-management"></a>Správa clusteru

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Jak pro cluster vytvořím klíče SSH?

Můžete použít standardní nástroje vašeho operačního systému a vytvořit pro cluster pár veřejného a privátního klíče SSH RSA pro ověřování u virtuálních počítačů. Pokyny najdete v doprovodných materiálech k [OS X a Linuxu](../../virtual-machines/linux/mac-create-ssh-keys.md) nebo [Windows](../../virtual-machines/linux/ssh-from-windows.md). 

Pokud k nasazení clusteru služby kontejneru použijete příkazy rozhraní příkazového řádku Azure, je možné klíče SSH pro cluster vygenerovat automaticky.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Jak vytvořím instanční objekt pro cluster Kubernetes?

K vytvoření clusteru Kubernetes ve službě Azure Container Service je potřeba také ID a heslo instančního objektu služby Azure Active Directory. Další informace najdete v tématu [o instančním objektu pro cluster Kubernetes](../../container-service/kubernetes/container-service-kubernetes-service-principal.md).

Pokud k nasazení clusteru Kubernetes použijete [příkazy rozhraní příkazového řádku Azure](../../container-service/dcos-swarm/container-service-create-acs-cluster-cli.md), je možné přihlašovací údaje instančního objektu pro cluster vygenerovat automaticky.

### <a name="how-large-a-cluster-can-i-create"></a>Jak velký cluster můžu vytvořit?
Můžete vytvořit cluster s 1, 3 nebo 5 řídicími uzly. Můžete vybrat až 100 uzlů agentů.

> [!IMPORTANT]
> Pro větší clustery a v závislosti na velikosti virtuálního počítače, kterou jste vybrali pro uzly, může být v rámci vašeho předplatného potřeba zvýšit kvótu pro jádra. Chcete-li požádat o zvýšení kvóty, otevřete bezplatnou [online žádost o zákaznickou podporu](../../azure-portal/supportability/how-to-create-azure-support-request.md). Pokud používáte [bezplatný účet Azure](https://azure.microsoft.com/free/), můžete použít pouze omezený počet výpočetních jader Azure.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Jak se dá po vytvoření clusteru zvýšit počet hlavních serverů? 
Jakmile je cluster vytvořený, počet hlavních serverů je pevně daný a není možné jej změnit. Při vytváření clusteru byste v ideálním případě měli zvolit více hlavních serverů pro zajištění vysoké dostupnosti.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Jak se dá po vytvoření clusteru zvýšit počet agentů? 
Počet agentů v clusteru můžete škálovat pomocí webu Azure Portal nebo nástrojů příkazového řádku. Viz [Škálování clusteru Azure Container Service](../../container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Jaké jsou adresy URL hlavních serverů a agentů? 
Adresy URL prostředků clusteru ve službě Azure Container Service vycházejí z předpony názvu DNS, kterou jste zadali, a názvu oblasti Azure, kterou jste vybrali pro nasazení. Například plně kvalifikovaný název domény (FQDN) hlavního uzlu je v tomto formátu:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Běžně používané adresy URL pro váš cluster najdete na webu Azure Portal, v Průzkumníku prostředků Azure a dalších nástrojích Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Jak zjistím, která verze orchestrátoru je spuštěna v mém clusteru?

* DC/OS: Viz [Dokumentace Mesosphere](https://docs.mesosphere.com/1.7/usage/cli/command-reference/).
* Docker Swarm: Spusťte příkaz `docker version`.
* Kubernetes: Spusťte příkaz `kubectl version`.

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Jak můžu upgradovat orchestrátor po nasazení?

Azure Container Service v současné době nenabízí nástroje pro upgrade verze orchestrátoru, který jste nasadili v clusteru. Pokud služba Container Service podporuje novější verzi, můžete nasadit nový cluster. Další možností je použít k místnímu upgradu clusteru nástroje specifické pro orchestrátor, pokud jsou k dispozici. Podívejte se například na [Upgrade DC/OS](http://docs.mesosphere.com/1.12/installing/production/upgrading).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Kde najdu připojovací řetězec SSH k mému clusteru?

Připojovací řetězec najdete na webu Azure Portal nebo pomocí nástrojů příkazového řádku Azure. 

1. Na portálu přejděte do skupiny prostředků požadovaného nasazení clusteru.  

2. Klikněte na **Přehled** a potom v části **Základní údaje** klikněte na **Nasazení**. 

3. V okně **Historie nasazení** klikněte na nasazení, jehož název začíná na **microsoft-acs** a následuje datem nasazení. Příklad: microsoft-acs-201701310000.  

4. Na stránce **Souhrn** v části **Výstupy** je uvedeno několik odkazů na cluster. **SSHMaster0** představuje připojovací řetězec SSH k prvnímu hlavnímu serveru ve vašem clusteru služby Container Service. 

Jak bylo uvedeno výše, plně kvalifikovaný název domény hlavního serveru můžete najít také pomocí nástrojů Azure. Vytvořte připojení SSH k hlavnímu serveru pomocí plně kvalifikovaného názvu domény hlavního serveru a uživatelského jména, které jste zadali při vytváření clusteru. Příklad:

```bash
ssh userName@masterFQDN –A –p 22 
```

Další informace najdete v [Připojení ke clusteru služby Azure Container Service](../../container-service/kubernetes/container-service-connect.md).

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>Nefunguje mi překlad názvů DNS ve Windows. Co bych měl/a dělat?

V systému Windows existují známé problémy služby DNS, jejichž opravy jsou stále aktivně odebírány. Ujistěte se prosím, že používáte nejnovější aktualizovanou verzi služby ACS-Engine a Windows (s nainstalovanou [KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) a [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848) ), aby vaše prostředí mohlo využívat výhod. Jinak si přečtěte kroky pro zmírnění rizika v následující tabulce:

| Příznak DNS | Alternativní řešení  |
|-------------|-------------|
|Když je kontejner úloh nestabilní a dojde u něj k chybě, vyčistí se obor názvů sítě. | Znovu nasaďte všechny ovlivněné služby. |
| Přístup k virtuální IP adrese služby nefunguje. | Nakonfigurujte kontroler [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) tak, aby vždy udržoval spuštěný jeden normální (neprivilegovaný) pod. |
|Když se uzel, na kterém je kontejner spuštěný, stane nedostupným, dotazy DNS můžou selhat a vytvářet negativní položky mezipaměti. | Uvnitř ovlivněných kontejnerů spusťte následující: <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> Pokud se tím problém nevyřeší, zkuste zakázat ukládání do mezipaměti DNS úplně: <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>Další kroky

* [Další informace](../../container-service/kubernetes/container-service-intro-kubernetes.md) o službě Azure Container Service.
* Nasazení clusteru služby kontejneru pomocí [portálu](../../container-service/dcos-swarm/container-service-deployment.md) nebo [Azure CLI](../../container-service/dcos-swarm/container-service-create-acs-cluster-cli.md)

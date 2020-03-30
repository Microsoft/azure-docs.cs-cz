---
title: Instalace konzula ve službě Azure Kubernetes Service (AKS)
description: Přečtěte si, jak nainstalovat a použít konzula k vytvoření sítě služeb v clusteru Služby Azure Kubernetes (AKS).
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273742"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instalace a použití konzula ve službě Azure Kubernetes Service (AKS)

[Konzul][consul-github] je síť služeb s otevřeným zdrojovým kódem, která poskytuje sadu funkcí klíče napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří zjišťování služeb, kontrola stavu, segmentace služeb a pozorovatelnost. Další informace o konzulovi najdete v oficiální dokumentaci [Co je konzul?][consul-docs-concepts]

Tento článek ukazuje, jak nainstalovat konzula. Komponenty konzula jsou nainstalovány do clusteru Kubernetes na AKS.

> [!NOTE]
> Tyto pokyny odkazují `1.6.0`na verzi konzula `2.14.2`a používají alespoň verzi Helm .
>
> Verze Consul `1.6.x` lze spustit proti verzím Kubernetes `1.13+`. Další verze konzula najdete na [GitHubu - Consul Releases][consul-github-releases] a informace o každé z verzí na [Consul- Release Notes][consul-release-notes].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Instalace komponent konzula na AKS
> * Ověření instalace konzula
> * Odinstalovat konzula z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.13` a `kubectl` výše, s povoleným RBAC) a navázali připojení ke clusteru. Pokud potřebujete pomoc s některou z těchto položek, naleznete [v aks rychlý start][aks-quickstart]. Ujistěte se, že váš cluster má alespoň 3 uzly ve fondu uzlů Linuxu.

Budete potřebovat [Helma,][helm] aby se řídil těmito pokyny a nainstaloval konzula. Doporučujeme, abyste měli v clusteru správně nainstalovanou a nakonfigurovanou nejnovější stabilní verzi. Pokud potřebujete pomoc s instalací helmu, přečtěte si [pokyny k instalaci helmu AKS][helm-install]. Všechny consul lusky musí být také naplánováno spuštění na linuxových uzlech.

Tento článek odděluje pokyny k instalaci konzula do několika samostatných kroků. Konečný výsledek má stejnou strukturu jako oficiální [pokyny pro][consul-install-k8]instalaci konzula .

### <a name="install-the-consul-components-on-aks"></a>Instalace komponent konzula na AKS

Začneme stažením verze `v0.10.0` grafu Consul Helm. Tato verze grafu obsahuje verzi `1.6.0`konzula .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Pomocí helmu a `consul-helm` staženého grafu nainstalujte komponenty Consul do `consul` oboru názvů v clusteru AKS. 

> [!NOTE]
> **Možnosti instalace**
> 
> V rámci naší instalace používáme následující možnosti:
> - `connectInject.enabled=true`- umožnit vstřikování proxy do lusků
> - `client.enabled=true`- umožnit klientům konzula spouštět na každém uzlu
> - `client.grpc=true`- povolit naslouchací proces gRPC pro connectInject
> - `syncCatalog.enabled=true`- synchronizace služeb Kubernetes a Consul
>
> **Voliči uzlů**
>
> Konzul v současné době musí být naplánováno spuštění na linuxových uzlech. Pokud máte v clusteru uzly Windows Server, musíte zajistit, aby byly moduly Consul spuštěny pouze v uzlech Linuxu. Budeme používat [voliče uzlů,][kubernetes-node-selectors] abychom se ujistili, že pody jsou naplánovány na správné uzly.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

Helm `Consul` graf nasazuje počet objektů. Seznam můžete vidět z výstupu `helm install` vašeho příkazu výše. Nasazení komponent konzula může trvat přibližně 3 minuty v závislosti na prostředí clusteru.

V tomto okamžiku jste nasadili konzula do clusteru AKS. Abychom zajistili úspěšné nasazení konzula, přejdeme k další části a ověříme instalaci konzula.

## <a name="validate-the-consul-installation"></a>Ověření instalace konzula

Zkontrolujte, zda byly prostředky úspěšně vytvořeny. Pomocí příkazů [get svc a][kubectl-get] [kubectl get pod][kubectl-get] použijte `consul` příkazy get pod pro dotaz na `helm install` obor názvů, kde byly komponenty konzula nainstalovány příkazem:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Následující příklad výstupu ukazuje služby a pody (naplánované na linuxových uzlech), které by nyní měly být spuštěny:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Všechny lusky by měly `Running`zobrazovat stav . Pokud vaše moduly nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělají. Pokud některé pody hlásí problém, použijte [příkaz kubectl describe pod][kubectl-describe] ke kontrole jejich výstupu a stavu.

## <a name="accessing-the-consul-ui"></a>Přístup k uzlicím konzulu

Consul UI byl nainstalován v našem nastavení výše a poskytuje konfiguraci založenou na uI pro konzula. UI pro konzula není veřejně vystaveno prostřednictvím externí IP adresy. Pro přístup k uživatelskému rozhraní konzula použijte příkaz [kubectl port-forward.][kubectl-port-forward] Tento příkaz vytvoří zabezpečené připojení mezi klientským počítačem a příslušným modulem v clusteru AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Nyní můžete otevřít prohlížeč a `http://localhost:8080/ui` nasměrovat ho na otevření uhlavního nastavení konzula. Při otevření ui byste měli vidět následující:

![UI konzula](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Odinstalovat konzula z AKS

> [!WARNING]
> Odstranění konzula ze spuštěného systému může mít za následek problémy související s provozem mezi vašimi službami. Ujistěte se, že jste před pokračováním zajistili, že váš systém bude fungovat správně bez konzula.

### <a name="remove-consul-components-and-namespace"></a>Odebrání součástí konzula a oboru názvů

Chcete-li odebrat konzula z clusteru AKS, použijte následující příkazy. Příkazy `helm delete` odstraní `consul` graf a `kubectl delete namespace` příkaz odebere `consul` obor názvů.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Další kroky

Chcete-li prozkoumat další možnosti instalace a konfigurace konzula, přečtěte si následující oficiální články o konzulovi:

- [Průvodce instalací helmu][consul-install-k8]
- [Konzul - Možnosti instalace helmu][consul-install-helm-options]

Můžete také sledovat další scénáře pomocí:

- [Příklad aplikace konzula][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md

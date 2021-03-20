---
title: Instalace Consul ve službě Azure Kubernetes (AKS)
description: Naučte se instalovat a používat Consul k vytvoření sítě v clusteru Azure Kubernetes Service (AKS).
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 7c5ad53c0040009e9ed1f28072540b46ce7b0b9a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94683915"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Instalace a použití Consul ve službě Azure Kubernetes Service (AKS)

[Consul][consul-github] je open source síť, která poskytuje klíčovou sadu funkcí napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří zjišťování služby, kontrola stavu, segmentace služby a pozorování. Další informace o Consul najdete v dokumentaci oficiální dokumentace k [Consul?][consul-docs-concepts] .

V tomto článku se dozvíte, jak nainstalovat Consul. Komponenty Consul jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Tyto pokyny odkazují na verzi Consul `1.6.0` a používají minimálně Helm verzi `2.14.2` .
>
> Verze Consul se `1.6.x` dají spouštět na Kubernetes verzích `1.13+` . Další verze Consul najdete na webu [GitHub-Consul releases][consul-github-releases] a v informacích o všech vydáních v [poznámkách k verzi Consul][consul-release-notes].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Instalace součástí Consul v AKS
> * Ověření instalace Consul
> * Odinstalace Consul z AKS

## <a name="before-you-begin"></a>Než začnete

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.13` a vyšší s povoleným KUBERNETES RBAC) a navázali jste `kubectl` připojení ke clusteru. Pokud potřebujete s kteroukoli z těchto položek pomáhat, přečtěte si [rychlý Start AKS][aks-quickstart]. Ujistěte se, že cluster má minimálně 3 uzly ve fondu uzlů Linux.

K provedení těchto pokynů budete potřebovat [Helm][helm] a nainstalujete Consul. Doporučuje se správně nainstalovat a nakonfigurovat nejnovější stabilní verze v clusteru. Pokud potřebujete pomoc s instalací Helm, přečtěte si [pokyny k instalaci AKS Helm][helm-install]. Všechny lusky Consul musí být také naplánované pro spouštění na uzlech se systémem Linux.

Tento článek odděluje pokyny k instalaci Consul do několika diskrétních kroků. Konečný výsledek je stejný ve struktuře jako oficiální [návod][consul-install-k8]k instalaci Consul.

### <a name="install-the-consul-components-on-aks"></a>Instalace součástí Consul v AKS

Začneme stažením verze `v0.10.0` grafu Consul Helm. Tato verze grafu obsahuje verzi Consul `1.6.0` .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

Pomocí Helm a staženého `consul-helm` grafu nainstalujte komponenty Consul do `consul` oboru názvů v clusteru AKS. 

> [!NOTE]
> **Možnosti instalace**
> 
> V rámci naší instalace používáme následující možnosti:
> - `connectInject.enabled=true` – Povolit vkládání proxy do lusků
> - `client.enabled=true` -Povolit spouštění klientů Consul na všech uzlech
> - `client.grpc=true` -Povolit naslouchací proces gRPC pro connectInject
> - `syncCatalog.enabled=true` – synchronizace Kubernetes a Consul služeb
>
> **Selektory uzlů**
>
> Consul je v současné době nutné naplánovat na spuštění v uzlech se systémem Linux. Pokud máte v clusteru uzly Windows serveru, musíte zajistit, aby se Consul lusky spouštěly jenom na uzlech se systémem Linux. Použijeme [Selektory uzlů][kubernetes-node-selectors] k ujištění, že lusky jsou naplánované na správných uzlech.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul`Graf Helm nasadí několik objektů. Seznam můžete zobrazit z výstupu `helm install` příkazu výše. Dokončení nasazení součástí Consul může trvat přibližně 3 minuty, v závislosti na prostředí clusteru.

V tomto okamžiku jste nasadili Consul do svého clusteru AKS. Abychom zajistili úspěšné nasazení Consul, pojďme přejít k další části a ověřit instalaci Consul.

## <a name="validate-the-consul-installation"></a>Ověření instalace Consul

Potvrďte, že se prostředky úspěšně vytvořily. Použijte příkazy [kubectl Get svc][kubectl-get] a [kubectl Get pod][kubectl-get] k dotazování `consul` oboru názvů, kde byly komponenty Consul nainstalovány `helm install` příkazem:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Následující příklad výstupu ukazuje služby a lusky (naplánované na uzlech se systémem Linux), které by nyní měly být spuštěny:

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

Všechny lusky by měly zobrazit stav `Running` . Pokud vaše lusky nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělá. Pokud jakékoli lusky nahlásí problém, zkontrolujte výstup a stav pomocí příkazu [kubectl popsat pod][kubectl-describe] .

## <a name="accessing-the-consul-ui"></a>Přístup k uživatelskému rozhraní Consul

Uživatelské rozhraní Consul bylo nainstalováno v naší instalaci výše a poskytuje konfiguraci založenou na uživatelském rozhraní pro Consul. Uživatelské rozhraní pro Consul se veřejně zveřejňuje prostřednictvím externí IP adresy. Chcete-li získat přístup k uživatelskému rozhraní Consul, použijte příkaz pro [přeposílání portů kubectl][kubectl-port-forward] . Tento příkaz vytvoří zabezpečené připojení mezi klientským počítačem a relevantním pod v clusteru AKS.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Teď můžete otevřít prohlížeč a nasměrovat ho na, `http://localhost:8080/ui` aby se otevřelo uživatelské rozhraní Consul. Při otevření uživatelského rozhraní byste měli vidět následující:

![Uživatelské rozhraní Consul](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Odinstalace Consul z AKS

> [!WARNING]
> Odstranění Consul ze spuštěného systému může vést k problémům souvisejícím s provozem mezi vašimi službami. Než budete pokračovat, ujistěte se, že jste provedli správné fungování vašeho systému bez Consul.

### <a name="remove-consul-components-and-namespace"></a>Odebrat součásti a obor názvů Consul

K odebrání Consul z clusteru AKS použijte následující příkazy. `helm delete`Příkazy odstraní `consul` graf a `kubectl delete namespace` příkaz odstraní `consul` obor názvů.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Další kroky

Pokud chcete prozkoumat další možnosti instalace a konfigurace pro Consul, přečtěte si následující oficiální články pro Consul:

- [Průvodce instalací Consul-Helm][consul-install-k8]
- [Consul – možnosti instalace Helm][consul-install-helm-options]

Můžete také postupovat podle dalších scénářů pomocí:

- [Consul – ukázková aplikace][consul-app-example]
- [Referenční architektura Consul Kubernetes][consul-reference]
- [Brány sítě Consul][consul-mesh-gateways]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-deployment-guide
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-mesh-gateways]: https://learn.hashicorp.com/consul/kubernetes/mesh-gateways
[consul-reference]: https://learn.hashicorp.com/consul/kubernetes/kubernetes-reference
[consul-app-example]: https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh
[install-wsl]: /windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md

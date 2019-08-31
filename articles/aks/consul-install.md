---
title: Instalace Hashicorp Consul ve službě Azure Kubernetes Service (AKS)
description: Naučte se instalovat a používat Consul k vytvoření sítě v clusteru Azure Kubernetes Service (AKS).
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189221"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>Instalace a použití Consul Connect ve službě Azure Kubernetes Service (AKS)

[Consul][consul-github] je open source síť, která poskytuje klíčovou sadu funkcí napříč mikroslužbami v clusteru Kubernetes. Mezi tyto funkce patří zjišťování služby, kontrola stavu, segmentace služby a pozorování. Další informace o Consul najdete v dokumentaci oficiální dokumentace k [Consul?][consul-docs-concepts] .

V tomto článku se dozvíte, jak nainstalovat Consul. Komponenty Consul jsou nainstalovány do clusteru Kubernetes v AKS.

> [!NOTE]
> Tyto pokyny odkazují na verzi `1.5`Consul.
>
> Consul `1.5.x` vydané verze byly testovány týmem Hashicorp proti verzím `1.12`Kubernetes, `1.14`, `1.14`. Další verze Consul najdete na webu [GitHub-Consul releases][consul-github-releases] a v informacích o všech vydáních v poznámkách k [verzi Consul][consul-release-notes].

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Instalace součástí Consul v AKS
> * Ověření instalace Consul
> * Odinstalace Consul z AKS

## <a name="before-you-begin"></a>Před zahájením

Kroky popsané v tomto článku předpokládají, že jste vytvořili cluster AKS (Kubernetes `1.11` a vyšší s povoleným RBAC) a `kubectl` navázali jste připojení ke clusteru. Pokud potřebujete s kteroukoli z těchto položek pomáhat, přečtěte si [rychlý Start AKS][aks-quickstart].

K provedení těchto pokynů budete potřebovat [Helm][helm] a nainstalujete Consul. Doporučuje se, aby byla verze `2.12.2` nebo novější správně nainstalovaná a nakonfigurovaná v clusteru. Pokud potřebujete pomoc s instalací Helm, přečtěte si [pokyny k instalaci AKS Helm][helm-install]. Všechny lusky Consul musí být také naplánované pro spouštění na uzlech se systémem Linux.

Tento článek odděluje pokyny k instalaci Consul do několika diskrétních kroků. Konečný výsledek je stejný ve struktuře jako oficiální [návod][consul-install-k8]k instalaci Consul.

### <a name="install-the-consul-components-on-aks"></a>Instalace součástí Consul v AKS

Nejdřív si naklonujte oficiální HashiCorp Consul na úložiště GitHub Kubernetes.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Pak bude potřeba vytvořit vlastní soubor hodnot Helm pro instalaci Consul. Před instalací Consul vytvořte následující soubor vlastních hodnot.

```bash
vim consul-values.yaml
```

Pak zkopírujte a vložte následující hodnoty do souboru Consul-Values. yaml.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Teď, když máme soubor vlastních hodnot, můžeme nainstalovat Consul do našeho clusteru AKS.

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
Graf `Consul` Helm nasadí několik objektů. Seznam můžete zobrazit z výstupu `helm install` příkazu výše. Dokončení nasazení součástí Consul může trvat 4 až 5 minut, a to v závislosti na prostředí clusteru.

> [!NOTE]
> Všechny lusky Consul musí být naplánované na spouštění na uzlech systému Linux. Pokud ve vašem clusteru kromě fondů uzlů pro Linux máte fondy uzlů Windows serveru, ověřte, že všechny Consul lusky mají naplánované spuštění na uzlech se systémem Linux.

V tomto okamžiku jste nasadili Consul do svého clusteru AKS. Abychom zajistili úspěšné nasazení Consul, pojďme přejít k další části a ověřit instalaci Consul.

## <a name="validate-the-consul-installation"></a>Ověření instalace Consul

Nejprve potvrďte, že byly vytvořeny očekávané služby. K zobrazení spuštěných služeb použijte příkaz [kubectl Get svc][kubectl-get] . Dotaz na `consul` obor názvů, ve kterém byly komponenty Consul nainstalovány `consul` pomocí grafu Helm:

```console
kubectl get svc --namespace consul
```

Následující příklad výstupu ukazuje služby, které by nyní měly být spuštěny:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Pak potvrďte, že byly vytvořeny požadované lusky. Použijte příkaz [kubectl Get lusks][kubectl-get] a znovu se Dotazujte `consul` na obor názvů:

```console
kubectl get pods --namespace consul
```

Následující příklad výstupu ukazuje, kde jsou spuštěny tyto lusky:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Všechny lusky by měly zobrazit stav `Running`. Pokud vaše lusky nemají tyto stavy, počkejte minutu nebo dvě, dokud to neudělá. Pokud jakékoli lusky nahlásí problém, zkontrolujte výstup a stav pomocí příkazu [kubectl popsat pod][kubectl-describe] .

## <a name="accessing-the-consul-ui"></a>Přístup k uživatelskému rozhraní Consul

Uživatelské rozhraní Consul bylo nainstalováno v naší instalaci výše, která poskytuje konfiguraci založenou na uživatelském rozhraní pro Consul. Uživatelské rozhraní pro Consul se veřejně zveřejňuje prostřednictvím externí IP adresy. Pro přístup k uživatelským rozhraním doplňku použijte příkaz [kubectl-dopředný port][kubectl-port-forward] . Tento příkaz vytvoří zabezpečené připojení mezi klientským počítačem a relevantním pod v clusteru AKS.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
Teď můžete otevřít prohlížeč a nasměrovat ho `http://localhost:8080/ui` na, aby se otevřelo uživatelské rozhraní Consul. Při otevření uživatelského rozhraní byste měli vidět následující:

![Uživatelské rozhraní Consul](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Odinstalace Consul z AKS

> [!WARNING]
> Odstranění Consul ze spuštěného systému může vést k problémům souvisejícím s provozem mezi vašimi službami. Než budete pokračovat, ujistěte se, že jste provedli správné fungování vašeho systému bez Consul.

### <a name="remove-consul-components-and-namespace"></a>Odebrat součásti a obor názvů Consul

K odebrání Consul z clusteru AKS použijte následující příkazy. Příkazy odstraní graf a`kubectl delete ns` příkaz odstraní`consul`obornázvů. `consul` `helm delete`

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>Další kroky

Pokud chcete prozkoumat další možnosti instalace a konfigurace pro Consul, přečtěte si následující oficiální články pro Consul:

- [Průvodce instalací Consul-Helm][consul-install-k8]
- [Consul – možnosti instalace Helm][consul-install-helm-options]

Pomocí [ukázkové aplikace Consul][consul-app-example]můžete také postupovat podle dalších scénářů.

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

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md

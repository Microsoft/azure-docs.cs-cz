---
title: 'Kurz: nasazení konfigurací pomocí GitOps v clusteru Kubernetes s povoleným ARC Azure'
description: Tento kurz ukazuje použití konfigurací v clusteru Kubernetes s povoleným ARC Azure. Informace o tomto procesu najdete v článku Konfigurace a GitOps – Kubernetes s podporou ARC Azure.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 64299bd05e82cf6f5452cde3f3da5622eff25e56
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121469"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Kurz: nasazení konfigurací pomocí GitOps v clusteru Kubernetes s povoleným ARC Azure 

V tomto kurzu použijete konfigurace pomocí GitOps v clusteru Kubernetes s povoleným obloukem Azure. Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvořte konfiguraci v clusteru Kubernetes s povoleným ARC Azure pomocí ukázkového úložiště Git.
> * Ověřte, že konfigurace byla úspěšně vytvořena.
> * Použijte formulář pro konfiguraci privátního úložiště Git.
> * Ověřte konfiguraci Kubernetes.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Existující Kubernetes připojený cluster s podporou Azure ARC.
    - Pokud jste ještě nepřipojili cluster, Projděte si náš průvodce [rychlým startem clusteru Kubernetes s povoleným připojením k Azure ARC](quickstart-connect-cluster.md).
- Porozumění výhodám a architektuře této funkce. Další informace najdete v [tématu Konfigurace a GitOps v článku Kubernetes s podporou ARC Azure](conceptual-configurations.md).

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

[Ukázkové úložiště](https://github.com/Azure/arc-k8s-demo) , které se používá v tomto článku, je rozčleněné na základě provozovatele clusteru. Manifesty v tomto úložišti zřídí několik oborů názvů, nasazují úlohy a poskytují určitou konfiguraci konkrétního týmu. Pomocí tohoto úložiště s GitOps se ve vašem clusteru vytvoří následující prostředky:

* Obory názvů: `cluster-config` , `team-a` , `team-b`
* Prostředí `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

`config-agent`Cyklické dotazování Azure na nové nebo aktualizované konfigurace. Tato úloha bude trvat až 30 sekund.

Pokud přidružíte privátní úložiště ke konfiguraci, proveďte následující kroky v části [použití konfigurace z privátního úložiště Git](#apply-configuration-from-a-private-git-repository).

## <a name="use-azure-cli"></a>Použití Azure CLI
Pomocí rozšíření Azure CLI pro `k8s-configuration` můžete propojit připojený cluster s [ukázkovým úložištěm Git](https://github.com/Azure/arc-k8s-demo). 
1. Pojmenujte tuto konfiguraci `cluster-config` .
1. Dejte agentovi pokyn k nasazení operátoru v `cluster-config` oboru názvů.
1. Udělte operátorovi `cluster-admin` oprávnění.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Použít veřejné úložiště Git

| Parametr | Formát |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. Git] nebo git://Server/repo [. Git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Použití privátního úložiště Git s klíči SSH a toků vytvořenými tokem

Přidejte veřejný klíč vygenerovaný pomocí toku k uživatelskému účtu ve vašem poskytovateli služby Git. Pokud se klíč přidá do úložiště místo uživatelského účtu, použijte `git@` místo `user@` v adrese URL místo. 

Další podrobnosti najdete v části [použití konfigurace z privátního úložiště Git](#apply-configuration-from-a-private-git-repository) .


| Parametr | Formát | Poznámky
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může nahradit `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Použití privátního úložiště Git s SSH a uživatelem poskytnutými klíči

Poskytněte vlastní privátní klíč přímo nebo v souboru. Klíč musí být ve [formátu PEM](https://aka.ms/PEMformat) a končit znakem nového řádku (\n). 

Přidejte přidružený veřejný klíč k uživatelskému účtu ve vašem poskytovateli služby Git. Pokud se klíč přidá do úložiště místo uživatelského účtu, použijte `git@` místo `user@` . 

Další podrobnosti najdete v části [použití konfigurace z privátního úložiště Git](#apply-configuration-from-a-private-git-repository) .  

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může nahradit `user@` |
| `--ssh-private-key` | klíč s kódováním base64 ve [formátu PEM](https://aka.ms/PEMformat) | Zadat klíč přímo |
| `--ssh-private-key-file` | Úplná cesta k místnímu souboru | Zadejte úplnou cestu k místnímu souboru, který obsahuje klíč PEM-Format.

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Použití privátního hostitele Git s SSH a uživatelem zadanými známými hostiteli

Operátor toku udržuje seznam běžných hostitelů Git v jeho známém souboru hostitelů, aby ověřil úložiště Git před vytvořením připojení SSH. Pokud používáte *Neběžné* úložiště Git nebo vlastního hostitele Git, můžete klíč hostitele zadat tak, aby tok mohl identifikovat vaše úložiště. 

Stejně jako soukromé klíče můžete přímo nebo v souboru poskytnout svůj known_hosts obsah. Při poskytování vlastního obsahu použijte [specifikace formátu obsahu known_hosts](https://aka.ms/KnownHostsFormat)společně s jedním z klíčových scénářů SSH výše.

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může nahradit `user@` |
| `--ssh-known-hosts` | kódovaný v kódování Base64 | Přímý poskytování obsahu známého hostitele |
| `--ssh-known-hosts-file` | Úplná cesta k místnímu souboru | Poskytnutí známého obsahu hostitelů v místním souboru |

### <a name="use-a-private-git-repository-with-https"></a>Použití privátního úložiště Git s protokolem HTTPS

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. Git] | HTTPS se základním ověřováním |
| `--https-user` | RAW nebo kódovaná kódování Base64 | Uživatelské jméno HTTPS |
| `--https-key` | RAW nebo kódovaná kódování Base64 | Osobní přístupový token nebo heslo HTTPS

>[!NOTE]
>* Graf operátoru Helm verze 1.2.0 + podporuje privátní ověřování pomocí protokolu HTTPS Helm.
>* Verze Helm protokolu HTTPS není pro clustery spravované AKS podporována.
>* Pokud potřebujete tok pro přístup k úložišti Git prostřednictvím proxy serveru, budete muset agenty Azure ARC aktualizovat pomocí nastavení proxy serveru. Další informace najdete v tématu [připojení pomocí odchozího proxy server](./connect-cluster.md#connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Další parametry

Upravte konfiguraci pomocí následujících volitelných parametrů:

| Parametr | Popis |
| ------------- | ------------- |
| `--enable-helm-operator`| Přepněte na povolení podpory pro nasazení grafu Helm. |
| `--helm-operator-params` | Hodnoty grafu pro operátor Helm (Pokud je povoleno) Například, `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Verze grafu pro operátor Helm (Pokud je povolená) Použijte verzi 1.2.0 +. Výchozí: ' 1.2.0 '. |
| `--operator-namespace` | Název oboru názvů operátoru. Výchozí: výchozí. Maximální počet: 23 znaků. |
| `--operator-params` | Parametry pro operátor Musí být zadány v jednoduchých uvozovkách. Například ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'```. 

### <a name="options-supported-in----operator-params"></a>Podporované možnosti  `--operator-params` :

| Možnost | Popis |
| ------------- | ------------- |
| `--git-branch`  | Větev úložiště Git, která se má použít pro Kubernetes manifesty Výchozí hodnota je "Master". Novější úložiště mají kořenovou větev s názvem `main` . v takovém případě je potřeba nastavit `--git-branch=main` . |
| `--git-path`  | Relativní cesta v úložišti Git pro tok, ve kterém se mají vyhledat manifesty Kubernetes. |
| `--git-readonly` | Úložiště Git bude považováno za jen pro čtení. Tok se do něj nepokouší zapisovat. |
| `--manifest-generation`  | Pokud je tato možnost povolená, bude tok Hledat. toků. yaml a spouštět Kustomize nebo jiné generátory manifestů. |
| `--git-poll-interval`  | Období, ve kterém se má dotaz na úložiště Git zapsat na nové potvrzení. Výchozí hodnota je `5m` (5 minut). |
| `--sync-garbage-collection`  | Pokud je tato možnost povolená, bude tok odstranit prostředky, které vytvořil, ale už se v Gitu nevyskytují. |
| `--git-label`  | Popisek, který bude sledovat průběh synchronizace. Slouží k označení větve Git.  Výchozí je `flux-sync`. |
| `--git-user`  | Uživatelské jméno pro potvrzení změn Git. |
| `--git-email`  | E-mail, který se má použít pro potvrzení Git 

Pokud nechcete, aby tok zapisoval do úložiště a `--git-user` nebo `--git-email` není nastavený, pak `--git-readonly` se automaticky nastaví.

Další informace najdete v [dokumentaci k toku](https://aka.ms/FluxcdReadme).

> [!TIP]
> Konfiguraci můžete vytvořit v Azure Portal na kartě **GitOps** prostředku Kubernetes s podporou ARC Azure.

## <a name="validate-the-configuration"></a>Ověření konfigurace

Pomocí Azure CLI ověřte, jestli se konfigurace úspěšně vytvořila.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Prostředek konfigurace se aktualizuje o stav dodržování předpisů, zprávy a informace o ladění.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Při vytvoření nebo aktualizaci konfigurace se stane několik věcí:

1. Azure ARC `config-agent` monitoruje Azure Resource Manager pro nové nebo aktualizované konfigurace ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) a zjišťuje novou `Pending` konfiguraci.
1. `config-agent`Přečte vlastnosti konfigurace a vytvoří cílový obor názvů.
1. Azure ARC `controller-manager` vytvoří účet služby Kubernetes a namapuje ho na [ClusterRoleBinding nebo RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) pro příslušná oprávnění ( `cluster` nebo `namespace` obor). Poté nasadí instanci `flux` .
1. Pokud používáte možnost SSH s klíči generovanými tokem, `flux` vygeneruje klíč SSH a zaprotokoluje veřejný klíč.
1. `config-agent`Nahlásí stav zpět do prostředku konfigurace v Azure.

Během procesu zřizování se prostředek konfigurace přesune po několika změnách stavu. Sledujte průběh pomocí `az k8s-configuration show ...` příkazu výše:

| Změna fáze | Popis |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Představuje počáteční a průběžné stavy. |
| `complianceStatus` -> `Installed`  | `config-agent` cluster byl úspěšně nakonfigurován a nasazen `flux` bez chyby. |
| `complianceStatus` -> `Failed` | `config-agent` došlo k chybě při nasazování `flux` . Podrobnosti jsou k dispozici v `complianceStatus.message` těle odpovědi. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Použít konfiguraci z privátního úložiště Git

Pokud používáte soukromé úložiště Git, musíte ve svém úložišti nakonfigurovat veřejný klíč SSH. Je možné, že vygenerujete veřejný klíč SSH buď nebo. Veřejný klíč můžete nakonfigurovat buď na konkrétním úložišti Git, nebo na uživateli Git, který má přístup k úložišti. 

### <a name="get-your-own-public-key"></a>Získání vlastního veřejného klíče

Pokud jste vygenerovali vlastní klíče SSH, pak už máte privátní a veřejné klíče.

#### <a name="get-the-public-key-using-azure-cli"></a>Získání veřejného klíče pomocí Azure CLI 

Pokud tok generuje klíče, použijte následující příkaz v Azure CLI.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Získat veřejný klíč z Azure Portal

Projděte si následující v Azure Portal, jestli tok generuje klíče.

1. V Azure Portal přejděte na prostředek připojeného clusteru.
2. Na stránce prostředek vyberte "GitOps" a podívejte se na seznam konfigurací pro tento cluster.
3. Vyberte konfiguraci, která používá soukromé úložiště Git.
4. V kontextovém okně, které se otevře, zkopírujte **veřejný klíč úložiště** do dolní části okna.

#### <a name="add-public-key-using-github"></a>Přidat veřejný klíč pomocí GitHubu

Použijte jednu z následujících možností:

* Možnost 1: přidejte veřejný klíč do svého uživatelského účtu (platí pro všechna úložiště ve vašem účtu):  
    1. Otevřete GitHub a klikněte na ikonu profilu v pravém horním rohu stránky.
    2. Klikněte na **Nastavení**.
    3. Klikněte na **klíče SSH a GPG**.
    4. Klikněte na **nový klíč SSH**.
    5. Zadejte název.
    6. Vložte veřejný klíč bez okolních uvozovek.
    7. Klikněte na **Přidat klíč SSH**.

* Možnost 2: přidejte veřejný klíč jako klíč nasazení do úložiště Git (týká se jenom tohoto úložiště):  
    1. Otevřete GitHub a přejděte do svého úložiště.
    1. Klikněte na **Nastavení**.
    1. Klikněte na **nasadit klíče**.
    1. Klikněte na **Přidat klíč nasazení**.
    1. Zadejte název.
    1. Ověřte možnost **Povolení přístupu pro zápis**.
    1. Vložte veřejný klíč bez okolních uvozovek.
    1. Klikněte na **Přidat klíč**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Přidání veřejného klíče pomocí úložiště Azure DevOps

K přidání klíče do klíčů SSH použijte následující postup:

1. V části **nastavení uživatele** v pravém horním rohu (vedle obrázku profilu) klikněte na **veřejné klíče SSH**.
1. Vyberte  **+ nový klíč**.
1. Zadejte název.
1. Vložte veřejný klíč bez okolních uvozovek.
1. Klikněte na **Přidat**.

## <a name="validate-the-kubernetes-configuration"></a>Ověření konfigurace Kubernetes

Po `config-agent` instalaci `flux` instance by prostředky uchovávané v úložišti Git měly začít tok do clusteru. Zkontrolujte, zda byly vytvořeny obory názvů, nasazení a prostředky pomocí následujícího příkazu:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Můžeme vidět, že `team-a` `team-b` `itops` `cluster-config` byly vytvořeny obory názvů,, a.

`flux`Operátor byl nasazen do `cluster-config` oboru názvů, jak je směrován podle prostředku konfigurace:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Další průzkum

Další prostředky nasazené jako součást úložiště konfigurace můžete prozkoumat pomocí:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte konfiguraci pomocí Azure CLI nebo Azure Portal. Po spuštění příkazu Odstranit se prostředek konfigurace okamžitě odstraní v Azure. Úplné odstranění přidružených objektů z clusteru by mělo proběhnout do 10 minut. Pokud je konfigurace ve stavu selhání při odebrání, může trvat až hodinu úplné odstranění přidružených objektů.

Když se konfigurace s `namespace` oborem odstraní, obor názvů se neodstraní pomocí ARC Azure, aby se předešlo přerušení stávajících úloh. V případě potřeby můžete tento obor názvů odstranit ručně pomocí `kubectl` .

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Změny v clusteru, které byly výsledkem nasazení ze sledovaných úložišť Git, se při odstranění konfigurace neodstraní.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu kurzu, kde se dozvíte, jak implementovat CI/CD pomocí GitOps.
> [!div class="nextstepaction"]
> [Implementace CI/CD pomocí GitOps](./tutorial-gitops-ci-cd.md)

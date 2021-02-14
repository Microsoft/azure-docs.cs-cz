---
title: Nasazení konfigurací pomocí GitOps v clusteru Kubernetes s podporou Azure Arc (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití GitOps ke konfiguraci clusteru Kubernetes s povoleným ARC Azure (Preview)
keywords: GitOps, Kubernetes, K8s, Azure, ARC, Azure Kubernetes Service, AKS, Containers
ms.openlocfilehash: 072bfc8c243eb9b69e06366961019b88b67e0941
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392234"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Nasazení konfigurací pomocí GitOps v clusteru Kubernetes s podporou Azure Arc (Preview)

V souvislosti s Kubernetes je GitOps postup, který deklaruje požadovaný stav konfigurací clusterů Kubernetes (nasazení, obory názvů atd.) v úložišti Git. Tato deklarace je následována nasazením těchto clusterů, které je založené na vyžádání, pomocí operátoru. 

Tento článek popisuje nastavení pracovních postupů GitOps v clusterech Kubernetes s povoleným ARC Azure.

Připojení mezi clusterem a úložištěm Git je vytvořeno jako `Microsoft.KubernetesConfiguration/sourceControlConfigurations` prostředek rozšíření v Azure Resource Manager. `sourceControlConfiguration`Vlastnosti prostředku reprezentují, kde a jak by se měly prostředky Kubernetes z Gitu přesměrovat do vašeho clusteru. `sourceControlConfiguration`Data jsou uložena v zašifrovaném umístění v databázi Azure Cosmos DB, aby se zajistila důvěrnost dat.

Služba `config-agent` spuštěná v clusteru zodpovídá za:
* Sledování nových nebo aktualizovaných `sourceControlConfiguration` prostředků rozšíření u prostředku Kubernetes s povoleným ARC Azure
* Nasazení operátora toku pro sledování úložiště Git pro každou z nich `sourceControlConfiguration` .
* Aplikují se jakékoli aktualizace, které jste udělali `sourceControlConfiguration` . 

`sourceControlConfiguration`Pro dosažení víceklientské architektury můžete vytvořit více prostředků na stejném clusteru Kubernetes s povoleným ARC Azure. Omezení nasazení na příslušné obory názvů vytvořením každého `sourceControlConfiguration` s jiným `namespace` oborem.

Úložiště Git může obsahovat:
* YAML – manifesty popisující jakékoli platné prostředky Kubernetes, včetně oborů názvů, ConfigMaps, nasazení, DaemonSets atd. 
* Helm grafy pro nasazení aplikací. 

Mezi běžné sady scénářů patří definování standardních hodnot konfigurace pro vaši organizaci, jako jsou například běžné role a vazby Azure, agenti monitorování nebo protokolování nebo služby pro clustery v rámci clusteru.

Stejný vzor lze použít ke správě větší kolekce clusterů, které mohou být nasazeny v heterogenních prostředích. Máte například jedno úložiště, které definuje základní konfiguraci pro vaši organizaci, která se vztahuje na více clusterů Kubernetes najednou. [Azure Policy může automatizovat](use-azure-policy.md) vytváření a `sourceControlConfiguration` pomocí konkrétní sady parametrů ve všech prostředcích Azure ARC s povolenými prostředky Kubernetes v rámci oboru (předplatného nebo skupiny prostředků).

Projděte si následující postup, ve kterém se dozvíte, jak použít sadu konfigurací s `cluster-admin` oborem.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte existující cluster Kubernetes s povoleným připojením k Azure ARC. Pokud potřebujete připojený cluster, přečtěte si [rychlý Start clusteru Kubernetes s povoleným připojením Azure ARC](./connect-cluster.md).

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

[Ukázkové úložiště](https://github.com/Azure/arc-k8s-demo) , které se používá v tomto článku, je rozčleněné na uživatele operátora clusteru, který by chtěl zřídit několik oborů názvů, nasadit společnou úlohu a poskytnout konfiguraci specifickou pro tým. Pomocí tohoto úložiště se ve vašem clusteru vytvoří následující prostředky:


* **Obory názvů:** `cluster-config` , `team-a` , `team-b`
* **Nasazení:**`cluster-config/azure-vote`
* **ConfigMap:**`team-a/endpoints`

`config-agent`Dotazování Azure na nové nebo aktualizované `sourceControlConfiguration` . Tato úloha bude trvat až 30 sekund.

Pokud přidružíte soukromé úložiště pomocí `sourceControlConfiguration` , proveďte kroky v části [použití konfigurace z privátního úložiště Git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Použití Azure CLI

Pomocí rozšíření Azure CLI pro `k8sconfiguration` můžete propojit připojený cluster s [ukázkovým úložištěm Git](https://github.com/Azure/arc-k8s-demo). 
1. Pojmenujte tuto konfiguraci `cluster-config` .
1. Dejte agentovi pokyn k nasazení operátoru v `cluster-config` oboru názvů.
1. Udělte operátorovi `cluster-admin` oprávnění.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

#### <a name="use-a-public-git-repo"></a>Použít veřejné úložiště Git

| Parametr | Formát |
| ------------- | ------------- |
| `--repository-url` | http [s]://Server/repo [. Git] nebo git://Server/repo [. Git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Použití privátního úložiště Git s klíči SSH a toků vytvořenými tokem

| Parametr | Formát | Poznámky
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může nahradit `user@`

> [!NOTE]
> Veřejný klíč generovaný tokem musí být přidán do uživatelského účtu vašeho poskytovatele služby Git. Pokud se klíč přidá do úložiště místo uživatelského účtu, použijte `git@` místo `user@` v adrese URL místo. Další podrobnosti najdete v části [použití konfigurace z privátního úložiště Git](#apply-configuration-from-a-private-git-repository) .

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Použití privátního úložiště Git s protokolem SSH a uživatelem poskytnutými klíči

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může nahradit `user@` |
| `--ssh-private-key` | klíč s kódováním base64 ve [formátu PEM](https://aka.ms/PEMformat) | Zadat klíč přímo |
| `--ssh-private-key-file` | Úplná cesta k místnímu souboru | Zadejte úplnou cestu k místnímu souboru, který obsahuje klíč PEM-Format.

> [!NOTE]
> Poskytněte vlastní privátní klíč přímo nebo v souboru. Klíč musí být ve [formátu PEM](https://aka.ms/PEMformat) a končit znakem nového řádku (\n).  Přidružený veřejný klíč musí být přidán k uživatelskému účtu ve vašem poskytovateli služby Git. Pokud se klíč přidá do úložiště místo uživatelského účtu, použijte `git@` místo `user@` . Další podrobnosti najdete v části [použití konfigurace z privátního úložiště Git](#apply-configuration-from-a-private-git-repository) .

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Použití privátního hostitele Git s SSH a uživatelem zadanými známými hostiteli

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může nahradit `user@` |
| `--ssh-known-hosts` | kódovaný v kódování Base64 | Přímý poskytování obsahu známého hostitele |
| `--ssh-known-hosts-file` | Úplná cesta k místnímu souboru | Poskytnutí známého obsahu hostitelů v místním souboru |

> [!NOTE]
> Aby bylo možné ověřit úložiště Git před vytvořením připojení SSH, operátor toku udržuje seznam běžných hostitelů Git v souboru známých hostitelů. Pokud používáte Neběžné úložiště Git nebo vlastního hostitele Git, možná budete muset zadat klíč hostitele, abyste zajistili, že tok může identifikovat vaše úložiště. Obsah known_hosts můžete poskytnout přímo nebo v souboru. Použijte [specifikace formátu obsahu known_hosts](https://aka.ms/KnownHostsFormat) ve spojení s jedním z klíčových SCÉNÁŘů SSH popsaných výše při poskytování vlastního obsahu.

#### <a name="use-a-private-git-repo-with-https"></a>Použití privátního úložiště Git s protokolem HTTPS

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[. Git] | HTTPS se základním ověřováním |
| `--https-user` | RAW nebo kódovaná kódování Base64 | Uživatelské jméno HTTPS |
| `--https-key` | RAW nebo kódovaná kódování Base64 | Osobní přístupový token nebo heslo HTTPS

> [!NOTE]
> Privátní ověřování pomocí protokolu HTTPS Helm je podporováno pouze pomocí grafu operátoru Helm verze 1.2.0 + (výchozí).
> Privátní ověřování pomocí protokolu HTTPS Helm se momentálně nepodporuje u clusterů spravovaných službami Azure Kubernetes.
> Pokud potřebujete tok pro přístup k úložišti Git prostřednictvím proxy serveru, budete muset agenty Azure ARC aktualizovat pomocí nastavení proxy serveru. Další informace najdete v tématu [připojení pomocí odchozího proxy server](./connect-cluster.md#connect-using-an-outbound-proxy-server).

#### <a name="additional-parameters"></a>Další parametry

Upravte konfiguraci pomocí následujících volitelných parametrů:

| Parametr | Popis |
| ------------- | ------------- |
| `--enable-helm-operator`| Přepněte na povolení podpory pro nasazení grafu Helm. |
| `--helm-operator-params` | Hodnoty grafu pro operátor Helm (Pokud je povoleno) Například, `--set helm.versions=v3`. |
| `--helm-operator-version` | Verze grafu pro operátor Helm (Pokud je povolená) Použijte verzi 1.2.0 +. Výchozí: ' 1.2.0 '. |
| `--operator-namespace` | Název oboru názvů operátoru. Výchozí: výchozí. Maximální počet: 23 znaků. |
| `--operator-params` | Parametry pro operátor Musí být zadány v jednoduchých uvozovkách. Například ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ```. 

##### <a name="options-supported-in----operator-params"></a>Podporované možnosti  `--operator-params` :

| Možnost | Popis |
| ------------- | ------------- |
| `--git-branch`  | Větev úložiště Git, která se má použít pro Kubernetes manifesty Výchozí hodnota je "Master". Novější úložiště mají kořenovou větev s názvem `main` . v takovém případě je potřeba nastavit `--git-branch=main` . |
| `--git-path`  | Relativní cesta v úložišti Git pro tok, ve kterém se mají vyhledat manifesty Kubernetes. |
| `--git-readonly` | Úložiště Git se bude považovat za jen pro čtení; Tok se do něj nepokouší zapisovat. |
| `--manifest-generation`  | Pokud je tato možnost povolená, bude tok Hledat. toků. yaml a spouštět Kustomize nebo jiné generátory manifestů. |
| `--git-poll-interval`  | Období, ve kterém se má dotaz na úložiště Git zapsat na nové potvrzení. Výchozí hodnota je `5m` (5 minut). |
| `--sync-garbage-collection`  | Pokud je tato možnost povolená, bude tok odstranit prostředky, které vytvořil, ale už se v Gitu nevyskytují. |
| `--git-label`  | Popisek, který bude sledovat průběh synchronizace. Slouží k označení větve Git.  Výchozí je `flux-sync`. |
| `--git-user`  | Uživatelské jméno pro potvrzení změn Git. |
| `--git-email`  | E-mail, který se má použít pro potvrzení Git 

Pokud nechcete, aby tok zapisoval do úložiště a `--git-user` nebo `--git-email` není nastaven, pak `--git-readonly` se automaticky nastaví.

Další informace najdete v [dokumentaci k toku](https://aka.ms/FluxcdReadme).

> [!TIP]
> Na `sourceControlConfiguration` kartě **GitOps** prostředku Kubernetes s povoleným obloukem Azure můžete vytvořit objekt na Azure Portal.

## <a name="validate-the-sourcecontrolconfiguration"></a>Ověřit sourceControlConfiguration

Pomocí Azure CLI ověřte, že se `sourceControlConfiguration` úspěšně vytvořil.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`Prostředek se aktualizuje se stavem dodržování předpisů, zprávami a informacemi o ladění.

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

Při `sourceControlConfiguration` Vytvoření nebo aktualizaci se v digestoři stane několik věcí:

1. Azure ARC `config-agent` monitoruje Azure Resource Manager pro nové nebo aktualizované konfigurace ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) a zjišťuje novou `Pending` konfiguraci.
1. `config-agent`Přečte vlastnosti konfigurace a vytvoří cílový obor názvů.
1. Azure ARC `controller-manager` připraví účet služby Kubernetes s příslušným oprávněním ( `cluster` nebo `namespace` oborem) a pak nasadí instanci `flux` .
1. Pokud používáte možnost SSH s klíči generovanými tokem, `flux` vygeneruje klíč SSH a zaprotokoluje veřejný klíč.
1. `config-agent`Stav hlásí zpět do `sourceControlConfiguration` prostředku v Azure.

Během procesu zřizování se `sourceControlConfiguration` přesunou mezi několika změnami stavu. Sledujte průběh pomocí `az k8sconfiguration show ...` příkazu výše:

| Změna fáze | Description |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Představuje počáteční a průběžné stavy. |
| `complianceStatus` -> `Installed`  | `config-agent` Služba byla schopna úspěšně nakonfigurovat cluster a nasadit `flux` bez chyb. |
| `complianceStatus` -> `Failed` | `config-agent` Při nasazování došlo k chybě `flux` . podrobnosti by měly být k dispozici v `complianceStatus.message` těle odpovědi. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Použít konfiguraci z privátního úložiště Git

Pokud používáte privátní úložiště Git, musíte ve svém úložišti nakonfigurovat veřejný klíč SSH. Veřejný klíč SSH bude buď ten, který vygeneruje tok, nebo ten, který zadáte. Veřejný klíč můžete nakonfigurovat buď na konkrétním úložišti Git, nebo na uživateli Git, který má přístup k úložišti. 

### <a name="get-your-own-public-key"></a>Získání vlastního veřejného klíče

Pokud jste vygenerovali vlastní klíče SSH, pak už máte privátní a veřejné klíče.

#### <a name="get-the-public-key-using-azure-cli"></a>Získání veřejného klíče pomocí Azure CLI 

Toto je užitečné, pokud tok generuje klíče.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Získat veřejný klíč z Azure Portal

Toto je užitečné, pokud tok generuje klíče.

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
    1. Otevřete GitHub a přejděte k úložišti.
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

**Výkonem**

```console
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

`flux`Operátor byl nasazen do `cluster-config` oboru názvů, jak je přesměrováno v naší `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Výkonem**

```console
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

## <a name="delete-a-configuration"></a>Odstraní konfiguraci.

Odstraňte `sourceControlConfiguration` pomocí Azure CLI nebo Azure Portal.  Po zahájení příkazu DELETE se `sourceControlConfiguration` prostředek v Azure okamžitě odstraní. Úplné odstranění přidružených objektů z clusteru by mělo proběhnout do 10 minut. Pokud `sourceControlConfiguration` je při odebrání v neúspěšném stavu, může trvat až hodinu úplné odstranění přidružených objektů.

> [!NOTE]
> Po `sourceControlConfiguration` `namespace` Vytvoření oboru s rozsahem můžou uživatelé s `edit` vazbou role v oboru názvů nasazovat úlohy na tento obor názvů. Pokud se tato akce `sourceControlConfiguration` s oborem názvů odstraní, obor názvů zůstane beze změny a nebude odstraněn, aby nedošlo k porušení těchto ostatních úloh. V případě potřeby můžete tento obor názvů odstranit ručně pomocí `kubectl` .  
> Změny v clusteru, které byly výsledkem nasazení ze sledovaného úložiště Git, se při odstranění neodstraní `sourceControlConfiguration` .

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Další kroky

- [Použití Helm s konfigurací správy zdrojového kódu](./use-gitops-with-helm.md)
- [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)
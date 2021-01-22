---
title: Nasazení konfigurací pomocí GitOps v clusteru Kubernetes s podporou Azure Arc (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití GitOps ke konfiguraci clusteru Kubernetes s povoleným ARC Azure (Preview)
keywords: GitOps, Kubernetes, K8s, Azure, ARC, Azure Kubernetes Service, AKS, Containers
ms.openlocfilehash: 751b274a9cae68f6bc9b1adc45804f2dd2ef4c72
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684753"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Nasazení konfigurací pomocí GitOps v clusteru Kubernetes s podporou Azure Arc (Preview)

GitOps, jak se týká Kubernetes, je postup, který deklaruje požadovaný stav konfigurace Kubernetes (nasazení, obory názvů atd.) v úložišti Git následovaným nasazením těchto konfigurací na základě požadavků na vyžádání do clusteru pomocí operátoru. Tento dokument popisuje nastavení takových pracovních postupů u clusterů Kubernetes s podporou ARC Azure.

Připojení mezi clusterem a úložištěm Git se vytvoří v Azure Resource Manager jako `Microsoft.KubernetesConfiguration/sourceControlConfigurations` prostředek rozšíření. `sourceControlConfiguration`Vlastnosti prostředku reprezentují, kde a jak by se měly prostředky Kubernetes z Gitu přesměrovat do vašeho clusteru. `sourceControlConfiguration`Data jsou uložená v klidovém stavu v Azure Cosmos DB databázi, aby se zajistila důvěrnost dat.

`config-agent`Spuštění ve vašem clusteru zodpovídá za sledování nových nebo aktualizovaných `sourceControlConfiguration` prostředků rozšíření u prostředku Kubernetes s povoleným ARC Azure, který slouží k nasazení operátoru toku pro sledování úložiště Git pro každou `sourceControlConfiguration` a uplatnění všech aktualizací, které se u nich udělaly `sourceControlConfiguration` . `sourceControlConfiguration`Pro dosažení víceklientské architektury je možné vytvořit více prostředků na stejném clusteru Kubernetes s povoleným ARC Azure. Můžete vytvořit každý `sourceControlConfiguration` s jiným `namespace` oborem a omezit tak nasazení na v rámci příslušných oborů názvů.

Úložiště Git může obsahovat YAML manifesty, které popisují jakékoli platné prostředky Kubernetes, včetně oborů názvů, ConfigMaps, nasazení, DaemonSets atd.  Může obsahovat také Helm grafy pro nasazení aplikací. Mezi běžné sady scénářů patří definování základní konfigurace pro vaši organizaci, která může zahrnovat běžné role a vazby Azure, agenty monitorování nebo protokolování nebo služby pro clustery v rámci clusteru.

Stejný vzor lze použít ke správě větší kolekce clusterů, které mohou být nasazeny v heterogenních prostředích. Například můžete mít jedno úložiště, které definuje konfiguraci standardních hodnot vaší organizace, a použít ho na desítky Kubernetes clusterů najednou. [Azure Policy může automatizovat](use-azure-policy.md) vytváření a `sourceControlConfiguration` s konkrétní sadou parametrů ve všech prostředcích Azure ARC s povoleným Kubernetes prostředky v oboru (předplatné nebo skupina prostředků).

Tato úvodní příručka vás provede použitím sady konfigurací s oborem Správce clusteru.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster Kubernetes s povoleným připojením k Arc Azure. Pokud potřebujete připojený cluster, přečtěte si [rychlý Start pro připojení clusteru](./connect-cluster.md).

## <a name="create-a-configuration"></a>Vytvořit konfiguraci

[Ukázkové úložiště](https://github.com/Azure/arc-k8s-demo) použité v tomto dokumentu je členěné kolem uživatele operátora clusteru, který by chtěl zřídit několik oborů názvů, nasadit společnou úlohu a poskytnout konfiguraci specifickou pro tým. Pomocí tohoto úložiště se ve vašem clusteru vytvoří následující prostředky:

**Obory názvů:** `cluster-config` , `team-a` , `team-b` 
 **nasazení:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

`config-agent`Cyklické dotazování Azure na nové nebo aktualizované `sourceControlConfiguration` každých 30 sekund, což je maximální doba potřebná `config-agent` k výběru nové nebo aktualizované konfigurace.
Pokud přidružíte soukromé úložiště s nástrojem `sourceControlConfiguration` , ujistěte se, že jste provedli také kroky v části [použití konfigurace z privátního úložiště Git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Použití Azure CLI

Pomocí rozšíření Azure CLI pro `k8sconfiguration` můžete propojit připojený cluster s [ukázkovým úložištěm Git](https://github.com/Azure/arc-k8s-demo). Této konfiguraci přiřadíme název `cluster-config` , dáte pokyn agentovi, aby nasadil operátor do `cluster-config` oboru názvů a udělí operátorovi `cluster-admin` oprávnění.

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
| --úložiště-adresa URL | http [s]://Server/repo [. Git] nebo git://Server/repo [. Git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Použití privátního úložiště Git s klíči SSH a toků vytvořenými tokem

| Parametr | Formát | Poznámky
| ------------- | ------------- | ------------- |
| --úložiště-adresa URL | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může dosadit za `user@`

> [!NOTE]
> Veřejný klíč generovaný tokem musí být přidán do uživatelského účtu vašeho poskytovatele služby Git. Pokud se klíč přidá do úložiště místo > uživatelského účtu, použijte `git@` místo `user@` v adrese URL. [Zobrazit další podrobnosti](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Použití privátního úložiště Git s protokolem SSH a uživatelem poskytnutými klíči

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| --úložiště-adresa URL  | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může dosadit za `user@` |
| --SSH-privátní – klíč | klíč s kódováním base64 ve [formátu PEM](https://aka.ms/PEMformat) | Zadat klíč přímo |
| --SSH-Private-Key-File | Úplná cesta k místnímu souboru | Zadejte úplnou cestu k místnímu souboru, který obsahuje klíč PEM-Format.

> [!NOTE]
> Poskytněte vlastní privátní klíč přímo nebo v souboru. Klíč musí být ve [formátu PEM](https://aka.ms/PEMformat) a končit znakem nového řádku (\n).  Přidružený veřejný klíč musí být přidán k uživatelskému účtu ve vašem poskytovateli služby Git. Pokud se klíč přidá do úložiště místo uživatelského účtu, použijte `git@` místo `user@` . [Zobrazit další podrobnosti](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Použití privátního hostitele Git s SSH a uživatelem zadanými známými hostiteli

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| --úložiště-adresa URL  | ssh://user@server/repo[. Git] nebo user@server:repo [. Git] | `git@` může dosadit za `user@` |
| --SSH – známé – hostitelé | kódovaný v kódování Base64 | poskytoval se známý obsah hostitelů přímo |
| --SSH-známé – Host-File | Úplná cesta k místnímu souboru | známý obsah hostitele poskytovaný v místním souboru

> [!NOTE]
> Operátor toku udržuje seznam běžných hostitelů Git v jeho známém souboru hostitelů, aby bylo možné ověřit úložiště Git před vytvořením připojení SSH. Pokud používáte Neběžné úložiště Git nebo vlastního hostitele Git, možná budete muset zadat klíč hostitele, abyste zajistili, že tok může identifikovat vaše úložiště. Svůj známý obsah hostitelů můžete poskytnout přímo nebo v souboru. [Zobrazit specifikace formátu obsahu známých hostitelů](https://aka.ms/KnownHostsFormat).
> Tuto možnost můžete použít ve spojení s jedním z klíčových scénářů SSH popsaných výše.

#### <a name="use-a-private-git-repo-with-https"></a>Použití privátního úložiště Git s protokolem HTTPS

| Parametr | Formát | Poznámky |
| ------------- | ------------- | ------------- |
| --úložiště-adresa URL | https://server/repo[. Git] | HTTPS se základním ověřováním |
| --https – uživatel | RAW nebo kódovaná kódování Base64 | Uživatelské jméno HTTPS |
| --https-Key | RAW nebo kódovaná kódování Base64 | Osobní přístupový token nebo heslo HTTPS

> [!NOTE]
> Privátní ověřování pomocí protokolu HTTPS Helm je podporováno pouze pomocí grafu Helm operator verze >= 1.2.0.  Ve výchozím nastavení se používá 1.2.0 verze.
> Privátní ověřování pomocí protokolu HTTPS Helm se momentálně nepodporuje pro spravované clustery služeb Azure Kubernetes.
> Pokud potřebujete tok pro přístup k úložišti Git prostřednictvím proxy serveru, budete muset agenty Azure ARC aktualizovat pomocí nastavení proxy serveru. [Další informace](https://docs.microsoft.com/azure/azure-arc/kubernetes/connect-cluster#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Další parametry

Pokud chcete konfiguraci přizpůsobit, můžete použít více parametrů:

`--enable-helm-operator` : *Volitelný* přepínač, který povolí podporu pro nasazení grafu Helm.

`--helm-operator-params` : *Volitelné* hodnoty grafu pro operátor Helm (Pokud je povoleno).  Například: "--set Helm. verze = V3".

`--helm-operator-chart-version` : *Volitelná* verze grafu pro operátor Helm (Pokud je povolená). Výchozí: ' 1.2.0 '.

`--operator-namespace` : *Volitelný* název oboru názvů operátoru. Výchozí: výchozí. Maximální počet 23 znaků.

`--operator-params` : *Volitelné* parametry pro operátor. Musí být zadány v jednoduchých uvozovkách. Například ```--operator-params='--git-readonly --git-path=releases --sync-garbage-collection' ```.

Možnosti podporované v--operator-params

| Možnost | Popis |
| ------------- | ------------- |
| --Git-větev  | Větev úložiště Git, která se má použít pro Kubernetes manifesty Výchozí hodnota je "Master". |
| --Git-Path  | Relativní cesta v úložišti Git pro tok, ve kterém se mají vyhledat manifesty Kubernetes. |
| --Git-ReadOnly | Úložiště Git se bude považovat za jen pro čtení; Tok se do něj nepokouší zapisovat. |
| --Manifest-Generation  | Pokud je tato možnost povolená, bude tok Hledat. toků. yaml a spouštět Kustomize nebo jiné generátory manifestů. |
| --Git-dotaz-interval  | Období, ve kterém se má dotaz na úložiště Git zapsat na nové potvrzení. Výchozí hodnota je ' 5 min ' (5 minut). |
| --Sync-uvolňování kolekce  | Pokud je tato možnost povolená, bude tok odstranit prostředky, které vytvořil, ale už se v Gitu nevyskytují. |
| --Git-Label  | Popisek pro sledování průběhu synchronizace, který se používá k označení větve Git  Výchozí hodnota je "tok-synchronizace". |
| --Git-User  | Uživatelské jméno pro potvrzení změn Git. |
| --Git-email  | E-mail, který se má použít pro potvrzení Git |

* Pokud nejsou nastavené možnosti--Git-User nebo--Git-email (což znamená, že nechcete, aby tok zapisoval do úložiště), pak bude automaticky nastavená možnost--Git-ReadOnly (Pokud jste ho ještě nastavili).

Další informace najdete v [dokumentaci ke službě tokem](https://aka.ms/FluxcdReadme).

> [!TIP]
> V Azure Portal na kartě **GitOps** prostředku Kubernetes s povoleným obloukem Azure je možné vytvořit sourceControlConfiguration.

## <a name="validate-the-sourcecontrolconfiguration"></a>Ověřit sourceControlConfiguration

Pomocí rozhraní příkazového řádku Azure CLI ověřte, že se `sourceControlConfiguration` úspěšně vytvořil.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Všimněte si, že `sourceControlConfiguration` prostředek je aktualizovaný se stavem dodržování předpisů, zprávami a informacemi o ladění.

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

Při `sourceControlConfiguration` vytvoření se v digestoři stane několik věcí:

1. Azure ARC `config-agent` monitoruje Azure Resource Manager pro nové nebo aktualizované konfigurace ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ).
1. `config-agent` oznámení o nové `Pending` konfiguraci
1. `config-agent` přečte vlastnosti konfigurace a připraví nasazení spravované instance. `flux`
    * `config-agent` vytvoří cílový obor názvů.
    * `config-agent` připraví účet služby Kubernetes s příslušným oprávněním ( `cluster` nebo `namespace` oborem).
    * `config-agent` nasadí instanci `flux`
    * `flux` vygeneruje klíč SSH a zaprotokoluje veřejný klíč (Pokud použijete možnost SSH s klíči generovanými tokem).
1. `config-agent` hlásí stav zpět do `sourceControlConfiguration` prostředku v Azure.

Během procesu zřizování se `sourceControlConfiguration` přesunou mezi několika změnami stavu. Sledujte průběh pomocí `az k8sconfiguration show ...` příkazu výše:

1. `complianceStatus` -> `Pending`: představuje počáteční a průběžné stavy
1. `complianceStatus` -> `Installed`: `config-agent` bylo možné úspěšně nakonfigurovat cluster a nasadit `flux` bez chyby.
1. `complianceStatus` -> `Failed`: `config-agent` došlo k chybě při nasazování `flux` , podrobnosti by měly být k dispozici v `complianceStatus.message` těle odpovědi.

## <a name="apply-configuration-from-a-private-git-repository"></a>Použít konfiguraci z privátního úložiště Git

Pokud používáte privátní úložiště Git, musíte v úložišti nakonfigurovat veřejný klíč SSH. Veřejný klíč můžete nakonfigurovat buď na konkrétním úložišti Git, nebo na uživateli Git, který má přístup k úložišti. Veřejný klíč SSH bude buď ten, který poskytnete, nebo ten, který vygeneruje tok.

**Získání vlastního veřejného klíče**

Pokud jste vygenerovali vlastní klíče SSH, pak už máte privátní a veřejné klíče.

**Získání veřejného klíče pomocí Azure CLI (užitečné, pokud tok vygeneruje klíče)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Získat veřejný klíč z Azure Portal (užitečné, pokud tok vygeneruje klíče)**

1. V Azure Portal přejděte na prostředek připojeného clusteru.
2. Na stránce prostředek vyberte "GitOps" a podívejte se na seznam konfigurací pro tento cluster.
3. Vyberte konfiguraci, která používá soukromé úložiště Git.
4. V kontextovém okně, které se otevře, zkopírujte **veřejný klíč úložiště** do dolní části okna.

Pokud používáte GitHub, použijte jednu z následujících dvou možností:

**Možnost 1: přidejte veřejný klíč do svého uživatelského účtu (platí pro všechna úložiště ve vašem účtu).**

1. Otevřete GitHub, klikněte na ikonu profilu v pravém horním rohu stránky.
2. Klikněte na **Nastavení** .
3. Klikněte na **klíče SSH a GPG**
4. Klikněte na **nový klíč SSH** .
5. Zadejte název.
6. Vložte veřejný klíč (mínus všechny okolní uvozovky).
7. Klikněte na **Přidat klíč SSH** .

**Možnost 2: přidejte veřejný klíč jako klíč nasazení do úložiště Git (týká se jenom tohoto úložiště).**

1. Otevřete GitHub, přejděte do svého úložiště, do **Nastavení** a pak **Nasaďte klíče** .
2. Klikněte na **Přidat klíč nasazení** .
3. Zadejte název.
4. Ověřit **Povolení přístupu pro zápis**
5. Vložte veřejný klíč (mínus všechny okolní uvozovky).
6. Klikněte na **Přidat klíč** .

**Pokud používáte úložiště Azure DevOps, přidejte klíč k klíčům SSH.**

1. V části **nastavení uživatele** v pravém horním rohu (vedle obrázku profilu) klikněte na **veřejné klíče SSH** .
1. Vybrat  **+ nový klíč**
1. Zadejte název.
1. Vložte veřejný klíč bez okolních nabídek.
1. Klikněte na **Přidat** .

## <a name="validate-the-kubernetes-configuration"></a>Ověření konfigurace Kubernetes

Po `config-agent` instalaci `flux` instance by prostředky uchovávané v úložišti Git měly začít tok do clusteru. Zkontrolujte, zda byly vytvořeny obory názvů, nasazení a prostředky:

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

Můžete prozkoumat další prostředky nasazené jako součást úložiště konfigurace:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Odstraní konfiguraci.

Odstraňte `sourceControlConfiguration` pomocí Azure CLI nebo Azure Portal.  Po zahájení příkazu DELETE se `sourceControlConfiguration` prostředek v Azure odstraní hned a úplné odstranění přidružených objektů z clusteru by se mělo provádět do 10 minut.  Pokud `sourceControlConfiguration` je při odstranění v neúspěšném stavu, může trvat až hodinu úplné odstranění přidružených objektů.

> [!NOTE]
> Po vytvoření sourceControlConfiguration s oborem názvů je možné pro uživatele s `edit` vazbou role v oboru názvů nasadit úlohy v tomto oboru názvů. Pokud se tato akce `sourceControlConfiguration` s oborem názvů odstraní, obor názvů zůstane beze změny a nebude odstraněn, aby nedošlo k porušení těchto ostatních úloh.  V případě potřeby můžete tento obor názvů odstranit ručně pomocí kubectl.
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

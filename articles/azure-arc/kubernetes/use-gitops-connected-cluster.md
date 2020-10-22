---
title: Nasazení konfigurací pomocí GitOps v clusteru Kubernetes s povoleným ARC (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Použití GitOps pro konfiguraci clusteru s podporou ARC Azure (Preview)
keywords: GitOps, Kubernetes, K8s, Azure, ARC, Azure Kubernetes Service, Containers
ms.openlocfilehash: 1a8839c2463494ba0e165bf9e1a5d22245fac8df
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371252"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Nasazení konfigurací pomocí GitOps v clusteru Kubernetes s povoleným ARC (Preview)

GitOps je postup, který deklaruje požadovaný stav konfigurace Kubernetes (nasazení, obory názvů atd.) v úložišti Git následovaným dotazem a nasazením těchto konfigurací na cluster pomocí operátoru. Tento dokument popisuje nastavení takových pracovních postupů u clusterů Kubernetes s podporou ARC Azure.

Připojení mezi clusterem a jedním nebo více úložišť Git je sledováno v Azure Resource Manager jako `sourceControlConfiguration` prostředek rozšíření. `sourceControlConfiguration`Vlastnosti prostředku reprezentují, kde a jak by se měly prostředky Kubernetes z Gitu přesměrovat do vašeho clusteru. `sourceControlConfiguration`Data jsou uložená v klidovém stavu v Azure Cosmos DB databázi, aby se zajistila důvěrnost dat.

`config-agent`Spuštění ve vašem clusteru zodpovídá za sledování nových nebo aktualizovaných `sourceControlConfiguration` prostředků rozšíření na prostředku Kubernetes ARC Azure s povoleným nasazením operátoru toku, který sleduje úložiště Git a šíří jakékoli aktualizace provedené v `sourceControlConfiguration` . Pro dosažení víceklientské architektury je dokonce možné vytvořit více `sourceControlConfiguration` prostředků s `namespace` rozsahem na stejném clusteru Kubernetes s povoleným ARC Azure. V takovém případě každý operátor může nasadit konfigurace pouze do příslušného oboru názvů.

Úložiště Git může obsahovat všechny platné prostředky Kubernetes, včetně oborů názvů, ConfigMaps, nasazení, DaemonSets atd.  Může obsahovat také Helm grafy pro nasazení aplikací. Mezi běžné sady scénářů patří definování základní konfigurace pro vaši organizaci, která může zahrnovat běžné role a vazby Azure, agenty monitorování nebo protokolování nebo služby pro clustery v rámci clusteru.

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

Pomocí rozšíření Azure CLI pro `k8sconfiguration` , pojďme propojit náš připojený cluster s [ukázkovým úložištěm Git](https://github.com/Azure/arc-k8s-demo). Této konfiguraci přiřadíme název `cluster-config` , dáte pokyn agentovi, aby nasadil operátor do `cluster-config` oboru názvů a udělí operátorovi `cluster-admin` oprávnění.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>úložiště – parametr adresy URL

Tady jsou podporované scénáře pro parametr hodnota--úložiště-adresa URL.

| Scénář | Formát | Popis |
| ------------- | ------------- | ------------- |
| Veřejné úložiště Git | http [s]://Server/repo.Git nebo git://server/repo.git   | Veřejné úložiště Git  |
| Privátní úložiště Git – SSH – klíče vytvořené tokem | SSH://[user@] Server/úložiště. Git nebo [user@] Server: úložiště. Git | Veřejný klíč generovaný tokem musí být přidán do uživatelského účtu nebo úložiště ve vašem poskytovateli služby Git. Další podrobnosti najdete [tady](#apply-configuration-from-a-private-git-repository). |

Tyto scénáře jsou podporovány tokem, ale ne ještě pomocí sourceControlConfiguration.

| Scénář | Formát | Popis |
| ------------- | ------------- | ------------- |
| Privátní úložiště Git – HTTPS | https://server/repo.git | Již brzy (bude podporovat uživatelské jméno/heslo, uživatelské jméno/token, certifikát) |
| Privátní úložiště Git – SSH – uživatelsky poskytnuté klíče | SSH://[user@] Server/úložiště. Git nebo [user@] Server: úložiště. Git | Již brzy |
| Privátní hostitel Git – SSH – vlastní known_hosts | SSH://[user@] Server/úložiště. Git nebo [user@] Server: úložiště. Git | Již brzy |

#### <a name="additional-parameters"></a>Další parametry

Chcete-li upravit vytvoření konfigurace, je zde několik dalších parametrů:

`--enable-helm-operator` : *Volitelný* přepínač, který povolí podporu pro nasazení grafu Helm.

`--helm-operator-chart-values` : *Volitelné* hodnoty grafu pro operátor Helm (Pokud je povoleno).  Například: "--set Helm. verze = V3".

`--helm-operator-chart-version` : *Volitelná* verze grafu pro operátor Helm (Pokud je povolená). Výchozí: ' 0.6.0 '.

`--operator-namespace` : *Volitelný* název oboru názvů operátoru. Výchozí: výchozí

`--operator-params` : *Volitelné* parametry pro operátor. Musí být zadány v jednoduchých uvozovkách. Například ```--operator-params='--git-readonly --git-path=releases' ```.

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

* Pokud má enableHelmOperator hodnotu true, pak operatorInstanceName + operatorNamespace řetězce nesmí být v kombinaci 47 znaků.  Pokud se tomuto limitu nebudete řídit, zobrazí se následující chyba:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Další informace najdete v [dokumentaci ke službě tokem](https://aka.ms/FluxcdReadme).

> [!TIP]
> Je možné vytvořit sourceControlConfiguration na Azure Portal i na kartě **Konfigurace** v okně prostředku Kubernetes s podporou ARC Azure.

## <a name="validate-the-sourcecontrolconfiguration"></a>Ověřit sourceControlConfiguration

Pomocí rozhraní příkazového řádku Azure CLI ověřte, že se `sourceControlConfiguration` úspěšně vytvořil.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Všimněte si, že `sourceControlConfiguration` prostředek je aktualizovaný se stavem dodržování předpisů, zprávami a informacemi o ladění.

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
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
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Při `sourceControlConfiguration` vytvoření se v digestoři stane několik věcí:

1. Azure ARC `config-agent` monitoruje Azure Resource Manager pro nové nebo aktualizované konfigurace ( `Microsoft.KubernetesConfiguration/sourceControlConfiguration` ).
1. `config-agent` oznámení o nové `Pending` konfiguraci
1. `config-agent` přečte vlastnosti konfigurace a připraví nasazení spravované instance. `flux`
    * `config-agent` vytvoří cílový obor názvů.
    * `config-agent` připraví účet služby Kubernetes s příslušným oprávněním ( `cluster` nebo `namespace` oborem).
    * `config-agent` nasadí instanci `flux`
    * `flux` vygeneruje klíč SSH a zaprotokoluje veřejný klíč.
1. `config-agent` oznamuje stav zpět do `sourceControlConfiguration`

Během procesu zřizování se `sourceControlConfiguration` přesunou mezi několika změnami stavu. Sledujte průběh pomocí `az k8sconfiguration show ...` příkazu výše:

1. `complianceStatus` -> `Pending`: představuje počáteční a průběžné stavy
1. `complianceStatus` -> `Installed`: `config-agent` bylo možné úspěšně nakonfigurovat cluster a nasadit `flux` bez chyby.
1. `complianceStatus` -> `Failed`: `config-agent` došlo k chybě při nasazování `flux` , podrobnosti by měly být k dispozici v `complianceStatus.message` těle odpovědi.

## <a name="apply-configuration-from-a-private-git-repository"></a>Použít konfiguraci z privátního úložiště Git

Pokud používáte privátní úložiště Git, musíte provést další úlohu, abyste ukončili smyčku: do úložiště přidejte veřejný klíč vygenerovaný `flux` jako **klíč nasazení** .

**Získání veřejného klíče pomocí Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Získat veřejný klíč z Azure Portal**

1. V Azure Portal přejděte na prostředek připojeného clusteru.
2. Na stránce prostředek vyberte konfigurace a podívejte se na seznam konfigurací pro tento cluster.
3. Vyberte konfiguraci, která používá soukromé úložiště Git.
4. V kontextovém okně, které se otevře, zkopírujte **veřejný klíč úložiště**do dolní části okna.

**Přidat veřejný klíč jako klíč nasazení do úložiště Git**

1. Otevřete GitHub, přejděte do svého úložiště, do **Nastavení**a pak **Nasaďte klíče** .
2. Klikněte na **Přidat klíč nasazení** .
3. Zadejte název.
4. Ověřit **Povolení přístupu pro zápis**
5. Vložte veřejný klíč (mínus všechny okolní uvozovky).
6. Klikněte na **Přidat klíč** .

Další informace o tom, jak tyto klíče spravovat, najdete v dokumentaci k GitHubu.

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

Odstraňte `sourceControlConfiguration` pomocí Azure CLI nebo Azure Portal.  Po zahájení příkazu DELETE se `sourceControlConfiguration` prostředek v Azure odstraní hned, ale může trvat až 1 hodinu, než se z clusteru dokončí úplné odstranění přidružených objektů (pro snížení tohoto časového prodlevy máme položku backlogu).

> [!NOTE]
> Po vytvoření sourceControlConfiguration s oborem názvů je možné používat pro uživatele s `edit` vazbou role v oboru názvů nasazení úloh v tomto oboru názvů. Pokud se tato akce `sourceControlConfiguration` s oborem názvů odstraní, obor názvů zůstane beze změny a nebude odstraněn, aby nedošlo k porušení těchto ostatních úloh.
> Změny v clusteru, které byly výsledkem nasazení ze sledovaného úložiště Git, se při odstranění neodstraní `sourceControlConfiguration` .

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Výkonem**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Další kroky

- [Použití Helm s konfigurací správy zdrojového kódu](./use-gitops-with-helm.md)
- [Řízení konfigurace clusteru pomocí Azure Policy](./use-azure-policy.md)

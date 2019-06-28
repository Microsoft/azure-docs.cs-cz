---
title: Konfigurace služby Azure Monitor pro sběr dat agenta kontejnerů | Dokumentace Microsoftu
description: Tento článek popisuje, jak můžete nakonfigurovat monitorování Azure pro kontejnery agenta k řízení stdout/stderr a proměnných prostředí protokolu kolekce.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341649"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurace agenta shromažďování dat pro monitorování Azure pro kontejnery

Azure Monitor pro kontejnery shromažďuje stdout a stderr a proměnných prostředí z kontejneru úloh nasazených do spravované clustery Kubernetes hostované ve službě Azure Kubernetes Service (AKS) z kontejnerizovaných agenta. Nastavení shromažďování dat agenta můžete nakonfigurovat tak, že vytvoříte vlastní Kubernetes ConfigMaps řízení tohoto prostředí. Tento článek ukazuje, jak vytvořit ConfigMap a konfigurovat shromažďování dat na základě vašich požadavků.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Konfigurace clusteru pomocí nastavení sběru vlastních dat

Soubor šablony ConfigMap se zadává, který umožňuje snadno upravovat pomocí vlastního nastavení bez nutnosti vytvářet od začátku. Než začnete, přečtěte si dokumentaci ke Kubernetes o [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) a seznamte se s postupy vytvoření, konfigurace a nasazení ConfigMaps. To vám umožní filtrovat stderr a stdout na obor názvů nebo celý cluster a proměnných prostředí pro každý kontejner spuštění napříč všemi podů/uzly v clusteru.

>[!IMPORTANT]
>Verze minimální agenta podporovaných touto funkcí je microsoft / oms:ciprod06142019 nebo novější. 

### <a name="overview-of-configurable-data-collection-settings"></a>Přehled nastavení shromažďování dat konfigurovatelné

Níže jsou nastavení, které lze nakonfigurovat pro sběr dat řídit.

|Klíč |Typ dat |Value |Popis |
|----|----------|------|------------|
|`schema-version` |Řetězec (rozlišuje velikost písmen) |v1 |Toto je verze schématu při analýze tento ConfigMap používaný agentem. Aktuálně podporované verze schématu je v1. Upravování této hodnoty není podporován a při vyhodnocování ConfigMap budou odmítnuty.|
|`config-version` |String | | Podporuje možnost udržovat přehled o tomto konfiguračním souboru verze v systému/úložiště řízení zdrojů. Maximální povolený počet znaků je 10 a všechny ostatní znaky jsou zkrácena. |
|`[log_collection_settings.stdout] enabled =` |Boolean | PRAVDA nebo NEPRAVDA | Tato volba určuje, zda je povoleno shromažďování protokolů stdout kontejneru. Pokud je nastavena na `true` a nevylučují se žádné obory názvů pro shromažďování protokolů stdout (`log_collection_settings.stdout.exclude_namespaces` nastavení níže), stdout protokoly se budou shromažďovat od všech kontejnerů napříč všemi podů/uzly v clusteru. Pokud není zadán v ConfigMaps, výchozí hodnota je `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|String | Pole s hodnotami oddělenými čárkou |Pole Kubernetes obory názvů, pro které stdout se nebudou shromažďovat protokoly. Toto nastavení platí pouze tehdy, pokud `log_collection_settings.stdout.enabled` je nastavena na `true`. Pokud není zadán v ConfigMap, výchozí hodnota je `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | PRAVDA nebo NEPRAVDA |Tato volba určuje, zda je povoleno shromažďování protokolů kontejneru stderr. Pokud je nastavena na `true` a nevylučují se žádné obory názvů pro shromažďování protokolů stdout (`log_collection_settings.stderr.exclude_namespaces` nastavení), stderr protokoly se budou shromažďovat od všech kontejnerů napříč všemi podů/uzly v clusteru. Pokud není zadán v ConfigMaps, výchozí hodnota je `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |String |Pole s hodnotami oddělenými čárkou |Pole názvových prostorů Kubernetes, pro které stderr se nebudou shromažďovat protokoly. Toto nastavení platí pouze tehdy, pokud `log_collection_settings.stdout.enabled` je nastavena na `true`. Pokud není zadán v ConfigMap, výchozí hodnota je `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | PRAVDA nebo NEPRAVDA | Tato volba určuje, zda je povoleno shromažďování proměnné prostředí. Pokud je nastavena na `false`, žádné proměnné prostředí jsou shromažďovány pro každý kontejner spuštění napříč všemi podů/uzly v clusteru. Pokud není zadán v ConfigMap, výchozí hodnota je `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Nakonfigurujte a nasaďte ConfigMaps

Proveďte následující postup pro konfiguraci a nasazení vaší ConfigMap konfigurační soubor do clusteru.

1. [Stáhněte si](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) yaml ConfigMap šablony soubor a uložte ho jako kontejner azm-ms-agentconfig.yaml.  
1. Upravte soubor yaml ConfigMap svými vlastními úpravami. 

    - Vyloučit konkrétní obory názvů pro shromažďování protokolů stdout, nakonfigurujte podle následujícího příkladu klíč/hodnota: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Chcete-li zakázat kolekci proměnných prostředí pro konkrétní kontejner, nastavte klíč/hodnota `[log_collection_settings.env_var] enabled = true` povolit kolekci proměnných globálně, a pak postupujte podle kroků [tady](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) k dokončení konfigurace pro konkrétní kontejner.
    - Zakázat shromažďování protokolů stderr celého clusteru, nakonfigurujte podle následujícího příkladu klíč/hodnota: `[log_collection_settings.stderr] enabled = false`.

1. Vytvořte ConfigMap spuštěním následujícího příkazu kubectl: `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Příklad: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Změna konfigurace může trvat několik minut na dokončení před uplatněním a restartuje všechny omsagent podů v clusteru. Restartování se pro všechny podů omsagent postupné restartování, ne všechny restartujte ve stejnou dobu. Po dokončení se restartuje, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek: `configmap "container-azm-ms-agentconfig" created`.

Ověření konfigurace se úspěšně použily, použijte následující příkaz k kontrolujte protokoly ze agenta pod: `kubectl logs omsagent-fdf58 -n=kube-system`. Pokud jsou chyby v konfiguraci z osmagent podů, ve výstupu se zobrazí chyby nějak takto:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Chyby omsagent zabránit analýze souboru by ji restartovat a použít výchozí konfiguraci. Po vyřešení chyby v ConfigMap uložit soubor yaml a použít aktualizované ConfigMaps spuštěním příkazu: `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Použití aktualizace ConfigMap

Pokud jste už nasadili ConfigMap pro váš cluster a chcete ho aktualizovat novější konfigurací, můžete jednoduše upravit soubor ConfigMap jste používali dříve a pak aplikovat pomocí stejný příkaz jako předtím `kubectl apply -f <configmap_yaml_file.yaml`.

Změna konfigurace může trvat několik minut na dokončení před uplatněním a restartuje všechny omsagent podů v clusteru. Restartování se pro všechny podů omsagent postupné restartování, ne všechny restartujte ve stejnou dobu. Po dokončení se restartuje, zobrazí se zpráva, která je podobný následujícímu a zahrnuje výsledek: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Ověřování schématu verze

Jsou k dispozici jako poznámka pod (verze schématu) omsagent pod verze schématu podporovanou konfiguraci. Můžete je zobrazit pomocí následujícího příkazu kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Ve výstupu se zobrazí podobný následujícímu s verzí schématu poznámky:

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Další postup

- Chcete-li pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty vašeho clusteru AKS, přečtěte si téma [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).
- Zobrazení [protokolu Příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) předem definovaných dotazů a příklady, které vyhodnotí nebo přizpůsobení pro výstrahy vizualizace a analýza vašich clusterů.
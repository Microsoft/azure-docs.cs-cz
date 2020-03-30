---
title: Konfigurace Azure Monitor pro sběr dat agenta kontejnerů | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak můžete nakonfigurovat Agent Azure Monitor pro kontejnery pro řízení stdout/stderr a proměnné prostředí kolekce protokolu.
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 28b93190298ae61732ff7d2e297899af4ba0e5f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933020"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurace shromažďování dat agenta pro Azure Monitor pro kontejnery

Azure Monitor pro kontejnery shromažďuje proměnné stdout, stderr a prostředí z úloh kontejnerů nasazených do spravovaných clusterů Kubernetes od kontejnerizovaného agenta. Nastavení shromažďování dat agenta můžete nakonfigurovat vytvořením vlastních kubernetes ConfigMaps pro řízení tohoto prostředí. 

Tento článek ukazuje, jak vytvořit ConfigMap a konfigurovat shromažďování dat na základě vašich požadavků.

>[!NOTE]
>Pro Azure Red Hat OpenShift šablona ConfigMap soubor se vytvoří v oboru názvů *openshift-azure protokolování.* 
>

## <a name="configmap-file-settings-overview"></a>Přehled nastavení souboru ConfigMap

Šablona ConfigMap soubor je k dispozici, který vám umožní snadno upravovat s vlastní nastavení, aniž by museli vytvářet od nuly. Než začnete, měli byste si přečíst dokumentaci Kubernetes o [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) a seznámit se s tím, jak vytvářet, konfigurovat a nasazovat ConfigMaps. To vám umožní filtrovat stderr a stdout podle oboru názvů nebo napříč celým clusterem a proměnné prostředí pro všechny kontejnery spuštěné napříč všemi pody/uzly v clusteru.

>[!IMPORTANT]
>Minimální verze agenta podporovaná ke shromažďování proměnných stdout, stderr a prostředí z úloh kontejneru je ciprod06142019 nebo novější. Chcete-li ověřit verzi agenta, vyberte na kartě **Uzel** uzel a v podokně vlastností hodnotu poznámky **vlastnosti Číslo obrázku agenta.** Další informace o verzích agenta a o tom, co je součástí každé verze, naleznete v [poznámkách k verzi agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Nastavení shromažďování dat

Následují nastavení, která lze nakonfigurovat pro řízení shromažďování dat.

|Klíč |Datový typ |Hodnota |Popis |
|----|----------|------|------------|
|`schema-version` |Řetězec (malá a velká písmena) |v1 |Toto je verze schématu používaná agentem při analýzě této configMap. Aktuálně podporovaná verze schématu je v1. Změna této hodnoty není podporována a bude odmítnuta při vyhodnocení configMap.|
|`config-version` |Řetězec | | Podporuje možnost sledovat verzi tohoto konfiguračního souboru v systému/úložišti správy zdrojového kódu. Maximální povolený počet znaků je 10 a všechny ostatní znaky jsou zkráceny. |
|`[log_collection_settings.stdout] enabled =` |Logická hodnota | true nebo false | To toto určuje, pokud je povolena kolekce protokolu kontejneru stdout. Pokud je `true` nastavena a žádné obory názvů jsou`log_collection_settings.stdout.exclude_namespaces` vyloučeny pro kolekci protokolu stdout (nastavení níže), protokoly stdout budou shromažďovány ze všech kontejnerů napříč všechny pody/uzly v clusteru. Pokud není zadán v ConfigMaps, `enabled = true`výchozí hodnota je . |
|`[log_collection_settings.stdout] exclude_namespaces =`|Řetězec | Pole oddělené čárkami |Pole kubernetes obory názvů, pro které stdout protokoly nebudou shromažďovány. Toto nastavení je `log_collection_settings.stdout.enabled` účinné `true`pouze v případě, že je nastaveno na . Pokud není zadán v ConfigMap, `exclude_namespaces = ["kube-system"]`výchozí hodnota je .|
|`[log_collection_settings.stderr] enabled =` |Logická hodnota | true nebo false |To toto určuje, pokud je povolena kolekce protokolu kontejneru stderr. Pokud je `true` nastavena a žádné obory názvů jsou`log_collection_settings.stderr.exclude_namespaces` vyloučeny pro kolekci protokolu stdout (nastavení), stderr protokoly budou shromažďovány ze všech kontejnerů napříč všechny pody/uzly v clusteru. Pokud není zadán v ConfigMaps, `enabled = true`výchozí hodnota je . |
|`[log_collection_settings.stderr] exclude_namespaces =` |Řetězec |Pole oddělené čárkami |Pole kubernetes obory názvů, pro které stderr protokoly nebudou shromažďovány. Toto nastavení je `log_collection_settings.stdout.enabled` účinné `true`pouze v případě, že je nastaveno na . Pokud není zadán v ConfigMap, `exclude_namespaces = ["kube-system"]`výchozí hodnota je . |
| `[log_collection_settings.env_var] enabled =` |Logická hodnota | true nebo false | Toto nastavení řídí kolekci proměnných prostředí napříč všemi `enabled = true` pody/uzly v clusteru a ve výchozím nastavení, pokud není zadáno v ConfigMaps. Pokud je kolekce proměnných prostředí globálně povolena, můžete ji zakázat `AZMON_COLLECT_ENV` pro konkrétní kontejner nastavením proměnné prostředí na **Hodnotu False** buď s nastavením Dockerfile, nebo v [konfiguračním souboru pro pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) v části **env:** . Pokud kolekce proměnných prostředí je globálně zakázána, pak nelze povolit kolekci pro konkrétní kontejner (to znamená, že pouze přepsat, které lze použít na úrovni kontejneru je zakázat kolekci, když je již povolena globálně.). |
| `[log_collection_settings.enrich_container_logs] enabled =` |Logická hodnota | true nebo false | Toto nastavení řídí obohacování protokolu kontejneru k naplnění hodnot vlastností Name a Image pro každý záznam protokolu zapsaný do tabulky ContainerLog pro všechny protokoly kontejnerů v clusteru. Je výchozí, `enabled = false` pokud není zadán v ConfigMap. |

ConfigMaps je globální seznam a může být pouze jeden ConfigMap použít na agenta. Nelze mít jiný ConfigMaps převažují nad kolekce.

## <a name="configure-and-deploy-configmaps"></a>Konfigurace a nasazení map ConfigMaps

Chcete-li nakonfigurovat a nasadit konfigurační soubor ConfigMap do clusteru, proveďte následující kroky.

1. [Stáhněte si](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) šablonu ConfigMap yaml soubor a uložte jej jako kontejner-azm-ms-agentconfig.yaml. 

   >[!NOTE]
   >Tento krok není vyžadován při práci s Azure Red Hat OpenShift, protože šablona ConfigMap již v clusteru existuje.

2. Upravte soubor Yaml ConfigMap s vlastními úpravami, abyste shromáždili proměnné stdout, stderr nebo prostředí. Pokud upravujete soubor Yaml ConfigMap pro Azure Red Hat OpenShift, nejprve spusťte příkaz `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` a otevřete soubor v textovém editoru.

    - Chcete-li vyloučit určité obory názvů pro kolekci protokolu stdout, nakonfigurujte klíč/hodnotu pomocí následujícího příkladu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Chcete-li zakázat kolekci proměnných prostředí `[log_collection_settings.env_var] enabled = true` pro konkrétní kontejner, nastavte klíč/hodnotu, aby byla globálně povolena kolekce proměnných, a postupujte [podle kroků zde](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) k dokončení konfigurace pro konkrétní kontejner.
    
    - Chcete-li zakázat celý cluster kolekce protokolu stderr, nakonfigurujte klíč/hodnotu pomocí následujícího příkladu: `[log_collection_settings.stderr] enabled = false`.

3. Pro clustery jiné než Azure Red Hat OpenShift vytvořte ConfigMap `kubectl apply -f <configmap_yaml_file.yaml>` spuštěním následujícího příkazu kubectl: v jiných clusterech než Azure Red Hat OpenShift. 
    
    Příklad: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    Pro Azure Red Hat OpenShift uložte změny v editoru.

Změna konfigurace může trvat několik minut před projevem a všechny omsagent pody v clusteru se restartuje. Restartování je postupné restartování pro všechny pody omsagent, ne všechny restartovat současně. Po dokončení restartování se zobrazí zpráva podobná následující a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Ověřit konfiguraci

Chcete-li ověřit, že konfigurace byla úspěšně použita v jiném clusteru než azure red hat `kubectl logs omsagent-fdf58 -n=kube-system`openshift, použijte následující příkaz ke kontrole protokolů z podu agenta: . Pokud jsou chyby konfigurace z omsagent pods, výstup se zobrazí chyby podobné následující:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Chyby související s použitím změn konfigurace jsou také k dispozici ke kontrole. K provedení dalších změn konfigurace jsou k dispozici následující možnosti:

- Z agenta pod protokoly `kubectl logs` pomocí stejného příkazu. 

    >[!NOTE]
    >Tento příkaz se nevztahuje na cluster Azure Red Hat OpenShift.
    > 

- Z živých protokolů. Živé protokoly zobrazují chyby podobné následujícímu:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Z tabulky **KubeMonAgentEvents** v pracovním prostoru Analýzy protokolů. Data jsou odesílána každou hodinu s *chybovou* závažností chyb pro chyby konfigurace. Pokud nejsou žádné chyby, položka v tabulce bude mít data s *informacemi o*závažnosti , která nehlásí žádné chyby. **Vlastnost Tags** obsahuje další informace o pod a id kontejneru, na kterém došlo k chybě a také první výskyt, poslední výskyt a počet za poslední hodinu.

- S Azure Red Hat OpenShift zkontrolujte protokoly omsagent prohledáním **tabulky ContainerLog** a ověřte, jestli je povolena kolekce protokolu openshift-azure-logging.

Po opravě chyby v ConfigMap na clusterech než Azure Red Hat OpenShift, uložte soubor yaml a `kubectl apply -f <configmap_yaml_file.yaml`použít aktualizované ConfigMaps spuštěním příkazu: . Pro Azure Red Hat OpenShift upravte a uložte aktualizované configmaps spuštěním příkazu:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Použití aktualizované hospo-

Pokud jste již nasadili ConfigMap na jiných clusterech než Azure Red Hat OpenShift a chcete jej aktualizovat novější konfigurací, můžete upravit soubor ConfigMap, `kubectl apply -f <configmap_yaml_file.yaml`který jste dříve používali, a potom použít stejný příkaz jako dříve . Pro Azure Red Hat OpenShift upravte a uložte aktualizované configmaps spuštěním příkazu:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Změna konfigurace může trvat několik minut před projevem a všechny omsagent pody v clusteru se restartuje. Restartování je postupné restartování pro všechny pody omsagent, ne všechny restartovat současně. Po dokončení restartování se zobrazí zpráva podobná následující a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Ověření verze schématu

Podporované verze schématu konfigurace jsou k dispozici jako anotace pod (verze schématu) na podu omsagent. Můžete je vidět pomocí následujícího příkazu kubectl:`kubectl describe pod omsagent-fdf58 -n=kube-system`

Výstup se zobrazí podobně jako následující verze anotačního schématu:

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

## <a name="next-steps"></a>Další kroky

- Azure Monitor pro kontejnery neobsahuje předdefinovanou sadu výstrah. Projděte [si vytvořit výstrahy výkonu pomocí Azure Monitor pro kontejnery se dozvíte,](container-insights-alerts.md) jak vytvořit doporučené výstrahy pro vysoké využití procesoru a paměti pro podporu devOps nebo provozní procesy a postupy.

- Díky monitorování, které umožňuje shromažďovat využití stavu a prostředků vašeho AKS nebo hybridního clusteru a úloh, které na nich běží, se dozvíte, [jak používat](container-insights-analyze.md) Azure Monitor pro kontejnery.

- Chcete-li zobrazit předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení pro výstrahy, vizualizaci nebo analýzu clusterů, zobrazte [příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) protokolu.

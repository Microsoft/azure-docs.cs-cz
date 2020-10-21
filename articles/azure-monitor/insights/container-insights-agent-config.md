---
title: Konfigurace Azure Monitor pro shromažďování dat agenta kontejnerů | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat agenta Azure Monitor for Containers pro řízení kolekce protokolů stdout/stderr a proměnných prostředí.
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 1644e541ee873a5bb058dd9bde2b82a907a400ff
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320411"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Konfigurace shromažďování dat agenta pro službu Azure Monitor pro kontejnery

Azure Monitor pro kontejnery shromáždí z kontejnerového agenta prostředí stdout, stderr a environmentální proměnné z úloh kontejneru nasazených do spravovaných clusterů Kubernetes. Nastavení shromažďování dat agenta můžete nakonfigurovat vytvořením vlastního ConfigMaps Kubernetes pro řízení tohoto prostředí. 

Tento článek ukazuje, jak vytvořit ConfigMap a nakonfigurovat shromažďování dat podle vašich požadavků.

>[!NOTE]
>V případě Azure Red Hat OpenShift se vytvoří soubor šablony ConfigMap v oboru názvů *OpenShift-Azure-Logging* . 
>

## <a name="configmap-file-settings-overview"></a>Přehled nastavení souboru ConfigMap

K dispozici je soubor šablony ConfigMap, který umožňuje snadnou úpravu pomocí vlastního nastavení bez nutnosti vytvářet zcela nové. Než začnete, měli byste si projít dokumentaci k Kubernetes o [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) a seznámit se s tím, jak vytvářet, konfigurovat a nasazovat ConfigMaps. To vám umožní filtrovat stderr a STDOUT na obor názvů nebo napříč celým clusterem a proměnné prostředí pro libovolný kontejner běžící v rámci všech lusků nebo uzlů v clusteru.

>[!IMPORTANT]
>Minimální verze agenta podporovaná pro shromažďování stdout, stderr a proměnných prostředí z úloh kontejneru je ciprod06142019 nebo novější. Chcete-li ověřit verzi agenta, na kartě **uzel** vyberte uzel a v podokně vlastností hodnotu poznámky pro vlastnost **značka image agenta** . Další informace o verzích agenta a o tom, co je součástí každé vydané verze, najdete v tématu [poznámky k verzi agenta](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Nastavení shromažďování dat

Následující tabulka popisuje nastavení, která můžete nakonfigurovat pro řízení shromažďování dat:

| Key | Datový typ | Hodnota | Popis |
|--|--|--|--|
| `schema-version` | Řetězec (rozlišuje velká a malá písmena) | V1 | Toto je verze schématu používaná agentem.<br> při analýze tohoto ConfigMap.<br> Aktuálně podporovaná verze schématu je v1.<br> Úprava této hodnoty není podporována a bude<br> odmítnuto, když je vyhodnocen ConfigMap |
| `config-version` | Řetězec |  | Podporuje schopnost sledovat tuto verzi konfiguračního souboru v systému správy zdrojů nebo v úložišti.<br> Maximální povolený počet znaků je 10 a všechny ostatní znaky jsou zkráceny. |
| `[log_collection_settings.stdout] enabled =` | Logická hodnota | true nebo false | Tento ovládací prvek určuje, zda je povoleno shromažďování protokolů kontejnerů STDOUT. Při nastavení na `true` a nejsou vyloučeny žádné obory názvů pro shromažďování protokolů STDOUT.<br> ( `log_collection_settings.stdout.exclude_namespaces` Nastavení níže), protokoly stdout budou shromažďovány ze všech kontejnerů napříč všemi lusky/uzly v clusteru. Pokud není zadán v ConfigMaps,<br> Výchozí hodnota je `enabled = true` . |
| `[log_collection_settings.stdout] exclude_namespaces =` | Řetězec | Pole oddělené čárkami | Pole oborů názvů Kubernetes, pro které se protokoly stdout nebudou shromažďovat Toto nastavení je platné pouze v případě, že<br> `log_collection_settings.stdout.enabled`<br> je nastaven na `true` .<br> Pokud není zadán v ConfigMap, výchozí hodnota je<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.stderr] enabled =` | Logická hodnota | true nebo false | Tyto ovládací prvky, pokud je povoleno shromažďování protokolů kontejneru stderr.<br> Při nastavení na `true` a nejsou vyloučeny žádné obory názvů pro shromažďování protokolů STDOUT.<br> ( `log_collection_settings.stderr.exclude_namespaces` nastavení), protokoly stderr budou shromažďovány ze všech kontejnerů napříč všemi lusky nebo uzly v clusteru.<br> Pokud není zadán v ConfigMaps, výchozí hodnota je<br> `enabled = true`. |
| `[log_collection_settings.stderr] exclude_namespaces =` | Řetězec | Pole oddělené čárkami | Pole oborů názvů Kubernetes, pro které nebudou shromažďovány protokoly stderr<br> Toto nastavení je platné pouze v případě, že<br> `log_collection_settings.stdout.enabled` je nastaven na `true` .<br> Pokud není zadán v ConfigMap, výchozí hodnota je<br> `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` | Logická hodnota | true nebo false | Toto nastavení řídí kolekci proměnných prostředí.<br> napříč všemi lusky/uzly v clusteru<br> a výchozí hodnota, `enabled = true` Pokud není zadána<br> v ConfigMaps.<br> Pokud je kolekce proměnných prostředí globálně povolená, můžete ji zakázat pro konkrétní kontejner.<br> nastavením proměnné prostředí<br> `AZMON_COLLECT_ENV` na **hodnotu false** buď s nastavením souboru Dockerfile, nebo v [konfiguračním souboru pro](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) bod pod **obálkou ENV:** .<br> Pokud je kolekce proměnných prostředí globálně zakázaná, nemůžete povolit shromažďování pro konkrétní kontejner (to znamená, že jediné přepsání, které může být použito na úrovni kontejneru, je zakázat shromažďování, pokud je již povoleno globálně). |
| `[log_collection_settings.enrich_container_logs] enabled =` | Logická hodnota | true nebo false | Toto nastavení řídí rozšíření protokolu kontejneru, aby se naplnily hodnoty vlastností název a obrázek.<br> pro každý záznam protokolu zapsaný do tabulky ContainerLog pro všechny protokoly kontejnerů v clusteru.<br> Nastaví se na výchozí hodnotu, `enabled = false` Pokud není zadána v ConfigMap. |
| `[log_collection_settings.collect_all_kube_events]` | Logická hodnota | true nebo false | Toto nastavení umožňuje shromažďování událostí Kube všech typů.<br> Ve výchozím nastavení nejsou shromažďovány události Kube s typem *Normal* . Pokud je toto nastavení nastaveno na hodnotu `true` , *běžné* události již nejsou filtrovány a jsou shromažďovány všechny události.<br> Ve výchozím nastavení je tato hodnota nastavena na `false` . |

### <a name="metric-collection-settings"></a>Nastavení kolekce metriky

Následující tabulka popisuje nastavení, která můžete konfigurovat pro řízení shromažďování metrik:

| Key | Datový typ | Hodnota | Popis |
|--|--|--|--|
| `[metric_collection_settings.collect_kube_system_pv_metrics] enabled =` | Logická hodnota | true nebo false | Toto nastavení umožňuje shromažďovat metriky využití trvalých svazků (PV) v oboru názvů Kube-System. Ve výchozím nastavení se neshromažďují metriky využití pro trvalé svazky s deklaracemi trvalých svazků v oboru názvů Kube-System. Když je toto nastavení nastaveno na `true` , shromažďují se metriky využití PV pro všechny obory názvů. Ve výchozím nastavení je tato hodnota nastavena na `false` . |

ConfigMaps je globální seznam a v agentovi může být použit pouze jeden ConfigMap. Nemůžete mít k dispozici další ConfigMaps pro kolekce.

## <a name="configure-and-deploy-configmaps"></a>Konfigurace a nasazení ConfigMaps

Provedením následujících kroků nakonfigurujete a nasadíte konfigurační soubor ConfigMap do clusteru.

1. Stáhněte si [soubor Template CONFIGMAP YAML](https://github.com/microsoft/Docker-Provider/blob/ci_prod/kubernetes/container-azm-ms-agentconfig.yaml) a uložte ho jako Container-AZM-MS-agentconfig. yaml. 

   > [!NOTE]
   > Tento krok není nutný při práci s Azure Red Hat OpenShift, protože šablona ConfigMap už v clusteru existuje.

2. Upravte soubor ConfigMap YAML s vlastními nastaveními pro shromažďování proměnných prostředí stdout, stderr a/nebo. Pokud upravujete soubor ConfigMap YAML pro Azure Red Hat OpenShift, nejprve spusťte příkaz `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` a otevřete soubor v textovém editoru.

    - Chcete-li vyloučit konkrétní obory názvů pro shromažďování protokolů stdout, nakonfigurujte klíč nebo hodnotu pomocí následujícího příkladu: `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]` .
    
    - Chcete-li zakázat shromažďování proměnných prostředí pro konkrétní kontejner, nastavte klíč/hodnotu `[log_collection_settings.env_var] enabled = true` pro povolení globální kolekce proměnných a pak postupujte podle kroků [zde](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) , abyste dokončili konfiguraci konkrétního kontejneru.
    
    - Pokud chcete zakázat shromažďování protokolů protokolu stderr v clusteru, nakonfigurujte klíč nebo hodnotu pomocí následujícího příkladu: `[log_collection_settings.stderr] enabled = false` .

3. Pro jiné clustery než Azure Red Hat OpenShift vytvořte ConfigMap spuštěním následujícího příkazu kubectl: `kubectl apply -f <configmap_yaml_file.yaml>` v jiných clusterech než Azure Red Hat OpenShift. 
    
    Příklad: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

    V případě Azure Red Hat OpenShift uložte změny v editoru.

Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" created` .

## <a name="verify-configuration"></a>Ověřit konfiguraci

Pokud chcete ověřit, jestli se konfigurace úspěšně použila na jiný cluster než Azure Red Hat OpenShift, pomocí následujícího příkazu zkontrolujte protokoly z agenta pod: `kubectl logs omsagent-fdf58 -n kube-system` . Pokud dojde k chybám konfigurace z omsagent lusků, ve výstupu se zobrazí chyby podobné následujícímu:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Chyby související s použitím změn konfigurace jsou k dispozici také ke kontrole. K dispozici jsou následující možnosti, které umožňují provést další řešení potíží se změnami konfigurace:

- Od agenta pod protokoly pomocí stejného `kubectl logs` příkazu. 

    >[!NOTE]
    >Tento příkaz se nedá použít pro cluster Azure Red Hat OpenShift.
    > 

- Z živých protokolů. Live logs zobrazuje chyby podobné následujícímu:

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- Z tabulky **KubeMonAgentEvents** v pracovním prostoru Log Analytics. Data se odesílají každou hodinu s *chybovou* závažností pro chyby konfigurace. Pokud nedochází k žádným chybám, bude mít položka v tabulce údaje *o*závažnosti, které hlásí žádné chyby. Vlastnost **tagss** obsahuje další informace o ID pod a kontejneru, na kterém došlo k chybě, a také o prvním výskytu, posledním výskytu a počtu za poslední hodinu.

- Pomocí Azure Red Hat OpenShift Zkontrolujte protokoly omsagent, a to tak, že vyhledáte tabulku **ContainerLog** a ověříte, jestli je povolená kolekce protokolů OpenShift-Azure-Logging.

Po opravě chyb v ConfigMap na jiných clusterech než Azure Red Hat OpenShift uložte soubor YAML a použijte aktualizovaný ConfigMaps spuštěním příkazu: `kubectl apply -f <configmap_yaml_file.yaml` . V případě Azure Red Hat OpenShift upravte a uložte aktualizované ConfigMaps spuštěním příkazu:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

## <a name="applying-updated-configmap"></a>Použití aktualizovaných ConfigMap

Pokud jste už nasadili ConfigMap na jiné clustery než Azure Red Hat OpenShift a chcete ji aktualizovat pomocí novější konfigurace, můžete upravit soubor ConfigMap, který jste dřív použili, a pak použít stejný příkaz jako předtím `kubectl apply -f <configmap_yaml_file.yaml` . V případě Azure Red Hat OpenShift upravte a uložte aktualizované ConfigMaps spuštěním příkazu:

``` bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Dokončení změny konfigurace může trvat několik minut, než se projeví, a všechny omsagent v clusteru se restartují. Restartování je postupné restartování pro všechny omsagent lusky, ne pro všechna restartování ve stejnou dobu. Po dokončení restartů se zobrazí zpráva podobná následujícímu příkladu a obsahuje výsledek: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verifying-schema-version"></a>Ověřuje se verze schématu.

Podporované verze schématu konfigurace jsou k dispozici jako Poznámka (verze schématu) na omsagent pod. Můžete je zobrazit pomocí následujícího příkazu kubectl: `kubectl describe pod omsagent-fdf58 -n=kube-system`

Ve výstupu se zobrazí zpráva podobná následující se schématy poznámky – verze:

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

- Azure Monitor pro kontejnery neobsahují předdefinovanou sadu výstrah. Přečtěte si téma [vytvoření výstrah výkonu s Azure monitor pro kontejnery](./container-insights-log-alerts.md) , kde se dozvíte, jak vytvořit Doporučené výstrahy pro zajištění vysokého využití procesoru a paměti, aby podporovaly vaše DevOps nebo provozní procesy a postupy.

- Díky monitorování s povoleným shromažďováním informací o stavu a využití prostředků v AKS nebo hybridním clusteru a úlohách, které se na nich běží, se naučíte, [Jak používat](container-insights-analyze.md) Azure monitor pro kontejnery.

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizace a analýzy clusterů.
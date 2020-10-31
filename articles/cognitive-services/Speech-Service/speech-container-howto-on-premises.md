---
title: Použití kontejnerů služby Speech s Kubernetes a Helm
titleSuffix: Azure Cognitive Services
description: Když použijete Kubernetes a Helm k definování imagí na kontejnerech pro text a převod textu na řeč, vytvoříme balíček Kubernetes. Tento balíček se nasadí do místního clusteru Kubernetes.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: aahi
ms.openlocfilehash: 277a3c1c53564d7c5dff6a87381680a7f41606de
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131594"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Použití kontejnerů služby Speech s Kubernetes a Helm

Jednou z možností správy vašich místních kontejnerů řeči je použití Kubernetes a Helm. Když použijete Kubernetes a Helm k definování imagí na kontejnerech pro text a převod textu na řeč, vytvoříme balíček Kubernetes. Tento balíček se nasadí do místního clusteru Kubernetes. Nakonec se podíváme, jak otestovat nasazené služby a různé možnosti konfigurace. Další informace o spouštění kontejnerů Docker bez orchestrace Kubernetes najdete v tématu [instalace a spuštění kontejnerů služby Speech](speech-container-howto.md).

## <a name="prerequisites"></a>Předpoklady

Před použitím kontejnerů řeči v místním prostředí použijte následující požadavky:

| Vyžadováno | Účel |
|----------|---------|
| Účet Azure | Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet][free-azure-account], ještě než začnete. |
| Přístup k Container Registry | Aby Kubernetes mohl načíst image Docker do clusteru, bude potřebovat přístup k registru kontejneru. |
| Kubernetes CLI | [KUBERNETES CLI][kubernetes-cli] se vyžaduje pro správu sdílených přihlašovacích údajů z registru kontejneru. Kubernetes je také potřeba před Helm, což je správce balíčků Kubernetes. |
| Helm CLI | Nainstalujte rozhraní příkazového [řádku Helm][helm-install], které se používá k instalaci grafu Helm (definice balíčku kontejneru). |
|Prostředek řeči |Aby bylo možné tyto kontejnery použít, je nutné mít následující:<br><br>Prostředek Azure _Speech_ pro získání přidruženého fakturačního klíče a identifikátoru URI koncového bodu faktury Obě hodnoty jsou k dispozici na stránkách přehled a klíče Azure Portal **řeči** a jsou požadovány ke spuštění kontejneru.<br><br>**{API_KEY}** : klíč prostředku<br><br>**{ENDPOINT_URI}** : příklad identifikátoru URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Doporučená konfigurace hostitelského počítače

Referenční informace najdete v tématu podrobnosti o [hostitelském počítači kontejneru služby Speech][speech-container-host-computer] . Tento *graf Helm* automaticky vypočítá požadavky na procesor a paměť na základě toho, kolik dekódování (souběžných požadavků) uživatel zadá. Navíc se upraví na základě toho, jestli jsou optimalizace pro vstup zvuku a textu nakonfigurované jako `enabled` . Graf Helm je ve výchozím nastavení na hodnotu, dvě souběžné požadavky a zakazování optimalizace.

| Služba | PROCESOR/kontejner | Paměť/kontejner |
|--|--|--|
| **Převod řeči na text** | jeden dekodér vyžaduje minimálně 1 150 millicores. Pokud `optimizedForAudioFile` je povolená, pak je potřeba 1 950 millicores. (výchozí: dva dekodéry) | Požadováno: 2 GB<br>Omezeno: 4 GB |
| **Převod textu na řeč** | Jedna souběžná žádost vyžaduje minimálně 500 millicores. Pokud `optimizeForTurboMode` je povolená, pak je potřeba 1 000 millicores. (výchozí: dvou souběžných požadavků) | Požadováno: 1 GB<br> Omezeno: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Připojení ke clusteru Kubernetes

V hostitelském počítači se očekává, že bude dostupný cluster Kubernetes. V tomto kurzu najdete informace o [nasazení clusteru Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) pro koncepční porozumění způsobu nasazení clusteru Kubernetes do hostitelského počítače.

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurace hodnot grafu Helm pro nasazení

Navštivte [Centrum Microsoft Helme][ms-helm-hub] , kde najdete všechny veřejně dostupné grafy Helm, které nabízí Microsoft. V centru Microsoft Helm najdete **místní graf Cognitive Services řeč** . **Místní Cognitive Services řeč** je graf, který nainstalujeme, ale nejdřív je potřeba vytvořit `config-values.yaml` soubor s explicitními konfiguracemi. Pojďme začít přidáním úložiště Microsoftu do naší instance Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Dále nakonfigurujeme hodnoty grafu Helm. Zkopírujte a vložte následující YAML do souboru s názvem `config-values.yaml` . Další informace o přizpůsobení **Cognitive Servicesho rozpoznávání řeči v místním Helm grafu** najdete v tématu [přizpůsobení Helm grafů](#customize-helm-charts). Nahraďte `# {ENDPOINT_URI}` `# {API_KEY}` komentáře a vlastními hodnotami.

```yaml
# These settings are deployment specific and users can provide customizations
# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: mcr.microsoft.com
    repository: azure-cognitive-services/speechservices/speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Pokud nejsou `billing` `apikey` zadány hodnoty a, vyprší platnost služeb po 15 minutách. Ověření se nezdaří, protože služby nebudou k dispozici.

### <a name="the-kubernetes-package-helm-chart"></a>Balíček Kubernetes (Helm graf)

*Graf Helm* obsahuje konfiguraci, které imagí Docker mají být vyžádané z `mcr.microsoft.com` registru kontejneru.

> [Graf Helm][helm-charts] je kolekce souborů, které popisují související sadu prostředků Kubernetes. Jeden graf se dá použít k nasazení jednoduchého, podobného memcached nebo nějakého složitého, jako je úplný zásobník webových aplikací se servery HTTP, databázemi, mezipamětemi a tak dále.

Poskytnuté *grafy Helm* si z registru kontejnerů stáhnou image služby pro rozpoznávání řeči, a to jak pro převod textu na řeč, tak i ze služeb převodu řeči na text `mcr.microsoft.com` .

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalace grafu Helm v clusteru Kubernetes

K instalaci *grafu Helm* je potřeba provést [`helm install`][helm-install-cmd] příkaz a nahradit ho `<config-values.yaml>` názvem odpovídající cestou a argumentu název souboru. `microsoft/cognitive-services-speech-onpremise`Graf Helm, na který se odkazuje níže, je k dispozici v [centru Microsoftu pro Microsoft Helm][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Tady je příklad výstupu, který byste mohli očekávat od úspěšného provedení instalace:

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Dokončení nasazení Kubernetes může trvat několik minut. Chcete-li ověřit, že jsou všechny lusky i služby správně nasazené a dostupné, spusťte následující příkaz:

```console
kubectl get all
```

Měli byste očekávat, že se zobrazí něco podobného následujícímu výstupu:

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>Ověření nasazení Helm pomocí testů Helm

Nainstalované grafy Helm definují *testy Helm* , které slouží jako pohodlí pro ověřování. Tyto testy ověřují připravenost služby. Pokud chcete ověřit služby převodu **řeči na text** i text **na řeč** , spusťte příkaz [Helm test][helm-test] .

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Tyto testy selžou, pokud stav POD není `Running` nebo pokud nasazení není uvedené pod `AVAILABLE` sloupcem. Musí být pacient, protože se to může trvat déle než 10 minut.

Tyto testy budou mít za následek výstup různých výsledků stavu:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Jako alternativu ke spuštění *testů Helm* můžete z příkazu shromáždit *externí IP* adresy a odpovídající porty `kubectl get all` . Pomocí IP adresy a portu otevřete webový prohlížeč a přejděte na stránku a `http://<external-ip>:<port>:/swagger/index.html` Zobrazte si stránky rozhraní API Swagger.

## <a name="customize-helm-charts"></a>Přizpůsobení Helm grafů

Grafy Helm jsou hierarchické. Hierarchicky umožňují dědění grafu, ale také v konceptu specifičnosti, kde nastavení, která jsou konkrétnější, přepíší zděděná pravidla.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Další kroky

Další podrobnosti o instalaci aplikací pomocí Helm ve službě Azure Kubernetes Service (AKS) [najdete tady][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontejnery služeb Cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md

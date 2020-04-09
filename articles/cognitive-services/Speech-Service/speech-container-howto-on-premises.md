---
title: Použití kontejnerů služby Řeči s Kubernetes a Helm
titleSuffix: Azure Cognitive Services
description: Pomocí Kubernetes a Helm k definování obrázků kontejnerů převodu řeči na text a převodu textu na řeč vytvoříme balíček Kubernetes. Tento balíček se nasadí do místního clusteru Kubernetes.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3c183f6d0e2d80ed497654448a726a1562bd046c
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874334"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Použití kontejnerů služby Řeči s Kubernetes a Helm

Jednou z možností správy kontejnerů řeči v místním prostředí je použití Kubernetes a Helm. Pomocí Kubernetes a Helm k definování obrázků kontejnerů převodu řeči na text a převodu textu na řeč vytvoříme balíček Kubernetes. Tento balíček se nasadí do místního clusteru Kubernetes. Nakonec se podíváme, jak otestovat nasazené služby a různé možnosti konfigurace. Další informace o spouštění kontejnerů Dockeru bez orchestrace Kubernetes najdete v [tématu instalace a spuštění kontejnerů služby rozpoznávání řeči](speech-container-howto.md).

## <a name="prerequisites"></a>Požadavky

Následující požadavky před použitím kontejnerů řeči v místním prostředí:

| Požaduje se | Účel |
|----------|---------|
| Účet Azure | Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,][free-azure-account] než začnete. |
| Přístup k registru kontejnerů | Aby Kubernetes natáhl iniciální sítě dockeru do clusteru, bude potřebovat přístup k registru kontejnerů. |
| Kubernetes CLI | [Kubernetes CLI][kubernetes-cli] je vyžadovánpro správu sdílených pověření z registru kontejneru. Kubernetes je také potřeba před Helmem, který je správcem balíčků Kubernetes. |
| Helm CLI | Nainstalujte [helmcli][helm-install], který se používá k instalaci kormidelník (definice balíčku kontejneru). |
|Zdroj řeči |Chcete-li používat tyto kontejnery, musíte mít:<br><br>Prostředek _Řeči_ Azure získat přidružený fakturační klíč a identifikátor URI koncového bodu fakturace. Obě hodnoty jsou k dispozici na webu Přehled **řeči** a klíče webu portálu Azure a jsou nutné ke spuštění kontejneru.<br><br>**{API_KEY}**: klíč prostředku<br><br>**{ENDPOINT_URI}:** Příkladem identifikátoru URI koncového bodu je:`https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Doporučená konfigurace hostitelského počítače

Podrobnosti [o hostitelském počítači kontejneru služby Rozpoznávání řeči][speech-container-host-computer] naleznete jako odkaz. Tento *kormidelní kormidelník* automaticky vypočítá požadavky na procesor a paměť na základě počtu dekódů (souběžných požadavků), které uživatel určí. Kromě toho se upraví na základě toho, zda jsou `enabled`optimalizace pro vstup zvuku a textu nakonfigurovány jako . Kormidelník výchozí, dva souběžné požadavky a zakázání optimalizace.

| Služba | CPU / kontejner | Paměť / Kontejner |
|--|--|--|
| **Převod řeči na text** | jeden dekodér vyžaduje minimálně 1150 milijamerů. Pokud `optimizedForAudioFile` je povoleno, pak 1950 milicores jsou požadovány. (výchozí: dva dekodéry) | Povinné: 2 GB<br>Omezeno: 4 GB |
| **Text-to-speech** | jeden souběžný požadavek vyžaduje minimálně 500 milicores. Pokud `optimizeForTurboMode` je povoleno, jsou požadovány 1 000 milijader. (výchozí: dva souběžné požadavky) | Povinné: 1 GB<br> Omezeno: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Připojení ke clusteru Kubernetes

Očekává se, že hostitelský počítač bude mít k dispozici cluster Kubernetes. V tomto kurzu o [nasazení clusteru Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md) pro koncepční pochopení, jak nasadit cluster Kubernetes do hostitelského počítače.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Sdílení přihlašovacích údajů Dockeru pomocí clusteru Kubernetes

Chcete-li povolit cluster Uubernetes na `docker pull` konfigurované bitové kopie z registru `containerpreview.azurecr.io` kontejneru, je třeba přenést pověření dockeru do clusteru. Spusťte níže uvedený [`kubectl create`][kubectl-create] příkaz a vytvořte tajný klíč registru *dockeru* na základě pověření poskytnutých z předpokladu přístupu k registru kontejneru.

Z vybraného rozhraní příkazového řádku spusťte následující příkaz. Nezapomeňte nahradit pověření `<username>` `<password>`registru `<email-address>` kontejneru a pověření kontejneru.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Pokud již máte přístup `containerpreview.azurecr.io` k registru kontejneru, můžete vytvořit tajný klíč Kubernetes pomocí obecný příznak místo. Zvažte následující příkaz, který se provede proti konfiguraci Dockeru JSON.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Následující výstup je vytištěn do konzoly po úspěšném vytvoření tajného klíče.

```console
secret "mcr" created
```

Chcete-li ověřit, že tajný [`kubectl get`][kubectl-get] klíč `secrets` byl vytvořen, spusťte s příznakem.

```console
kubectl get secrets
```

Spuštění vytiskne `kubectl get secrets` všechny nakonfigurované tajné klíče.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Konfigurace hodnot grafu helmu pro nasazení

Navštivte [Microsoft Helm Hub][ms-helm-hub] pro všechny veřejně dostupné kormidelní kormidelní kormidelní kyna, které nabízí Microsoft. V centru Microsoft Helm Hub najdete **místní graf řeči kognitivních služeb**. **Virtuální řeč služby Cognitive Services v místním prostředí** je graf, `config-values.yaml` který nainstalujeme, ale musíme nejprve vytvořit soubor s explicitními konfiguracemi. Začněme přidáním úložiště Microsoft do naší instance Helm.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

Dále nakonfigurujeme hodnoty helmového grafu. Zkopírujte a vložte následující yaml do souboru s názvem `config-values.yaml`. Další informace o přizpůsobení **grafu místního helmu řeči služby Cognitive Services**naleznete v [tématu přizpůsobení kormidelních diagramů](#customize-helm-charts). `# {ENDPOINT_URI}` Nahraďte `# {API_KEY}` komentáře a vlastními hodnotami.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
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
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> Pokud `billing` hodnoty `apikey` a nejsou k dispozici, služby vyprší po 15 minutách. Stejně tak se ověření nezdaří, protože služby nebudou k dispozici.

### <a name="the-kubernetes-package-helm-chart"></a>Balíček Kubernetes (Helmův graf)

*Helm graf* obsahuje konfiguraci, které image dockeru `containerpreview.azurecr.io` pro vyžádat z registru kontejneru.

> [Helm chart][helm-charts] je kolekce souborů, které popisují související sadu prostředků Kubernetes. Jeden graf může být použit k nasazení něco jednoduchého, jako memcached pod nebo něco složitého, jako je úplný zásobník webových aplikací s HTTP servery, databázemi, mezipaměti a tak dále.

Za *předpokladu, helm grafy* vyžádat iktorády služby řeči, a to `containerpreview.azurecr.io` jak pro převod textu na řeč a služby převodu řeči na text z registru kontejneru.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Instalace grafu Helm v clusteru Kubernetes

Chcete-li nainstalovat *kormidelník graf* budeme muset provést [`helm install`][helm-install-cmd] příkaz, nahradí `<config-values.yaml>` s příslušnou cestu a název souboru argument. Níže `microsoft/cognitive-services-speech-onpremise` uvedený graf Helm je k dispozici na [webu Microsoft Helm Hub zde][ms-helm-hub-speech-chart].

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

Zde je příklad výstupu, který můžete očekávat z úspěšného spuštění instalace:

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

Nasazení Kubernetes může trvat déle než několik minut. Chcete-li ověřit, zda jsou pody i služby správně nasazeny a k dispozici, proveďte následující příkaz:

```console
kubectl get all
```

Měli byste očekávat, že uvidíte něco podobného následujícímu výstupu:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Ověření nasazení helmu pomocí testů helmu

Nainstalované grafy helmu definují *testy Helm*, které slouží jako pohodlí pro ověření. Tyto testy ověřují připravenost služby. Chcete-li ověřit **služby převodu řeči na text** i **převod textu na řeč,** provedeme příkaz [Test helmu.][helm-test]

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Tyto testy se nezdaří, `Running` pokud stav POD není nebo `AVAILABLE` pokud nasazení není uveden ve sloupci. Buďte trpěliví, protože to může trvat déle než deset minut.

Tyto testy budou výstup různé výsledky stavu:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Jako alternativu k provádění *testů kormidla*můžete shromažďovat *externí IP* adresy `kubectl get all` a odpovídající porty z příkazu. Pomocí IP a portu otevřete webový `http://<external-ip>:<port>:/swagger/index.html` prohlížeč a přejděte na stránku (stránky) API.

## <a name="customize-helm-charts"></a>Přizpůsobení kormidelních grafů

Grafy kormidel jsou hierarchické. Hierarchické umožňuje dědičnost grafu, ale také obstarává koncept specifičnosti, kde nastavení, které jsou konkrétnější přepsat zděděná pravidla.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Další kroky

Další podrobnosti o instalaci aplikací pomocí helmu ve službě Azure Kubernetes Service (AKS) [najdete na tomto webu][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontejnery služeb cognitive services][cog-svcs-containers]

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

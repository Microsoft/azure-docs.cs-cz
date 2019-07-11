---
title: Použití Kubernetes on-premises
titleSuffix: Azure Cognitive Services
description: Definování Image řeči na text a převod textu na řeč kontejnerů pomocí Kubernetes a Helm, vytvoříme balíček Kubernetes. Tento balíček se nasadí v místním clusteru Kubernetes.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/10/2019
ms.author: dapine
ms.openlocfilehash: 33d9de956a6d43145fc68f4ec46b09b8e8bf0188
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786244"
---
# <a name="use-kubernetes-on-premises"></a>Použití Kubernetes on-premises

Definování Image řeči na text a převod textu na řeč kontejnerů pomocí Kubernetes a Helm, vytvoříme balíček Kubernetes. Tento balíček se nasadí v místním clusteru Kubernetes. Nakonec se podíváme, jak otestovat nasazených služeb a různé možnosti konfigurace.

## <a name="prerequisites"></a>Požadavky

Před použitím řeči kontejnery v místním, musí splňovat následující požadavky:

|Požadováno|Účel|
|--|--|
| Účet Azure | Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet][free-azure-account] před tím, než začnete. |
| Přístup k registru kontejneru | Aby stažení imagí dockeru do clusteru Kubernetes ji bude potřebovat přístup k registru kontejneru. Je potřeba [žádat o přístup k registru kontejneru][speech-preview-access] první. |
| Kubernetes CLI | [Příkazového řádku Kubernetes][kubernetes-cli] je nezbytný pro správu sdílené přihlašovací údaje ze služby container registry. Kubernetes je také potřeba před Helm, což je Správce balíčků Kubernetes. |
| Helm CLI | Jako součást [Helm CLI][helm-install] install, you'll also need to initialize Helm which will install [Tiller][tiller-install]. |
|Prostředek řeči |Chcete-li použít tyto kontejnery, musíte mít:<br><br>A _řeči_ prostředků Azure můžete získat přidružený klíč účtování a fakturace identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na webu Azure portal **řeči** stránky přehled a klíče a jsou potřebná ke spuštění kontejneru.<br><br>**{KLÍČ ROZHRANÍ API}** : klíč prostředku<br><br>**{ENDPOINT_URI}** : Příklad identifikátor URI koncového bodu je: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>Konfigurace počítače se doporučený hostitel

Odkazovat [Speech Service kontejneru hostitelský počítač][speech-container-host-computer] podrobnosti jako odkaz. To *grafu helmu* automaticky vypočítá procesoru a paměti požadavky, které jsou založené na tom, kolik dekóduje (souběžných požadavků), který uživatel zadá. Kromě toho se upraví podle toho, jestli je optimalizace audio/textové zadání nastavily `enabled`. Výchozí grafu helm do dvou souběžných požadavků a vypnutím optimalizace.

| Služba | Využití procesoru / kontejner | Paměť / kontejner |
|--|--|--|
| **Speech-to-Text** | jeden dekodér vyžaduje minimálně 1,150 jednotkách millicore. Pokud `optimizedForAudioFile` je povolené, pak 1,950 jednotkách millicore jsou povinné. (výchozí: dva dekodérů) | Požadováno: 2 GB<br>Limited:  4 GB |
| **Text-to-Speech** | jeden souběžný požadavek vyžaduje minimálně jednotkách millicore 500. Pokud `optimizeForTurboMode` je povoleno, 1 000 jednotkách millicore jsou povinné. (výchozí: dvou souběžných požadavků) | Požadováno: 1 GB<br> Limited: 2 GB |

## <a name="connect-to-the-kubernetes-cluster"></a>Připojte se ke clusteru Kubernetes

Hostitelský počítač má mít cluster Kubernetes ve službě k dispozici. Najdete v tomto kurzu na [nasadit Kubernetes cluster](../../aks/tutorial-kubernetes-deploy-cluster.md) pro rámcové informace o tom, jak nasadit Kubernetes cluster na hostitelském počítači.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Přihlašovací údaje Dockeru pro sdílení obsahu s clusterem Kubernetes

Povolit clusteru Kubernetes do `docker pull` nakonfigurovanou Image z `containerpreview.azurecr.io` registru kontejneru, se musí přenést přihlašovací údaje dockeru do clusteru. Spustit [ `kubectl create` ][kubectl-create] následujícího příkazu vytvořte *tajný klíč registru dockeru* na základě přihlašovacích údajů k dispozici z požadovaného softwaru přístup k registru kontejneru.

Z rozhraní příkazového řádku podle vlastní volby, spusťte následující příkaz. Nezapomeňte nahradit `<username>`, `<password>`, a `<email-address>` pomocí přihlašovacích údajů registru kontejneru.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> Pokud už máte přístup k `containerpreview.azurecr.io` registru kontejneru, můžete vytvořit pomocí obecného příznak místo tajného kódu Kubernetes. Vezměte v úvahu následující příkaz, který se spustí před konfigurací Docker JSON.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

Po úspěšném vytvoření tajného klíče, vytiskne se následující výstup do konzoly.

```console
secret "containerpreview" created
```

Pokud chcete ověřit, zda byl vytvořen tajný kód, spusťte [ `kubectl get` ][kubectl-get] s `secrets` příznak.

```console
kuberctl get secrets
```

Provádění `kubectl get secrets` vytiskne všechny nakonfigurované tajné kódy.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>Nakonfigurujte hodnoty v grafu Helm pro nasazení

Přejděte [centrum Microsoftu Helm][ms-helm-hub] pro všemi grafy helm veřejně dostupné nabízené microsoftem. Z centra společnosti Microsoft Helm, zjistíte, **Cognitive Services řeči On-Premises tabulka**. **Cognitive Services řeči On-Premises** je graf nainstalujeme, ale musíte nejprve vytvořit `config-values.yaml` soubor s explicitní konfigurace. Začněme přidáním úložiště společnosti Microsoft do našich Helm instance.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

V dalším kroku nakonfigurujeme naše hodnoty v grafu Helm. Zkopírujte a vložte následující kód YAML do souboru s názvem `config-values.yaml`. Další informace o přizpůsobení **Cognitive Services řeči On-Premises Helm Chart**, naleznete v tématu [přizpůsobit helmu](#customize-helm-charts). Nahradit `billing` a `apikey` hodnoty vlastními.

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >

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
      - containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # < Your billing URL >
      apikey: # < Your API Key >
```

> [!IMPORTANT]
> Pokud `billing` a `apikey` nejsou k dispozici hodnoty, služby skončí za 15 minut. Ověření se nezdaří, nebudou k dispozici služby.

### <a name="the-kubernetes-package-helm-chart"></a>Balíček Kubernetes (grafu helmu)

*Grafu helmu* obsahuje konfiguraci které Image dockeru pro vyžádání obsahu ze `containerpreview.azurecr.io` registru kontejneru.

> A [grafu helmu][helm-charts] je kolekce souborů, které popisují sadu související prostředky Kubernetesu. Jeden graf zneužít k nasazení něco jednoduchého, jako memcached pod nebo něco komplexní, například zásobníku aplikace plně webovými servery protokolu HTTP, databází, mezipaměti a tak dále.

Poskytnuté *grafy Helm* stažení imagí dockeru služby řeči, převodu textu na řeč a řeči na text služby od `containerpreview.azurecr.io` registru kontejneru.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Nainstalovat Helm chart v clusteru Kubernetes

K instalaci *grafu helmu* budeme muset provést [ `helm install` ][helm-install-cmd] příkaz a nahraďte `<config-values.yaml>` s příslušnou argumentem Název a cesta k souboru. `microsoft/cognitive-services-speech-onpremise` Grafu helmu odkazuje níže je k dispozici na [Helm Centrum Microsoftu][ms-helm-hub-speech-chart].

```console
helm install microsoft/cognitive-services-speech-onpremise \
    --version 0.1.0 \
    --values <config-values.yaml> \
    --name onprem-speech
```

Tady je příklad výstupu, který očekáváte, abyste viděli spuštění úspěšné instalaci:

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

Nasazení Kubernetes můžete převzít kontrolu nad několik minut na dokončení. Abyste se přesvědčili, že podů a služby správně nasazen a dostupný, spusťte následující příkaz:

```console
kubectl get all
```

By se měl vypadat podobně jako následující výstup:

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

### <a name="verify-helm-deployment-with-helm-tests"></a>Ověření nasazení Helm pomocí Helm testů

Definovat nainstalované helmu *Helm testy*, který bude sloužit jako usnadnění pro ověření. Tyto testy ověření připravenosti služby. Ověřit oba **speech to text** a **převod textu na řeč** services, jsme budete spouštět [Helm test][helm-test] příkazu.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> Tyto testy se nezdaří, pokud není stav POD `Running` nebo pokud nasazení není uvedený v seznamu `AVAILABLE` sloupce. Buďte prosím trpěliví. jak to může trvat více než deset minut na dokončení.

Tyto testy se výstupní výsledky různých stavů:

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

Jako alternativu k provádění *helm testy*, můžete shromažďovat, *externí IP adresa* adresy a odpovídající porty z `kubectl get all` příkazu. Pomocí IP adresy a portu, otevřete webový prohlížeč a přejděte do `http://<external-ip>:<port>:/swagger/index.html` do rozhraní API swaggeru stránky zobrazení.

## <a name="customize-helm-charts"></a>Přizpůsobení helmu

Grafy Helm, jsou hierarchická. Umožňuje vrácení hierarchický graf dědičnosti, je také určeného pro Koncept specifičnosti, kde nastavení, které jsou určené přepsat zděděný pravidla.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

## <a name="next-steps"></a>Další postup

Podrobné informace o instalaci aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS) [najdete zde][installing-helm-apps-in-aks].

> [!div class="nextstepaction"]
> [Kontejnery služby cognitive Services][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/helm/#helm-install
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/developing_charts
[speech-preview-access]: https://aka.ms/speechcontainerspreview
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md

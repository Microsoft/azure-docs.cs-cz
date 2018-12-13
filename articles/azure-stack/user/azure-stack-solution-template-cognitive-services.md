---
title: Nasazení služby Azure Cognitive Services k Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nasadit Azure Cognitive Services do služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.openlocfilehash: 5af508714b5eae5cdd23c940af0ae21300c0c5b8
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194660"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Nasazení služby Azure Cognitive Services k Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!Note]  
> Azure Cognitive Services ve službě Azure Stack je ve verzi preview.

Podpora kontejnerů ve službě Azure Stack můžete použít Azure Cognitive Services. Podpora kontejnerů ve službě Azure Cognitive Services umožňuje používat stejná bohatá rozhraní API, které jsou dostupné v Azure. Používání kontejnerů umožňuje flexibilitu při nasazení a hostování služeb poskytovaných v [kontejnery Dockeru](https://www.docker.com/what-container). Podpora kontejnerů je aktuálně dostupná ve verzi preview pro podmnožinu služeb Azure Cognitive Services, včetně částí [pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview), a [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), a [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Kontejnerizace je přístup k distribuci softwaru, ve kterém aplikace nebo služby, včetně jeho závislosti a konfigurace, jsou sbaleny jako image kontejneru. S téměř nebo vůbec žádné změny můžete nasadit bitovou kopii k hostiteli. Každý kontejner je izolovaná od jiných kontejnerů a od základního operačního systému. Samotný systém, pouze obsahuje komponenty potřebné ke spuštění bitové kopie. Hostitel kontejneru má menší nároky na místo než virtuální počítač. Kromě toho můžete vytvořit kontejnery z bitových kopií pro krátkodobé úkoly a odebrat, pokud už je nepotřebujete.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Používat kontejnery pomocí služeb Cognitive Services v Azure stacku

- **Řízení nad daty**  
  Povolit uživatelům vaší aplikace máte kontrolu nad svými daty při používání služeb Cognitive Services. Cognitive Services můžete doručovat aplikace uživatelům, kteří nemůže odesílat data do globálního Azure nebo ve veřejném cloudu.

- **Aktualizace ovládacího prvku modelu**  
  Poskytují aplikace uživatelům na verzi a aktualizace modelů nasazení v jejich řešení.

- **Přenosné architektury**  
  Umožňují vytvářet architektury přenosné aplikace tak, aby vaše řešení můžete nasadit do veřejného cloudu, privátního cloudu na pracoviště nebo na hraničních zařízeních. Kontejner můžete nasadit do služby Azure Kubernetes Service, Azure Container Instances, nebo do clusteru Kubernetes ve službě Azure Stack. Další informace najdete v tématu [nasazení Kubernetes pro Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

- **Vysoká propustnost a nízká latence**  
   Umožní uživatelům vaší aplikace ke škálování se špičkám provozu, nízké latence a Vysoká propustnost. Povolení služby Cognitive Services ke spuštění ve službě Azure Kubernetes Service fyzicky blízko jejich aplikační logiku a data.

Pomocí služby Azure Stack nasazení služeb Cognitive Services kontejnerů v clusteru Kubernetes spolu s aplikace kontejnerů pro zajištění vysoké dostupnosti a elastické škálování. Díky kombinaci služeb Cognitive services komponenty sestavené v App Services, funkce, objekt Blob úložiště, nebo SQL nebo mySQL databáze můžete vyvíjet aplikace. 

Podrobné informace o kontejnerech služby Cognitive Services, přejděte na [podpora kontejnerů ve službě Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Nasazení Azure API pro rozpoznávání tváře

Tento článek popisuje, jak nasadit Azure API pro rozpoznávání tváře na clusteru Kubernetes ve službě Azure Stack. Stejný přístup můžete použít k nasazení dalších služeb cognitive services kontejnerů v clusteru Kubernetes se službou Azure Stack.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete muset:

1.  Požádat o přístup k registru kontejneru přetahování imagí kontejneru pro rozpoznávání tváře z Azure Cognitive Services Container Registry. Podrobnosti najdete v části [žádat o přístup k registru kontejneru soukromého](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Příprava cluster Kubernetes ve službě Azure Stack. Můžete postupovat podle článku [nasazení Kubernetes pro Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Vytvoření prostředků Azure

Vytvořte prostředek služby Cognitive Services v Azure ve verzi preview pro rozpoznávání tváře, LUIS nebo rozpoznat Text kontejnery, v uvedeném pořadí. Je potřeba použít předplatné key a koncového bodu adresy URL z prostředku nástroje pro vytvoření instance kontejnerů služby cognitive Services.

1.  Vytvoření prostředku Azure na webu Azure Portal. Pokud chcete zobrazit náhled kontejnery pro rozpoznávání tváře, musíte nejprve vytvořit odpovídající prostředek pro rozpoznávání tváře na webu Azure Portal. Další informace najdete v tématu [rychlý start: Vytvoření účtu služeb Cognitive Services na webu Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

    >  [!Note]  
    >  Prostředek pro rozpoznávání tváře nebo pro počítačové zpracování obrazu musí používat F0 cenovou úroveň.

2.  Získáte klíče koncového bodu adresy URL a předplatného pro prostředky Azure. Po vytvoření prostředku Azure musíte použít předplatné key a koncového bodu adresy URL z tohoto prostředku pro vytvoření instance kontejneru odpovídající pro rozpoznávání tváře, LUIS nebo rozpoznat Text ve verzi Preview.

## <a name="create-a-kubernetes-secret"></a>Vytvoření tajného kódu Kubernetes 

Využijte použití Kubectl vytvoření tajného kódu příkazu získat přístup k registru kontejneru soukromého. Nahraďte **&lt;uživatelské jméno&gt;** s uživatelským jménem a **&lt;heslo&gt;** s heslem v přihlašovací údaje, které jste získali od Azure Kognitivní služby týmu.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Příprava YAML konfigurace souboru

Chcete použít YAML konfigurovat souborů pro zjednodušení nasazení služby cognitive Services v clusteru Kubernetes.

Tady je ukázka YAML nakonfigurovat soubor pro rozpoznávání tváře službu nasadíte do služby Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

V tomto YAML nakonfigurovat soubor, použijte tajný klíč, který jste použili k získání služby cognitive Services imagí kontejnerů ze služby Azure Container Registry. A použijte soubor tajného kódu pro nasazení konkrétní repliky kontejneru. Můžete také vytvořit nástroj pro vyrovnávání zatížení, abyste měli jistotu, že uživatelé mají přístup k této službě externě.

Podrobnosti o klíčová pole:

| Pole | Poznámky |
| --- | --- |
| replicaNumber | Definuje počáteční repliky instance, které chcete vytvořit. Změníte její škálu seděl později po dokončení nasazení. |
| Vlastnost imageLocation | Určuje umístění konkrétní služby cognitive Services image kontejneru v ACR. Například služba pro rozpoznávání tváře: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |Adresa URL koncového bodu, které jste si poznamenali v kroku [vytvoření prostředků Azure](#create-azure-resources) |
| ApiKey | Klíč předplatného jste si poznamenali v kroku [vytvoření prostředků Azure](#create-azure-resources) |
| secretName | Název tajného kódu, který právě jste si poznamenali v kroku [vytvořit secrete získat přístup k registru kontejneru soukromého](#create-secrete-to-access-the-private-container-registry) |

## <a name="deploy-the-cognitive-service"></a>Nasazení služby cognitive Services

Použijte následující příkaz k nasazení kontejnerů služby cognitive Services

```bash  
    Kubectl apply -f <yamlFineName>
```
Použijte následující příkaz k monitorování, jak nasazení: 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Testování služby cognitive Services

Přístup [specifikace OpenAPI](https://swagger.io/docs/specification/about/) (dříve specifikace Swagger), popisující operací podporované instance kontejneru, z **/swagger** relativní identifikátor URI pro tento kontejner. Například následující identifikátor URI poskytuje přístup k specifikace OpenAPI pro analýzu mínění kontejneru, která byla vytvořena instance v předchozím příkladu:

```HTTP  
http:<External IP>:5000/swagger
```

Externí IP adresu můžete získat pomocí následujícího příkazu: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Vyzkoušejte si služby s využitím Pythonu

Můžete zkusit spuštěním skriptů Pythonu a nějaké jednoduché ověření služeb Cognitive services v Azure stacku. Existují oficiální ukázky quickstart Pythonu pro [pro počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/overview), a [rozhraní Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), a [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) () Služba LUIS) pro vaši informaci.

Existují dvě věci potřeba mít na paměti, aby se spustily služby spuštěné v kontejnerech aplikace v Pythonu: 
1. Cognitive services v kontejneru není nutné dílčí klíče pro ověřování, ale je ještě nutné zadat libovolný řetězec jako zástupný symbol pro uspokojení sady SDK. 
2. Nahradit base_URL koncový bod IP adresou aktuální služby 

Tady je ukázka Python skript, které pro rozpoznávání tváře služby SDK pro Python k detekci a snímků tváří v obrázku. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Další postup

[Jak nainstalovat a spustit kontejnery rozhraní API pro počítačové zpracování obrazu.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Jak nainstalovat a spustit kontejnery API pro rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#create-a-face-resource-on-azure)

[Jak nainstalovat a spustit kontejnery rozhraní Text Analytics API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Jak nainstalovat a spustit kontejnery Language Understanding (LIUS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
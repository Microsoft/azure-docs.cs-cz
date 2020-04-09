---
title: Použití nástroje Docker Compose k nasazení několika kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nasadit více kontejnerů služeb Cognitive Services. Tento článek ukazuje, jak organizovat více imonl ů kontejnerů Dockerpomocí Docker compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 458cda927a6a123fcd9962efc6ab705e13f43286
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878778"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Použití nástroje Docker Compose k nasazení několika kontejnerů

Tento článek ukazuje, jak nasadit více kontejnerů Azure Cognitive Services. Konkrétně se dozvíte, jak pomocí Docker Compose organizovat více ibližů kontejneru Dockeru.

> [Docker Compose](https://docs.docker.com/compose/) je nástroj pro definování a spouštění aplikací Dockeru s více kontejnery. V compose použijete soubor YAML ke konfiguraci služeb vaší aplikace. Potom můžete vytvořit a spustit všechny služby z konfigurace spuštěním jediného příkazu.

Může být užitečné orchestrovat více iobrazek kontejnerů v jednom hostitelském počítači. V tomto článku budeme skládat dohromady kontejnery pro rozpoznávání čtení a formuláře.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které musí být nainstalovány a spuštěny místně:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* [Modul dockeru](https://www.docker.com/products/docker-engine). Zkontrolujte, zda v okně konzoly funguje cli dockeru.
* Prostředek Azure se správnou cenovou úrovní. S tímto kontejnerem fungují pouze následující cenové úrovně:
  * **Zdroj počítačového vidění** pouze s cenovou úrovní F0 nebo Standard.
  * **Prostředek nástroje pro rozpoznávání formulářů** pouze s cenovou úrovní F0 nebo Standard.
  * **Prostředek služeb Cognitive Services** s cenovou úrovní S0.

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Vyplňte a odešlete [formulář žádosti o kontejnery řeči služby Cognitive Services](https://aka.ms/speechcontainerspreview/). 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Soubor Docker Compose

Soubor YAML definuje všechny služby, které mají být nasazeny. Tyto služby spoléhají `DockerFile` na image existujícího kontejneru nebo existující image kontejneru. V takovém případě použijeme dva obrázky náhledu. Zkopírujte a vložte následující soubor YAML a uložte jej jako *docker-compose.yaml*. Zadejte příslušné **apikey**, **fakturace**a **EndpointUri** hodnoty v souboru.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Vytvořte adresáře v hostitelském počítači, které jsou určeny pod uzlu **svazků.** Tento přístup je vyžadován, protože adresáře musí existovat před pokusem o připojení bitové kopie pomocí svazek vazby.

## <a name="start-the-configured-docker-compose-services"></a>Spuštění nakonfigurovaných služeb Docker Compose

Soubor Docker Compose umožňuje správu všech fází v životním cyklu definované služby: spuštění, zastavení a opětovné sestavení služeb; zobrazení stavu služby; a streamování protokolů. Otevřete rozhraní příkazového řádku z adresáře projektu (kde je umístěn soubor docker-compose.yaml).

> [!NOTE]
> Chcete-li se vyhnout chybám, ujistěte se, že hostitelský počítač správně sdílí jednotky s Docker Engine. Pokud se například *e:\publicpreview* používá jako adresář v souboru *docker-compose.yaml,* sdílejte jednotku **E** s Dockerem.

Z rozhraní příkazového řádku spusťte následující příkaz pro spuštění (nebo restartování) všech služeb definovaných v souboru *docker-compose.yaml:*

```console
docker-compose up
```

Při prvním spuštění příkazu **docker-compose up** pomocí této konfigurace vytáhne bitové kopie nakonfigurované pod uzlem **služby** a pak je stáhne a připojí:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Po stažení obrázků jsou spuštěny služby bitových kopií:

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Ověření dostupnosti služby

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Zde je několik příkladů výstupu:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>Zkušební nádoby

Otevřete prohlížeč v hostitelském počítači a přejděte na **localhost** pomocí zadaného portu ze http://localhost:5021/swagger/index.htmlsouboru *docker-compose.yaml,* například . Můžete například použít **funkci Try It** v rozhraní API k testování koncového bodu nástroje pro rozpoznávání formulářů. Oba kontejnery naparování stránky by měly být k dispozici a testovatelné.

![Kontejner nástroje pro rozpoznávání formulářů](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontejnery služeb Cognitive Services](../cognitive-services-container-support.md)

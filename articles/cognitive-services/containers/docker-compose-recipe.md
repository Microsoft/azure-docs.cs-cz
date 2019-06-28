---
title: Docker compose recepty kontejneru
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nasadit několik služeb Cognitive Services kontejnerů. Tento postup ukazuje, jak orchestrovat více imagí kontejnerů Dockeru pomocí Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445794"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Použití více kontejnerů v privátní síti pomocí Docker Compose

Zjistěte, jak nasadit několik služeb Cognitive Services kontejnerů. Tento postup ukazuje, jak orchestrovat více imagí kontejnerů Dockeru pomocí Docker Compose.

> [Docker Compose](https://docs.docker.com/compose/) je nástroj pro definování a spouštění více kontejnerů aplikací Dockeru. S Compose použijte soubor YAML konfigurace služby pro vaše aplikace. Pak pomocí jediného příkazu, vytvoření a spuštění všech služeb z vaší konfigurace.

V případě potřeby a Orchestrace několik imagí kontejneru na jeden hostitelský počítač může být zajímavé. V tomto článku jsme vám sjednotí rozpoznat Text služba a Služba rozpoznávání formuláře.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které musí být nainstalován a spuštěn místně.

* Použijte předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* [Modul docker](https://www.docker.com/products/docker-engine) a ověřte, že rozhraní příkazového řádku Dockeru funguje v okně konzoly.
* Prostředek Azure s správné cenovou úroveň. Ne všechny cenové úrovně pracovat s tímto kontejnerem:
  * **Počítačové zpracování obrazu** pouze úrovní prostředků, které F0 nebo standardní ceny.
  * **Formulář pro rozpoznávání** pouze úrovní prostředků, které F0 nebo standardní ceny.
  * **Služby cognitive Services** cenová úroveň prostředku s S0.

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Vyplňte a odešlete [formulář žádosti o kontejnerech řeči Cognitive Services](https://aka.ms/speechcontainerspreview/) chcete požádat o přístup ke kontejneru. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker compose souboru

Soubor YAML definuje všechny služby pro nasazení. Tyto služby využívají buď `DockerFile` nebo existující image kontejneru, v tomto případě použijeme dvě Image ve verzi preview. Zkopírujte a vložte následující soubor YAML a uložte ho jako *docker compose.yaml*. Zadejte odpovídající _apikey_, _fakturační_, a _identifikátor URI koncového bodu_ hodnoty v _docker-compose.yml_ souboru níže.

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
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Vytvoření adresáře na hostitelském počítači, které jsou uvedené v části `volumes` uzlu. To je požadována, protože adresářů, musí existovat před pokusem o připojení bitové kopie s vazbami svazku.

## <a name="start-the-configured-docker-compose-services"></a>Spuštění nakonfigurované docker compose služby

Docker compose souboru umožňuje správu všech definovaných služby životního cyklu; spuštění/zastavení a opětovné vytvoření služby, zobrazení stavu služby a streamování protokolů. Otevřete rozhraní příkazového řádku z adresáře projektu (kde *docker compose.yaml* nachází soubor).

> [!NOTE]
> Aby nedocházelo k chybám, ujistěte se, že hostitelský počítač se správně sdílení jednotek s **modul Docker**. Například pokud *e:\publicpreview* slouží jako adresář v *docker compose.yaml* sdílet *E jednotka* s dockerem.

Z rozhraní příkazového řádku spusťte následující příkaz ke spuštění (nebo restartování) všechny služby, které jsou definovány v *docker compose.yaml*:

```console
docker-compose up
```

První čas spuštění `docker-compose up` příkazu s touto konfigurací **Docker** přetáhne Image nakonfigurovaná pod daným `services` uzlu – stažení/připojení je:

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
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

Stažení Image a image služby jsou spuštěny.

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

## <a name="verify-the-service-availability"></a>Ověřit dostupnost služby

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Tady je příklad výstupu:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Test kontejneru rozpoznání textu

Otevřete na hostitelském počítači prohlížeč a přejděte do `localhost` s zadaný port z *docker compose.yaml*, například `http://localhost:5021/swagger/index.html`. Zkuste jej funkce můžete použít rozhraní API k otestování koncový bod rozpoznání textu.

![Rozpoznávání textu Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Test kontejneru rozlišovač formuláře

Otevřete na hostitelském počítači prohlížeč a přejděte do `localhost` s zadaný port z *docker compose.yaml*, například `http://localhost:5010/swagger/index.html`. Zkuste jej funkce můžete použít rozhraní API pro testování koncového bodu modulu pro rozpoznávání formuláře.

![Formulář pro rozpoznávání Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kontejnery služby cognitive Services](../cognitive-services-container-support.md)
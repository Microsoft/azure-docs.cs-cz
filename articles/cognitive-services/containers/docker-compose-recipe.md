---
title: Použití nástroje Docker Compose k nasazení několika kontejnerů
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nasadit více kontejnerů Cognitive Services. V tomto článku se dozvíte, jak orchestrovat více imagí kontejnerů Docker pomocí Docker Compose.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: aahi
ms.openlocfilehash: cedcf8a3fcd656c4af0ca7493c598791d35d20d9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996117"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Použití nástroje Docker Compose k nasazení několika kontejnerů

V tomto článku se dozvíte, jak nasadit více kontejnerů Azure Cognitive Services. Konkrétně se naučíte, jak používat Docker Compose k orchestraci více imagí kontejneru Docker.

> [Docker Compose](https://docs.docker.com/compose/) je nástroj pro definování a spouštění aplikací Docker pro více kontejnerů. V sestavách pomocí souboru YAML nakonfigurujete služby vaší aplikace. Pak můžete vytvořit a spustit všechny služby z konfigurace spuštěním jediného příkazu.

Může být užitečné pro orchestraci více imagí kontejneru v jednom hostitelském počítači. V tomto článku si vyžádáme kontejnery pro čtení a rozpoznávání formulářů.

## <a name="prerequisites"></a>Požadavky

Tento postup vyžaduje několik nástrojů, které je třeba nainstalovat a spustit místně:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/cognitive-services).
* [Modul Docker](https://www.docker.com/products/docker-engine). Potvrďte, že rozhraní příkazového řádku Docker funguje v okně konzoly.
* Prostředek Azure se správnou cenovou úrovní. S tímto kontejnerem fungují jenom následující cenové úrovně:
  * **Počítačové zpracování obrazu** prostředek s pouze cenovou úrovní F0 nebo Standard.
  * Prostředek **pro rozpoznávání formulářů** s cenovou úrovní F0 nebo Standard.
  * **Cognitive Services** prostředku pomocí cenové úrovně S0
* Pokud používáte kontejner ověřovaného náhledu, budete muset vyplnit [formulář žádosti online](https://aka.ms/csgate/) , abyste ho mohli použít.

## <a name="docker-compose-file"></a>Soubor Docker Compose

Soubor YAML definuje všechny služby, které mají být nasazeny. Tyto služby spoléhají na buď `DockerFile` nebo existující image kontejneru. V tomto případě použijeme dvě image ve verzi Preview. Zkopírujte a vložte následující soubor YAML a uložte ho jako *Docker-tváře. yaml*. Zadejte v souboru příslušné hodnoty **apikey**, **fakturace** a **hodnot endpointuri** .

```yaml
version: '3.7'
services:
  forms:
    image: "mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout"
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
    image: "mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Vytvořte adresáře na hostitelském počítači, které jsou zadány pod uzlem **svazky** . Tento přístup je nutný, protože adresáře musí existovat předtím, než se pokusíte připojit bitovou kopii pomocí vazeb svazků.

## <a name="start-the-configured-docker-compose-services"></a>Spusťte nakonfigurované služby Docker Compose.

Soubor Docker Compose umožňuje správu všech fází v životním cyklu definované služby: spouštění, zastavování a obnovování služeb; zobrazení stavu služby; a streamování protokolů. Otevřete rozhraní příkazového řádku z adresáře projektu (kde se nachází soubor Docker-tváře. yaml).

> [!NOTE]
> Aby se předešlo chybám, ujistěte se, že hostitelský počítač správně sdílí jednotky s modulem Docker. Například pokud se *E:\publicpreview* používá jako adresář v souboru *Docker-tváře. yaml* , sdílejte jednotku **E** s Docker.

V rozhraní příkazového řádku spusťte následující příkaz, který spustí (nebo restartuje) všechny služby definované v souboru *Docker-tváře. yaml* :

```console
docker-compose up
```

První Docker spustí příkaz **Docker-sestavit** pomocí této konfigurace, načte image nakonfigurované v uzlu **služby** a pak je stáhne a připojí:

```console
Pulling forms (mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout:)...
latest: Pulling from azure-cognitive-services/form-recognizer/layout
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
Pulling ocr (mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview:)...
latest: Pulling from /azure-cognitive-services/vision/read:3.1-preview
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

Po stažení imagí se služby image Services spustí:

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

Tady je příklad výstupu:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout          latest
4be104c126c5        mcr.microsoft.com/azure-cognitive-services/vision/read:3.1-preview         latest
```

### <a name="test-containers"></a>Kontejnery testů

Otevřete na hostitelském počítači prohlížeč a v souboru *Docker-YAML* , který používá zadaný port, přejít na **localhost** http://localhost:5021/swagger/index.html . Můžete například použít funkci **try it** v rozhraní API k otestování koncového bodu pro rozpoznávání formulářů. Stránky Swagger kontejneru by měly být k dispozici a testovatelné.

![Kontejner pro rozpoznávání formulářů](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kontejnery Cognitive Services](../cognitive-services-container-support.md)

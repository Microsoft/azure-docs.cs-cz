---
title: Konfigurace kontejneru pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak nakonfigurovat kontejner nástroje pro rozpoznávání formulářů tak, aby analyzovat data formuláře a tabulky.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: bc48c0ba23e73adec312adfeeb1fcd57dba6ceec
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879152"
---
# <a name="configure-form-recognizer-containers"></a>Konfigurace kontejnerů nástroje pro rozpoznávání formulářů

Pomocí kontejnerů Azure Form Recognizer můžete vytvořit architekturu aplikace, která je optimalizovaná tak, aby využívala jak robustní cloudové funkce, tak hraniční lokalitu.

Prostředí run-time nástroje Pro rozpoznávání formulářů `docker run` nakonfigurujete pomocí argumentů příkazu. Tento kontejner má několik požadovaných nastavení a několik volitelných nastavení. Několik příkladů naleznete v části ["Příklad příkazů spuštění dockeru".](#example-docker-run-commands) Nastavení specifická pro kontejner jsou nastavení fakturace.

> [!IMPORTANT]
> Kontejnery nástroje pro rozpoznávání formulářů aktuálně používají verzi 1.0 rozhraní API pro rozpoznávání formulářů. K nejnovější verzi rozhraní API můžete získat přístup pomocí spravované služby.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) a jsou používány společně. Je nutné zadat platné hodnoty pro všechna tři nastavení; v opačném případě se kontejner nespustí. Další informace o použití těchto nastavení konfigurace k vytvoření instance kontejneru naleznete v tématu [Fakturace](form-recognizer-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

Toto `ApiKey` nastavení určuje klíč prostředků Azure, který se používá ke sledování fakturačních údajů pro kontejner. Hodnota pro ApiKey musí být platný klíč pro prostředek nástroje pro `Billing` rozpoznávání _formulářů,_ který je určen v části Nastavení konfigurace fakturace.

Toto nastavení najdete na webu Azure Portal v části **Správa prostředků nástroje pro rozpoznávání formulářů**v části **Klíče**.

## <a name="applicationinsights-setting"></a>ApplicationInsights, nastavení

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

Toto `Billing` nastavení určuje identifikátor URI koncového bodu prostředku _nástroje pro rozpoznávání formulářů v_ Azure, který se používá k měření fakturačních informací pro kontejner. Hodnota pro toto nastavení konfigurace musí být platný identifikátor URI koncového bodu pro prostředek _nástroje pro rozpoznávání formulářů v_ Azure. Kontejner hlásí využití přibližně každých 10 až 15 minut.

Toto nastavení najdete na webu Azure Portal v části **Přehled rozpoznávání formulářů**v části **Koncový bod**.

|Požaduje se| Name (Název) | Datový typ | Popis |
|--|------|-----------|-------------|
|Ano| `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace. Další informace o získání fakturačního identifikátoru URI naleznete v [tématu shromažďování požadovaných parametrů](form-recognizer-container-howto.md#gathering-required-parameters). Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení fluentdu

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení pověření proxy http

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]


## <a name="mount-settings"></a>Nastavení připojení

Pomocí vazební připojení číst a zapisovat data do a z kontejneru. Vstupní nebo výstupní připojení můžete určit zadáním `--mount` volby v [ `docker run` příkazu](https://docs.docker.com/engine/reference/commandline/run/).

Kontejner Nástroje pro rozpoznávání formulářů vyžaduje vstupní připojení a výstupní připojení. Vstupní připojení může být jen pro čtení a je vyžadováno pro přístup k datům, která se používají pro školení a vyhodnocování. Výstupní připojení musí být zapisovatelné a použít k ukládání modelů a dočasných dat.

Přesná syntaxe umístění připojení hostitele se liší v závislosti na hostitelském operačním systému. Umístění připojení [hostitelského počítače](form-recognizer-container-howto.md#the-host-computer) navíc nemusí být přístupné z důvodu konfliktu mezi oprávněními účtu služby Docker a oprávněními umístění připojení hostitele.

|Nepovinné| Name (Název) | Datový typ | Popis |
|-------|------|-----------|-------------|
|Požaduje se| `Input` | Řetězec | Cíl vstupního připojení. Výchozí hodnota je `/input`.    <br><br>Příklad:<br>`--mount type=bind,src=c:\input,target=/input`|
|Požaduje se| `Output` | Řetězec | Cíl výstupního připojení. Výchozí hodnota je `/output`.  <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="example-docker-run-commands"></a>Příklad příkazů spuštění dockeru

Následující příklady používají nastavení konfigurace pro ilustraci, jak psát a používat `docker run` příkazy. Když je spuštěn, kontejner pokračuje v běhu, dokud [jej nezastavíte](form-recognizer-container-howto.md#stop-the-container).

* **Znak pokračování řádku**: Příkazy Dockeru v následujících\\částech používají jako znak pokračování řádku znak zpětného lomítka ( ). V závislosti na požadavcích hostitelského operačního systému tento znak nahraďte nebo odeberte.
* **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste obeznámeni s kontejnery Dockeru.

Nahraďte {_argument_name_} v následující tabulce vlastními hodnotami:

| Zástupný symbol | Hodnota |
|-------------|-------|
| **{FORM_RECOGNIZER_API_KEY}** | Klíč, který se používá ke spuštění kontejneru. Je k dispozici na stránce Klíče pro rozpoznávání formulářů portálu Azure. |
| **{FORM_RECOGNIZER_ENDPOINT_URI}** | Hodnota IDENTIFIKÁTORU URI fakturačního koncového bodu je dostupná na stránce Přehled rozpoznávání formulářů portálu Azure Portal.|
| **{COMPUTER_VISION_API_KEY}** | Klíč je k dispozici na stránce Azure portal Computer Vision API Keys.|
| **{COMPUTER_VISION_ENDPOINT_URI}** | Koncový bod fakturace. Pokud používáte cloudový prostředek počítačového zpracování obrazu, hodnota URI je dostupná na stránce Přehled rozhraní API pro počítačové zpracování počítače na portálu Azure. Pokud používáte kontejner *kognitivní služby rozpoznávat text,* použijte adresu URL koncového bodu `docker run` fakturace, která je předána do kontejneru v příkazu. |

Podrobnosti o tom, jak tyto hodnoty získat, naleznete v [tématu shromažďování požadovaných parametrů.](form-recognizer-container-howto.md#gathering-required-parameters)

[!INCLUDE [cognitive-services-custom-subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> Chcete-li kontejner spustit, zadejte možnosti `Eula`, `Billing`a; `ApiKey` v opačném případě se kontejner nespustí. Další informace naleznete v [tématu Fakturace](#billing-configuration-setting).

## <a name="form-recognizer-container-docker-examples"></a>Příklady dockeru kontejneru nástroje pro rozpoznávání formulářů

Následující příklady Dockeru jsou pro kontejner nástroje pro rozpoznávání formulářů.

### <a name="basic-example-for-form-recognizer"></a>Základní příklad pro rozpoznávání formulářů

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

### <a name="logging-example-for-form-recognizer"></a>Příklad protokolování pro nástroj pro rozpoznávání formulářů

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
Logging:Console:LogLevel:Default=Information
```

## <a name="next-steps"></a>Další kroky

* Zkontrolujte [instalaci a spuštění kontejnerů](form-recognizer-container-howto.md).

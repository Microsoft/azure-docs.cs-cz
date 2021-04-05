---
title: Konfigurace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči poskytuje každý kontejner se společným rozhraním konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení kontejnerů.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: e65bb7c7d8fc04baec6b50a53519e689e748fbe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96012165"
---
# <a name="configure-speech-service-containers"></a>Konfigurace kontejnerů služby Speech

Díky kontejnerům pro rozpoznávání řeči můžou zákazníci vytvářet jednu architekturu aplikace pro rozpoznávání řeči, která je optimalizovaná tak, aby využila výhod robustních cloudových funkcí i možností Edge. Pět kontejnerů řeči teď podporujeme, jedná se o **Převod řeči na text**, **vlastní převod řeči na text**, převod **textu na řeč**, **neuronové text na řeč** a **vlastní převod textu na řeč**.

Prostředí modulu runtime kontejneru **řeči** se konfiguruje pomocí `docker run` argumentů příkazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení fakturace jsou specifická pro kontejner.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)Nastavení, [`Billing`](#billing-configuration-setting) a [`Eula`](#eula-setting) se používají společně a musíte zadat platné hodnoty pro všechny tři z nich. v opačném případě se Váš kontejner nespustí. Další informace o tom, jak pomocí těchto nastavení konfigurace vytvořit instanci kontejneru, najdete v tématu [fakturace](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey`Nastavení určuje klíč prostředku Azure, který se používá ke sledování fakturačních informací pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platným klíčem pro prostředek _řeči_ zadaný pro [`Billing`](#billing-configuration-setting) nastavení konfigurace.

Toto nastavení najdete na následujícím místě:

- Azure Portal: Správa prostředků **řeči** v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

`Billing`Nastavení určuje identifikátor URI koncového bodu prostředku _řeči_ v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _řeči_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

- Azure Portal: Přehled **rozpoznávání řeči** , označený `Endpoint`

| Vyžadováno | Name | Datový typ | Popis |
| -------- | ---- | --------- | ----------- |
| Ano | `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace Další informace o získání identifikátoru URI fakturace najdete v tématu [shromáždění požadovaných parametrů](speech-container-howto.md#gathering-required-parameters). Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení smlouvy EULA

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluent

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Ke čtení a zápisu dat do a z kontejneru použijte připojení BIND. Zadáním `--mount` Možnosti v příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) můžete zadat vstupní připojení nebo připojení pro výstup.

Standardní kontejnery řeči nepoužívají vstupní ani výstupní připojení k ukládání dat o školeních nebo službách. Vlastní kontejnery řeči se však spoléhají na připojení svazků.

Přesná syntaxe umístění hostitelského připojení se liší v závislosti na hostitelském operačním systému. Kromě toho je možné, že umístění pro připojení k [hostitelskému počítači](speech-container-howto.md#the-host-computer)není přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby Docker a oprávněním pro umístění připojení hostitele.

| Volitelné | Name | Datový typ | Popis |
| -------- | ---- | --------- | ----------- |
| Nepovolené | `Input` | Řetězec | Standardní kontejnery řeči to nepoužívají. Vlastní kontejnery řeči používají [připojení svazků](#volume-mount-settings).                                                                                    |
| Volitelné | `Output` | Řetězec | Cíl připojení pro výstup. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Nastavení připojení svazku

Vlastní kontejnery řeči používají k uchování vlastních modelů [připojení svazků](https://docs.docker.com/storage/volumes/) . Připojení svazku můžete zadat přidáním `-v` Možnosti (nebo `--volume` ) do příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) .

Vlastní modely se stáhnou při prvním ingestování nového modelu v rámci příkazu vlastního kontejneru řeči Docker run. Sekvenční spuštění stejného `ModelId` vlastního kontejneru řeči bude používat dříve stažený model. Pokud není k dispozici připojení svazku, vlastní modely nelze zachovat.

Nastavení připojení svazku se skládá ze tří `:` polí s oddělovači barev:

1. První pole je název svazku na hostitelském počítači, například _C:\input_.
2. Druhé pole je adresář v kontejneru, například _/usr/local/Models_.
3. Třetí pole (volitelné) je čárkami oddělený seznam možností, další informace najdete v tématu [použití svazků](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Příklad připojení svazku

```bash
-v C:\input:/usr/local/models
```

Tento příkaz připojí adresář _C:\input_ hostitelského počítače k adresáři _/usr/local/Models_ kontejnerů.

> [!IMPORTANT]
> Nastavení připojení svazku se dá použít jenom pro kontejnery **Custom Speechho** textu na text a **na vlastní řeč** . Neuronové **Převod řeči na text** a pro  převod textu **na řeč** nepoužívají připojení svazků.

## <a name="example-docker-run-commands"></a>Příklady příkazů pro spuštění Docker

Následující příklady používají konfigurační nastavení k ilustraci, jak psát a používat `docker run` příkazy. Po spuštění bude kontejner dál běžet, dokud ho [nezastavíte](speech-container-howto.md#stop-the-container) .

- **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\` jako znak pro pokračování řádku. Tuto položku nahraďte nebo odeberte na základě požadavků vašich hostitelských operačních systémů.
- **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste obeznámeni s kontejnery Docker.

Nahradit {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Klíč koncového bodu `Speech` prostředku na `Speech` stránce klíčů Azure.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na `Speech` stránce Přehled Azure. | Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](speech-container-howto.md#gathering-required-parameters) . |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí. Další informace najdete v tématu [fakturace](#billing-configuration-setting).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure Speech.

## <a name="speech-container-docker-examples"></a>Příklady Docker kontejneru řeči

Následující příklady Docker jsou pro kontejner řeči.

## <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Základní příklad pro převod řeči na text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Příklad protokolování pro převod řeči na text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Custom Speech na text](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Základní příklad pro Custom Speech textu

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Příklad protokolování pro Custom Speech textu

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Základní příklad pro převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Příklad protokolování pro převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Základní příklad pro vlastní převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Příklad protokolování pro vlastní převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[Neuronové převodu textu na řeč](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>Základní příklad pro neuronové převodu textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>Příklad protokolování pro převod textu na řeč v neuronové

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-detection"></a>[Rozpoznávání jazyka řeči](#tab/lid)

### <a name="basic-example-for-speech-language-detection"></a>Základní příklad pro detekci jazyka řeči

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-detection"></a>Příklad protokolování pro detekci jazyka řeči

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Další kroky

- Přečtěte si [, jak nainstalovat a spustit kontejnery](speech-container-howto.md) .

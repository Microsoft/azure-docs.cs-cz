---
title: Konfigurace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči poskytuje každý kontejner se společným rozhraním konfigurace, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení kontejnerů.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 34b4664ec13f7ba1871433e37d86170b2207a17a
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816568"
---
# <a name="configure-speech-service-containers"></a>Konfigurace kontejnerů služby Speech

Díky kontejnerům pro rozpoznávání řeči můžou zákazníci vytvářet jednu architekturu aplikace pro rozpoznávání řeči, která je optimalizovaná tak, aby využila výhod robustních cloudových funkcí i možností Edge. Čtyři kontejnery řeči teď podporujeme, jedná se o **Převod řeči na text**, **vlastní převod řeči na text**, převod **textu na řeč**a **vlastní převod textu na řeč**.

Prostředí modulu runtime kontejneru **řeči** je konfigurováno pomocí `docker run`ch argumentů příkazu. Tento kontejner má několik požadovaných nastavení spolu s několika volitelnými nastaveními. Několik [příklady](#example-docker-run-commands) příkazu jsou k dispozici. Nastavení fakturace jsou specifická pro kontejner.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), A [ `Eula` ](#eula-setting) nastavení se používají společně a pro všechny tři je; v opačném případě je nutné zadat platné hodnoty kontejner se nespustí. Další informace o používání těchto nastavení konfigurace pro vytvoření instance kontejneru najdete v tématu [fakturace](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

`ApiKey` Nastavení určuje klíč prostředku Azure používá ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí být platným klíčem pro prostředek _řeči_ zadaný pro nastavení konfigurace [`Billing`](#billing-configuration-setting) .

Toto nastavení najdete na následujícím místě:

- Azure Portal: Správa prostředků **řeči** v části **klíče**

## <a name="applicationinsights-setting"></a>Nastavení ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Konfigurace nastavení fakturace

Nastavení `Billing` Určuje identifikátor URI koncového bodu prostředku _řeči_ v Azure, který se používá pro informace o fakturaci pro daný kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a tato hodnota musí být platným identifikátorem URI koncového bodu pro prostředek _řeči_ v Azure. Kontejner hlásí využití každých 10 až 15 minut.

Toto nastavení najdete na následujícím místě:

- Azure Portal: Přehled **rozpoznávání řeči** , označený `Endpoint`

| Požaduje se | Name (Název) | Data type | Popis |
| -------- | ---- | --------- | ----------- |
| Ano | `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace Další informace o získání identifikátoru URI fakturace najdete v tématu [shromáždění požadovaných parametrů](speech-container-howto.md#gathering-required-parameters). Další informace a úplný seznam regionálních koncových bodů najdete v tématu [názvy vlastních subdomén pro Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Smlouva EULA nastavení

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení přihlašovacích údajů proxy serveru HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojování

Použití vazby připojí ke čtení a zápisu dat do a z kontejneru. Můžete určit vstupní připojení nebo připojení výstup tak, že zadáte `--mount` možnost [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkazu.

Standardní kontejnery řeči nepoužívají vstupní ani výstupní připojení k ukládání dat o školeních nebo službách. Vlastní kontejnery řeči se však spoléhají na připojení svazků.

Syntaxe umístění hostitele připojení se liší v závislosti na operačním systému hostitele. Kromě toho [hostitelský počítač](speech-container-howto.md#the-host-computer)na umístění připojení nemusí být přístupné z důvodu konfliktu mezi oprávnění používat účet služby docker a hostiteli připojit umístění oprávnění.

| Volitelné | Name (Název) | Data type | Popis |
| -------- | ---- | --------- | ----------- |
| Není povoleno | `Input` | Řetězec | Standardní kontejnery řeči to nepoužívají. Vlastní kontejnery řeči používají [připojení svazků](#volume-mount-settings).                                                                                    |
| Volitelné | `Output` | Řetězec | Cíl připojení výstupu. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Nastavení připojení svazku

Vlastní kontejnery řeči používají k uchování vlastních modelů [připojení svazků](https://docs.docker.com/storage/volumes/) . Přidáním možnosti `-v` (nebo `--volume`) do příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) můžete zadat připojení svazku.

Vlastní modely se stáhnou při prvním ingestování nového modelu v rámci příkazu vlastního kontejneru řeči Docker run. Sekvenční spuštění stejného `ModelId` vlastního kontejneru řeči bude používat dříve stažený model. Pokud není k dispozici připojení svazku, vlastní modely nelze zachovat.

Nastavení připojení svazku se skládá ze tří `:` oddělených polí:

1. První pole je název svazku na hostitelském počítači, například _C:\input_.
2. Druhé pole je adresář v kontejneru, například _/usr/local/Models_.
3. Třetí pole (volitelné) je čárkami oddělený seznam možností, další informace najdete v tématu [použití svazků](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Příklad připojení svazku

```bash
-v C:\input:/usr/local/models
```

Tento příkaz připojí adresář _C:\input_ hostitelského počítače k adresáři _/usr/local/Models_ kontejnerů.

> [!IMPORTANT]
> Nastavení připojení svazku se dá použít jenom pro kontejnery **Custom Speechho** textu na text a **na vlastní řeč** . Standardní kontejnery **pro převod řeči na text** a **Převod textu na řeč** nepoužívají připojení svazků.

## <a name="example-docker-run-commands"></a>Spusťte příkazy dockeru příklad

Následující příklady ukazují, jak napsat a použít pomocí nastavení konfigurace `docker run` příkazy. Po spuštění kontejneru nadále běžel dokud [Zastavit](speech-container-howto.md#stop-the-container) ho.

- **Znak pro pokračování řádku**: příkazy Docker v následujících částech používají zpětné lomítko, `\`jako znak pro pokračování řádku. Nahraďte nebo odstraňte tuto podle požadavků vašeho hostitelského operačního systému.
- **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste obeznámeni s kontejnery Docker.

Nahradit {_argument_name_} s vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Klíč koncového bodu prostředku `Speech` na stránce klíčů Azure `Speech`.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | Hodnota fakturačního koncového bodu je k dispozici na stránce Přehled služby Azure `Speech`. | Explicitní příklady najdete v tématu [shromažďování požadovaných parametrů](speech-container-howto.md#gathering-required-parameters) . |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru. Další informace najdete v tématu [fakturace](#billing-configuration-setting).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků Azure Speech.

## <a name="speech-container-docker-examples"></a>Příklady Docker kontejneru řeči

Následující příklady Docker jsou pro kontejner řeči.

## <a name="speech-to-texttabstt"></a>[Převod řeči na text](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Základní příklad pro převod řeči na text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Příklad protokolování pro převod řeči na text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-texttabcstt"></a>[Custom Speech na text](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Základní příklad pro Custom Speech textu

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Příklad protokolování pro Custom Speech textu

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speechtabtss"></a>[Převod textu na řeč](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Základní příklad pro převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Příklad protokolování pro převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speechtabctts"></a>[Vlastní převod textu na řeč](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Základní příklad pro vlastní převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Příklad protokolování pro vlastní převod textu na řeč

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Další kroky

- Kontrola [instalace a spouštění kontejnerů](speech-container-howto.md)

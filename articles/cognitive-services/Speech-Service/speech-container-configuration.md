---
title: Konfigurace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání řeči poskytuje každému kontejneru společnou konfigurační architekturu, takže můžete snadno konfigurovat a spravovat úložiště, protokolování a telemetrii a nastavení zabezpečení pro vaše kontejnery.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: dd5a531e4a979cba9c2a766c7774762a0427ad02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037328"
---
# <a name="configure-speech-service-containers"></a>Konfigurace kontejnerů služby rozpoznávání řeči

Kontejnery řeči umožňují zákazníkům vytvořit jednu architekturu aplikace pro rozpoznávání řeči, která je optimalizovaná tak, aby využívala výhod jak robustních cloudových funkcí, tak hraniční lokality. Čtyři kontejnery řeči, které nyní podporujeme, **jsou, převod řeči na text**, vlastní **řeč-k-text**, **převod textu na řeč**a vlastní text na **řeč**.

Prostředí runtime **kontejneru řeči** `docker run` je konfigurováno pomocí argumentů příkazu. Tento kontejner má několik požadovaných nastavení, spolu s několika volitelnými nastaveními. K dispozici je několik [příkladů](#example-docker-run-commands) příkazu. Nastavení specifická pro kontejner jsou nastavení fakturace.

## <a name="configuration-settings"></a>Nastavení konfigurace

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> Nastavení [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting), [`Eula`](#eula-setting) a jsou používány společně a je nutné zadat platné hodnoty pro všechny tři z nich; jinak se kontejner nespustí. Další informace o použití těchto nastavení konfigurace k vytvoření instance kontejneru naleznete v tématu [Fakturace](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Nastavení konfigurace ApiKey

Toto `ApiKey` nastavení určuje klíč prostředků Azure, který slouží ke sledování fakturačních údajů pro kontejner. Je nutné zadat hodnotu pro ApiKey a hodnota musí _Speech_ být platný [`Billing`](#billing-configuration-setting) klíč pro prostředek řeči zadaný pro nastavení konfigurace.

Toto nastavení naleznete na následujícím místě:

- Portál Azure: Správa prostředků **řeči** v části **Klíče**

## <a name="applicationinsights-setting"></a>ApplicationInsights, nastavení

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Nastavení konfigurace fakturace

Toto `Billing` nastavení určuje identifikátor URI koncového bodu prostředku _řeči_ v Azure, který slouží k měření fakturačních informací pro kontejner. Je nutné zadat hodnotu pro toto nastavení konfigurace a hodnota musí být platný identifikátor URI koncového bodu pro prostředek _řeči v_ Azure. Kontejner hlásí využití přibližně každých 10 až 15 minut.

Toto nastavení naleznete na následujícím místě:

- Portál Azure: Přehled **řeči** s označením`Endpoint`

| Požaduje se | Name (Název) | Datový typ | Popis |
| -------- | ---- | --------- | ----------- |
| Ano | `Billing` | Řetězec | Identifikátor URI koncového bodu fakturace. Další informace o získání fakturačního identifikátoru URI naleznete v [tématu shromažďování požadovaných parametrů](speech-container-howto.md#gathering-required-parameters). Další informace a úplný seznam místních koncových bodů naleznete [v tématu Vlastní názvy subdomén pro služby Cognitive Services](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Nastavení Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Nastavení fluentdu

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Nastavení pověření proxy http

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Nastavení protokolování

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Nastavení připojení

Pomocí vazební připojení číst a zapisovat data do a z kontejneru. Vstupní připojení nebo výstupní připojení můžete určit `--mount` zadáním možnosti v příkazu [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/)

Standardní kontejnery řeči nepoužívají vstupní nebo výstupní připojení k ukládání dat školení nebo služby. Vlastní kontejnery řeči však spoléhají na připojení svazku.

Přesná syntaxe umístění připojení hostitele se liší v závislosti na hostitelském operačním systému. Umístění připojení [hostitelského počítače](speech-container-howto.md#the-host-computer)navíc nemusí být přístupné z důvodu konfliktu mezi oprávněními používanými účtem služby dockeru a oprávněními umístění připojení hostitele.

| Nepovinné | Name (Název) | Datový typ | Popis |
| -------- | ---- | --------- | ----------- |
| Nepovolené | `Input` | Řetězec | Standardní kontejnery řeči nepoužívají toto. Vlastní kontejnery řeči používají [připojení svazku](#volume-mount-settings).                                                                                    |
| Nepovinné | `Output` | Řetězec | Cíl výstupního připojení. Výchozí hodnota je `/output`. Toto je umístění protokolů. To zahrnuje protokoly kontejnerů. <br><br>Příklad:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Nastavení připojení svazku

Vlastní kontejnery řeči používají [připojení svazku](https://docs.docker.com/storage/volumes/) k zachování vlastních modelů. Připojení svazku můžete určit `-v` přidáním `--volume`možnosti (nebo) do příkazu [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/)

Vlastní modely jsou staženy při prvním, kdy je nový model ingestován jako součást příkazu spuštění vlastního kontejneru řeči. Sekvenční `ModelId` spuštění stejné pro vlastní kontejner řeči bude používat dříve stažený model. Pokud není k dispozici připojení svazku, vlastní modely nelze zachovat.

Nastavení připojení svazku se `:` skládá ze tří barevně oddělených polí:

1. První pole je název svazku v hostitelském počítači, například _C:\input_.
2. Druhé pole je adresář v kontejneru, například _/usr/local/models_.
3. Třetí pole (volitelné) je seznam možností oddělených čárkami, další informace naleznete v tématu [použití svazků](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Příklad připojení svazku

```bash
-v C:\input:/usr/local/models
```

Tento příkaz připojí hostitelský počítač _C:\vstupní_ adresář do adresáře kontejnerů _/usr/local/models._

> [!IMPORTANT]
> Nastavení připojení svazku se vztahuje pouze na **vlastní kontejnery převodu řeči na text** a vlastní převod textu na **řeč.** Standardní **kontejnery převodu řeči na text** a **převod textu na řeč** nepoužívají připojení svazku.

## <a name="example-docker-run-commands"></a>Příklad příkazů spuštění dockeru

Následující příklady používají nastavení konfigurace pro ilustraci, jak psát a používat `docker run` příkazy. Po spuštění kontejneru pokračuje v běhu, dokud jej [nezastavíte.](speech-container-howto.md#stop-the-container)

- **Znak pokračování řádku**: Příkazy Dockeru v následujících `\`částech používají jako znak pokračování řádku znak zpětného lomítka . Nazákladě požadavků hostitelského operačního systému jej nahraďte nebo odeberte.
- **Pořadí argumentů**: Neměňte pořadí argumentů, pokud nejste obeznámeni s kontejnery Dockeru.

Nahraďte {_argument_name_} vlastními hodnotami:

| Zástupný symbol | Hodnota | Formát nebo příklad |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | Klíč koncového bodu `Speech` prostředku na `Speech` stránce Azure Keys.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | Hodnota koncového bodu fakturace `Speech` je dostupná na stránce Přehled Azure. | Viz [shromažďování požadovaných parametrů](speech-container-howto.md#gathering-required-parameters) pro explicitní příklady. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí. Další informace naleznete v [tématu Fakturace](#billing-configuration-setting).
> Hodnota ApiKey je **klíč** ze stránky klíče prostředků řeči Azure.

## <a name="speech-container-docker-examples"></a>Příklady Dockeru kontejneru řeči

Následující příklady Dockeru jsou pro kontejner řeči.

## <a name="speech-to-text"></a>[Převod řeči na text](#tab/stt)

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

## <a name="custom-speech-to-text"></a>[Vlastní převod řeči na text](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Základní příklad pro vlastní převod řeči na text

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Příklad protokolování pro vlastní převod řeči na text

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

## <a name="text-to-speech"></a>[Převod textu na řeč](#tab/tss)

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

## <a name="custom-text-to-speech"></a>[Vlastní převod textu na řeč](#tab/ctts)

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

- Přečtěte [si, jak nainstalovat a spustit kontejnery](speech-container-howto.md)

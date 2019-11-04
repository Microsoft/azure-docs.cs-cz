---
title: Instalace a spuštění kontejnerů – Translator Text
titleSuffix: Azure Cognitive Services
description: Postup stažení, instalace a spuštění kontejnerů pro Translator Text Analytics v tomto výukovém kurzu.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507877"
---
# <a name="install-and-run-translator-text-containers"></a>Instalace a spuštění kontejnerů Translator Text

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

Kontejnery umožňují spouštět rozhraní API Translator Text ve vlastním prostředí a jsou skvělé pro konkrétní požadavky na zabezpečení a zabezpečení dat.

## <a name="prerequisites"></a>Předpoklady

Před použitím Translator Text kontejnerů musíte splnit následující předpoklady:

|Požaduje se|Účel|
|--|--|
|Modul Docker| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Informace o úvodu k Docker a kontejneru najdete v tématu [Přehled Docker](https://docs.docker.com/engine/docker-overview/).<br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.|

## <a name="request-access-to-the-container-registry"></a>Požádat o přístup k registru kontejneru

Aby bylo možné požádat o přístup ke kontejneru, musíte nejprve dokončit a odeslat [formulář žádosti o rozpoznávání kontejnerů Translator text](https://aka.ms/translatorcontainerform) .

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>Hostitelský počítač

Hostitel je počítač s platformou x64 s operačním systémem Linux, který spouští kontejner Docker. Může to být počítač v místním prostředí nebo hostská služba Docker v Azure, například:

* Služba Azure Kubernetes
* Azure Container Instances.
* [Kubernetes](https://kubernetes.io/) cluster byl nasazen do Azure Stack.

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

V následující tabulce jsou popsány minimální a doporučené PROCESORové jádra, minimálně 2,6 GHz nebo rychlejší a paměť v gigabajtech (GB), které se mají přidělit pro každý kontejner Translator Text.

| Kontejner | Minimální | Dvojice jazyků |
|-----------|---------|---------------|
| Translator Text | 4 jádra, 4 GB paměti | 4 |

Pro každou dvojici jazyků doporučujeme mít 1 GB paměti. Ve výchozím nastavení má kontejner Translator Text 3 nebo 4 páry jazyků v závislosti na `<image-tag>`, kterou používáte. Podrobnosti najdete v tématu [podporované jazyky a překlady](#supported-languages-and-translation) . Základní a paměť odpovídají nastavení `--cpus` a `--memory`, které se používají jako součást příkazu `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí `docker pull`

Image kontejneru pro Translator Text jsou k dispozici v následujícím úložišti kontejnerů. Tabulka také mapuje značky image kontejneru a jejich odpovídající podporované jazyky.

| Kontejner | Značka obrázku | Podporované jazyky |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE`a `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR`a `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Vyžádané čtení Docker pro kontejner Translator Text

Chcete-li provést příkaz [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , musíte nejprve získat přístup k registru kontejneru. V rozhraní příkazového řádku Azure získáte přihlášení k Azure Container Registry pomocí příkazu [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) .

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Tento příkaz ověří aktuálního uživatele s odpovídajícím Azure Container Registry. Nyní můžete spustit příkaz `docker pull`.

> [!NOTE]
> V závislosti na tom, jakou jazykovou podporu potřebujete, zadejte odpovídající `<image-tag>`.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

1. [Spusťte kontejner](#run-the-container-with-docker-run). K dispozici jsou další [příklady](translator-text-container-config.md#example-docker-run-commands) `docker run` příkazu.
1. [Dotaz na koncový bod překladu kontejneru](#query-the-containers-translate-endpoint)

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner pomocí `docker run`

Pomocí příkazu [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) spusťte kterýkoli ze tří kontejnerů. K dispozici jsou [příklady](translator-text-container-config.md#example-docker-run-commands) `docker run` příkazu.

### <a name="run-container-example-of-docker-run-command"></a>Příklad spuštění kontejneru příkazu Docker run

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Tento příkaz:

* Spustí kontejner Translator Text z image kontejneru.
* Přiděluje 4 PROCESORové jádro a 4 gigabajty (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Přijme smlouvu koncového uživatele (EULA).
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

### <a name="how-to-collect-docker-logs"></a>Jak shromažďovat protokoly Docker

Pro účely řešení potíží může být užitečné zobrazit protokoly Docker z spuštění kontejneru. Nejprve spusťte příkaz [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) s příznakem formátování, abyste omezili zobrazené podrobnosti pro všechny kontejnery.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Pak spusťte příkaz [Docker logs](https://docs.docker.com/engine/reference/commandline/logs/) s `<Container ID>` pro příslušný kontejner, abyste zobrazili jeho protokoly.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

Výše uvedený příkaz pro zápis do protokolu Docker bude shromažďovat protokoly "Error" za poslední čtyři hodiny.

## <a name="supported-languages-and-translation"></a>Podporované jazyky a překlad

Metoda `POST /translate` podporuje následující převody jazyků, přechod z *angličtiny* do cílového jazyka a naopak. Počítejte s tím, že zatímco můžete přejít na angličtinu a z jednoho z uvedených jazyků, *nemůžete* přejít z jednoho *neanglického* jazyka do jiného *než anglického* jazyka.

> [!NOTE]
> Pro zajištění optimální kvality by měli zákazníci odeslat jenom jednu větu na žádost.

| Převod jazyka | Převod jazyka ISO | Značky obrázku |
|--|--|:--|
| Angličtina: left_right_arrow: čínština | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Angličtina: left_right_arrow: Ruština | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Angličtina: left_right_arrow: Arabština | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Angličtina: left_right_arrow: němčina | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` a `de_en_es_fr_1.0.0` |
| Angličtina: left_right_arrow: Španělština | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Angličtina: left_right_arrow: francouzština | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> Aby bylo možné spustit kontejner, je nutné zadat `Eula`. v opačném případě se kontejner nespustí.

## <a name="query-the-containers-translate-endpoint"></a>Dotazování koncového bodu překladu kontejneru

Kontejner poskytuje rozhraní API koncového bodu překladu založené na REST. V několika příkladech použití tohoto koncového bodu jsou následující:

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Spusťte následující příkaz složené aplikace z požadovaného rozhraní příkazového řádku.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Pokud se tento oblý příspěvek pokusíte ještě předtím, než je kontejner připravený, dokončí se odpověď "služba je dočasně nedostupná". Stačí počkat na dokončení kontejneru a pak to zkuste znovu.

Následující výstup bude vytištěn do konzoly.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Přejděte na stránku Swagger, http://localhost:5000/swagger/index.html

1. Vyberte **post/Translate**
1. Vyberte **vyzkoušet**
1. Zadejte parametr **from** jako `en-US`
1. Zadejte parametr **do** `de-DE`
1. Jako `3.0` zadejte parametr **verze API** .
1. V části **text**nahraďte `string` následujícím kódem JSON.
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Vyberte **Execute (spustit**). výsledné překlady jsou výstupem v **těle odpovědi**. Měli byste očekávat něco podobného jako v následujícím příkladu:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Spusťte Visual Studio a vytvořte novou konzolovou aplikaci.
1. Upravte soubor `*.csproj` a přidejte tak `<LangVersion>7.1</LangVersion>` uzel – to určuje C# 7,1.
1. Přidejte balíček NuGet [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) verze 11.0.2.
1. V `Program.cs`nahraďte veškerý existující kód následujícím kódem:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Stisknutím klávesy **F5** program spusťte.
1. Následující výstup bude vytištěn do konzoly.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](translator-text-container-config.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postupy pro stažení, instalaci a spuštění kontejnerů Translator Text. Souhrn:

* Translator Text poskytuje pro Docker více kontejnerů pro Linux, které zapouzdřují různé páry jazyků.
* Image kontejneru se stáhnou z registru kontejneru Preview.
* Image kontejneru se spouštějí v Docker.
* Můžete použít REST API nebo SDK pro volání operací v kontejnerech Translator Text zadáním identifikátoru URI hostitele kontejneru.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](translator-text-container-config.md) pro nastavení konfigurace
* Přečtěte si [Nejčastější dotazy ke kontejneru (FAQ)](../containers/container-faq.md) k vyřešení problémů souvisejících s funkcemi.

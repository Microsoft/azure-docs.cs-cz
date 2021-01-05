---
title: Instalace a spuštění kontejnerů Docker pro LUIS
titleSuffix: Azure Cognitive Services
description: Pomocí kontejneru LUIS můžete načítat vyškolené nebo publikované aplikace a získat přístup k jejímu předpovědiu v místním prostředí.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: aahi
keywords: místní, Docker, kontejner
ms.openlocfilehash: 2bef6aa4e624386750a4c989d7e56cc1b22aaa5e
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861994"
---
# <a name="install-and-run-docker-containers-for-luis"></a>Instalace a spuštění kontejnerů Docker pro LUIS

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Kontejnery umožňují používat LUIS ve vlastním prostředí. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat. V tomto článku se dozvíte, jak stáhnout, nainstalovat a spustit kontejner LUIS.

Kontejner Language Understanding (LUIS) načte váš vyškolený nebo publikovaný Language Understanding model. Kontejner Docker jako [aplikace Luis](https://www.luis.ai)poskytuje přístup k dotazu předpovědi z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrajte je zpátky do aplikace Language Understanding, aby se zlepšila přesnost předpovědi aplikace.

Následující video znázorňuje použití tohoto kontejneru.

[![Ukázka kontejneru pro Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

Pokud chcete spustit kontejner LUIS, vezměte na vědomí následující požadavky:

|Povinné|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure. <br><br> **V systému Windows** musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.|
|`Cognitive Services`Soubor Luis [zabalených aplikací](luis-how-to-start-new-app.md) a prostředků Azure |Aby bylo možné kontejner používat, musíte mít:<br><br>* _Cognitive Services_ prostředek Azure a související fakturační klíč identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách přehledu a klíčů pro daný prostředek a jsou požadovány ke spuštění kontejneru. <br>* Vyškolená nebo publikovaná aplikace zabalená jako připojená vstup do kontejneru s jeho přidruženým ID aplikace. Zabalený soubor můžete získat z portálu LUIS nebo z rozhraní API pro vytváření obsahu. Pokud získáváte LUIS zabalenou aplikaci z [rozhraní API pro vytváření obsahu](#authoring-apis-for-package-file), budete také potřebovat svůj _klíč pro vytváření obsahu_.<br><br>Tyto požadavky slouží k předání argumentů příkazového řádku do následujících proměnných:<br><br>**{AUTHORING_KEY}**: Tento klíč se používá k získání zabalené aplikace ze služby Luis v cloudu a k nahrání protokolů dotazů zpátky do cloudu. Formát je `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` .<br><br>**{APP_ID}**: Toto ID se používá k výběru aplikace. Formát je `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .<br><br>**{API_KEY}**: Tento klíč se používá ke spuštění kontejneru. Klíč koncového bodu můžete najít na dvou místech. První je Azure Portal v seznamu klíčů prostředků _Cognitive Services_ . Klíč koncového bodu je k dispozici také na portálu LUIS na stránce klíče a nastavení koncového bodu. Nepoužívejte počáteční klíč.<br><br>**{ENDPOINT_URI}**: koncový bod uvedený na stránce Přehled.<br><br>Klíč pro [vytváření obsahu a klíč koncového bodu](luis-limits.md#key-limits) mají různé účely. Nepoužívejte je zaměnitelné. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Vytváření rozhraní API pro soubor balíčku

Vytváření rozhraní API pro zabalené aplikace:

* [Rozhraní API publikovaného balíčku](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Rozhraní API balíčku bez publikování, jenom pro školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejner a doporučení

V níže uvedené tabulce jsou uvedeny minimální a doporučené hodnoty pro hostitele kontejneru. Vaše požadavky se mohou měnit v závislosti na objemu přenosů.

|Kontejner| Minimum | Doporučeno | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|LUIS|1 jádro, 2 GB paměti|1 jádro, 4 GB paměti|20, 40|

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* TPS-transakcí za sekundu

Základní a paměť odpovídají `--cpus` `--memory` nastavení a, která se používají jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí `docker pull`

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu si stáhněte image kontejneru z `mcr.microsoft.com/azure-cognitive-services/language/luis` úložiště:

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Úplný popis dostupných značek, jako je například `latest` použitý v předchozím příkazu, najdete v tématu [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) v Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na [hostitelském počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

![Proces použití Language Understandingho kontejneru (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportujte balíček](#export-packaged-app-from-luis) pro kontejner z portálu Luis nebo z rozhraní Luis API.
1. Přesuňte soubor balíčku do požadovaného **vstupního** adresáře v [hostitelském počítači](#the-host-computer). Neměňte přejmenování, změnu, přepsání ani dekomprimaci souboru balíčku LUIS.
1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným _vstupním připojením_ a nastavením fakturace. [](luis-container-configuration.md#example-docker-run-commands) `docker run` K dispozici jsou další příklady příkazu.
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint)
1. Až budete s kontejnerem hotovi, [importujte protokoly koncových bodů](#import-the-endpoint-logs-for-active-learning) z výstupního připojení na portálu Luis a [zastavte](#stop-the-container) kontejner.
1. K vylepšení aplikace použijte [aktivní učení](luis-how-to-review-endpoint-utterances.md) na portálu Luis na stránce **zkontrolovat koncový bod projevy** .

Aplikace spuštěná v kontejneru se nedá změnit. Aby bylo možné aplikaci v kontejneru změnit, je nutné změnit aplikaci ve službě LUIS pomocí portálu [Luis](https://www.luis.ai) nebo použít [rozhraní API pro vytváření](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)Luis. Pak proveďte výuku nebo publikování a pak stáhněte nový balíček a znovu spusťte kontejner.

Aplikaci LUIS uvnitř kontejneru nejde exportovat zpátky do služby LUIS. Odeslat lze pouze protokoly dotazů.

## <a name="export-packaged-app-from-luis"></a>Exportovat zabalenou aplikaci z LUIS

Kontejner LUIS vyžaduje vyškolenou nebo publikovanou aplikaci LUIS pro zodpovězení dotazů na předpovědi uživatele projevy. K získání aplikace LUIS použijte buď vyškolené nebo publikované balíčky API.

Výchozím umístěním je `input` podadresář v souvislosti s tím, kde `docker run` příkaz spustíte.

Po spuštění kontejneru Docker umístěte soubor balíčku do adresáře a odkažte tento adresář jako vstupní připojení.

### <a name="package-types"></a>Typy balíčků

Vstupní adresář pro připojení může obsahovat současně **produkční**, **pracovní** a modelované modely **verzí** aplikace. Všechny balíčky jsou připojené.

|Typ balíčku|Rozhraní API koncového bodu dotazu|Dostupnost dotazů|Formát souboru balíčku|
|--|--|--|--|
|Verzí|ZÍSKAT, ZVEŘEJNIT|Pouze kontejner|`{APP_ID}_v{APP_VERSION}.gz`|
|Příprava|ZÍSKAT, ZVEŘEJNIT|Azure a kontejner|`{APP_ID}_STAGING.gz`|
|Výroba|ZÍSKAT, ZVEŘEJNIT|Azure a kontejner|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Neměňte přejmenování, změnu, přepsání nebo dekomprimaci souborů balíčku LUIS.

### <a name="packaging-prerequisites"></a>Požadavky na sbalení

Před zabalením aplikace LUIS musíte mít následující:

|Požadavky na balení|Podrobnosti|
|--|--|
|Instance prostředků Azure _Cognitive Services_|Mezi podporované oblasti patří<br><br>Západní USA ( `westus` )<br>Západní Evropa ( `westeurope` )<br>Austrálie – východ ( `australiaeast` )|
|Školená nebo publikovaná aplikace LUIS|Bez [nepodporovaných závislostí][unsupported-dependencies]. |
|Přístup k systému souborů [hostitelského počítače](#the-host-computer) |Hostitelský počítač musí umožňovat [vstupní připojení](luis-container-configuration.md#mount-settings).|

### <a name="export-app-package-from-luis-portal"></a>Exportovat balíček aplikace z portálu LUIS

[Portál](https://www.luis.ai) Luis nabízí možnost Exportovat balíček trained nebo publikované aplikace.

### <a name="export-published-apps-package-from-luis-portal"></a>Exportovat balíček publikované aplikace z portálu LUIS

Balíček publikované aplikace je k dispozici na stránce seznam **Moje aplikace** .

1. Přihlaste se k [portálu](https://www.luis.ai)Luis.
1. Zaškrtněte políčko vlevo od názvu aplikace v seznamu.
1. Vyberte položku **exportu** z kontextového panelu nástrojů nad seznamem.
1. Vyberte **Exportovat pro kontejner (gzip)**.
1. Vyberte prostředí **produkčního slotu** nebo **přípravného slotu**.
1. Balíček se stáhne z prohlížeče.

![Exportujte publikovaný balíček pro kontejner z nabídky export stránky aplikace.](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Exportovat balíček aplikace s verzí z portálu LUIS

Balíček aplikace se správou verzí je k dispozici na stránce seznam **verzí** .

1. Přihlaste se k [portálu](https://www.luis.ai)Luis.
1. Vyberte v seznamu tuto aplikaci.
1. V navigačním panelu aplikace vyberte **Spravovat** .
1. Vyberte **verze** v levém navigačním panelu.
1. Zaškrtněte políčko vlevo od názvu verze v seznamu.
1. Vyberte položku **exportu** z kontextového panelu nástrojů nad seznamem.
1. Vyberte **Exportovat pro kontejner (gzip)**.
1. Balíček se stáhne z prohlížeče.

![Exportujte vyškolený balíček pro kontejner z nabídky export stránky verze](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Exportovat balíček publikované aplikace z rozhraní API

K zabalení aplikace LUIS, kterou jste už [publikovali](luis-how-to-publish-app.md), použijte následující metodu REST API. Nahrazení vlastních hodnot pro zástupné symboly v volání rozhraní API pomocí tabulky pod specifikací protokolu HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Hodnota |
|-------------|-------|
| **{APP_ID}** | ID aplikace publikované aplikace LUIS |
| **{SLOT_NAME}** | Prostředí publikované aplikace v LUIS Použijte jednu z následujících hodnot:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Klíč pro tvorbu účtu LUIS pro publikovanou aplikaci LUIS<br/>Svůj klíč pro vytváření můžete získat ze stránky **uživatelských nastavení** na portálu Luis. |
| **{AZURE_REGION}** | Příslušná oblast Azure:<br/><br/>`westus` -Západní USA<br/>`westeurope` -Západní Evropa<br/>`australiaeast` – Austrálie – východ |

Pokud si chcete stáhnout publikovaný balíček, přečtěte si [tu dokumentaci k rozhraní API][download-published-package]. Po úspěšném stažení je odpověď souborem balíčku LUIS. Uložte soubor do umístění úložiště zadaného pro vstupní připojení kontejneru.

### <a name="export-versioned-apps-package-from-api"></a>Exportovat balíček aplikace s verzí z rozhraní API

K zabalení aplikace LUIS, kterou jste už [proškolenou](luis-how-to-train.md), použijte následující metodu REST API. Nahrazení vlastních hodnot pro zástupné symboly v volání rozhraní API pomocí tabulky pod specifikací protokolu HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Hodnota |
|-------------|-------|
| **{APP_ID}** | ID aplikace vyškolené aplikace LUIS |
| **{APP_VERSION}** | Verze aplikace vyškolené aplikace LUIS |
| **{AUTHORING_KEY}** | Klíč pro tvorbu účtu LUIS pro publikovanou aplikaci LUIS<br/>Svůj klíč pro vytváření můžete získat ze stránky **uživatelských nastavení** na portálu Luis. |
| **{AZURE_REGION}** | Příslušná oblast Azure:<br/><br/>`westus` -Západní USA<br/>`westeurope` -Západní Evropa<br/>`australiaeast` – Austrálie – východ |

Pokud chcete stáhnout balíček se správou verzí, přečtěte si [tady dokumentaci k rozhraní API][download-versioned-package]. Po úspěšném stažení je odpověď souborem balíčku LUIS. Uložte soubor do umístění úložiště zadaného pro vstupní připojení kontejneru.

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Podrobnosti o tom, jak získat hodnoty a, najdete v článku [shromáždění požadovaných parametrů](#gathering-required-parameters) `{ENDPOINT_URI}` `{API_KEY}` .

[](luis-container-configuration.md#example-docker-run-commands) `docker run` K dispozici jsou příklady příkazů.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/language/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* V tomto příkladu se používá adresář mimo `C:` jednotku, aby nedocházelo ke konfliktům oprávnění ve Windows. Pokud potřebujete použít konkrétní adresář jako vstupní adresář, bude pravděpodobně nutné udělit oprávnění služby Docker.
* Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.
* Pokud používáte jiný operační systém, použijte pro připojení správnou konzolu, terminál, syntaxi složky a znak pro pokračování řádku pro svůj systém. Tyto příklady předpokládají konzolu Windows se znakem pro pokračování řádku `^` . Vzhledem k tomu, že kontejner je operačním systémem Linux, cílový připojení používá syntaxi složky ve stylu systému Linux.

Tento příkaz:

* Spustí kontejner z image kontejneru LUIS.
* Načte aplikaci LUIS ze vstupního připojení na *C:\input*, která se nachází na hostiteli kontejneru.
* Přiděluje dvě jádra procesoru a 4 gigabajty (GB) paměti.
* Zveřejňuje port TCP 5000 a přiděluje pro kontejner pseudo TTY.
* Uloží protokoly kontejnerů a LUIS do výstupního připojení na *C:\output*, které se nachází na hostiteli kontejneru.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači.

[](luis-container-configuration.md#example-docker-run-commands) `docker run` K dispozici jsou další příklady příkazu.

> [!IMPORTANT]
> `Eula` `Billing` `ApiKey` Aby bylo možné spustit kontejner, musí být zadány možnosti, a. v opačném případě se kontejner nespustí.  Další informace najdete v tématu [fakturace](#billing).
> Hodnota ApiKey je **klíč** ze stránky **prostředků Azure** na portálu Luis a je dostupný i na `Cognitive Services` stránce klíčů prostředků Azure.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Rozhraní API koncových bodů podporovaná kontejnerem

Verze V2 a [V3](luis-migration-api-v3.md) rozhraní API jsou k dispozici společně s kontejnerem.

## <a name="query-the-containers-prediction-endpoint"></a>Zadání dotazu do prediktivního koncového bodu kontejneru

Kontejner poskytuje rozhraní API prediktivního koncového bodu pro dotazy založené na REST. Koncové body pro publikované (pracovní nebo produkční) aplikace mají _jinou_ trasu než koncové body pro aplikace s verzemi.

Pro rozhraní API kontejneru použijte hostitele `http://localhost:5000`.

# <a name="v3-prediction-endpoint"></a>[Prediktivní koncový bod V3](#tab/v3)

|Typ balíčku|Příkaz HTTP|Trasa|Parametry dotazů|
|--|--|--|--|
|Publikováno|ZÍSKAT, ZVEŘEJNIT|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Verzí|ZÍSKAT, ZVEŘEJNIT|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Parametry dotazu konfigurují jak a co je vráceno v odpovědi na dotaz:

|Parametr dotazu|Typ|Účel|
|--|--|--|
|`query`|řetězec|Utterance uživatele.|
|`verbose`|boolean|Logická hodnota označující, zda se mají vracet všechna metadata pro předpovězené modely. Výchozí hodnota je false.|
|`log`|boolean|Protokoluje dotazy, které se dají použít později pro [aktivní učení](luis-how-to-review-endpoint-utterances.md). Výchozí hodnota je false.|
|`show-all-intents`|boolean|Logická hodnota označující, zda se mají vracet všechny záměry nebo nejvyšší záměr vyhodnocování Výchozí hodnota je false.|

# <a name="v2-prediction-endpoint"></a>[Koncový bod pro předpověď v2](#tab/v2)

|Typ balíčku|Příkaz HTTP|Trasa|Parametry dotazů|
|--|--|--|--|
|Publikováno|[získat](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [zveřejnit](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Verzí|ZÍSKAT, ZVEŘEJNIT|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Parametry dotazu konfigurují jak a co je vráceno v odpovědi na dotaz:

|Parametr dotazu|Typ|Účel|
|--|--|--|
|`q`|řetězec|Utterance uživatele.|
|`timezoneOffset`|číslo|TimezoneOffset umožňuje [změnit časové pásmo](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) používané předem vytvořenou entitou datetimeV2.|
|`verbose`|boolean|Vrátí všechny záměry a jejich skóre, pokud je nastaveno na hodnotu true. Výchozí hodnota je false, která vrací pouze nejvyšší záměr.|
|`staging`|boolean|Vrátí dotaz z výsledků přípravného prostředí, pokud je nastavena hodnota true. |
|`log`|boolean|Protokoluje dotazy, které se dají použít později pro [aktivní učení](luis-how-to-review-endpoint-utterances.md). Platí výchozí hodnota.|

**_

### <a name="query-the-luis-app"></a>Dotazování aplikace LUIS

Příkladem SLOŽENÉho příkazu pro dotazování kontejneru pro publikovanou aplikaci je:

# <a name="v3-prediction-endpoint"></a>[Prediktivní koncový bod V3](#tab/v3)

K dotazování modelu ve slotu použijte následující rozhraní API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Chcete-li provést dotazy do prostředí _ *fázování**, nahraďte `production` v trase `staging` :

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

K dotazování modelu verze použijte následující rozhraní API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Koncový bod pro předpověď v2](#tab/v2)

K dotazování modelu ve slotu použijte následující rozhraní API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Chcete-li provést dotazy do **přípravného** prostředí, změňte hodnotu parametru řetězce **pracovního** dotazu na hodnotu true:

`staging=true`

K dotazování modelu verze použijte následující rozhraní API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Název verze má maximálně 10 znaků a obsahuje pouze znaky, které jsou povoleny v adrese URL.

**_

## <a name="import-the-endpoint-logs-for-active-learning"></a>Import protokolů koncového bodu pro aktivní učení

Pokud je pro kontejner LUIS zadáno výstupní připojení, soubory protokolu dotazu aplikace jsou uloženy ve výstupním adresáři, kde `{INSTANCE_ID}` je ID kontejneru. Protokol dotazu aplikace obsahuje dotaz, odpověď a časová razítka pro každý dotaz předpovědi odeslaný do kontejneru LUIS.

Následující umístění ukazuje strukturu vnořených adresářů pro soubory protokolu kontejneru.
```
/output/luis/{INSTANCE_ID}/
```

Na portálu LUIS vyberte svou aplikaci a pak vyberte _ *importovat protokoly koncových bodů**, aby se tyto protokoly nahrály.

![Importovat soubory protokolu kontejneru pro aktivní učení](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po nahrání protokolu [Zkontrolujte koncový bod](./luis-concept-review-endpoint-utterances.md) projevy na portálu Luis.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

Chcete-li vypnout kontejner, v prostředí příkazového řádku, kde je kontejner spuštěn, stiskněte **kombinaci kláves CTRL + C**.

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](luis-container-configuration.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejner LUIS odesílá informace o fakturaci do Azure pomocí prostředku _Cognitive Services_ ve vašem účtu Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnerů](luis-container-configuration.md).

## <a name="summary"></a>Shrnutí

V tomto článku jste zjistili koncepty a pracovní postup pro stažení, instalaci a spuštění kontejnerů Language Understanding (LUIS). Souhrn:

* Language Understanding (LUIS) poskytuje jeden kontejner pro Linux pro Docker poskytující dotaz koncového bodu předpovědi z projevy.
* Image kontejneru se stáhnou z Microsoft Container Registry (MCR).
* Image kontejneru se spouštějí v Docker.
* Můžete použít REST API k dotazování koncových bodů kontejneru zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru je nutné zadat informace o fakturaci.

> [!IMPORTANT]
> Nemusíte spouštět kontejnery Cognitive Services bez připojení k Azure pro měření. Zákazníci musí povolit kontejnerům, aby ve všech časech komunikovaly informace o fakturaci. Kontejnery Cognitive Services neodesílají zákaznická data (například obrázek nebo analyzovaný text) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [konfigurace kontejnerů](luis-container-configuration.md) pro nastavení konfigurace.
* Omezení známých schopností najdete v tématu [omezení kontejneru Luis](luis-container-limitations.md) .
* Problémy související s LUIS funkcemi najdete v tématu [řešení potíží](troubleshooting.md) .
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
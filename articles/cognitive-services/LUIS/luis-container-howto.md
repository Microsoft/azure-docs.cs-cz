---
title: Kontejnery Dockeru – LUIS
titleSuffix: Azure Cognitive Services
description: Kontejner LUIS načte trénované nebo publikované aplikace do kontejneru dockeru a poskytuje přístup k předpovědi dotazu z koncových bodů rozhraní API kontejneru.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fec6b16eb7f80369904eefc407a9a9c8d6629c9a
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879320"
---
# <a name="install-and-run-luis-docker-containers"></a>Instalace a spuštění kontejnerů instalačního programu LUIS
 
Kontejner language understanding (LUIS) načte trénovaný nebo publikovaný model language understanding. Jako [aplikace LUIS](https://www.luis.ai)poskytuje kontejner dockeru přístup k předpovědím dotazů z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrát je zpět do aplikace Language Understanding, abyste zlepšili přesnost předpovědi aplikace.

Následující video ukazuje použití tohoto kontejneru.

[![Ukázka kontejneru pro služby Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit kontejner LUIS, poznamenejte si následující požadavky:

|Požaduje se|Účel|
|--|--|
|Docker Engine| Potřebujete modul Docker Engine nainstalovaný v [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).<br><br> Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker také nakonfigurován pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Měli byste mít základní znalosti konceptů Dockeru, jako jsou registry, úložiště, kontejnery `docker` a image kontejnerů, stejně jako znalost základních příkazů.| 
|Zdroj `Cognitive Services` Azure a soubor [s balených aplikací](luis-how-to-start-new-app.md) LUIS |Chcete-li kontejner používat, musíte mít:<br><br>* Prostředek _Azure služeb Cognitive Services_ a přidružený fakturační klíč identifikátor URI koncového bodu fakturace. Obě hodnoty jsou k dispozici na stránce Přehled a Klíče pro prostředek a jsou nutné ke spuštění kontejneru. <br>* Trénovaná nebo publikovaná aplikace zabalená jako připojený vstup do kontejneru s přidruženým ID aplikace. Sbalený soubor můžete získat z portálu LUIS nebo z vytváření api. Pokud získáváte luis zabalené aplikace z [vytváření API](#authoring-apis-for-package-file), budete také potřebovat váš klíč _pro vytváření_.<br><br>Tyto požadavky se používají k předání argumentů příkazového řádku následujícím proměnným:<br><br>**{AUTHORING_KEY}:** Tento klíč se používá k získání zabalené aplikace ze služby LUIS v cloudu a nahrání protokolů dotazů zpět do cloudu. Formát je `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}:** Toto ID slouží k výběru aplikace. Formát je `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}:** Tento klíč se používá ke spuštění kontejneru. Klíč koncového bodu najdete na dvou místech. První je portál Azure v seznamu klíčů prostředků _cognitive services._ Klíč koncového bodu je také k dispozici na portálu LUIS na stránce Nastavení klíčů a koncového bodu. Nepoužívejte startovací tlačítko.<br><br>**{ENDPOINT_URI}:** Koncový bod uvedený na stránce Přehled.<br><br>Klíč [pro vytváření a klíč koncového bodu](luis-boundaries.md#key-limits) mají různé účely. Nepoužívejte je zaměnitelně. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Vytváření api pro soubor balíčku

Vytváření api pro zabalené aplikace:

* [Publikované rozhraní API balíčku](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Nepublikované rozhraní API pro školení pouze pro balíček](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavky na kontejnery a doporučení

Tento kontejner podporuje minimální a doporučené hodnoty pro nastavení:

|Kontejner| Minimální | Doporučené | Tps<br>(Minimální, maximální)|
|-----------|---------|-------------|--|
|LUIS|1 jádro, 2 GB paměti|1 jádro, 4 GB paměti|20, 40|

* Každé jádro musí být nejméně 2,6 gigahertzů (GHz) nebo rychlejší.
* TPS - transakce za sekundu

Jádro a paměť `--cpus` odpovídají `--memory` nastavení a, které se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získejte obrázek kontejneru pomocí`docker pull`

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu stáhněte bitovou `mcr.microsoft.com/azure-cognitive-services/luis` kopii kontejneru z úložiště:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Úplný popis dostupných značek, `latest` jako je například použit v předchozím příkazu, najdete v [tématu LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) na Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Jak kontejner používat

Jakmile je kontejner v [hostitelském počítači](#the-host-computer), použijte následující proces pro práci s kontejnerem.

![Proces použití kontejneru pro porozumění jazykům (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportovat balíček](#export-packaged-app-from-luis) pro kontejner z portálu LUIS nebo luis api.
1. Přesuňte soubor balíčku do požadovaného **vstupního** adresáře v [hostitelském počítači](#the-host-computer). Nepřejmenovávejte, neměňte, nepřepisujte ani nedekomprimujte soubor balíčku LUIS.
1. [Spusťte kontejner](#run-the-container-with-docker-run)s požadovaným _nastavením připojení a fakturace._ Další [příklady](luis-container-configuration.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici. 
1. [Dotazování koncového bodu předpověď kontejneru](#query-the-containers-prediction-endpoint). 
1. Po dokončení s kontejnerem [importujte protokoly koncového bodu](#import-the-endpoint-logs-for-active-learning) z výstupního připojení na portálu LUIS a [zastavte](#stop-the-container) kontejner.
1. Použijte [aktivní učení](luis-how-to-review-endpoint-utterances.md) portálu LUIS na stránce **Revize promluv koncového bodu** ke zlepšení aplikace.

Aplikaci spuštěnou v kontejneru nelze změnit. Chcete-li změnit aplikaci v kontejneru, musíte změnit aplikaci ve službě LUIS pomocí portálu [LUIS](https://www.luis.ai) nebo použít [vytváření LUIS API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Potom trénujte a/nebo publikujte, pak stáhněte nový balíček a znovu spusťte kontejner.

Aplikaci LUIS uvnitř kontejneru nelze exportovat zpět do služby LUIS. Lze odeslat pouze protokoly dotazů. 

## <a name="export-packaged-app-from-luis"></a>Export balené aplikace z LUIS

Kontejner LUIS vyžaduje trénované nebo publikované aplikace LUIS odpovědět na dotazy předpověď uživatele projevy. Chcete-li získat aplikaci LUIS, použijte trénované nebo publikované rozhraní API balíčku. 

Výchozí umístění je `input` podadresář ve vztahu k `docker run` místu, kde příkaz spouštěte.  

Umístěte soubor balíčku do adresáře a odkazovat na tento adresář jako vstupní připojení při spuštění kontejneru dockeru. 

### <a name="package-types"></a>Typy balíčků

Vstupní připojit adresář může obsahovat **produkční**, **pracovní**a **verze** modely aplikace současně. Všechny balíčky jsou namontovány.

|Typ balíčku|Rozhraní API koncového bodu dotazu|Dostupnost dotazu|Formát názvu souboru balíčku|
|--|--|--|--|
|Neobsahují|ZÍSKAT, ZVEŘEJNIT|Pouze kontejner|`{APP_ID}_v{APP_VERSION}.gz`|
|Příprava|ZÍSKAT, ZVEŘEJNIT|Azure a kontejner|`{APP_ID}_STAGING.gz`|
|Výroba|ZÍSKAT, ZVEŘEJNIT|Azure a kontejner|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Nepřejmenovávejte, neměňte, nepřepisujte ani nedekomprimujte soubory balíčku LUIS.

### <a name="packaging-prerequisites"></a>Požadavky na balení

Před balením aplikace LUIS musíte mít následující:

|Požadavky na balení|Podrobnosti|
|--|--|
|Instance prostředků _Azure Cognitive Services_|Podporované oblasti zahrnují<br><br>Západní USA`westus`( )<br>Západní Evropa`westeurope`( )<br>Austrálie –`australiaeast`východ ( )|
|Vyškolená nebo publikovaná aplikace LUIS|Bez [nepodporovaných závislostí][unsupported-dependencies]. |
|Přístup k systému souborů [hostitelského počítače](#the-host-computer) |Hostitelský počítač musí povolit [vstupní připojení](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Export balíčku aplikace z portálu LUIS

[Portál](https://www.luis.ai) LUIS poskytuje možnost exportovat trénovaný nebo publikovaný balíček aplikace.

### <a name="export-published-apps-package-from-luis-portal"></a>Exportovat balíček publikované aplikace z portálu LUIS

Balíček publikované aplikace je k dispozici na stránce se seznamem **Moje aplikace.** 

1. Přihlaste se k [portálu](https://www.luis.ai)LUIS .
1. Zaškrtněte políčko vlevo od názvu aplikace v seznamu. 
1. Vyberte položku **Exportovat** z kontextového panelu nástrojů nad seznamem.
1. Vyberte **export pro kontejner (GZIP).**
1. Vyberte prostředí **produkčního slotu** nebo **pracovního slotu**.
1. Balíček je stažen z prohlížeče.

![Export publikovaného balíčku pro kontejner z nabídky Export na stránce aplikace](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Export balíčku aplikace s verzí z portálu LUIS

Balíček aplikace s verzí je k dispozici na stránce se **seznamem Verze.**

1. Přihlaste se k [portálu](https://www.luis.ai)LUIS .
1. Vyberte v seznamu tuto aplikaci. 
1. Na navigačním panelu aplikace vyberte **Spravovat.**
1. Na levém navigačním panelu **vyberte Verze.**
1. Zaškrtněte políčko vlevo od názvu verze v seznamu.
1. Vyberte položku **Exportovat** z kontextového panelu nástrojů nad seznamem.
1. Vyberte **export pro kontejner (GZIP).**
1. Balíček je stažen z prohlížeče.

![Export trénovaného balíčku pro kontejner z nabídky Export na stránce Verze](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Export publikovaného balíčku aplikace z rozhraní API

Pomocí následující metody rozhraní REST API zabalte aplikaci LUIS, kterou jste už [publikovali](luis-how-to-publish-app.md). Nahrazení vlastních příslušných hodnot zástupnými symboly ve volání rozhraní API pomocí tabulky pod specifikací HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Hodnota |
|-------------|-------|
| **{APP_ID}** | ID aplikace publikované aplikace LUIS. |
| **{SLOT_NAME}** | Prostředí publikované aplikace LUIS. Použijte jednu z následujících hodnot:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | Klíč pro vytváření účtu LUIS pro publikovanou aplikaci LUIS.<br/>Klíč pro vytváření můžete získat na stránce **Nastavení uživatele** na portálu LUIS. |
| **{AZURE_REGION}** | Příslušná oblast Azure:<br/><br/>`westus`- Západní USA<br/>`westeurope`- Západní Evropa<br/>`australiaeast`- Austrálie – východ |

Chcete-li stáhnout publikovaný balíček, naleznete v [dokumentaci k rozhraní API zde][download-published-package]. Pokud byla úspěšně stažena, odpověď je soubor balíčku LUIS. Uložte soubor do umístění úložiště určeného pro vstupní připojení kontejneru. 

### <a name="export-versioned-apps-package-from-api"></a>Export balíčku aplikace s verzí z rozhraní API

Pomocí následující metody rozhraní REST API zabalte aplikaci LUIS, kterou jste již [vycvičili](luis-how-to-train.md). Nahrazení vlastních příslušných hodnot zástupnými symboly ve volání rozhraní API pomocí tabulky pod specifikací HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Hodnota |
|-------------|-------|
| **{APP_ID}** | ID aplikace trénované aplikace LUIS. |
| **{APP_VERSION}** | Verze aplikace aplikace trénované luis. |
| **{AUTHORING_KEY}** | Klíč pro vytváření účtu LUIS pro publikovanou aplikaci LUIS.<br/>Klíč pro vytváření můžete získat na stránce **Nastavení uživatele** na portálu LUIS. |
| **{AZURE_REGION}** | Příslušná oblast Azure:<br/><br/>`westus`- Západní USA<br/>`westeurope`- Západní Evropa<br/>`australiaeast`- Austrálie – východ |

Chcete-li stáhnout balíček s verzí, naleznete [v dokumentaci][download-versioned-package]k rozhraní API zde . Pokud byla úspěšně stažena, odpověď je soubor balíčku LUIS. Uložte soubor do umístění úložiště určeného pro vstupní připojení kontejneru. 

## <a name="run-the-container-with-docker-run"></a>Spusťte nádobu s`docker run`

Ke spuštění kontejneru použijte příkaz [spustit docker.](https://docs.docker.com/engine/reference/commandline/run/) Podrobnosti o tom, jak získat hodnoty `{ENDPOINT_URI}` `{API_KEY}` a, naleznete v [části shromažďování požadovaných parametrů.](#gathering-required-parameters)

[Příklady](luis-container-configuration.md#example-docker-run-commands) příkazu `docker run` jsou k dispozici.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Tento příklad používá adresář `C:` mimo jednotku, aby se zabránilo konfliktům oprávnění v systému Windows. Pokud potřebujete použít konkrétní adresář jako vstupní adresář, budete možná muset udělit oprávnění služby dockeru. 
* Neměňte pořadí argumentů, pokud nejste obeznámeni s kontejnery dockeru.
* Pokud používáte jiný operační systém, použijte správnou konzolu/terminál, syntaxi složky pro připojení a znak pokračování řádku pro váš systém. Tyto příklady předpokládají konzolu systému `^`Windows se znakem pokračování řádku . Vzhledem k tomu, že kontejner je operační systém Linux, cílové připojení používá syntaxi složky ve stylu Linuxu.

Tento příkaz:

* Spustí kontejner z image kontejneru LUIS
* Načte aplikaci LUIS ze vstupního připojení na *C:\input*, umístěného na hostiteli kontejneru.
* Přiděluje dvě jádra procesoru a 4 gigabajty (GB) paměti.
* Zpřístupní port TCP 5000 a přidělí pseudo-TTY pro kontejner
* Uloží protokoly kontejneru a luis pro výstupní připojení na *C:\output*, který se nachází na hostiteli kontejneru
* Automaticky odebere kontejner po jeho ukončení. Bitová kopie kontejneru je stále k dispozici v hostitelském počítači. 

Další [příklady](luis-container-configuration.md#example-docker-run-commands) `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`a `ApiKey` možnosti musí být zadány ke spuštění kontejneru; v opačném případě se kontejner nespustí.  Další informace naleznete v [tématu Fakturace](#billing).
> Hodnota ApiKey je **klíč** ze stránky **Prostředků Azure** na portálu LUIS `Cognitive Services` a je také k dispozici na stránce klíče prostředků Azure.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Api koncového bodu podporovaná kontejnerem

Verze rozhraní API v 2 i [V3](luis-migration-api-v3.md) jsou k dispozici s kontejnerem. 

## <a name="query-the-containers-prediction-endpoint"></a>Dotaz na koncový bod předpovědi kontejneru

Kontejner poskytuje úložiště dat na základě dotazu předpověď koncový bod API. Koncové body pro publikované (pracovní nebo produkční) aplikace mají _jinou_ trasu než koncové body pro aplikace s verzí.

Použijte hostitele `http://localhost:5000`, pro kontejnerová řešení API.

# <a name="v3-prediction-endpoint"></a>[Koncový bod predikce V3](#tab/v3)

|Typ balíčku|Příkaz HTTP|Trasa|Parametry dotazu|
|--|--|--|--|
|Publikováno|ZÍSKAT, ZVEŘEJNIT|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Neobsahují|ZÍSKAT, ZVEŘEJNIT|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Parametry dotazu konfigurují, jak a co je vráceno v odpovědi na dotaz:

|Parametr dotazu|Typ|Účel|
|--|--|--|
|`query`|řetězec|Projev uživatele.|
|`verbose`|Boolean|Logická hodnota označující, zda mají být vrácena všechna metadata pro předpovídané modely. Výchozí hodnota je false.|
|`log`|Boolean|Protokoly dotazy, které lze použít později pro [aktivní učení](luis-how-to-review-endpoint-utterances.md). Výchozí hodnota je false.|
|`show-all-intents`|Boolean|Logická hodnota označující, zda mají být vráceny všechny záměry nebo pouze hlavní záměr hodnocení. Výchozí hodnota je false.|

# <a name="v2-prediction-endpoint"></a>[Koncový bod predikce V2](#tab/v2)

|Typ balíčku|Příkaz HTTP|Trasa|Parametry dotazu|
|--|--|--|--|
|Publikováno|[ZÍSKAT](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [PŘÍSPĚVEK](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Neobsahují|ZÍSKAT, ZVEŘEJNIT|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Parametry dotazu konfigurují, jak a co je vráceno v odpovědi na dotaz:

|Parametr dotazu|Typ|Účel|
|--|--|--|
|`q`|řetězec|Projev uživatele.|
|`timezoneOffset`|číslo|TimeZoneOffset umožňuje [změnit časové pásmo](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) používané předem sestavené entity datetimeV2.|
|`verbose`|Boolean|Vrátí všechny záměry a jejich skóre, pokud je nastavena na hodnotu true. Výchozí hodnota je false, která vrátí pouze nejvyšší záměr.|
|`staging`|Boolean|Vrátí dotaz z výsledků pracovního prostředí, pokud je nastavena na hodnotu true. |
|`log`|Boolean|Protokoly dotazy, které lze použít později pro [aktivní učení](luis-how-to-review-endpoint-utterances.md). Platí výchozí hodnota.|

***

### <a name="query-the-luis-app"></a>Dotaz na aplikaci LUIS

Příklad příkazu CURL pro dotazování na kontejner pro publikovanou aplikaci je:

# <a name="v3-prediction-endpoint"></a>[Koncový bod predikce V3](#tab/v3)

Chcete-li zadat dotaz na model v patici, použijte následující rozhraní API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Chcete-li provádět **Staging** dotazy na `production` pracovní prostředí, `staging`nahraďte v trase :

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Chcete-li zadat dotaz na model s verzí, použijte následující rozhraní API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Koncový bod predikce V2](#tab/v2)

Chcete-li zadat dotaz na model v patici, použijte následující rozhraní API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Chcete-li provést dotazy do **pracovního** prostředí, změňte hodnotu parametru řetězce **pracovního** dotazu na hodnotu true: 

`staging=true`

Chcete-li zadat dotaz na model s verzí, použijte následující rozhraní API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Název verze má maximálně 10 znaků a obsahuje pouze znaky povolené v adrese URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Import protokolů koncových bodů pro aktivní učení

Pokud je pro kontejner LUIS zadáno výstupní připojení, soubory protokolu dotazů aplikací jsou uloženy ve výstupním adresáři, kde `{INSTANCE_ID}` je ID kontejneru. Protokol dotazu aplikace obsahuje dotaz, odpověď a časová razítka pro každý dotaz předpověď odeslané do kontejneru LUIS. 

Následující umístění zobrazuje vnořenou adresářovou strukturu pro soubory protokolu kontejneru.
```
/output/luis/{INSTANCE_ID}/
```
 
Na portálu LUIS vyberte aplikaci a pak vyberte **Importovat protokoly koncových bodů** a nahrajte tyto protokoly. 

![Import souborů protokolu kontejneru pro aktivní učení](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po nahrání protokolu [zkontrolujte projevy koncového bodu](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) na portálu LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

Chcete-li kontejner vypnout, stiskněte v prostředí příkazového řádku, kde je kontejner spuštěn, **kombinaci kláves Ctrl+C**.

## <a name="troubleshooting"></a>Řešení potíží

Pokud spustíte kontejner s povoleným výstupním [připojením](luis-container-configuration.md#mount-settings) a protokolováním, kontejner generuje soubory protokolu, které jsou užitečné k řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Fakturace

Kontejner LUIS odesílá fakturační údaje do Azure pomocí prostředku _služeb Cognitive Services_ na vašem účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech naleznete v [tématu Konfigurace kontejnerů](luis-container-configuration.md).

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste se naučili koncepty a pracovní postupy pro stahování, instalaci a spouštění kontejnerů pro porozumění jazykům (LUIS). Souhrn:

* Language Understanding (LUIS) poskytuje jeden kontejner Linuxpro Docker poskytující předpovědi dotazu koncového bodu projevy.
* Image kontejnerů se stahují z registru Microsoft Container Registry (MCR).
* Image kontejnerů běží v Dockeru.
* Rozhraní REST API můžete použít k dotazování koncových bodů kontejneru zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instancí kontejneru je nutné zadat fakturační údaje.

> [!IMPORTANT]
> Kontejnery služeb Cognitive Services nejsou licencovány ke spuštění bez připojení k Azure pro měření. Zákazníci musí povolit kontejnery komunikovat fakturační údaje se službou měření za všech okolností. Kontejnery služeb Cognitive Services neodesílají data zákazníků (například obrázek nebo text, který se analyzuje) společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Přečtěte [si panel Konfigurace kontejnerů](luis-container-configuration.md) pro nastavení konfigurace.
* Omezení [kontejnerů LUIS](luis-container-limitations.md) najdete v tématu známá omezení schopností.
* Informace o řešení problémů souvisejících s funkcemi služby LUIS naleznete v [tématu Poradce při potížích.](troubleshooting.md)
* Použití více [kontejnerů služeb Cognitive Services](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container

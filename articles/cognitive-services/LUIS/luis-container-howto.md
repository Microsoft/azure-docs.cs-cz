---
title: Kontejnery Dockeru
titleSuffix: Language Understanding - Azure Cognitive Services
description: Kontejner LUIS načte trénovaného nebo publikované aplikace do kontejneru dockeru a poskytuje přístup k předpovědi dotazu z koncových bodů rozhraní API kontejneru.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 97f11523c0418caaee66930c87a7de64570097d6
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296895"
---
# <a name="install-and-run-luis-docker-containers"></a>Nainstalujte a spusťte LUIS kontejnery dockeru
 
Language Understanding (LUIS) kontejneru načte trénovaného nebo publikované model Language Understanding, jsou označovány také jako [aplikace LUIS](https://www.luis.ai), do kontejneru dockeru a poskytuje přístup k předpovědi dotazu z kontejneru API Koncové body. Můžete shromažďovat protokoly dotazů z kontejneru a nahrání tyto zpět na model Azure Language Understanding zvyšte přesnost předpovědi aplikace.

Následující video ukazuje použití tohoto kontejneru.

[![Ukázkový kontejner pro služby Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit kontejner LUIS, musíte mít následující: 

|Požaduje se|Účel|
|--|--|
|Modul docker| Je nutné modul Docker nainstalovaný na [hostitelský počítač](#the-host-computer). Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **Na Windows**, Docker musí být taky nakonfigurovaný pro podporu kontejnerů Linuxu.<br><br>|
|Znalost Dockeru | Byste měli mít základní znalost konceptů Dockeru, jako je registrů, úložiště, kontejnery a Image kontejneru, jakož i znalost basic `docker` příkazy.| 
|Language Understanding (LUIS) prostředků a přidružené aplikace |Chcete-li použít kontejner, musíte mít:<br><br>* A [ _Language Understanding_ prostředků Azure](luis-how-to-azure-subscription.md), spolu s klíčem přidruženého koncového bodu a koncový bod identifikátoru URI (použito jako fakturace koncový bod).<br>* A trénovaného nebo publikované aplikace lze zabalit jako připojené vstup do kontejneru s jeho přidružené ID aplikace.<br>* Klíč pro vytváření obsahu a stáhněte balíček aplikace, pokud je to z rozhraní API.<br><br>Tyto požadavky se používají k předání argumentů příkazového řádku následující proměnné:<br><br>**{AUTHORING_KEY}**: Tento klíč se používá k získání zabalené aplikace ze služby LUIS v cloudu a nahrajte protokoly dotazů zpátky do cloudu. Formát je `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Tento Identifikátor slouží k výběru aplikace. Formát je `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}**: Tento klíč se používá ke spuštění kontejneru. Klíč koncového bodu můžete najít na dvou místech. První je na webu Azure portal v rámci _Language Understanding_ klíče seznamu zdrojů. Klíč koncového bodu je také k dispozici na portálu služby LUIS nad klíči a koncový bod stránku nastavení. Nepoužívejte klávesu starter.<br><br>**{BILLING_ENDPOINT}**: Fakturační hodnota koncového bodu je k dispozici na stránce s přehledem Language Understanding webu Azure portal. Příklad: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>[Vytváření klíč a klíče koncového bodu](luis-boundaries.md#key-limits) mají různé účely. Nepoužívejte je Zaměnitelně. |

### <a name="the-host-computer"></a>Hostitelském počítači

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Tento kontejner podporuje minimální a doporučené hodnoty pro nastavení:

|Nastavení| Minimální | Doporučené |
|-----------|---------|-------------|
|Jádra<BR>`--cpus`|1 jádro|1 jádro|
|Memory (Paměť)<BR>`--memory`|2 GB|4 GB|
|Transakce za sekundu<BR>(TPS)|20 TPS|40 TPS|

Každé jádro, musí být aspoň 2.6 gigahertz (GHz) nebo rychlejší.

`--cpus` a `--memory` nastavení jsou použita jako součást `docker run` příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru s `docker pull`

Použití [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru z `mcr.microsoft.com/azure-cognitive-services/luis` úložiště:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Použití [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru.

Úplný popis dostupných značek jako například `latest` použili v předchozím příkazu, naleznete v tématu [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) na Docker Hubu.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Jak používat kontejneru

Jakmile bude kontejner ve [hostitelský počítač](#the-host-computer), použijte následující postup pro práci s kontejnerem.

![Proces pomocí kontejneru Language Understanding (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportovat balíček](#export-packaged-app-from-luis) pro kontejner ze služby LUIS portal nebo rozhraním API LUIS.
1. Přesunout soubor balíčku do požadované **vstupní** adresáři [hostitelský počítač](#the-host-computer). Přejmenovat, změnit nebo dekomprimovat soubor balíčku LUIS.
1. [Spuštění kontejneru](##run-the-container-with-docker-run), s požadovanými _vstupní připojení_ a fakturaci. nastavení. Další [příklady](luis-container-configuration.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 
1. [Dotazování na koncový bod kontejneru předpovědi](#query-the-containers-prediction-endpoint). 
1. Jakmile budete hotovi s kontejnerem, [importovat protokoly koncového bodu](#import-the-endpoint-logs-for-active-learning) z výstupu připojit na portálu služby LUIS a [Zastavit](#stop-the-container) kontejneru.
1. Použití služby LUIS portálu [aktivně učit](luis-how-to-review-endoint-utt.md) na **zkontrolujte koncový bod projevy** stránku ke zlepšení aplikace.

Aplikace spuštěné v kontejneru se nedá změnit. V pořadí změnu aplikace v kontejneru, je třeba změnit aplikace pomocí služby LUIS [LUIS](https://www.luis.ai) portálu nebo pomocí LUIS [rozhraní API pro vytváření](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Potom trénování a/nebo publikovat, pak stáhněte nový balíček a znovu spusťte kontejner.

Aplikace LUIS uvnitř kontejneru nelze exportovat zpět do služby LUIS. Můžete nahrát pouze protokoly dotazů. 

## <a name="export-packaged-app-from-luis"></a>Export zabalené aplikace ze služby LUIS

Kontejner LUIS vyžaduje, aby trénovaného nebo publikované aplikace LUIS zodpovědět dotazy předpovědi projevy uživatele. Pokud chcete získat aplikaci LUIS, použijte buď trénovaného nebo publikovaný balíček rozhraní API. 

Výchozí umístění je `input` podadresář ve vztahu k, kde je spuštěn `docker run` příkazu.  

Umístěte soubor balíčku v adresáři a odkazují na tento adresář jako vstupní připojení při spuštění kontejneru dockeru. 

### <a name="package-types"></a>Typy balíčků

Může obsahovat vstupní přípojného adresáře **produkční**, **pracovní**, a **Trained** verzí aplikace současně. Všechny balíčky jsou připojené. 

|Typ balíčku|Rozhraní API pro dotazy koncového bodu|Dostupnost dotazu|Formát názvu souboru balíčku|
|--|--|--|--|
|Školení|GET, Post|Pouze kontejner|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Fázování|GET, Post|Azure a kontejnerů|`{APPLICATION_ID}_STAGING.gz`|
|Výroba|GET, Post|Azure a kontejnerů|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Přejmenovat, změnit nebo dekomprimovat soubory balíčku LUIS.

### <a name="packaging-prerequisites"></a>Požadavky na balení

Než připravíte balíček aplikace LUIS, musíte mít následující:

|Požadavky na balení|Podrobnosti|
|--|--|
|Azure _Language Understanding_ instance prostředku|Podporované oblasti patří<br><br>USA – západ (```westus```)<br>Západní Evropa (```westeurope```)<br>Austrálie – východ (```australiaeast```)|
|Trénované nebo publikované aplikace LUIS|Bez [nepodporované závislosti](#unsupported-dependencies). |
|Přístup k [hostitelský počítač](#the-host-computer)pro systém souborů |Musíte povolit hostitelský počítač [vstupní připojení](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Exportovat balíček aplikace z portálu služby LUIS

LUIS [portál](https://www.luis.ai) umožňuje exportovat balíček trénovaného nebo publikované aplikace. 

### <a name="export-published-apps-package-from-luis-portal"></a>Exportovat balíček publikované aplikace z portálu služby LUIS

Publikovanou aplikaci balíčku je k dispozici **Moje aplikace** stránku se seznamem. 

1. Přihlaste se LUIS [portál](https://www.luis.ai).
1. V seznamu vyberte zaškrtávací políčko nalevo od názvu aplikace. 
1. Vyberte **exportovat** položky z kontextové nástrojů nad seznamem.
1. Vyberte **Export pro kontejner (GZIP)**.
1. Vyberte prostředí, které **produkčního slotu** nebo **přípravný slot**.
1. Balíček se stahuje z prohlížeče.

![Exportovat balíček publikované pro kontejner exportu v nabídce na stránce aplikace](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Exportovat balíček trénovaného aplikace z portálu služby LUIS

Balíček trénovaného aplikace je k dispozici **verze** stránku se seznamem. 

1. Přihlaste se LUIS [portál](https://www.luis.ai).
1. Vyberte aplikaci v seznamu. 
1. Vyberte **spravovat** navigačním panelu aplikace.
1. Vyberte **verze** v levém navigačním panelu.
1. V seznamu vyberte zaškrtávací políčko nalevo od názvu verze.
1. Vyberte **exportovat** položky z kontextové nástrojů nad seznamem.
1. Vyberte **Export pro kontejner (GZIP)**.
1. Balíček se stahuje z prohlížeče.

![Exportovat natrénované balíček pro kontejner z nabídky Export verze stránky](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>Exportovat balíček publikované aplikace z rozhraní API

Použijte následující metodu rozhraní REST API zabalit aplikaci LUIS, který jste již [publikované](luis-how-to-publish-app.md). Nahraďte vlastním příslušné hodnoty pro zástupné symboly ve volání rozhraní API pomocí tabulky níže specifikaci protokolu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Hodnota |
|-------------|-------|
|{APPLICATION_ID} | ID aplikace publikované aplikace LUIS. |
|{APPLICATION_ENVIRONMENT} | Prostředí publikované aplikace LUIS. Použijte jednu z následujících hodnot:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Vytváření klíč účtu služby LUIS pro publikovanou aplikaci LUIS.<br/>Můžete získat klíč pro vytváření obsahu z **uživatelská nastavení** stránky na portálu služby LUIS. |
|{AZURE_REGION} | Příslušné oblasti Azure:<br/><br/>```westus``` – USA – západ<br/>```westeurope``` – Evropa západ<br/>```australiaeast``` – Austrálie – východ |

Použijte následující příkaz CURL ke stažení zveřejněný balíček, nahraďte vlastními hodnotami:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

V případě úspěchu je odpověď na soubor balíčku LUIS. Uložte soubor v zadané umístění úložiště pro vstupní připojení kontejneru. 

### <a name="export-trained-apps-package-from-api"></a>Exportovat balíček trénovaného aplikace z rozhraní API

Použijte následující metodu rozhraní REST API pro zabalení aplikace LUIS, který jste již [trénovaného](luis-how-to-train.md). Nahraďte vlastním příslušné hodnoty pro zástupné symboly ve volání rozhraní API pomocí tabulky níže specifikaci protokolu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Hodnota |
|-------------|-------|
|{APPLICATION_ID} | ID aplikace trénovaného aplikace LUIS. |
|{APPLICATION_VERSION} | Verze aplikace trénovaného aplikace LUIS. |
|{AUTHORING_KEY} | Vytváření klíč účtu služby LUIS pro publikovanou aplikaci LUIS.<br/>Můžete získat klíč pro vytváření obsahu z **uživatelská nastavení** stránky na portálu služby LUIS.  |
|{AZURE_REGION} | Příslušné oblasti Azure:<br/><br/>```westus``` – USA – západ<br/>```westeurope``` – Evropa západ<br/>```australiaeast``` – Austrálie – východ |

Stáhnout balíček trénovaného použijte následující příkaz CURL:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

V případě úspěchu je odpověď na soubor balíčku LUIS. Uložte soubor v zadané umístění úložiště pro vstupní připojení kontejneru. 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s `docker run`

Použití [dockeru spustit](https://docs.docker.com/engine/reference/commandline/run/) příkaz ke spuštění kontejneru. Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{ENDPOINT_KEY} | Tento klíč se používá ke spuštění kontejneru. Nepoužívejte klávesu starter. |
|{BILLING_ENDPOINT} | Fakturační hodnota koncového bodu je k dispozici na stránce s přehledem Language Understanding webu Azure portal.|

Tyto parametry nahraďte vlastními hodnotami v následujícím příkladu `docker run` příkazu.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Ve výstupu předchozího příkazu využívá adresáři `c:` disku, aby se zabránilo konfliktům oprávnění na Windows. Pokud je potřeba použít konkrétní adresář jako vstupní adresář, budete muset udělit dockeru služby oprávnění. Ve výstupu předchozího příkazu docker používá zpětné lomítko `\`, jako znak pro pokračování řádku. Nahraďte nebo odstraňte to na základě vašich [hostitelský počítač](#the-host-computer) požadavky na operační systém. Pořadí argumentů nezmění, pokud máte velmi zkušenosti s kontejnery dockeru.


Tento příkaz:

* Spuštění kontejneru služby luis image kontejneru
* Načte ze vstupní připojení na c:\input umístěný na hostiteli aplikace LUIS
* Přidělí dvě jader procesoru a paměti, 4 gigabajty (GB)
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Uloží kontejneru a LUIS protokoly do výstupního připojení na c:\output umístěný na hostiteli
* Po ukončení automaticky odstraní kontejner. Image kontejneru je stále k dispozici na hostitelském počítači. 

Další [příklady](luis-container-configuration.md#example-docker-run-commands) z `docker run` příkazu jsou k dispozici. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).
> Hodnota ApiKey **klíč** z klíče a koncových bodů stránky na portálu služby LUIS a je také k dispozici na na stránce klíče Azure Language Understanding prostředků.  

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncový bod kontejneru predikcí

Kontejner poskytuje koncový bod předpovědi dotazů založených na REST API. Koncové body pro publikované aplikace (přípravné nebo produkční) mít _různých_ trasy než koncové body pro trénovaný aplikace. 

Použít hostitele, https://localhost:5000, pro kontejner rozhraní API. 

|Typ balíčku|Metoda|Trasa|Parametry dotazu|
|--|--|--|--|
|Publikováno|[Získat](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [příspěvku](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q = {q}<br>& pracovní<br>[& timezoneOffset]<br>[& podrobné]<br>[& protokolu]<br>|
|Školení|GET, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q = {q}<br>[& timezoneOffset]<br>[& podrobné]<br>[& protokolu]|

Konfigurovat parametry dotazu jak a co je vrácená v odpovědi na dotaz:

|Parametr dotazu|Type|Účel|
|--|--|--|
|`q`|řetězec|Utterance uživatele.|
|`timezoneOffset`|číslo|TimezoneOffset umožňuje [změnit časové pásmo](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) používané datetimeV2 předem připravených entit.|
|`verbose`|Boolean|Vrátí všechny příkazy a jejich výsledky, pokud je nastavena na hodnotu true. Výchozí hodnota je false, která vrací pouze hlavní záměr.|
|`staging`|Boolean|Vrátí dotaz z pracovní prostředí výsledky, pokud je nastavena na hodnotu true. |
|`log`|Boolean|Zaznamenává dotazy, které je možné použít později pro [aktivně učit](luis-how-to-review-endoint-utt.md). Výchozí hodnota je true.|

### <a name="query-published-app"></a>Dotaz publikované aplikace

Je například příkaz CURL pro dotazování na kontejner pro publikované aplikace:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Dotazy na **pracovní** prostředí, změna **pracovní** dotazování řetězcovou hodnotu parametru na hodnotu true: 

`staging=true`

### <a name="query-trained-app"></a>Dotazování trénovaného aplikace

Příklad příkaz CURL pro dotazování na kontejner pro trénovaný aplikace je: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Název verze s maximálně 10 znaků a obsahuje pouze znaků povolených v adrese URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Import protokolů koncový bod pro aktivní učení

Pokud připojení výstupu je zadaný pro kontejner LUIS, souborů protokolů dotazu aplikace se uloží ve výstupním adresáři, kde {INSTANCE_ID} je ID kontejneru. Protokol dotazu aplikace obsahuje dotaz, odpověď a časová razítka pro každý dotaz předpovědi odeslané do kontejneru služby LUIS. 

Do následujícího umístění znázorňuje strukturu vnořené adresář pro soubory protokolů kontejneru.
`
/output/luis/{INSTANCE_ID}/
`
 
Z portálu služby LUIS, vyberte svou aplikaci a pak vyberte **importovat protokoly koncového bodu** nahrát protokoly. 

![Naimportujte soubory protokolů kontejneru pro aktivní učení](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po nahrání protokolu [zkontrolujte koncový bod](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) projevy na portálu služby LUIS.

## <a name="stop-the-container"></a>Zastavit kontejner

Chcete-li vypnout kontejneru, v prostředí příkazového řádku, ve kterém je kontejner spuštěný, stiskněte **Ctrl + C**.

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s výstupem [připojit](luis-container-configuration.md#mount-settings) a povolení protokolování kontejneru vygeneruje soubory protokolů, které jsou užitečné při řešení potíží, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="containers-api-documentation"></a>Dokumentace k rozhraní API kontejneru

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Fakturace

Odešle kontejneru LUIS fakturační údaje do Azure, pomocí _Language Understanding_ prostředků v účtu Azure. 

Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (utterance) společnosti Microsoft. 

`docker run` Používá následující argumenty pro účely fakturace:

| Možnost | Popis |
|--------|-------------|
| `ApiKey` | Klíč rozhraní API _Language Understanding_ prostředek, který používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musí být nastavená na klíče rozhraní API pro zřízené LUIS Azure prostředek určený v `Billing`. |
| `Billing` | Koncový bod _Language Understanding_ prostředek, který používá ke sledování fakturační údaje.<br/>Hodnota této možnosti musí nastavena na identifikátor URI se zřídil prostředek LUIS Azure koncový bod.|
| `Eula` | Označuje, že jste přijali licenci pro kontejner.<br/>Hodnota této možnosti musí být nastavená na `accept`. |

> [!IMPORTANT]
> Všechny tři možnosti je nutné zadat platnou hodnotou nebo kontejneru se nespustí.

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](luis-container-configuration.md).

## <a name="unsupported-dependencies"></a>Nepodporovaná závislosti

Aplikace LUIS můžete použít, pokud ho **neobsahuje** některý z následujících podmínek:

Konfigurace nepodporované aplikací.|Podrobnosti|
|--|--|
|Nepodporovaná kontejneru jazykových verzí| Němčina (de-DE)<br>Holandština (nl-NL)<br>Japonština (ja-JP)<br>|
|Nepodporovaná domén|Předem připravených domén, včetně předem připravených domény záměry a entity|
|Nepodporovaná entity pro všechny jazykové verze|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) předem připravených entit pro všechny jazykové verze|
|Nepodporovaná entity pro jazykovou verzi Angličtina (en US)|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) předem připravených entit|
|Dočištění řeči|Vnější závislosti nejsou podporovány v kontejneru.|
|Analýza mínění|Vnější závislosti nejsou podporovány v kontejneru.|
|Pro kontrolu pravopisu Bingu|Vnější závislosti nejsou podporovány v kontejneru.|

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili, koncepty a pracovní postup pro stažení, instalaci a používání kontejnerů Language Understanding (LUIS). Souhrn:

* Language Understanding (LUIS) poskytuje jeden kontejner Linuxu pro Docker poskytuje koncový bod dotazu predikce projevy.
* Image kontejneru se stáhnou z registru Microsoft kontejneru (MCR).
* Spuštění imagí kontejnerů v Dockeru.
* Rozhraní REST API můžete použít k dotazování koncových bodů kontejneru tak, že zadáte identifikátor URI kontejneru hostitele.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Cognitive Services kontejnery Neodesílat data zákazníků (třeba image nebo text, který je analyzován) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](luis-container-configuration.md) nastavení konfigurace
* Odkazovat na [– nejčastější dotazy (FAQ)](luis-resources-faq.md) k vyřešení problémů týkajících se služby LUIS funkce.
* Použití více [kontejnery Cognitive Services](../cognitive-services-container-support.md)

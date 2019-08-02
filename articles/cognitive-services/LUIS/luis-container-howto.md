---
title: Kontejnery Docker – LUIS
titleSuffix: Azure Cognitive Services
description: Kontejner LUIS načte vaši vyškolenou nebo publikovanou aplikaci do kontejneru Docker a poskytne přístup k dotazu předpovědi z koncových bodů rozhraní API kontejneru.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dapine
ms.openlocfilehash: 2b87f9bcbaa0fd9d8a23d774e0765e1eb5b56633
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563896"
---
# <a name="install-and-run-luis-docker-containers"></a>Instalace a spuštění kontejnerů Docker LUIS
 
Kontejner Language Understanding (LUIS) načte vyškolený nebo publikovaný Language Understanding model, také jako [aplikaci Luis](https://www.luis.ai), do kontejneru Docker a poskytuje přístup k předpovědi dotazů z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrajte je zpátky do aplikace Language Understanding, aby se zlepšila přesnost předpovědi aplikace.

Následující video znázorňuje použití tohoto kontejneru.

[![Ukázkový kontejner pro služby Cognitive Services](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné spustit kontejner LUIS, musíte mít následující: 

|Požadováno|Účel|
|--|--|
|Modul Docker| Potřebujete modul Docker nainstalovaný na [hostitelském počítači](#the-host-computer). Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/)a [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).<br><br> Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure. <br><br> **V systému Windows**musí být Docker taky nakonfigurovaný tak, aby podporoval kontejnery Linux.<br><br>|
|Znalost pomocí Docker | Měli byste mít základní znalosti konceptů Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.| 
|Soubor `Cognitive Services` Luis zabalených [aplikací](luis-how-to-start-new-app.md#export-app-for-containers) a prostředků Azure |Aby bylo možné kontejner používat, musíte mít:<br><br>* _Cognitive Services_ prostředek Azure a související fakturační klíč identifikátor URI koncového bodu. Obě hodnoty jsou k dispozici na stránkách přehledu a klíčů pro daný prostředek a jsou požadovány ke spuštění kontejneru. Musíte přidat `luis/v2.0` směrování k identifikátoru URI koncového bodu, jak je znázorněno v následujícím příkladu BILLING_ENDPOINT_URI. <br>* Vyškolená nebo publikovaná aplikace zabalená jako připojená vstup do kontejneru s jeho přidruženým ID aplikace. Zabalený soubor můžete získat z portálu LUIS nebo z rozhraní API pro vytváření obsahu. Pokud získáváte LUIS zabalenou aplikaci z [rozhraní API pro vytváření obsahu](#authoring-apis-for-package-file), budete také potřebovat svůj _klíč pro vytváření obsahu_.<br><br>Tyto požadavky slouží k předání argumentů příkazového řádku do následujících proměnných:<br><br>**{AUTHORING_KEY}** : Tento klíč se používá k získání zabalené aplikace ze služby LUIS v cloudu a k nahrání protokolů dotazů zpět do cloudu. Formát je `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Toto ID se používá k výběru aplikace. Formát je `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : Tento klíč se používá ke spuštění kontejneru. Klíč koncového bodu můžete najít na dvou místech. První je Azure Portal v seznamu klíčů prostředků _Cognitive Services_ . Klíč koncového bodu je k dispozici také na portálu LUIS na stránce klíče a nastavení koncového bodu. Nepoužívejte počáteční klíč.<br><br>**{ENDPOINT_URI}** : Koncový bod, jak je uvedeno na stránce Přehled.<br><br>Klíč pro [vytváření obsahu a klíč koncového bodu](luis-boundaries.md#key-limits) mají různé účely. Nepoužívejte je zaměnitelné. |

### <a name="authoring-apis-for-package-file"></a>Vytváření rozhraní API pro soubor balíčku

Vytváření rozhraní API pro zabalené aplikace:

* [Rozhraní API publikovaného balíčku](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Rozhraní API balíčku bez publikování, jenom pro školení](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>Hostitelský počítač

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Požadavků na kontejner a doporučení

Tento kontejner podporuje minimální a doporučené hodnoty pro nastavení:

|Kontejner| Minimální | Doporučené | TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|LUIS|1 jádro, 2 GB paměti|1 jádro, 4 GB paměti|20,40|

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* TPS-transakcí za sekundu

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.

## <a name="get-the-container-image-with-docker-pull"></a>Získat image kontejneru pomocí`docker pull`

Pomocí příkazu si stáhněte image kontejneru `mcr.microsoft.com/azure-cognitive-services/luis` z úložiště: [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) Pomocí příkazu Stáhněte image kontejneru.

Úplný popis dostupných značek, `latest` jako je například použitý v předchozím příkazu, najdete v tématu [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) v Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Jak používat kontejner

Jakmile je kontejner na hostitelském [počítači](#the-host-computer), použijte následující postup pro práci s kontejnerem.

![Proces použití Language Understandingho kontejneru (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportujte balíček](#export-packaged-app-from-luis) pro kontejner z portálu Luis nebo z rozhraní Luis API.
1. Přesuňte soubor balíčku do požadovaného **vstupního** adresáře v hostitelském [počítači](#the-host-computer). Neměňte přejmenování, změnu, přepsání nebo dekomprimaci souboru balíčku LUIS.
1. [Spusťte kontejner](##run-the-container-with-docker-run)s požadovaným vstupním _připojením_ a nastavením fakturace. K [](luis-container-configuration.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu. 
1. [Dotazování koncového bodu předpovědi kontejneru](#query-the-containers-prediction-endpoint) 
1. Až budete s kontejnerem hotovi, [importujte protokoly koncových bodů](#import-the-endpoint-logs-for-active-learning) z výstupního připojení na portálu Luis a [](#stop-the-container) zastavte kontejner.
1. K vylepšení aplikace použijte [aktivní učení](luis-how-to-review-endpoint-utterances.md) na portálu Luis na stránce **zkontrolovat koncový bod projevy** .

Aplikace spuštěná v kontejneru se nedá změnit. Aby bylo možné aplikaci změnit v kontejneru, je nutné změnit aplikaci ve službě LUIS pomocí portálu [Luis](https://www.luis.ai) nebo použít [rozhraní API pro vytváření](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)Luis. Pak proveďte výuku nebo publikování a pak stáhněte nový balíček a znovu spusťte kontejner.

Aplikaci LUIS uvnitř kontejneru nejde exportovat zpátky do služby LUIS. Odeslat lze pouze protokoly dotazů. 

## <a name="export-packaged-app-from-luis"></a>Exportovat zabalenou aplikaci z LUIS

Kontejner LUIS vyžaduje vyškolenou nebo publikovanou aplikaci LUIS pro zodpovězení dotazů na předpovědi uživatele projevy. K získání aplikace LUIS použijte buď vyškolené nebo publikované balíčky API. 

Výchozím umístěním je `input` podadresář v souvislosti s tím, kde `docker run` příkaz spustíte.  

Po spuštění kontejneru Docker umístěte soubor balíčku do adresáře a odkažte tento adresář jako vstupní připojení. 

### <a name="package-types"></a>Typy balíčků

Vstupní adresář pro připojení může obsahovat současně **produkční**, **pracovní**a **výukové** verze aplikace. Všechny balíčky jsou připojené. 

|Typ balíčku|Rozhraní API koncového bodu dotazu|Dostupnost dotazů|Formát souboru balíčku|
|--|--|--|--|
|Trénovaný|Získat, zveřejnit|Pouze kontejner|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Fázování|Získat, zveřejnit|Azure a kontejner|`{APPLICATION_ID}_STAGING.gz`|
|Provozní|Získat, zveřejnit|Azure a kontejner|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Neměňte přejmenování, změnu, přepsání nebo dekomprimaci souborů balíčku LUIS.

### <a name="packaging-prerequisites"></a>Požadavky na sbalení

Před zabalením aplikace LUIS musíte mít následující:

|Požadavky na balení|Podrobnosti|
|--|--|
|Instance prostředků Azure _Cognitive Services_|Mezi podporované oblasti patří<br><br>Západní USA (```westus```)<br>Západní Evropa (```westeurope```)<br>Austrálie – východ```australiaeast```()|
|Školená nebo publikovaná aplikace LUIS|Bez [nepodporovaných závislostí](#unsupported-dependencies). |
|Přístup k systému souborů [hostitelského počítače](#the-host-computer) |Hostitelský počítač musí umožňovat [vstupní připojení](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Exportovat balíček aplikace z portálu LUIS

[Portál](https://www.luis.ai) Luis nabízí možnost Exportovat balíček trained nebo publikované aplikace. 

### <a name="export-published-apps-package-from-luis-portal"></a>Exportovat balíček publikované aplikace z portálu LUIS

Balíček publikované aplikace je k dispozici na stránce seznam **Moje aplikace** . 

1. Přihlaste se k [portálu](https://www.luis.ai)Luis.
1. Zaškrtněte políčko vlevo od názvu aplikace v seznamu. 
1. Vyberte položku **exportu** z kontextového panelu nástrojů nad seznamem.
1. Vyberte **Exportovat pro kontejner (gzip)** .
1. Vyberte prostředí produkčního **slotu** nebo **přípravného slotu**.
1. Balíček se stáhne z prohlížeče.

![Exportujte publikovaný balíček pro kontejner z nabídky export stránky aplikace.](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Exportovat balíček školené aplikace z portálu LUIS

Balíček aplikace, který je k dispozici, je k dispozici na stránce seznam **verzí** . 

1. Přihlaste se k [portálu](https://www.luis.ai)Luis.
1. Vyberte aplikaci v seznamu. 
1. V navigačním panelu aplikace vyberte **Spravovat** .
1. Vyberte **verze** v levém navigačním panelu.
1. Zaškrtněte políčko vlevo od názvu verze v seznamu.
1. Vyberte položku **exportu** z kontextového panelu nástrojů nad seznamem.
1. Vyberte **Exportovat pro kontejner (gzip)** .
1. Balíček se stáhne z prohlížeče.

![Exportujte vyškolený balíček pro kontejner z nabídky export stránky verze](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>Exportovat balíček publikované aplikace z rozhraní API

K zabalení aplikace LUIS, kterou jste už publikovali, použijte následující metodu [](luis-how-to-publish-app.md)REST API. Nahrazení vlastních hodnot pro zástupné symboly v volání rozhraní API pomocí tabulky pod specifikací protokolu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Value |
|-------------|-------|
|{APPLICATION_ID} | ID aplikace publikované aplikace LUIS |
|{APPLICATION_ENVIRONMENT} | Prostředí publikované aplikace v LUIS Použijte jednu z následujících hodnot:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Klíč pro tvorbu účtu LUIS pro publikovanou aplikaci LUIS<br/>Svůj klíč pro vytváření můžete získat ze stránky **uživatelských nastavení** na portálu Luis. |
|{AZURE_REGION} | Příslušná oblast Azure:<br/><br/>```westus```-Západní USA<br/>```westeurope```-Západní Evropa<br/>```australiaeast```– Austrálie – východ |

Pokud si chcete stáhnout publikovaný balíček, přečtěte si [tady dokumentaci k rozhraní API][download-published-package]. Po úspěšném stažení je odpověď souborem balíčku LUIS. Uložte soubor do umístění úložiště zadaného pro vstupní připojení kontejneru. 

### <a name="export-trained-apps-package-from-api"></a>Exportovat balíček školené aplikace z rozhraní API

K zabalení aplikace LUIS, kterou jste už proškolenou, použijte následující [](luis-how-to-train.md)metodu REST API. Nahrazení vlastních hodnot pro zástupné symboly v volání rozhraní API pomocí tabulky pod specifikací protokolu HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Zástupný symbol | Value |
|-------------|-------|
|{APPLICATION_ID} | ID aplikace vyškolené aplikace LUIS |
|{APPLICATION_VERSION} | Verze aplikace vyškolené aplikace LUIS |
|{AUTHORING_KEY} | Klíč pro tvorbu účtu LUIS pro publikovanou aplikaci LUIS<br/>Svůj klíč pro vytváření můžete získat ze stránky **uživatelských nastavení** na portálu Luis.  |
|{AZURE_REGION} | Příslušná oblast Azure:<br/><br/>```westus```-Západní USA<br/>```westeurope```-Západní Evropa<br/>```australiaeast```– Austrálie – východ |

Pokud si chcete stáhnout vyškolený balíček, přečtěte si [tady dokumentaci k rozhraní API][download-trained-package]. Po úspěšném stažení je odpověď souborem balíčku LUIS. Uložte soubor do umístění úložiště zadaného pro vstupní připojení kontejneru. 

## <a name="run-the-container-with-docker-run"></a>Spusťte kontejner s`docker run`

Ke spuštění kontejneru použijte příkaz [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) . Příkaz používá následující parametry:

| Zástupný symbol | Hodnota |
|-------------|-------|
|{API_KEY} | Tento klíč se používá ke spuštění kontejneru. Nepoužívejte počáteční klíč. |
|{ENDPOINT_URI} | Hodnota koncového bodu je k dispozici na `Cognitive Services` stránce Přehled Azure Portal. |

Tyto parametry nahraďte vlastními hodnotami v následujícím ukázkovém `docker run` příkazu. Spusťte příkaz v konzole Windows.

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

* V tomto příkladu se používá adresář mimo `C:` jednotku, aby nedocházelo ke konfliktům oprávnění ve Windows. Pokud je potřeba použít konkrétní adresář jako vstupní adresář, budete muset udělit dockeru služby oprávnění. 
* Neměňte pořadí argumentů, pokud neznáte kontejnery Docker.
* Pokud používáte jiný operační systém, použijte pro připojení správnou konzolu, terminál, syntaxi složky a znak pro pokračování řádku pro svůj systém. Tyto příklady předpokládají konzolu Windows se znakem `^`pro pokračování řádku. Vzhledem k tomu, že kontejner je operačním systémem Linux, cílový připojení používá syntaxi složky ve stylu systému Linux.

Tento příkaz:

* Spustí kontejner z image kontejneru LUIS.
* Načte aplikaci LUIS ze vstupního připojení na c:\input, která se nachází na hostiteli kontejneru.
* Přiděluje dvě jádra procesoru a 4 gigabajty (GB) paměti.
* Zpřístupňuje TCP port 5000 a přiděluje pseudo-TTY pro kontejner
* Uloží protokoly kontejnerů a LUIS do výstupního připojení na c:\output, které se nachází na hostiteli kontejneru.
* Po ukončení automaticky odstraní kontejner. Bitová kopie kontejneru je stále k dispozici na hostitelském počítači. 

K [](luis-container-configuration.md#example-docker-run-commands) dispozici jsou `docker run` další příklady příkazu. 

> [!IMPORTANT]
> `Eula`, `Billing`, A `ApiKey` možnosti musí být zadán pro spuštění kontejneru; v opačném případě nebude spuštění kontejneru.  Další informace najdete v tématu [fakturace](#billing).
> Hodnota ApiKey je **klíč** ze stránky klíče a koncové body na portálu Luis a je k dispozici také na stránce klíče prostředků `Cognitive Services` Azure.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>Rozhraní API koncových bodů podporovaná kontejnerem

V kontejneru jsou k dispozici verze rozhraní API v2 i [V3 (Preview)](luis-migration-api-v3.md) . 

## <a name="query-the-containers-prediction-endpoint"></a>Dotazování koncového bodu předpovědi kontejneru

Kontejner poskytuje rozhraní API koncového bodu předpovědi založené na REST. Koncové body pro publikované (pracovní nebo produkční) aplikace mají _jinou_ trasu než koncové body pro školené aplikace. 

Pro rozhraní API kontejneru `https://localhost:5000`použijte hostitele. 

|Typ balíčku|Metoda|Trasa|Parametry dotazu|
|--|--|--|--|
|Publikování|[Získat](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [zveřejnit](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q = {q}<br>& fázování<br>[& timezoneOffset]<br>[& verbose]<br>[protokol &]<br>|
|Trénovaný|Získat, zveřejnit|/luis/v2.0/apps/{appId}/versions/{versionId}?|q = {q}<br>[& timezoneOffset]<br>[& verbose]<br>[protokol &]|

Parametry dotazu konfigurují jak a co je vráceno v odpovědi na dotaz:

|Parametr dotazu|type|Účel|
|--|--|--|
|`q`|řetězec|Utterance uživatele.|
|`timezoneOffset`|číslo|TimezoneOffset umožňuje [změnit časové pásmo](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) používané předem vytvořenou entitou datetimeV2.|
|`verbose`|boolean|Vrátí všechny záměry a jejich skóre, pokud je nastaveno na hodnotu true. Výchozí hodnota je false, která vrací pouze nejvyšší záměr.|
|`staging`|boolean|Vrátí dotaz z výsledků přípravného prostředí, pokud je nastavena hodnota true. |
|`log`|boolean|Protokoluje dotazy, které se dají použít později pro [aktivní učení](luis-how-to-review-endpoint-utterances.md). Výchozí hodnota je true.|

### <a name="query-published-app"></a>Dotaz na publikovanou aplikaci

Příkladem SLOŽENÉho příkazu pro dotazování kontejneru pro publikovanou aplikaci je:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Chcete-li provést dotazy  do přípravného prostředí, změňte hodnotu parametru řetězce **pracovního** dotazu na hodnotu true: 

`staging=true`

### <a name="query-trained-app"></a>Aplikace s výukou pro dotazy

Příkladem SLOŽENÉho příkazu pro dotazování kontejneru pro školenou aplikaci je: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Název verze má maximálně 10 znaků a obsahuje pouze znaky, které jsou povoleny v adrese URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Import protokolů koncového bodu pro aktivní učení

Pokud je pro kontejner LUIS zadaný výstupní připojení, soubory protokolu dotazu aplikace se uloží do výstupního adresáře, kde {INSTANCE_ID} je ID kontejneru. Protokol dotazu aplikace obsahuje dotaz, odpověď a časová razítka pro každý dotaz předpovědi odeslaný do kontejneru LUIS. 

Následující umístění ukazuje strukturu vnořených adresářů pro soubory protokolu kontejneru.
```
/output/luis/{INSTANCE_ID}/
```
 
Na portálu LUIS vyberte svoji aplikaci a pak vyberte **importovat protokoly koncových bodů** , aby se tyto protokoly nahrály. 

![Importovat soubory protokolu kontejneru pro aktivní učení](./media/luis-container-how-to/upload-endpoint-log-files.png)

Po nahrání protokolu [Zkontrolujte koncový bod](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) projevy na portálu Luis.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Zastavení kontejneru

Chcete-li vypnout kontejner, v prostředí příkazového řádku, kde je kontejner spuštěn, stiskněte **kombinaci kláves CTRL + C**.

## <a name="troubleshooting"></a>Řešení potíží

Pokud spouštíte kontejner s povoleným výstupním [připojením](luis-container-configuration.md#mount-settings) a povolíte protokolování, kontejner generuje soubory protokolu, které jsou užitečné při řešení problémů, ke kterým dochází při spuštění nebo spuštění kontejneru. 

## <a name="billing"></a>Fakturace

Kontejner LUIS odesílá informace o fakturaci do Azure pomocí prostředku _Cognitive Services_ ve vašem účtu Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Další informace o těchto možnostech najdete v tématu [konfigurace kontejnery](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Podporované závislosti pro `latest` kontejner

Nejnovější kontejner vydaný v 2019 Build bude podporovat:

* Kontrola pravopisu Bingu: požadavky na koncový bod předpovědi dotazu `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` s parametry řetězce dotazu. Další informace najdete v [kurzu kontrola pravopisu Bingu v7](luis-tutorial-bing-spellcheck.md) . Pokud se tato funkce použije, kontejner odešle utterance do vašeho prostředku služby Kontrola pravopisu Bingu v7.
* [Nové předem připravené domény](luis-reference-prebuilt-domains.md): tyto domény zaměřené na podnikové sítě zahrnují entity, příklady projevy a vzory. Rozšíří tyto domény pro vlastní použití. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Nepodporované závislosti `latest` pro kontejner

Pokud vaše aplikace LUIS obsahuje nepodporované závislosti, nebudete moct [Exportovat pro kontejner](#export-packaged-app-from-luis) , dokud neodeberete nepodporované funkce. Při pokusu o export do kontejneru nahlásí portál LUIS nepodporované funkce, které je třeba odebrat.

Aplikaci LUIS můžete použít, pokud **neobsahuje žádnou z** následujících závislostí:

Nepodporované konfigurace aplikací|Podrobnosti|
|--|--|
|Nepodporované jazykové verze kontejneru| Holandština (nl-NL)<br>Japonština (ja-JP)<br>Němčina se podporuje jenom s [1.0.2 provádějících tokenizaci](luis-language-support.md#custom-tokenizer-versions).|
|Nepodporované entity pro všechny jazykové verze|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) předem vytvořenou entitu pro všechny jazykové verze|
|Nepodporované entity pro jazykovou verzi anglické (EN-US)|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) předem připravené entity|
|Neprojevení řeči|Externí závislosti nejsou v kontejneru podporovány.|
|Analýza mínění|Externí závislosti nejsou v kontejneru podporovány.|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Souhrn

V tomto článku jste zjistili koncepty a pracovní postup pro stažení, instalaci a spuštění kontejnerů Language Understanding (LUIS). Souhrn:

* Language Understanding (LUIS) poskytuje jeden kontejner pro Linux pro Docker poskytující dotaz koncového bodu předpovědi z projevy.
* Image kontejneru se stáhnou z Microsoft Container Registry (MCR).
* Spuštění imagí kontejnerů v Dockeru.
* Můžete použít REST API k dotazování koncových bodů kontejneru zadáním identifikátoru URI hostitele kontejneru.
* Při vytváření instance kontejneru, je nutné zadat fakturační informace.

> [!IMPORTANT]
> Cognitive Services kontejnery nejsou licencované k používání bez připojení k Azure pro monitorování míry využívání. Zákazníci musí umožňují používání kontejnerů ke komunikaci fakturační údaje ke službě monitorování míry využití po celou dobu. Kontejnery Cognitive Services neodesílají zákaznická data (například obrázek nebo analyzovaný text) společnosti Microsoft.

## <a name="next-steps"></a>Další postup

* Kontrola [konfigurace kontejnery](luis-container-configuration.md) nastavení konfigurace
* Problémy související s LUIS funkcemi najdete v tématu [řešení potíží](troubleshooting.md) .
* Použít více [Cognitive Servicesch kontejnerů](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip
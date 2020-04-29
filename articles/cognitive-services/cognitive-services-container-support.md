---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak můžou kontejnery Docker získat Cognitive Services blíž k vašim datům.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7a38ec47d416027e8ea3fa772ae01e4f6264197a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876813"
---
# <a name="container-support-in-azure-cognitive-services"></a>Podpora kontejnerů v Azure Cognitive Services

Podpora kontejnerů v Azure Cognitive Services umožňuje vývojářům používat stejná bohatá rozhraní API, která jsou k dispozici v Azure, a nabízí flexibilitu v tom, kde nasadit a hostovat služby, které jsou dodávány s [kontejnery Docker](https://www.docker.com/what-container). Podpora kontejnerů je aktuálně k dispozici pro podmnožinu Azure Cognitive Services, včetně částí:

> [!div class="checklist"]
> * [Detektor anomálií][ad-containers]
> * [Computer Vision][cv-containers]
> * [Tvář][fa-containers]
> * [Rozpoznávání formulářů][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Rozhraní API služby Speech][sp-containers]
> * [Analýza textu][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kontejnering je přístup k distribuci softwaru, ve kterém je aplikace nebo služba, včetně jejích závislostí & konfigurace, zabaleny společně jako image kontejneru. Na hostiteli kontejneru se dá nasadit image kontejneru s minimální nebo žádnou úpravou. Kontejnery jsou izolované od ostatních a základního operačního systému s menším objemem než virtuální počítač. Kontejnery lze vytvořit z imagí kontejnerů pro krátkodobé úlohy a odebrat, pokud již nepotřebujete.

Cognitive Services prostředky jsou k dispozici v [Microsoft Azure](https://azure.microsoft.com). Přihlaste se k [Azure Portal](https://portal.azure.com/) , abyste mohli vytvořit a prozkoumat prostředky Azure pro tyto služby.

## <a name="features-and-benefits"></a>Funkce a výhody

- **Neměnná infrastruktura**: umožňuje týmům DevOps využívat konzistentní a spolehlivé sady známých systémových parametrů a může se tak přizpůsobit. Kontejnery poskytují flexibilitu pro Pivot v předvídatelném ekosystému a nepoužívejte přitom posun konfigurace.
- **Kontrola nad daty**: umožňuje zákazníkům zvolit, kde Cognitive Services zpracovávat jejich data. To je nezbytné pro zákazníky, kteří nemůžou posílat data do cloudu, ale potřebují přístup k Cognitive Services technologii. Podpora konzistence v hybridních prostředích – napříč daty, správou, identitou a zabezpečením.
- **Řízení aktualizací modelů**: Poskytněte zákazníkům flexibilitu při vytváření verzí a aktualizaci modelů nasazených ve svých řešeních.
- **Přenosná architektura**: umožňuje vytvořit přenosnou architekturu aplikace, která se dá nasadit v Azure, místně i na hraničních zařízeních. Kontejnery se dají nasadit přímo do [služby Azure Kubernetes](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)nebo do clusteru [Kubernetes](https://kubernetes.io/) nasazeného do [Azure Stack](/azure-stack/operator). Další informace najdete v tématu [nasazení Kubernetes do Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Vysoká propustnost/nízká latence**: Poskytněte zákazníkům možnost škálování pro zajištění vysoké propustnosti a nízké latence tím, že umožníte, aby Cognitive Services běžet fyzicky blízko jejich aplikační logiky a dat. Kontejnery nemění počet transakcí za sekundu (TPS) a dají se škálovat na maximum i na kapacitu, pokud zadáte potřebné hardwarové prostředky.
- **Škálovatelnost**: s neustále rostoucím oblíbenkou pro kontejnery a software orchestrace kontejnerů, jako je Kubernetes; škálovatelnost je k dispozici na Forefrontu technologických předběžných. Sestavování s škálovatelnou Clusterovou nadací aplikace pro vývoj aplikací pro vysokou dostupnost.

## <a name="containers-in-azure-cognitive-services"></a>Kontejnery v Azure Cognitive Services

Kontejnery Azure Cognitive Services poskytují následující sadu kontejnerů Docker, z nichž každá obsahuje podmnožinu funkcí ze služeb v Azure Cognitive Services:

| Služba | Podporovaná cenová úroveň | Kontejner | Popis |
|---------|----------|----------|-------------|
|[Detektor anomálií][ad-containers] |F0, S0|**Anomálie – detektor** |Rozhraní API pro detekci anomálií umožňuje monitorovat a zjišťovat anomálie v datech časových řad pomocí strojového učení.<br>[Vyžádání přístup](https://aka.ms/adcontainer)|
|[Computer Vision][cv-containers] |F0, S1|**Čtení** |Extrahuje vytištěný text z obrázků různých objektů s různými povrchy a pozadími, jako jsou například příjmy, plakáty a vizitky. Kontejner pro čtení také detekuje *ručně psaný text* v obrázcích a poskytuje podporu PDF/TIFF/vícestránkového textu.<br/><br/>**Důležité informace:** Kontejner pro čtení aktuálně funguje pouze v angličtině.|
|[Tvář][fa-containers] |F0, S0|**Tvář** |Detekuje lidské obličeje na obrázcích a identifikuje atributy, včetně orientačních bodů (jako jsou nos a oči), pohlaví, věk a další funkce obličeje v počítači. Kromě detekce může ploška kontrolovat, jestli se dvě plošky ve stejné imagi nebo různých obrázcích shodují s použitím skóre spolehlivosti, nebo porovnávat plošky s databází, abyste viděli, jestli už existuje podobný vzhled nebo identický obličej. Můžete také uspořádat podobné plošky do skupin pomocí sdílených vizuálních vlastností.<br>[Vyžádání přístup](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Nástroj pro rozpoznávání formulářů][fr-containers] |F0, S0|**Rozpoznávání formulářů** |Porozumění formuláře používá technologii strojového učení k identifikaci a extrakci párů klíč-hodnota a tabulek z formulářů.<br>[Vyžádání přístup](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Luis** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Načte vyškolený nebo publikovaný Language Understanding model, označovaný také jako aplikace LUIS, do kontejneru Docker a poskytuje přístup k předpovědi dotazů z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrajte je zpátky na [portál Luis](https://www.luis.ai) , aby se zlepšila přesnost předpovědi aplikace.|
|[Rozhraní API služby Speech][sp-containers-stt] |F0, S0|**Převod řeči na text** |Přepisuje plynulou řeč v reálném čase do textové podoby.|
|[Rozhraní API služby Speech][sp-containers-cstt] |F0, S0|**Custom Speech na text** |Transcribes průběžné rozpoznávání řeči v reálném čase na text pomocí vlastního modelu.|
|[Rozhraní API služby Speech][sp-containers-tts] |F0, S0|**Převod textu na řeč** |Převádí text do přirozeně znějící řeči.|
|[Rozhraní API služby Speech][sp-containers-ctts] |F0, S0|**Vlastní převod textu na řeč** |Převede text na přirozený zvuk řeči pomocí vlastního modelu.|
|[Analýza textu][ta-containers-keyphrase] |F0, S|**Extrakce klíčových frází** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extrahuje klíčové fráze k identifikaci hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál). |
|[Analýza textu][ta-containers-language]|F0, S|**Rozpoznávání jazyka** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Pro až 120 jazyků rozpozná, který jazyk je vstupní text napsán, a nahlaste jeden kód jazyka pro každý dokument odeslaný na žádost. Kód jazyka spárovaný se skóre označuje sílu skóre. |
|[Analýza textu][ta-containers-sentiment]|F0, S|**Analýza mínění** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyzuje nezpracovaný text pro potvrzení o kladné nebo záporné mínění. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení. Modely analýzy jsou předem vyškoleny pomocí rozsáhlého textu a technologií přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](./text-analytics/language-support.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Kromě toho jsou některé kontejnery podporovány v Cognitive Services klíče prostředků [**nabídky vše v jednom**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Můžete vytvořit jeden Cognitive Services prostředek All-in-One a použít stejný fakturační klíč v rámci podporovaných služeb pro následující služby:

* Computer Vision
* Tvář
* LUIS
* Analýza textu

## <a name="container-availability-in-azure-cognitive-services"></a>Dostupnost kontejneru v Azure Cognitive Services

Kontejnery Azure Cognitive Services jsou veřejně dostupné prostřednictvím vašeho předplatného Azure a image kontejnerů Docker se dají získat buď z Microsoft Container Registry nebo z Docker Hub. Pomocí příkazu [Docker Pull](https://docs.docker.com/engine/reference/commandline/pull/) můžete stáhnout image kontejneru z příslušného registru.

> [!IMPORTANT]
> V současné době je nutné dokončit proces registrace pro přístup k následujícím kontejnerům, ve kterých vyplníte a odešlete dotazník s dotazy ohledně vás, vaší společnosti a případu použití, pro který chcete kontejnery implementovat. Po udělení přístupu a zadání přihlašovacích údajů pak můžete načíst image kontejneru z privátního registru kontejneru hostovaného Azure Container Registry.
> * [Detektor anomálií](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Tvář](Face/face-how-to-install-containers.md)
> * [Rozpoznávání formulářů](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Čtení](computer-vision/computer-vision-how-to-install-containers.md)
> * [Převod řeči na text a převod textu na řeč](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete používat kontejnery Cognitive Services Azure, musíte splnit následující předpoklady:

**Modul Docker**: musíte mít místně nainstalovaný modul Docker. Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)a [Windows](https://docs.docker.com/docker-for-windows/). V systému Windows musí být Docker nakonfigurovaný tak, aby podporoval kontejnery Linux. Kontejnery Docker je také možné nasadit přímo do [služby Azure Kubernetes](../aks/index.yml) nebo [Azure Container Instances](../container-instances/index.yml).

Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure.

Seznamte **se s microsoft Container registry a Docker**: měli byste mít základní znalosti konceptů Microsoft Container registry a Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.

Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).

Jednotlivé kontejnery můžou mít své vlastní požadavky, včetně požadavků na přidělení serverů a paměti.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o [receptech kontejnerů](containers/container-reuse-recipe.md) , které můžete použít s Cognitive Services.

Instalace a zkoumání funkcí poskytovaných kontejnery v Azure Cognitive Services:

* [Kontejnery detektoru anomálií][ad-containers]
* [Kontejnery Počítačové zpracování obrazu][cv-containers]
* [Kontejnery obličeje][fa-containers]
* [Kontejnery pro rozpoznávání formulářů][fr-containers]
* [Kontejnery Language Understanding (LUIS)][lu-containers]
* [Kontejnery rozhraní API služby Speech Service][sp-containers]
* [Kontejnery Analýza textu][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
---
title: Použití kontejnerů Azure Cognitive Services v místním prostředí
titleSuffix: Azure Cognitive Services
description: Naučte se používat kontejnery Docker k použití Cognitive Services v místním prostředí.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 12/16/2020
ms.author: aahi
keywords: místní, Docker, kontejner, Kubernetes
ms.openlocfilehash: 007dfe6d67d504286b9546fe0139055b58dc700f
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285599"
---
# <a name="azure-cognitive-services-containers"></a>Kontejnery Azure Cognitive Services

Azure Cognitive Services poskytuje několik [kontejnerů Docker](https://www.docker.com/what-container) , které umožňují používat stejná rozhraní API, která jsou dostupná v Azure, v místním prostředí. Pomocí těchto kontejnerů získáte flexibilitu Cognitive Services blíž k vašim datům v případě dodržování předpisů, zabezpečení nebo jiných provozních důvodů. Podpora kontejnerů je aktuálně k dispozici pro podmnožinu Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kontejnering je přístup k distribuci softwaru, ve kterém je aplikace nebo služba, včetně jejích závislostí & konfigurace, zabaleny společně jako image kontejneru. Na hostiteli kontejneru se dá nasadit image kontejneru s minimální nebo žádnou úpravou. Kontejnery jsou izolované od ostatních a základního operačního systému s menším objemem než virtuální počítač. Kontejnery lze vytvořit z imagí kontejnerů pro krátkodobé úlohy a odebrat, pokud již nepotřebujete.

## <a name="features-and-benefits"></a>Funkce a výhody

- **Neměnná infrastruktura**: umožňuje týmům DevOps využívat konzistentní a spolehlivé sady známých systémových parametrů a může se tak přizpůsobit. Kontejnery poskytují flexibilitu pro Pivot v předvídatelném ekosystému a nepoužívejte přitom posun konfigurace.
- **Kontrola nad daty**: Určete, kam se budou data zpracovávat pomocí Cognitive Services. To může být nezbytné v případě, že nemůžete odesílat data do cloudu, ale potřebujete přístup k rozhraní API služeb Cognitive Services. Podpora konzistence v hybridních prostředích – napříč daty, správou, identitou a zabezpečením.
- **Řízení aktualizací modelů**: flexibilita při vytváření verzí a aktualizaci modelů nasazených ve svých řešeních.
- **Přenosná architektura**: umožňuje vytvořit přenosnou architekturu aplikace, která se dá nasadit v Azure, místně i na hraničních zařízeních. Kontejnery se dají nasadit přímo do [služby Azure Kubernetes](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)nebo do clusteru [Kubernetes](https://kubernetes.io/) nasazeného do [Azure Stack](/azure-stack/operator). Další informace najdete v tématu [nasazení Kubernetes do Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Vysoká propustnost/nízká latence**: Poskytněte zákazníkům možnost škálování pro zajištění vysoké propustnosti a nízké latence tím, že umožníte, aby Cognitive Services běžet fyzicky blízko jejich aplikační logiky a dat. Kontejnery nemění počet transakcí za sekundu (TPS) a dají se škálovat na maximum i na kapacitu, pokud zadáte potřebné hardwarové prostředky.
- **Škálovatelnost**: s neustále rostoucím oblíbenkou pro kontejnery a software orchestrace kontejnerů, jako je Kubernetes; škálovatelnost je k dispozici na Forefrontu technologických předběžných. Sestavování s škálovatelnou Clusterovou nadací aplikace pro vývoj aplikací pro vysokou dostupnost.

## <a name="containers-in-azure-cognitive-services"></a>Kontejnery v Azure Cognitive Services

Kontejnery Azure Cognitive Services poskytují následující sadu kontejnerů Docker, z nichž každá obsahuje podmnožinu funkcí ze služeb ve službě Azure Cognitive Services. Pokyny a umístění imagí najdete v následujících tabulkách. K dispozici je také seznam [imagí kontejneru](containers/container-image-tags.md) .

### <a name="decision-containers"></a>Kontejnery pro rozhodování

| Služba |  Kontejner | Description | Dostupnost |
|--|--|--|--|
| [Detektor anomálií][ad-containers] | **Detektor anomálií** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Rozhraní API pro detekci anomálií umožňuje monitorovat a zjišťovat anomálie v datech časových řad pomocí strojového učení. | Obecná dostupnost |

### <a name="language-containers"></a>Jazykové kontejnery

| Služba |  Kontejner | Description | Dostupnost |
|--|--|--|--|
| [LUIS][lu-containers] |  **Luis** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Načte vyškolený nebo publikovaný Language Understanding model, označovaný také jako aplikace LUIS, do kontejneru Docker a poskytuje přístup k předpovědi dotazů z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrajte je zpátky na [portál Luis](https://www.luis.ai) , aby se zlepšila přesnost předpovědi aplikace. | Obecná dostupnost |
| [Analýza textu][ta-containers-keyphrase] | **Extrakce klíčových frází** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extrahuje klíčové fráze k identifikaci hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál). | Preview |
| [Analýza textu][ta-containers-language] |  **Textový rozpoznávání jazyka** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Pro až 120 jazyků rozpozná, který jazyk je vstupní text napsán, a nahlaste jeden kód jazyka pro každý dokument odeslaný na žádost. Kód jazyka spárovaný se skóre označuje sílu skóre. | Preview |
| [Analýza textu][ta-containers-sentiment] | **Analýza mínění V3** ([Obrázek](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analyzuje nezpracovaný text pro potvrzení o kladné nebo záporné mínění. Tato verze analýzy mínění vrací popisky mínění (například *kladné* nebo *záporné*) pro každý dokument a větu v něm. |  Obecná dostupnost |
| [Analýza textu][ta-containers-health] |  **Analýza textu pro zdravotnictví** | Extrakce a označení lékařských informací z nestrukturovaného klinického textu. | Gated Preview. [Požádat o přístup][request-access] |

### <a name="speech-containers"></a>Kontejnery služby Speech

> [!NOTE]
> Chcete-li používat kontejnery řeči, budete muset vyplnit [formulář žádosti online](https://aka.ms/csgate).

| Služba |  Kontejner | Description | Dostupnost |
|--|--|--|
| [Rozhraní API služby Speech][sp-containers-stt] |  **Převod řeči na text** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Přepisuje plynulou řeč v reálném čase do textové podoby. | Obecná dostupnost |
| [Rozhraní API služby Speech][sp-containers-cstt] | **Custom Speech k textu** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcribes průběžné rozpoznávání řeči v reálném čase na text pomocí vlastního modelu. | Obecná dostupnost |
| [Rozhraní API služby Speech][sp-containers-tts] | **Převod textu na řeč** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Převádí text do přirozeně znějící řeči. | Obecná dostupnost |
| [Rozhraní API služby Speech][sp-containers-ctts] | **Vlastní převod textu na řeč** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Převede text na přirozený zvuk řeči pomocí vlastního modelu. | Gated Preview |
| [Rozhraní API služby Speech][sp-containers-ntts] | **Neuronové převodu textu na řeč** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Převede text na přirozený zvuk hlasu pomocí vysoce neuronové síťové technologie a umožní vám tak více přirozeného řeči. | Obecná dostupnost |
| [Rozhraní API služby Speech][sp-containers-lid] | **Rozpoznávání jazyka řeči** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Určuje jazyk mluveného zvuku. | Gated Preview |

### <a name="vision-containers"></a>Kontejnery zpracování obrazu

> [!WARNING]
> 11. června 2020 Microsoft oznámil, že nebude prodávat technologii rozpoznávání tváří policejním oddělením v USA, dokud nebude zavedena přísná regulace založená na lidských právech. Zákazníci tak nebudou moci používat funkce rozpoznávání obličeje nebo funkce, které jsou součástí služeb Azure, jako je například obličej nebo Video Indexer, pokud je zákazník nebo je povolen používání těchto služeb v rámci nebo pro, policejní oddělení v USA.

| Služba |  Kontejner | Description | Dostupnost |
|--|--|--|--|
| [Počítačové zpracování obrazu][cv-containers] | **Číst optické rozpoznávání znaků** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Kontejner OCR pro čtení umožňuje extrahovat vytištěný a rukou psaný text z obrázků a dokumentů s podporou formátů souborů JPEG, PNG, BMP, PDF a TIFF. Další informace najdete v [dokumentaci k rozhraní API pro čtení](./computer-vision/overview-ocr.md). | Gated Preview. [Požádat o přístup][request-access] |
| [Prostorová analýza][spa-containers] | **Prostorová analýza** ([Obrázek](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analyzuje video streamování v reálném čase, které vám pomůže pochopit prostorové vztahy mezi lidmi, jejich pohybem a interakcemi s objekty ve fyzických prostředích. | Gated Preview. [Požádat o přístup][request-access] |
| [Rozpoznávání tváře][fa-containers] | **Rozpoznávání tváře** | Detekuje lidské obličeje na obrázcích a identifikuje atributy, včetně orientačních bodů (jako jsou nos a oči), pohlaví, věk a další funkce obličeje v počítači. Kromě detekce může ploška kontrolovat, jestli se dvě plošky ve stejné imagi nebo různých obrázcích shodují s použitím skóre spolehlivosti, nebo porovnávat plošky s databází, abyste viděli, jestli už existuje podobný vzhled nebo identický obličej. Můžete také uspořádat podobné plošky do skupin pomocí sdílených vizuálních vlastností. | Neaktivní |
| [Nástroj pro rozpoznávání formulářů][fr-containers] | **Rozpoznávání formulářů** | Porozumění formuláře používá technologii strojového učení k identifikaci a extrakci párů klíč-hodnota a tabulek z formulářů. | Neaktivní | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Kromě toho jsou v Cognitive Services nabídce [více služeb](cognitive-services-apis-create-account.md) podporovány některé kontejnery. Můžete vytvořit jeden Cognitive Services prostředek All-in-One a použít stejný fakturační klíč v rámci podporovaných služeb pro následující služby:

* Počítačové zpracování obrazu
* Rozpoznávání tváře
* LUIS
* Analýza textu

## <a name="prerequisites"></a>Požadavky

Než začnete používat kontejnery Cognitive Services Azure, musíte splnit následující předpoklady:

**Modul Docker**: musíte mít místně nainstalovaný modul Docker. Docker poskytuje balíčky, které konfigurují prostředí Docker v systémech [MacOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)a [Windows](https://docs.docker.com/docker-for-windows/). V systému Windows musí být Docker nakonfigurovaný tak, aby podporoval kontejnery Linux. Kontejnery Docker je také možné nasadit přímo do [služby Azure Kubernetes](../aks/index.yml) nebo [Azure Container Instances](../container-instances/index.yml).

Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojit se a odeslat fakturační data do Azure.

Seznamte **se s microsoft Container registry a Docker**: měli byste mít základní znalosti konceptů Microsoft Container registry a Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a taky znalosti základních `docker` příkazů.

Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).

Jednotlivé kontejnery můžou mít své vlastní požadavky, včetně požadavků na přidělení serverů a paměti.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Ukázky pro vývojáře

Ukázky pro vývojáře jsou k dispozici v našem [úložišti GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

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
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u
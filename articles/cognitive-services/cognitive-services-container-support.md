---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak získat Cognitive Services blíž ke svým datům kontejnery Dockeru.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 8/21/2019
ms.author: dapine
ms.openlocfilehash: 88d140c4890492b6a53c2e73328ab814293550e6
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994935"
---
# <a name="container-support-in-azure-cognitive-services"></a>Podpora kontejnerů ve službě Azure Cognitive Services

Podpora kontejnerů ve službě Azure Cognitive Services umožňuje vývojářům používat stejná bohatá rozhraní API, které jsou dostupné v Azure a umožňuje flexibilitu při nasazení a hostování služeb, které jsou součástí [kontejnery Dockeru](https://www.docker.com/what-container). Podpora kontejnerů je v současnosti k dispozici ve verzi Preview pro podmnožinu Cognitive Services Azure, včetně částí:

* [Detektor anomálií][ad-containers]
* [Počítačové zpracování obrazu][cv-containers]
* [Rozpoznávání tváře][fa-containers]
* [Nástroj pro rozpoznávání formulářů][fr-containers]
* [Language Understanding (LUIS)][lu-containers]
* [Rozhraní API služby Speech][sp-containers]
* [Analýza textu][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kontejnerizace je přístup k distribuci softwaru, ve kterém k aplikaci nebo službě, včetně konfigurace a jeho závislostí je oprav jako image kontejneru. S téměř nebo vůbec žádné změny je možné nasadit image kontejneru na hostiteli. Kontejnery jsou izolované od sebe navzájem a základní operační systém s menší nároky na místo než virtuální počítač. Kontejnery můžete vytvořené z imagí kontejneru pro krátkodobé úlohy a odebrat, pokud už je nepotřebujete.

Cognitive Services prostředky jsou k dispozici v [Microsoft Azure](https://azure.microsoft.com). Přihlaste se [webu Azure portal](https://portal.azure.com/) k vytvoření a prozkoumáte prostředky Azure pro tyto služby.

## <a name="features-and-benefits"></a>Funkce a výhody

- **Kontrola nad daty**: Umožněte zákazníkům, aby si zvolili, kde Cognitive Services zpracovávat jejich data. To je nezbytné pro zákazníky, které nelze odesílat data do cloudu, ale přístup k technologie služeb Cognitive Services. Podpora konzistence v hybridních prostředích – napříč daty, správou, identitou a zabezpečením.
- **Kontrola aktualizací modelů**: Poskytněte zákazníkům flexibilitu při vytváření verzí a aktualizaci modelů nasazených ve svých řešeních.
- **Přenosná architektura**: Povolte vytvoření přenositelné architektury aplikace, kterou je možné nasadit v Azure, místně i na hraničních zařízeních. Kontejnery, které je možné nasadit přímo do [Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml), nebo [Kubernetes](https://kubernetes.io/) clusteru nasadí do [Azure Zásobník](/azure-stack/operator). Další informace najdete v tématu [nasazení Kubernetes pro Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Vysoká propustnost/nízká latence**: Poskytněte zákazníkům možnost škálování na vysokou propustnost a nízkou latenci tím, že umožníte Cognitive Services běžet fyzicky blízko jejich aplikační logiky a dat. Kontejnery není limit transakcí za sekundu (TPS) a můžete provést škálování současně i horizontální navyšování kapacity pro zpracování poptávky, pokud zadáte nezbytné hardwarové prostředky. 

## <a name="containers-in-azure-cognitive-services"></a>Kontejnery v Azure Cognitive Services

Kontejnery služby Azure Cognitive Services nabízejí následující sadu kontejnerů Dockeru, z nichž každý obsahuje podmnožinu funkcí ze služeb Azure Cognitive Services:

| Služba | Podporovaná cenová úroveň | Kontejner | Popis |
|---------|----------|----------|-------------|
|[Detektor anomálií][ad-containers] |F0, S0|**Anomálie – detektor** |Rozhraní API pro detekci anomálií umožňuje monitorovat a zjišťovat anomálie v datech časových řad pomocí strojového učení.<br>[Žádost o přístup](https://aka.ms/adcontainer)|
|[Počítačové zpracování obrazu][cv-containers] |F0, S1|**Rozpoznání textu** |Extrahuje tištěný text z obrázků různé objekty s různými povrchy a pozadími, jako je potvrzení a plakáty nebo vizitky.<br/><br/>**Důležité:** Kontejner rozpoznat Text v současné době používá pouze angličtinu.<br>[Žádost o přístup](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Rozpoznávání tváře][fa-containers] |F0, S0|**Rozpoznávání tváře** |Zjistí lidských tváří na obrázcích a Určuje atributy, včetně orientačních bodů pro rozpoznávání tváře (například ústa a oči), pohlaví, věk a další funkce rozpoznávání obličeje předpovědět počítače. Kromě zjišťování můžete pro rozpoznávání tváře zkontrolujte, jestli dvě tváře na stejnou bitovou kopii nebo jinou Image jsou stejné s použitím skóre spolehlivosti nebo porovnání proti databázi a zjistěte, jestli podobně vypadajících tváří, nebo identické pro rozpoznávání tváře již existuje. Můžete také uspořádat podobných tváří do skupin pomocí sdílené visual vlastností.<br>[Žádost o přístup](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Nástroj pro rozpoznávání formulářů][fr-containers] |F0, S0|**Nástroj pro rozpoznávání formulářů** |Porozumění formuláře používá technologii strojového učení k identifikaci a extrakci párů klíč-hodnota a tabulek z formulářů.<br>[Žádost o přístup](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**Služba LUIS** ([image](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Načte trénovaného nebo publikované Language Understanding modelu, označované také jako aplikace LUIS, do kontejneru dockeru a poskytuje přístup k předpovědi dotazu z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrání tyto zpět a [LUIS portál](https://www.luis.ai) zvyšte přesnost předpovědi aplikace.|
|[Rozhraní API služby Speech][sp-containers] |F0, S0|**Převod řeči na text** |Přepisuje plynulou řeč v reálném čase do textové podoby.<br>[Žádost o přístup](https://aka.ms/speechcontainerspreview/)|
|[Rozhraní API služby Speech][sp-containers] |F0, S0|**Převod textu na řeč** |Převádí text do přirozeně znějící řeči.<br>[Žádost o přístup](https://aka.ms/speechcontainerspreview/)|
|[Analýza textu][ta-containers] |F0, S|**Extrakce frází klíč** ([image](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extrahuje klíčových frází pro identifikaci hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál). |
|[Analýza textu][ta-containers]|F0, S|**Rozpoznávání jazyka** ([image](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Až 120 jazyků zjišťuje, jaké vstupní text je napsaný v jazyce a sestavy jeden jazyk kódu pro každý dokument podání žádosti. Kód jazyka spárovaný se skóre označuje sílu skóre. |
|[Analýza textu][ta-containers]|F0, S|**Analýza subjektivního hodnocení** ([image](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyzuje nezpracovaný text pro příčiny o pozitivní nebo negativní zabarvení. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení. Modely analýzu jsou předem trénuje pomocí rozsáhlé tělo technologií text a přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](./text-analytics/language-support.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Kromě toho jsou některé kontejnery podporovány v Cognitive Services klíče prostředků [**nabídky vše v jednom**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) . Můžete vytvořit jeden Cognitive Services prostředek All-in-One a použít stejný fakturační klíč v rámci podporovaných služeb pro následující služby:

* Počítačové zpracování obrazu
* Tvář
* LUIS
* Analýza textu

## <a name="container-availability-in-azure-cognitive-services"></a>Dostupnost kontejneru ve službě Azure Cognitive Services

Kontejnery služby Azure Cognitive Services jsou veřejně dostupné prostřednictvím předplatného Azure a imagí kontejnerů Dockeru můžete načíst z Microsoft Container Registry nebo Docker Hubu. Můžete použít [operace docker pull](https://docs.docker.com/engine/reference/commandline/pull/) příkaz Stáhnout image kontejneru z registru odpovídající.

> [!IMPORTANT]
> V současné době je nutné dokončit proces registrace pro přístup k následujícím kontejnerům, ve kterých vyplníte a odešlete dotazník s dotazy ohledně vás, vaší společnosti a případu použití, pro který chcete kontejnery implementovat. Po udělení přístupu a zadání přihlašovacích údajů pak můžete načíst image kontejneru z privátního registru kontejneru hostovaného Azure Container Registry.
> * [Detektor anomálií](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Rozpoznávání tváře](Face/face-how-to-install-containers.md)
> * [Nástroj pro rozpoznávání formulářů](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Rozpoznání textu](Computer-vision/computer-vision-how-to-install-containers.md)
> * [Převod řeči na text a převod textu na řeč](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů Azure Cognitive Services, musí splňovat následující požadavky:

**Modul Docker**: Musíte mít místně nainstalovaný modul Docker. Docker nabízí balíčky, které nakonfigurují prostředí Dockeru na [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), a [Windows](https://docs.docker.com/docker-for-windows/). Na Windows Docker musí být nakonfigurované pro podporu kontejnerů Linuxu. Kontejnery dockeru je také možné nasadit přímo do [Azure Kubernetes Service](../aks/index.yml) nebo [Azure Container Instances](../container-instances/index.yml).

Docker je třeba nastavit umožňující kontejnery a spojte se s odesílat fakturačních dat do Azure.

Seznamte **se s Microsoft Container registry a Docker**: Měli byste mít základní porozumění konceptům Microsoft Container Registry i Docker, jako jsou registry, úložiště, kontejnery a image kontejnerů, a také znalosti základních `docker` příkazů.

Základy Dockeru a kontejnerech základní informace o najdete v článku [přehled Dockeru](https://docs.docker.com/engine/docker-overview/).

Jednotlivých kontejnerů může mít vlastní požadavky, včetně serveru a požadavky na přidělení paměti.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si o [receptech kontejnerů](containers/container-reuse-recipe.md) , které můžete použít s Cognitive Services.

Instalace a prozkoumejte funkce poskytované službou kontejnerů ve službě Azure Cognitive Services:

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
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
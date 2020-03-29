---
title: Podpora kontejnerů
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak můžou kontejnery Dockeru přibližovat služby Cognitive Services vašim datům.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: d75962b98543991a065f6b165279215614175925
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219409"
---
# <a name="container-support-in-azure-cognitive-services"></a>Podpora kontejnerů ve službách Azure Cognitive Services

Podpora kontejnerů ve službě Azure Cognitive Services umožňuje vývojářům používat stejná bohatá rozhraní API, která jsou dostupná v Azure, a umožňuje flexibilitu v tom, kam nasadit a hostovat služby doporučované s [kontejnery Dockeru](https://www.docker.com/what-container). Podpora kontejnerů je momentálně dostupná pro podmnožinu služeb Azure Cognitive Services, včetně částí:

> [!div class="checklist"]
> * [Detektor anomálií][ad-containers]
> * [Počítačové vidění][cv-containers]
> * [Tvář][fa-containers]
> * [Rozpoznávání formulářů][fr-containers]
> * [Language Understanding (LUIS)][lu-containers]
> * [Rozhraní API služby Speech][sp-containers]
> * [Analýza textu][ta-containers]

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Kontejnerizace je přístup k distribuci softwaru, ve kterém je aplikace nebo služba, včetně jejích závislostí & konfigurace, zabalena společně jako image kontejneru. S malou nebo žádnou úpravou image kontejneru lze nasadit na hostitele kontejneru. Kontejnery jsou izolované od sebe navzájem a základní operační systém, s menší nároky než virtuální počítač. Kontejnery lze vytvořit instance z iobrazek kontejnerů pro krátkodobé úlohy a odebrat, když už není potřeba.

Prostředky služeb Cognitive Services jsou k dispozici v [Microsoft Azure](https://azure.microsoft.com). Přihlaste se k [portálu Azure](https://portal.azure.com/) a vytvořte a prozkoumejte prostředky Azure pro tyto služby.

## <a name="features-and-benefits"></a>Funkce a výhody

- **Neměnná infrastruktura**: Umožněte týmům DevOps využít konzistentní a spolehlivou sadu známých systémových parametrů a zároveň se přizpůsobit změnám. Kontejnery poskytují flexibilitu pro otáčení v rámci předvídatelného ekosystému a vyhnout se posunu konfigurace.
- **Kontrola nad daty**: Umožněte zákazníkům vybrat si, kde tyto služby Cognitive Services svá data zpracovávají. To je nezbytné pro zákazníky, kteří nemohou odesílat data do cloudu, ale potřebují přístup k technologii služeb Cognitive Services. Podpora konzistence v hybridních prostředích – napříč daty, správou, identitou a zabezpečením.
- **Kontrola nad aktualizacemi modelu**: Poskytněte zákazníkům flexibilitu při správu verzí a aktualizaci modelů nasazených v jejich řešeních.
- **Přenosná architektura**: Povolit vytvoření architektury přenosných aplikací, kterou lze nasadit v Azure, místně a hraniční. Kontejnery se dá nasadit přímo do [služby Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)nebo do clusteru [Kubernetes](https://kubernetes.io/) nasazeného do [Azure Stacku](/azure-stack/operator). Další informace najdete [v tématu Nasazení Kubernetes do Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Vysoká propustnost / nízká latence**: Poskytněte zákazníkům možnost škálovat pro vysoké požadavky na propustnost a nízkou latenci tím, že umožníte službám Cognitive Services fyzicky spouštět v blízkosti jejich aplikační logiky a dat. Kontejnery nezavršují transakce za sekundu (TPS) a lze je škálovat, aby bylo možné škálovat, aby bylo možné zpracovat poptávku, pokud poskytnete potřebné hardwarové prostředky.
- **Škálovatelnost**: Se stále rostoucí popularitou kontejnerizace a kontejnerové orchestrace software, jako je Například Kubernetes; škálovatelnost je v popředí technologického pokroku. Vývoj aplikací na základě škálovatelného clusteru zajišťuje vysokou dostupnost.

## <a name="containers-in-azure-cognitive-services"></a>Kontejnery ve službách Azure Cognitive Services

Kontejnery Azure Cognitive Services poskytují následující sadu kontejnerů Dockeru, z nichž každá obsahuje podmnožinu funkcí ze služeb ve službách Azure Cognitive Services:

| Služba | Podporovaná cenová úroveň | Kontejner | Popis |
|---------|----------|----------|-------------|
|[Detektor anomálií][ad-containers] |F0, S0|**Detektor anomálií** |Rozhraní API detektoru anomálií umožňuje sledovat a detekovat abnormality v datech časových řad pomocí strojového učení.<br>[Vyžádání přístup](https://aka.ms/adcontainer)|
|[Počítačové vidění][cv-containers] |F0, S1|**Čtení** |Extrahuje vytištěný text z obrázků různých objektů s různými povrchy a pozadími, jako jsou účtenky, plakáty a vizitky. Kontejner Pro čtení také detekuje *ručně psaný text* v obrazech a poskytuje podporu PDF/TIFF/více stránek.<br/><br/>**Důležité:** Read kontejner aktuálně funguje pouze s angličtinou.|
|[Tvář][fa-containers] |F0, S0|**Tvář** |Detekuje lidské tváře v obrazech a identifikuje atributy, včetně orientačních bodů obličeje (například nosů a očí), pohlaví, věku a dalších strojově předpovídaných obličejových rysů. Kromě detekce může plocha zkontrolovat, zda jsou dvě plochy ve stejném obrázku nebo v různých obrázcích stejné pomocí skóre spolehlivosti, nebo porovnat plochy s databází, abyste zjistili, zda již existuje podobně vypadající nebo identická plocha. Může také uspořádat podobné tváře do skupin pomocí sdílených vizuálních vlastností.<br>[Vyžádání přístup](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Nástroj pro rozpoznávání formulářů][fr-containers] |F0, S0|**Rozpoznávání formulářů** |Form Understanding používá technologii strojového učení k identifikaci a extrahování párů klíč-hodnota a tabulek z formulářů.<br>[Vyžádání přístup](https://aka.ms/FormRecognizerContainerRequestAccess)|
|[LUIS][lu-containers] |F0, S0|**LUIS** ([obrázek](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409))|Načte trénovaný nebo publikovaný model language understanding, označovaný také jako aplikace LUIS, do kontejneru dockeru a poskytuje přístup k předpovědím dotazů z koncových bodů rozhraní API kontejneru. Můžete shromažďovat protokoly dotazů z kontejneru a nahrát je zpět na [portál LUIS](https://www.luis.ai) ke zlepšení přesnosti předpovědi aplikace.|
|[Rozhraní API služby Speech][sp-containers-stt] |F0, S0|**Převod řeči na text** |Přepisuje plynulou řeč v reálném čase do textové podoby.|
|[Rozhraní API služby Speech][sp-containers-cstt] |F0, S0|**Vlastní převod řeči na text** |Přepisuje souvislou řeč v reálném čase do textu pomocí vlastního modelu.|
|[Rozhraní API služby Speech][sp-containers-tts] |F0, S0|**Převod textu na řeč** |Převádí text do přirozeně znějící řeči.|
|[Rozhraní API služby Speech][sp-containers-ctts] |F0, S0|**Vlastní převod textu na řeč** |Převede text na přirozeně znějící řeč pomocí vlastního modelu.|
|[Analýza textu][ta-containers-keyphrase] |F0, S|**Extrakce klíčových frází** ([obrázek](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) |Extrahuje klíčové fráze k identifikaci hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál). |
|[Analýza textu][ta-containers-language]|F0, S|**Detekce jazyka** ([obrázek](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) |Až pro 120 jazyků zjistí, ve kterém jazyce je vstupní text napsán, a nahlásí jeden kód jazyka pro každý dokument odeslaný na žádost. Kód jazyka spárovaný se skóre označuje sílu skóre. |
|[Analýza textu][ta-containers-sentiment]|F0, S|**Analýza mínění** ([obrázek](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) |Analyzuje nezpracovaný text pro stopy o pozitivním nebo negativním sentimentu. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení. Modely analýzy jsou předem trénované pomocí rozsáhlého textu a technologií přirozeného jazyka od společnosti Microsoft. Pro [vybrané jazyky](./text-analytics/language-support.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace. |

<!--
|[Personalizer](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Kromě toho některé kontejnery jsou podporovány v kognitivní služby [**All-In-One nabízí**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) klíče prostředků. Můžete vytvořit jeden prostředek služby Cognitive Services All-In-One a použít stejný fakturační klíč napříč podporovanými službami pro následující služby:

* Počítačové zpracování obrazu
* Tvář
* LUIS
* Analýza textu

## <a name="container-availability-in-azure-cognitive-services"></a>Dostupnost kontejnerů ve službách Azure Cognitive Services

Kontejnery Azure Cognitive Services jsou veřejně dostupné prostřednictvím vašeho předplatného Azure a image kontejnerů Dockeru se můžou vyžádat z registru Microsoft Container nebo Docker Hubu. Příkaz [vyžádat docker](https://docs.docker.com/engine/reference/commandline/pull/) můžete stáhnout bitovou kopii kontejneru z příslušného registru.

> [!IMPORTANT]
> V současné době je nutné dokončit proces registrace pro přístup k následujícím kontejnerům, ve kterých vyplňujete a odešlete dotazník s dotazy týkajícími se vás, vaší společnosti a případu použití, pro který chcete kontejnery implementovat. Jakmile se vám bude udělit přístup a zadáte přihlašovací údaje, můžete vyžádat ibi kontejnerů z registru privátního kontejneru hostovaného registrem kontejnerů Azure.
> * [Detektor anomálií](Anomaly-Detector/anomaly-detector-container-howto.md#request-access-to-the-container-registry)
> * [Tvář](Face/face-how-to-install-containers.md)
> * [Rozpoznávání formulářů](form-recognizer/form-recognizer-container-howto.md#request-access-to-the-container-registry)
> * [Čtení](computer-vision/computer-vision-how-to-install-containers.md)
> * [Převod řeči na text a převod textu na řeč](Speech-Service/speech-container-howto.md#request-access-to-the-container-registry)

[!INCLUDE [Container repositories and images](containers/includes/cognitive-services-container-images.md)]

## <a name="prerequisites"></a>Požadavky

Před použitím kontejnerů Azure Cognitive Services musíte splňovat následující požadavky:

**Docker Engine**: Musíte mít Docker Engine nainstalován místně. Docker poskytuje balíčky, které konfigurují prostředí Dockeru v [systémech macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms)a [Windows](https://docs.docker.com/docker-for-windows/). V systému Windows musí být Docker nakonfigurován tak, aby podporoval kontejnery Linuxu. Kontejnery Dockeru se taky můžou nasadit přímo do [služby Azure Kubernetes Service](../aks/index.yml) nebo [Azure Container Instance .](../container-instances/index.yml)

Docker musí být nakonfigurovaný tak, aby umožňoval kontejnerům připojení k fakturačním datům a odesílání fakturačních dat do Azure.

**Znalost microsoft container registru a Dockeru**: Měli byste mít základní znalosti o Microsoft Container Registry a Docker koncepty, jako jsou `docker` registry, úložiště, kontejnery a image kontejnerů, stejně jako znalost základních příkazů.

Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview](https://docs.docker.com/engine/docker-overview/) (Přehled Dockeru).

Jednotlivé kontejnery mohou mít také své vlastní požadavky, včetně požadavků na přidělení serveru a paměti.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

[!INCLUDE [Discoverability of more container information](../../includes/cognitive-services-containers-discoverability.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si o [receptech kontejnerů, které](containers/container-reuse-recipe.md) můžete používat se službami Cognitive Services.

Nainstalujte a prozkoumejte funkce poskytované kontejnery ve službách Azure Cognitive Services:

* [Kontejnery na detektory anomálií][ad-containers]
* [Kontejnery pro počítačové vidění][cv-containers]
* [Čelní kontejnery][fa-containers]
* [Kontejnery pro rozpoznávání formulářů][fr-containers]
* [Kontejnery pro porozumění jazykům (LUIS)][lu-containers]
* [Kontejnery rozhraní API služby rozpoznávání řeči][sp-containers]
* [Kontejnery analýzy textu][ta-containers]

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
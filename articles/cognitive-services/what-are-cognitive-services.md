---
title: Co je Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services zpřístupňuje AI všem vývojářům bez nutnosti odborného učení v oblasti strojového učení a dat. Stačí, abyste z vaší aplikace navolali volání rozhraní API, abyste mohli přidat možnost zobrazení (rozšířené vyhledávání a rozpoznávání obrázků), slyšet, mluvit, Hledat a provádět rozhodování v aplikacích.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: vyvnímání služby, rozpoznávání, rozpoznávání, rozpoznávání, služby AI, porozumění rozpoznávání, funkce rozpoznávání
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: d134532e7f285f391b6791a9fc73d5e3251ddac8
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369405"
---
# <a name="what-are-azure-cognitive-services"></a>Co je Azure Cognitive Services?

Azure Cognitive Services jsou cloudové služby s využitím rozhraní REST API a sad SDK klientských knihoven, které vám pomůžou s vytvářením informací o rozpoznávání v aplikacích. Do svých aplikací můžete přidat funkce rozpoznávání, aniž byste museli mít umělou Intelligence (AI) nebo dovednosti v oblasti datových věd. Azure Cognitive Services sestávají z různých služeb AI, které vám umožní vytvářet vnímání řešení, která můžou vidět, slyšet, mluvit, pochopit a dokonce rozhodnout.

## <a name="categories-of-cognitive-services"></a>Kategorie Cognitive Services

Katalog služeb rozpoznávání, které poskytují porozumění rozpoznávání, je rozdělen do pěti hlavních pilířů:

* Obraz
* Řeč
* Jazyk
* Rozhodnutí
* Search

V následujících částech tohoto článku najdete seznam služeb, které jsou součástí těchto pěti pilířů.

## <a name="vision-apis"></a>Rozhraní API pro visioning

|Název služby|Popis služby|
|:-----------|:------------------|
|[Počítačové zpracování obrazu](./computer-vision/index.yml "Computer Vision")|Služba Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům rozpoznávání pro zpracování imagí a vracení informací.|
|[Custom Vision Service](./custom-vision-service/overview.md "Custom Vision Service")|Custom Vision Service umožňuje vytvářet vlastní klasifikátory obrázků.|
|[Rozpoznávání tváře](./face/index.yml "Tvář")| Služba obličeje poskytuje přístup k pokročilým algoritmům obličeje a umožňuje detekci a rozpoznávání atributů obličeje.|
|[Rozpoznávání formulářů](./form-recognizer/index.yml "Rozpoznávání formulářů")|Nástroj pro rozpoznávání formulářů identifikuje a extrahuje páry klíč-hodnota a tabulková data z dokumentů formulářů; pak výstup strukturovaných dat včetně relací v původním souboru.|
|[Rozpoznávání rukopisu](./ink-recognizer/index.yml "Rozpoznávání rukopisu") (vyřazení z provozu)|Nástroj pro rozpoznávání rukopisu umožňuje rozpoznávat a analyzovat data tahů digitálního inkoustu, tvary a Rukopisný obsah a vytvářet výstupy struktury dokumentů se všemi rozpoznanými entitami.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Video Indexer vám umožní extrahovat z videa přehledy.|

## <a name="speech-apis"></a>Rozhraní API pro rozpoznávání řeči

|Název služby|Popis služby|
|:-----------|:------------------|
|[Služba Speech](./speech-service/index.yml "Služba Speech")|Služba Speech přidává do aplikací funkce podporující rozpoznávání řeči. Služba Speech zahrnuje různé možnosti, jako je převod řeči na text, převod textu na řeč, překlad řeči a mnoho dalšího.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Rozhraní API pro jazyk

|Název služby|Popis služby|
|:-----------|:------------------|
|[Language Understanding LUIS](./luis/index.yml "Language Understanding")|Služba Language Understanding (LUIS) umožňuje vaší aplikaci pochopit, co lidé vlastními slovy chtějí.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|QnA Maker vám umožní vytvořit službu otázek a odpovědí z částečně strukturovaného obsahu.|
|[Analýza textu](./text-analytics/index.yml "Analýza textu")| Analýza textu poskytuje zpracování v přirozeném jazyce přes nezpracovaný text pro analýzu míněníí, extrakci klíčových frází a detekci jazyka.|
|[Překladač](./translator/index.yml "Překladač")|Překladatel poskytuje textový překlad založený na počítačích téměř v reálném čase.|
| [Asistivní čtečka](./immersive-reader/index.yml "Asistivní čtečka") | Moderní čtečka přidává do vašich aplikací možnosti čtení z obrazovky a porozumění. |

## <a name="decision-apis"></a>Rozhraní API pro rozhodování

|Název služby|Popis služby|
|:-----------|:------------------|
|[Detektor anomálií](./anomaly-detector/index.yml "Detektor anomálií") |Detekce anomálií umožňuje monitorovat a detekovat anomálie v datech časových řad.|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator poskytuje monitorování možného urážlivého, nežádoucího a rizikového obsahu.|
|[Poradce pro metriky](./metrics-advisor/index.yml) (Preview) | Poradce metrik nabízí přizpůsobitelnou detekci anomálií v datech variate časových řad a plně vybavený webový portál, který vám pomůže tuto službu používat.|
|[Personalizace](./personalizer/index.yml "Personalizace")|Přizpůsobování umožňuje vybrat nejlepší možnosti, které se uživatelům zobrazí, a naučit se jejich chování v reálném čase.|

## <a name="search-apis"></a>Rozhraní API pro vyhledávání

> [!NOTE]
> Hledáte [Azure kognitivní hledání](../search/index.yml)? I když používá Cognitive Services pro některé úkoly, jedná se o jinou technologii vyhledávání, která podporuje jiné scénáře.

|Název služby|Popis služby|
|:-----------|:------------------|
|[Vyhledávání zpráv Bingu](/azure/cognitive-services/bing-news-search/ "Vyhledávání zpráv Bingu")|Vyhledávání zpráv Bingu vrátí seznam článků s příspěvky, které byly pro dotaz uživatele relevantní.|
|[Vyhledávání videí Bingu](/azure/cognitive-services/Bing-Video-Search/ "Vyhledávání videí Bingu")|Vvyhledávání videí Bingu vrátí seznam videí, které byly pro dotaz uživatele důležité.|
|[Vyhledávání na webu Bingu](./bing-web-search/index.yml "Vyhledávání na webu Bingu")|Vyhledávání na webu Bingu vrátí seznam výsledků hledání, které byly pro dotaz uživatele důležité.|
|[Automatické návrhy Bingu](/azure/cognitive-services/Bing-Autosuggest "Automatické návrhy Bingu")|Automatické návrhy Bingu umožňuje odeslat do Bingu výraz částečného vyhledávacího dotazu a vrátit se do seznamu navrhovaných dotazů.|
|[Vlastní vyhledávání Bingu](/azure/cognitive-services/bing-custom-search "Vlastní vyhledávání Bingu")|Vlastní vyhledávání Bingu vám umožní vytvářet prostředí s přizpůsobeným vyhledáváním pro témata, o kterých se zajímáte.|
|[Vyhledávání entit Bingu](/azure/cognitive-services/bing-entities-search/ "Vyhledávání entit Bingu")|Vyhledávání entit Bingu vrátí informace o entitách, které Bing určí pro dotaz uživatele.|
|[Vyhledávání obrázků Bingu](/azure/cognitive-services/bing-image-search "Vyhledávání obrázků Bingu")|Vyhledávání obrázků Bingu vrátí zobrazení obrázků, které byly pro dotaz uživatele důležité.|
|[Vizuální vyhledávání Bingu](/azure/cognitive-services/bing-visual-search "Vizuální vyhledávání Bingu")|Vizuální vyhledávání Bingu poskytuje přehledy o imagi, jako jsou například vizuálně podobné obrázky, nákupy zdrojů pro produkty nalezené v imagi a související hledání.|
|[Vyhledávání místních firem Bingu](/azure/cognitive-services/bing-local-business-search/ "Vyhledávání místních firem Bingu")| Rozhraní API pro místní vyhledávání Bingu umožňuje vašim aplikacím najít kontaktní a umístění informací o místních firmách na základě vyhledávacích dotazů.|
|[Kontrola pravopisu Bingu](/azure/cognitive-services/bing-spell-check/ "Kontrola pravopisu Bingu")|Kontrola pravopisu Bingu umožňuje provádět kontextové gramatiky a kontrolu pravopisu.|

## <a name="development-options"></a>Možnosti vývoje 

S Azure a Cognitive Services máte přístup k několika možnostem vývoje, jako je například:

* Nástroje pro automatizaci a integraci, jako je Logic Apps a automatizace.
* Možnosti nasazení, například Azure Functions a App Service 
* Cognitive Services kontejnery Docker pro zabezpečený přístup.
* Nástroje, jako jsou Apache Spark, Azure Databricks, Azure synapse Analytics a služba Azure Kubernetes pro scénáře s velkými objemy dat. 

Další informace najdete v tématu [možnosti vývoje Cognitive Services](./cognitive-services-development-options.md).

## <a name="learn-with-the-quickstarts"></a>Seznamte se s rychlými starty

Začněte tím, že vytvoříte prostředek Cognitive Services s praktickými rychlými starty pomocí následujících metod:

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "portál Azure")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Klientské knihovny pro sadu Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "rozpoznávání služeb – rozhraní API – vytvoření-účet-klient-knihovna? pivots = program-Language-CSharp")
* [Šablony Azure Resource Manageru (ARM)](./create-account-resource-manager-template.md?tabs=portal "Šablony Azure Resource Manageru (ARM)")

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Bezpečné použití Cognitive Services

Azure Cognitive Services poskytuje vrstvený model zabezpečení, včetně [ověřování](authentication.md "ověřování") prostřednictvím přihlašovacích údajů Azure Active Directory, platného klíče prostředku a [virtuálních sítí Azure](cognitive-services-virtual-networks.md "Virtuální sítě Azure").

## <a name="containers-for-cognitive-services"></a>Kontejnery pro Cognitive Services

 Cognitive Services poskytuje kontejnery pro nasazení v cloudu Azure nebo v místním prostředí. Přečtěte si další informace o [Cognitive Servicesch kontejnerech](cognitive-services-container-support.md "Kontejnery služeb Cognitive Services").

## <a name="regional-availability"></a>Regionální dostupnost

Rozhraní API v Cognitive Services se hostují na rostoucí síti datových center spravovaných Microsoftem. Regionální dostupnost pro každé rozhraní API najdete v [seznamu oblastí Azure](https://azure.microsoft.com/regions "Seznam oblastí Azure").

Hledáte oblast, kterou ještě nepodporujeme? Dejte nám prosím jistotu, že na našem [fóru UserVoice](https://cognitive.uservoice.com/ "Fórum UserVoice")napíšeme žádost o funkci.

## <a name="supported-cultural-languages"></a>Podporované kulturní jazyky

Cognitive Services podporuje široké spektrum kultur jazyků na úrovni služby. Dostupnost jazyka pro každé rozhraní API můžete najít v [seznamu podporovaných jazyků](language-support.md "Seznam podporovaných jazyků").

## <a name="certifications-and-compliance"></a>Certifikace a dodržování předpisů

Cognitive Services bylo uděleno certifikace, jako je třeba certifikace v CSA STAR, FedRAMP střední a HIPAA BAA. Můžete [si stáhnout](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "stáhnout") certifikace pro vlastní audity a bezpečnostní recenze.

Pro pochopení ochrany osobních údajů a správy dat navštivte [Centrum zabezpečení](https://servicetrust.microsoft.com/ "Centrum zabezpečení").

## <a name="support"></a>Podpora

Cognitive Services poskytuje několik možností podpory, které vám pomůžou s tím, jak se můžete pohybovat při vytváření inteligentních aplikací. Cognitive Services má také silnou komunitu vývojářů, kteří vám pomůžou odpovědět na konkrétní otázky. Úplný seznam dostupných možností najdete v tématu [Cognitive Services podpory a možnosti pomoci](cognitive-services-support-options.md "Podpora Cognitive Services a možnosti pomoci").

## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu Cognitive Services](cognitive-services-apis-create-account.md "Vytvoření účtu Cognitive Services")
* [Co je nového v Cognitive Services docs](whats-new-docs.md "Co je nového v Cognitive Services docs")
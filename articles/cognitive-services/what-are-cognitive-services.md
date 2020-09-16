---
title: Co je Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services jsou Cloud Services s rozhraními REST API a sady SDK klientské knihovny, které můžete používat s Microsoft Azure k vytváření inteligentních aplikací.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: vyvnímání služby, rozpoznávání, rozpoznávání, rozpoznávání, služby AI, porozumění rozpoznávání, funkce rozpoznávání
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 08/28/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 6490b3cf2d3813a2740c548f778e65f1df6e7b01
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90603654"
---
# <a name="what-are-azure-cognitive-services"></a>Co je Azure Cognitive Services?

Azure Cognitive Services jsou cloudové služby s rozhraními REST API a sady SDK klientské knihovny, které vývojářům umožňují vytvářet v aplikacích rozpoznávání dat bez nutnosti přímých dovedností (AI) nebo znalostí v oblasti datových věd. Azure Cognitive Services umožňuje vývojářům snadno přidat funkce rozpoznávání do svých aplikací s rozpoznáváním řešení, která mohou vidět, slyšet, mluvit, pochopit a dokonce i začít.

Katalog služeb AI, které poskytují porozumění rozpoznávání, je rozdělen do pěti hlavních pilířů:

* Obraz
* Řeč
* Jazyk
* Vyhledávání na webu
* Rozhodnutí

Aktuální seznam nové dokumentace je k dispozici v části [novinky v Cognitive Services docs](whats-new-docs.md).

## <a name="vision-apis"></a>Rozhraní API pro visioning

|Název služby|Popis služby|
|:-----------|:------------------|
|[Počítačové zpracování obrazu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/ "Počítačové zpracování obrazu")|Služba Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům rozpoznávání pro zpracování imagí a vracení informací.|
|[Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/home "Custom Vision Service")|Custom Vision Service umožňuje vytvářet vlastní klasifikátory obrázků.|
|[Rozpoznávání tváře](https://docs.microsoft.com/azure/cognitive-services/face/ "Tvář")| Služba obličeje poskytuje přístup k pokročilým algoritmům obličeje a umožňuje detekci a rozpoznávání atributů obličeje.|
|[Nástroj pro rozpoznávání formulářů](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/ "Rozpoznávání formulářů") (Preview)|Nástroj pro rozpoznávání formulářů identifikuje a extrahuje páry klíč-hodnota a tabulková data z dokumentů formulářů; pak výstup strukturovaných dat včetně relací v původním souboru.|
|[Rozpoznávání rukopisu](https://docs.microsoft.com/azure/cognitive-services/ink-recognizer/ "Rozpoznávání rukopisu") (vyřazení z provozu)|Nástroj pro rozpoznávání rukopisu umožňuje rozpoznávat a analyzovat data tahů digitálního inkoustu, tvary a Rukopisný obsah a vytvářet výstupy struktury dokumentů se všemi rozpoznanými entitami.|
|[Video Indexer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview "Video Indexer")|Video Indexer vám umožní extrahovat z videa přehledy.|

## <a name="speech-apis"></a>Rozhraní API pro rozpoznávání řeči

|Název služby|Popis služby|
|:-----------|:------------------|
|[Služba řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/ "Služba Speech")|Služba Speech přidává do aplikací funkce podporující rozpoznávání řeči.|
|[Rozhraní API pro rozpoznávání mluvčího](https://docs.microsoft.com/azure/cognitive-services/speaker-recognition/home "Rozhraní API pro rozpoznání mluvčího") (Preview)|Rozhraní API pro rozpoznávání mluvčího poskytuje algoritmy pro identifikaci a ověřování mluvčího.|
|[Zpracování řeči Bingu](https://docs.microsoft.com/azure/cognitive-services/speech/home "Zpracování řeči Bingu") (vyřazení z provozu)|Rozhraní API pro zpracování řeči Bingu poskytuje snadný způsob, jak ve svých aplikacích vytvářet funkce podporující rozpoznávání řeči.|
|[Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/ "Translator Speech") (vyřazení z provozu)|Translator Speech je služba strojového překladu.|

> [!NOTE]
> Hledáte [Azure kognitivní hledání](https://docs.microsoft.com/azure/search/)? I když používá Cognitive Services pro některé úkoly, jedná se o jinou technologii vyhledávání, která podporuje jiné scénáře.


## <a name="language-apis"></a>Rozhraní API pro jazyk

|Název služby|Popis služby|
|:-----------|:------------------|
|[Language Understanding LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/ "Language Understanding")|Služba Language Understanding (LUIS) umožňuje vaší aplikaci pochopit, co osoba chce, podle svých vlastních slov.|
|[QnA Maker](https://docs.microsoft.com/azure/cognitive-services/qnamaker/index "QnA Maker")|QnA Maker vám umožní vytvořit službu otázek a odpovědí z částečně strukturovaného obsahu.|
|[Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/ "Analýza textu")|Analýza textu poskytuje zpracování v přirozeném jazyce přes nezpracovaný text pro analýzu míněníí, extrakci klíčových frází a rozpoznání jazyka.|
|[Translator](https://docs.microsoft.com/azure/cognitive-services/translator/ "Překladač")|Překladatel poskytuje textový překlad založený na počítačích téměř v reálném čase.|


## <a name="search-apis"></a>Rozhraní API pro vyhledávání

|Název služby|Popis služby|
|:-----------|:------------------|
|[Vyhledávání zpráv Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/ "Vyhledávání zpráv Bingu")|Vyhledávání zpráv Bingu vrátí seznam článků s příspěvky, které byly pro dotaz uživatele relevantní.|
|[Vyhledávání videí Bingu](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search/ "Vyhledávání videí Bingu")|Vvyhledávání videí Bingu vrátí seznam videí, které byly pro dotaz uživatele důležité.|
|[Vyhledávání na webu Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/ "Vyhledávání na webu Bingu")|Vyhledávání na webu Bingu vrátí seznam výsledků hledání, které byly pro dotaz uživatele důležité.|
|[Automatické návrhy Bingu](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest "Automatické návrhy Bingu")|Automatické návrhy Bingu umožňuje odeslat do Bingu výraz částečného vyhledávacího dotazu a vrátit se do seznamu navrhovaných dotazů.|
|[Vlastní vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search "Vlastní vyhledávání Bingu")|Vlastní vyhledávání Bingu vám umožní vytvářet prostředí s přizpůsobeným vyhledáváním pro témata, o kterých se zajímáte.|
|[Vyhledávání entit Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/ "Vyhledávání entit Bingu")|Vyhledávání entit Bingu vrátí informace o entitách, které Bing určí pro dotaz uživatele.|
|[Vyhledávání obrázků Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-image-search "Vyhledávání obrázků Bingu")|Vyhledávání obrázků Bingu vrátí zobrazení obrázků, které byly pro dotaz uživatele důležité.|
|[Vizuální vyhledávání Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search "Vizuální vyhledávání Bingu")|Vizuální vyhledávání Bingu poskytuje přehledy o imagi, jako jsou například vizuálně podobné obrázky, nákupy zdrojů pro produkty nalezené v imagi a související hledání.|
|[Vyhledávání místních firem Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-local-business-search/ "Vyhledávání místních firem Bingu")| Rozhraní API pro místní vyhledávání Bingu umožňuje vašim aplikacím najít kontaktní a umístění informací o místních firmách na základě vyhledávacích dotazů.|
|[Kontrola pravopisu Bingu](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/ "Kontrola pravopisu Bingu")|Kontrola pravopisu Bingu umožňuje provádět kontextové gramatiky a kontrolu pravopisu.|

## <a name="decision-apis"></a>Rozhraní API pro rozhodování

|Název služby|Popis služby|
|:-----------|:------------------|
|[Detektor anomálií](https://docs.microsoft.com/azure/cognitive-services/anomaly-detector/ "Detektor anomálií") (Preview)|Detekce anomálií umožňuje monitorovat a detekovat anomálie v datech časových řad.|
|[Content Moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview "Content Moderator")|Content Moderator poskytuje monitorování možného urážlivého, nežádoucího a rizikového obsahu.|
|[Personalizace](https://docs.microsoft.com/azure/cognitive-services/personalizer/ "Personalizace")|Přizpůsobování umožňuje vybrat nejlepší možnosti, které se uživatelům zobrazí, a naučit se jejich chování v reálném čase.|

## <a name="learn-with-the-quickstarts"></a>Seznamte se s rychlými starty

Přečtěte si o vytváření prostředků Cognitive Services s praktickými rychlými starty pomocí:

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "portál Azure")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Klientské knihovny pro sadu Azure SDK](cognitive-services-apis-create-account-cli.md?tabs=windows "rozpoznávání služeb – rozhraní API – vytvoření-účet-klient-knihovna? pivots = program-Language-CSharp")
* [Šablony Azure Resource Manageru (ARM)](resource-manager-template.md?tabs=portal "Šablony Azure Resource Manageru (ARM)")

## <a name="subscription-management"></a>Správa předplatného

Jakmile se přihlásíte pomocí svého účtu Microsoft, můžete získat přístup k [předplatným](https://www.microsoft.com/cognitive-services/subscriptions "Moje předplatné") , abyste viděli používané produkty, zbývající kvótu a možnost přidávat do předplatného další produkty.

## <a name="upgrade-to-unlock-higher-limits"></a>Upgradovat na odemknout vyšší limity

Všechna rozhraní API mají úroveň Free, která má omezení využití a propustnosti.  Tyto limity můžete zvýšit pomocí placené nabídky a výběrem vhodné cenové úrovně při nasazení služby v Azure Portal. [Přečtěte si další informace o nabídkách a cenách](https://azure.microsoft.com/pricing/details/cognitive-services/ "nabídky a ceny"). Budete muset nastavit účet předplatitele Azure pomocí platební karty a telefonního čísla. Pokud máte zvláštní požadavek nebo chcete mluvit jenom na prodej, klikněte na tlačítko "kontaktujte nás" v horní části stránky s cenami.

## <a name="regional-availability"></a>Regionální dostupnost

Rozhraní API v Cognitive Services se hostují na rostoucí síti datových center spravovaných Microsoftem. Regionální dostupnost pro každé rozhraní API najdete v [seznamu oblastí Azure](https://azure.microsoft.com/regions "Seznam oblastí Azure").

Hledáte oblast, kterou ještě nepodporujeme? Dejte nám prosím jistotu, že na našem [fóru UserVoice](https://cognitive.uservoice.com/ "Fórum UserVoice")napíšeme žádost o funkci.

## <a name="supported-cultural-languages"></a>Podporované kulturní jazyky

 Cognitive Services podporuje široké spektrum kultur jazyků na úrovni služby. Dostupnost jazyka pro každé rozhraní API můžete najít v [seznamu podporovaných jazyků](language-support.md "Seznam podporovaných jazyků").

## <a name="securing-resources"></a>Zabezpečení prostředků

Azure Cognitive Services poskytuje vrstvený model zabezpečení, včetně [ověřování](authentication.md "ověřování") prostřednictvím přihlašovacích údajů Azure Active Directory, platného klíče prostředku a [virtuálních sítí Azure](cognitive-services-virtual-networks.md "Virtuální sítě Azure").

## <a name="container-support"></a>Podpora kontejnerů

 Cognitive Services poskytuje kontejnery pro nasazení v cloudu Azure nebo v místním prostředí. Přečtěte si další informace o [Cognitive Servicesch kontejnerech](cognitive-services-container-support.md "Kontejnery služeb Cognitive Services").

## <a name="certifications-and-compliance"></a>Certifikace a dodržování předpisů

Cognitive Services bylo uděleno certifikace, jako je třeba certifikace v CSA STAR, FedRAMP střední a HIPAA BAA.

Můžete [si stáhnout](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "stáhnout") certifikace pro vlastní audity a bezpečnostní recenze.

Pro pochopení ochrany osobních údajů a správy dat navštivte [Centrum zabezpečení](https://servicetrust.microsoft.com/ "Centrum zabezpečení").

## <a name="support"></a>Podpora

Cognitive Services poskytuje několik [možností podpory](cognitive-services-support-options.md "Možnosti podpory").




## <a name="next-steps"></a>Další kroky

* [Vytvoření účtu Cognitive Services](cognitive-services-apis-create-account.md "Vytvoření účtu Cognitive Services")

---
title: Možnosti vývoje v Azure Cognitive Services
description: Naučte se používat Azure Cognitive Services s různými možnostmi vývoje a nasazení, jako jsou klientské knihovny, rozhraní REST API, Logic Apps, Power Automate, Azure Functions, Azure App Service, Azure Databricks a spousta dalších.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 4eaa33778287bfcda45547c24e6abe0606b6baa7
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94368793"
---
# <a name="cognitive-services-development-options"></a>Možnosti vývoje služby Cognitive Services

Tento dokument poskytuje podrobný přehled možností vývoje a nasazení, které vám pomůžou začít s Azure Cognitive Services.

Azure Cognitive Services jsou cloudové služby AI, které vývojářům umožňují vytvářet v aplikacích a produktech inteligentní informace bez obsáhlých znalostí strojového učení. Díky Cognitive Services máte přístup k funkcím AI nebo modelům, které jsou připravené, připravené a aktualizované Microsoftem, aby je bylo možné používat ve svých aplikacích. V mnoha případech máte také možnost přizpůsobit modely pro své obchodní potřeby. 

Cognitive Services jsou uspořádány do čtyř kategorií: rozhodnutí, jazyk, řeč a vize. Obvykle byste k těmto službám měli přístup prostřednictvím rozhraní REST API, klientských knihoven a vlastních nástrojů (například rozhraní příkazového řádku) poskytovaných společností Microsoft. To je ale jenom jedna cesta k úspěchu. Prostřednictvím Azure máte také přístup k několika možnostem vývoje, jako je například:

* Nástroje pro automatizaci a integraci, jako je Logic Apps a automatizace.
* Možnosti nasazení, například Azure Functions a App Service 
* Cognitive Services kontejnery Docker pro zabezpečený přístup.
* Nástroje, jako jsou Apache Spark, Azure Databricks, Azure synapse Analytics a služba Azure Kubernetes pro scénáře s velkými objemy dat. 

Než se pustíme do, je důležité, abyste věděli, že se Cognitive Services primárně používá pro dvě samostatné úlohy. V závislosti na úloze, kterou chcete provést, máte k dispozici různé možnosti vývoje a nasazení. 

* [Vývojové možnosti pro předpověď a analýzu](#development-options-for-prediction-and-analysis)
* [Nástroje pro přizpůsobení a konfiguraci modelů](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Vývojové možnosti pro předpověď a analýzu 

Nástroje, které použijete k přizpůsobení a konfiguraci modelů, se liší od těch, které použijete k volání Cognitive Services. Nejvíce Cognitive Services umožňuje odesílat data a získávat přehledy bez jakýchkoli úprav. Příklad: 

* Do služby Počítačové zpracování obrazu můžete odeslat image, která detekuje slova a fráze nebo spočítá počet osob v rámci rámce.
* Zvukový soubor můžete odeslat do služby pro rozpoznávání řeči a získat přepis a převést řeč na text ve stejnou dobu.
* PDF můžete odeslat do služby pro rozpoznávání formulářů a zjistit tabulky, buňky a text uvnitř těchto buněk a získat výstup JSON s souřadnicemi a podrobnostmi.

Azure nabízí celou řadu nástrojů, které jsou navržené pro různé typy uživatelů, mnohé z nich je možné používat s Cognitive Services. Nástroje založené na návrháři jsou nejjednodušší k použití a jsou rychlé pro nastavení a automatizaci, ale mohou mít omezení, když přijdou k přizpůsobení. Naše rozhraní REST API a klientské knihovny poskytují uživatelům větší kontrolu a flexibilitu, ale vyžadují více úsilí, dobu a odbornosti k sestavení řešení. Pokud používáte rozhraní REST API a klientské knihovny, očekává se, že budete pracovat s moderními programovacími jazyky, jako je C#, Java, Python, JavaScript nebo jiný oblíbený programovací jazyk. 

Pojďme se podívat na různé způsoby, jak můžete pracovat s Cognitive Services.

### <a name="client-libraries-and-rest-apis"></a>Klientské knihovny a rozhraní REST API

Klientské knihovny Cognitive Services a rozhraní REST API poskytují přímý přístup k vaší službě. Tyto nástroje poskytují programový přístup k Cognitive Services, jejich základním modelům a v mnoha případech umožňují programově přizpůsobit modely a řešení. 

* **Cíloví uživatelé** : vývojáři a vědečtí data
* **Výhody** : poskytuje největší flexibilitu při volání služeb z jakéhokoli jazyka a prostředí. 
* **Uživatelské rozhraní** : pouze není k dispozici kód
* **Předplatné** : účet Azure + Cognitive Services prostředky

Pokud chcete získat další informace o dostupných klientských knihovnách a rozhraních REST API, Využijte náš [Cognitive Services přehled](index.yml) a seznamte se s jedním z našich rychlých startů pro vize, rozhodování, jazyk a řeč.

### <a name="cognitive-services-for-big-data"></a>Cognitive Services pro velký objem dat

Díky Cognitive Services pro velké objemy dat můžete průběžně zlepšovat inteligentní modely přímo do &trade; výpočtů Apache Spark a SQL. Tyto nástroje uvolňují vývojáře z podrobností o sítích nízké úrovně, aby se mohli soustředit na vytváření inteligentních distribuovaných aplikací. Cognitive Services pro velké objemy dat podporuje následující platformy a konektory: Azure Databricks, Azure synapse, Azure Kubernetes Service a datové konektory.

* **Cíloví uživatelé** : vědečtí data a technici dat
* **Výhody** : Cognitive Services Azure pro velké objemy dat umožňuje uživatelům kanál terabajtů dat prostřednictvím Cognitive Services pomocí Apache Spark &trade; . Vytváření rozsáhlých inteligentních aplikací s libovolným úložištěm dat je snadné.
* **Uživatelské rozhraní** : pouze není k dispozici kód
* **Předplatné** : účet Azure + Cognitive Services prostředky

Pokud chcete získat další informace o velkých objemech dat pro Cognitive Services, je vhodné začít s [přehledem](./big-data/cognitive-services-for-big-data.md). Pokud jste připraveni začít sestavovat, vyzkoušejte naše ukázky [Pythonu](./big-data/samples-python.md) nebo [Scala](./big-data/samples-scala.md) .

### <a name="azure-functions-and-azure-service-web-jobs"></a>Webové úlohy služby Azure Functions a Azure

[Webové úlohy](../app-service/index.yml) [Azure Functions](../azure-functions/index.yml) a Azure App Service poskytují integrační služby pro první kód navržené pro vývojáře a jsou postavené na [Azure App Services](../app-service/index.yml). Tyto produkty poskytují infrastrukturu bez serveru pro psaní kódu. V tomto kódu můžete volat naše služby pomocí našich klientských knihoven a rozhraní REST API. 

* **Cíloví uživatelé** : vývojáři a vědečtí data
* **Výhody** : výpočetní služba bez serveru, která umožňuje spuštění kódu aktivovaného událostmi. 
* **Uživatelské rozhraní** : Ano
* **Předplatné** : účet Azure + Cognitive Services prostředek + Azure Functions předplatné

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic Apps](../logic-apps/index.yml) sdílet stejné návrháře a konektory pracovních postupů, jako je Power Automate, ale poskytuje pokročilejší a řídicí prostředí, včetně integrace se sadou Visual Studio a DevOps. Power Automate usnadňuje integraci s vašimi prostředky služeb rozpoznávání prostřednictvím konektorů specifických pro služby, které poskytují proxy nebo obálku kolem rozhraní API. Jedná se o stejné konektory jako ty, které jsou k dispozici v Power automatu. 

* **Cíloví uživatelé** : vývojáři, integrátory, IT specialisté, DevOps
* **Výhody** : vývojový model Designer-First (deklarativní) poskytující pokročilé možnosti a integraci v řešení s nízkým kódem
* **Uživatelské rozhraní** : Ano
* **Předplatné** : účet Azure + Cognitive Services nasazení prostředků a Logic Apps

### <a name="power-automate"></a>Power Automate 

Power Automate je služba v [Power platformou](/power-platform/) , která vám pomůže vytvářet automatizované pracovní postupy mezi aplikacemi a službami bez psaní kódu. Nabízíme několik konektorů, které usnadňují interakci s vaším prostředkem Cognitive Services v řešení Power Automate. Automatické automatizace je postavená na Logic Apps. 

* **Cíloví** uživatelé: obchodní uživatelé (analytici) a správci služby SharePoint
* **Výhody** : automatizace opakujících se ručních úloh jednoduše pomocí nahrávání kliknutí myší, klávesových úhozů a zkopírování kroků vložení z plochy.
* **Nástroje uživatelského rozhraní** : Ano – pouze uživatelské rozhraní
* **Předplatné** : účet Azure + Cognitive Services prostředek + Power automatizuje předplatné + předplatné Office 365

### <a name="ai-builder"></a>AI Builder 

[Tvůrce AI](/ai-builder/overview) je schopnost Microsoft Power Platform, kterou můžete využít ke zlepšení výkonu podniku automatizací procesů a předvídání výsledků. Tvůrce AI přináší výkon AI do vašich řešení prostřednictvím prostředí typu Point-to-Click. Mnoho služeb rozpoznávání, jako je například funkce pro rozpoznávání formulářů, Analýza textu a Počítačové zpracování obrazu, je přímo integrováno zde a nemusíte vytvářet vlastní Cognitive Services. 

* **Cíloví** uživatelé: obchodní uživatelé (analytici) a správci služby SharePoint
* **Výhody** : řešení klíč, které přináší sílu AI prostřednictvím prostředí typu Point-to-Click. Nevyžaduje se žádné kódování ani dovednosti v oblasti datových věd.
* **Nástroje uživatelského rozhraní** : Ano – pouze uživatelské rozhraní
* **Odběry** : Tvůrce AI

### <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

Ke správě nasazení můžete použít akce Azure DevOps a GitHub. V [níže uvedené části](#continuous-integration-and-delivery-with-devops-and-github-actions) se probírají dva příklady integrace CI/CD, které vám umožní naučit a nasazovat vlastní modely pro rozpoznávání řeči a službu Language UNDERSTANDING (Luis). 

* **Cíloví uživatelé** : vývojáři, odborníci na data a technici na data
* **Výhody** : umožňuje průběžně upravovat, aktualizovat a nasazovat aplikace a modely programově. Při pravidelném používání vašich dat za účelem vylepšení a aktualizace modelů pro rozpoznávání řeči, vize, jazyk a rozhodování je významná výhoda. 
* **Nástroje uživatelského rozhraní** : jenom N/a-Code 
* **Předplatné** : účet Azure + Cognitive Services Resource + GitHub

## <a name="tools-to-customize-and-configure-models"></a>Nástroje pro přizpůsobení a konfiguraci modelů

Jak budete postupovat na cestě při vytváření aplikace nebo pracovního postupu s Cognitive Services, můžete zjistit, že je potřeba model přizpůsobit, abyste dosáhli požadovaného výkonu. Mnohé z našich služeb vám umožňují sestavovat sestavování předem vytvořených modelů, aby vyhovovaly vašim konkrétním obchodním potřebám. Pro všechny naše přizpůsobitelné služby poskytujeme prostředí založené na uživatelském rozhraní pro procházení procesu i rozhraní API pro školení řízené kódem. Příklad:

* Chcete vytvořit výuku Custom Speech modelu, aby správně rozpoznala lékařské výrazy s chybovými zprávami (WER) pod 3%
* Chcete vytvořit klasifikátor obrázku s Custom Vision, který může sdělit rozdíl mezi jehličnatými a listnatými stromy
* Chcete vytvořit vlastní hlas neuronové s osobními daty hlasu pro vylepšené automatizované prostředí pro zákazníky

Nástroje, které budete používat pro výuku a konfiguraci modelů, se liší od těch, které použijete k volání Cognitive Services. V mnoha případech Cognitive Services, který podporuje přizpůsobení, poskytovat portály a nástroje uživatelského rozhraní navržené tak, aby vám pomohly vyškolit, vyhodnocovat a nasazovat modely. Pojďme se rychle podívat na několik možností:<br><br>

| Pilíř | Služba | Přizpůsobení uživatelského rozhraní | Rychlý start |
|--------|---------|------------------|------------|
| Obraz | Custom Vision | https://www.customvision.ai/ | [Rychlý start](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Obraz | Rozpoznávání formulářů | Ukázkový nástroj pro popisky | [Rychlý start](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Rozhodnutí | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Rychlý start](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Rozhodnutí | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [Rychlý start](./metrics-advisor/quickstarts/web-portal.md) |
| Rozhodnutí | Personalizace | Uživatelské rozhraní je k dispozici v Azure Portal v rámci prostředku pro přizpůsobování. | [Rychlý start](./personalizer/quickstart-personalizer-sdk.md) |
| Jazyk | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Jazyk | QnA Maker | https://www.qnamaker.ai/ | [Rychlý start](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Jazyk | Překladatel nebo vlastní Překladatel | https://portal.customtranslator.azure.ai/ | [Rychlý start](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Řeč | Vlastní příkazy | https://speech.microsoft.com/ | [Rychlý start](./speech-service/custom-commands.md) |
| Řeč | Custom Speech | https://speech.microsoft.com/ | [Rychlý start](./speech-service/how-to-custom-speech.md) |
| Řeč | Vlastní hlas | https://speech.microsoft.com/ | [Rychlý start](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Průběžná integrace a doručování s akcemi DevOps a GitHubu

Language Understanding a služba rozpoznávání řeči nabízí řešení pro kontinuální integraci a průběžné nasazování, která využívají akce Azure DevOps a GitHubu. Tyto nástroje se používají pro automatizované školení, testování a správu verzí vlastních modelů. 

* [CI/CD pro Custom Speech](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD pro LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>Kontejnery on-Prem 

Mnohé z Cognitive Services můžete nasadit v kontejnerech pro přístup k Prem a použití. Pomocí těchto kontejnerů získáte flexibilitu Cognitive Services blíž k vašim datům v případě dodržování předpisů, zabezpečení nebo jiných provozních důvodů. Úplný seznam Cognitive Services kontejnerů najdete v tématu [kontejnery Prem pro Cognitive Services](./cognitive-services-container-support.md).

## <a name="next-steps"></a>Další kroky
<!--
* Learn more about low code development options for Cognitive Services -->
* [Vytvoření prostředku Cognitive Services a zahájení sestavování](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)
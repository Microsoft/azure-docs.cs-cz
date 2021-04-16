---
title: Language Understanding (LUIS) – přehled
description: Language Understanding (LUIS) – cloudová služba API, která využívá strojové učení ke konverzaci, přirozenému jazyku pro předpověď významu a extrakci informací.
keywords: Azure, umělal Intelligence, AI, zpracování přirozeného jazyka, NLP, přirozené jazykové porozumění, NLU, LUIS, konverzace AI, AI chatovací robot, NLP AI, Azure Luis
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/13/2021
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: f46586b3f120cf191d88b7de9cf8686ca9b16cca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503761"
---
# <a name="what-is-language-understanding-luis"></a>Co je Language Understanding (LUIS)?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Language Understanding (LUIS) je cloudová konverzační služba pro konverzaci, která aplikuje vlastní informace o strojovém učení na konverzaci, text přirozeného jazyka a předpovídá celkový význam a vyžádá si relevantní a podrobné informace. LUIS poskytuje přístup prostřednictvím [vlastního portálu](https://www.luis.ai), [rozhraní API][endpoint-apis] a [klientských knihoven SDK](client-libraries-rest-api.md).

Při prvním přihlášení se pomocí těchto kroků [přihlaste k portálu Luis](sign-in-luis-portal.md "přihlášení k portálu LUIS") , abyste mohli začít, vyzkoušet si Luis [předem sestavené doménové](luis-get-started-create-app.md) aplikace nebo můžete [sestavit aplikaci](get-started-portal-build-app.md).

Tato dokumentace obsahuje následující typy článků:  

* [**Rychlé starty**](luis-get-started-create-app.md) jsou pokyny Začínáme, které vám pomohou při provádění požadavků na službu.  
* Návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném [**způsobu.**](luis-how-to-start-new-app.md)  
* [**Koncepty**](artificial-intelligence.md) poskytují podrobné vysvětlení funkcí a funkcí služby.  
* [**Kurzy**](tutorial-intents-only.md) jsou delší než příručky, které ukazují, jak používat službu jako součást v širších obchodních řešeních.  

## <a name="what-does-luis-offer"></a>Co nabídka LUIS 

* **Jednoduchost**: Luis vám přesměruje nutnost interních odborných znalostí AI nebo jakýchkoli předchozích znalostí strojového učení. Jediným kliknutím můžete vytvořit vlastní aplikaci pro konverzaci AI. Vlastní aplikaci můžete vytvořit pomocí jednoho z našich šablon [rychlý Start](get-started-portal-build-app.md)nebo můžete použít některou z našich [předem připravených doménových](luis-get-started-create-app.md) aplikací.
* **Zabezpečení, ochrana osobních údajů a dodržování předpisů**: na základě infrastruktury Azure Luis nabízí zabezpečení, ochranu osobních údajů a dodržování předpisů na podnikové úrovni. Vaše data zůstanou pořád pro sebe; data můžete kdykoli odstranit. Vaše data se zašifrují v úložišti. Další informace o tom najdete [tady](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy).
* **Integrace**: snadno Integrujte svou aplikaci Luis s jinými službami Microsoftu, jako je [Microsoft Bot Framework](https://docs.microsoft.com/composer/tutorial/tutorial-luis), [QnA maker](../QnAMaker/choose-natural-language-processing-service.md)a [Speech Service](../Speech-Service/quickstarts/intent-recognition.md).


## <a name="luis-scenarios"></a>Scénáře LUIS
* [Vytvoření firemního robota na podnikové úrovni](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/conversational-bot): Tato referenční architektura popisuje, jak vytvořit robota konverzací na podnikové úrovni (chatovací robot) pomocí rozhraní Azure bot Framework.
* [Obchodní chatovací robot](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/commerce-chatbot): společně Azure Bot Service Služba Language Understanding umožňuje vývojářům vytvářet konverzační rozhraní pro různé scénáře, jako je bankovnictví, cestování a zábava.
* [Řízení zařízení IoT pomocí hlasového asistenta](https://docs.microsoft.com/azure/architecture/solution-ideas/articles/iot-controlling-devices-with-voice-assistant): Vytvářejte bezproblémová rozhraní pro konverzaci se všemi vašimi zařízeními přístupnými v Internetu – z připojeného televizoru nebo nákupem ledničky do zařízení v připojené napájecím zařízení.


## <a name="application-development-life-cycle"></a>Životní cyklus vývoje aplikací

![Životní cyklus vývoje aplikací LUIS](./media/luis-overview/luis-dev-lifecycle.png "Životní cyklus LUIS aplikace Develooment")

-   **Plán**: Identifikujte scénáře, pro které mohou uživatelé používat vaši aplikaci. Definujte akce a relevantní informace, které je třeba rozpoznat.
-   **Sestavení**: pro vývoj aplikací použijte svůj prostředek pro vytváření obsahu. Začněte definováním [záměrů](luis-concept-intent.md) a [entit](luis-concept-entity-types.md). Pak přidejte školicí [projevy](luis-concept-utterance.md) pro každý záměr. 
-   **Testování a zlepšení**: Začněte s testováním modelu pomocí jiných projevy, abyste získali představu o tom, jak se aplikace chová, a můžete rozhodnout, jestli potřebujete nějaké zlepšení. Tuto aplikaci můžete vylepšit pomocí těchto [osvědčených postupů](luis-concept-best-practices.md). 
-   **Publikování**: Nasaďte aplikaci pro předpověď a Dotazujte koncový bod pomocí prostředku předpovědi. Další informace o vytváření a předpovědi prostředků [najdete tady](luis-how-to-azure-subscription.md#luis-resources). 
-   **Připojit**: Připojte se k jiným službám, jako je [Microsoft Bot Framework](https://docs.microsoft.com/composer/tutorial/tutorial-luis), [QnA maker](../QnAMaker/choose-natural-language-processing-service.md)a [Speech Service](../Speech-Service/quickstarts/intent-recognition.md). 
-   **Upřesnění**: [Zkontrolujte projevy koncového bodu](luis-concept-review-endpoint-utterances.md) a Vylepšete svou aplikaci pomocí příkladů reálného života.

Přečtěte si další informace o plánování a sestavování [aplikace.](luis-how-plan-your-app.md)

## <a name="next-steps"></a>Další kroky

* [Co je nového](whats-new.md "Novinky") u služby a dokumentace
* [Sestavení aplikace LUIS](tutorial-intents-only.md)
* [Referenční materiály k rozhraní API][endpoint-apis]
* [Osvědčené postupy](luis-concept-best-practices.md)
* [Materiály pro vývojáře](developer-reference-resource.md "Materiály pro vývojáře") pro Luis.
* [Plánování aplikace](luis-how-plan-your-app.md "Plánování aplikace") s využitím [záměrů](luis-concept-intent.md "záměry") a [entit](luis-concept-entity-types.md "podnikům")

[bot-framework]: /bot-framework/
[flow]: /connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/

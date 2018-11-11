---
title: Co je QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker je cloudová služba API, která poskytuje nejlepší odpovědi tím, že na otázky uživatelů v přirozeném jazyce používá vlastní inteligentní funkce strojového učení.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: bd859183a13e0f8a21cdd2eabb464b718e949464
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212212"
---
# <a name="what-is-qna-maker"></a>Co je QnA Maker?

QnA Maker je cloudová služba API, která nad vašimi daty vytváří konverzační vrstvu otázek a odpovědí. 

QnA Maker umožňuje vytvořit znalostní bázi na základě částečně strukturovaného obsahu, jako jsou adresy URL s nejčastějšími dotazy, příručky k produktům, dokumenty podpory nebo vlastní otázky a odpovědi. Služba QnA Maker odpovídá na otázky uživatelů v přirozeném jazyce tak, že je porovnává s nejlepší možnou odpovědí v otázkách a odpovědích ve znalostní bázi.

Snadno použitelný [webový portál](https://qnamaker.ai) vám umožní vytvořit, spravovat, trénovat a publikovat službu bez jakýchkoli zkušeností s vývojem. Po publikování služby do koncového bodu může klientská aplikace, například chatbot, prostřednictvím správy konverzace s uživatelem získat otázky a odpovídat na ně. 

![Přehled](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Klíčové procesy služby QnA Maker

QnA Maker pro vaše data zajišťuje dvě klíčové služby:

* **Extrakce:** Strukturovaná data otázek a odpovědí se extrahují ze [zdrojů strukturovaných a částečně strukturovaných dat](../Concepts/data-sources-supported.md), jako jsou nejčastější dotazy a příručky k produktům. Extrakci je možné provést během [vytváření](https://aka.ms/qnamaker-docs-createkb) znalostní báze nebo později v rámci úprav.

* **Hledání shody:** Po [natrénování a otestování](https://aka.ms/qnamaker-docs-trainkb) znalostní báze ji můžete [publikovat](https://aka.ms/qnamaker-docs-publishkb). Tím se povolí koncový bod pro znalostní bázi služby QnA Maker, který pak můžete použít ve svém chatbotu nebo klientské aplikaci. Tento koncový bod přijímá otázky uživatelů a jako odpověď nabízí nejlepší odpověď ve znalostní bázi společně se skóre spolehlivosti shody.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>Architektura služby QnA Maker

Architektura služby QnA Maker se skládá z následujících dvou komponent:

1. **Služby pro správu služby QnA Maker:** Prostředí pro správu znalostní báze služby QnA Maker od počátečního vytvoření, aktualizací, trénování až po publikování. Tyto aktivity je možné provádět přes [portál](https://qnamaker.ai) nebo [rozhraní API pro správu](https://aka.ms/qnamaker-v4-apis). 

2. **Data a modul runtime služby QnA Maker:** Toto se nasadí do vašeho předplatného Azure v zadané oblasti. Obsah vaší znalostní báze se ukládá ve službě [Azure Search](https://azure.microsoft.com/services/search/) a koncový bod se nasazuje jako [App Service](https://azure.microsoft.com/services/app-service/). Můžete nasadit také prostředek [Application Insights](https://azure.microsoft.com/services/application-insights/) pro účely analýz.

![Architektura](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Stručný přehled služby

- Ucelené prostředí **bez kódu** pro [vytvoření chatbota pro nejčastější dotazy](https://aka.ms/qnamaker-docs-create-faqbot).
- **Předpovědi bez omezování využití sítě**. Plaťte za hostování služby, a ne za počet transakcí. Další podrobnosti najdete na [stránce s cenami](https://aka.ms/qnamaker-docs-pricing).
- **Škálování podle potřeby**. Zvolte vhodné skladové položky jednotlivých komponent, které budou vyhovovat vašemu scénáři. Podívejte se, jak [zvolit kapacitu](https://aka.ms/qnamaker-docs-capacity) pro vaši službu QnA Maker.
- **Úplné dodržování předpisů pro data**. Komponenty služby předpovědí se nasazují do vašeho předplatného Azure a splňují vaše požadavky na dodržování předpisů.


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření služby QnA Maker](../how-to/set-up-qnamaker-service-azure.md)

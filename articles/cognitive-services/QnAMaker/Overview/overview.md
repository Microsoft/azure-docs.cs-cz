---
title: Co je QnA Maker?
titleSuffix: Azure Cognitive Services
description: QnA Maker je cloudová služba API, která poskytuje nejlepší odpovědi tím, že na otázky uživatelů v přirozeném jazyce používá vlastní inteligentní funkce strojového učení.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: 963769315302ba4e7d1600253b617c7cb0f02bc5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794246"
---
# <a name="what-is-qna-maker"></a>Co je QnA Maker?

QnA Maker je cloudová služba API, která nad vašimi daty vytváří konverzační vrstvu otázek a odpovědí. 

QnA Maker umožňuje vytvořit znalostní bázi na základě částečně strukturovaného obsahu, jako jsou adresy URL s nejčastějšími dotazy, příručky k produktům, dokumenty podpory nebo vlastní otázky a odpovědi. Služba QnA Maker odpovídá na otázky uživatelů v přirozeném jazyce tak, že je porovnává s nejlepší možnou odpovědí v otázkách a odpovědích ve znalostní bázi.

Snadno použitelný [webový portál](https://qnamaker.ai) vám umožní vytvořit, spravovat, trénovat a publikovat službu bez jakýchkoli zkušeností s vývojem. Po publikování služby do koncového bodu může klientská aplikace, například chatbot, prostřednictvím správy konverzace s uživatelem získat otázky a odpovídat na ně. 

![Přehled](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Klíčové procesy služby QnA Maker

QnA Maker pro vaše data zajišťuje dvě klíčové služby:

* **Extrakce**: Otázka – odpověď strukturovaná data extrahují z částečně strukturovaných a strukturovaných [zdroje dat](../Concepts/data-sources-supported.md) , jako jsou nejčastější dotazy a produktových příruček. Extrakci je možné provést během [vytváření](https://aka.ms/qnamaker-docs-createkb) znalostní báze nebo později v rámci úprav.

* **Odpovídající**: Jakmile znalostní báze [trénují a testovat](https://aka.ms/qnamaker-docs-trainkb), můžete [publikovat](https://aka.ms/qnamaker-docs-publishkb) ho. Tím se povolí koncový bod pro znalostní bázi služby QnA Maker, který pak můžete použít ve svém chatbotu nebo klientské aplikaci. Tento koncový bod přijímá otázky uživatelů a jako odpověď nabízí nejlepší odpověď ve znalostní bázi společně se skóre spolehlivosti shody.

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

1. **Služby pro správu nástroje QnA Maker**: Rozhraní pro správu prostředí pro nástroje QnA Maker znalostní báze, který obsahuje úvodní vytvoření, aktualizace, trénování a publikování. Tyto aktivity je možné provádět přes [portál](https://qnamaker.ai) nebo [rozhraní API pro správu](https://go.microsoft.com/fwlink/?linkid=2092179). 

2. **Nástroj QnA Maker dat a modulu runtime**: Nasadí se v rámci vašeho předplatného Azure v zadané oblasti. Obsah vaší znalostní báze se ukládá ve službě [Azure Search](https://azure.microsoft.com/services/search/) a koncový bod se nasazuje jako [App Service](https://azure.microsoft.com/services/app-service/). Můžete nasadit také prostředek [Application Insights](https://azure.microsoft.com/services/application-insights/) pro účely analýz.

![Architektura](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Stručný přehled služby

- Kompletní **bez kódu** docházet k [vytváření robotů](../Quickstarts/create-publish-knowledge-base.md#create-a-bot) ze znalostní báze.
- **Předpovědi bez omezování využití sítě**. Plaťte za hostování služby, a ne za počet transakcí. Další podrobnosti najdete na [stránce s cenami](https://aka.ms/qnamaker-docs-pricing).
- **Škálování podle potřeby**. Zvolte vhodné skladové položky jednotlivých komponent, které budou vyhovovat vašemu scénáři. Podívejte se, jak [zvolit kapacitu](https://aka.ms/qnamaker-docs-capacity) pro vaši službu QnA Maker.


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření služby QnA Maker](../how-to/set-up-qnamaker-service-azure.md)

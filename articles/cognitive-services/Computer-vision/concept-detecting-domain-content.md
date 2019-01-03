---
title: Zjištění obsahu specifického pro doménu – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak zadat doménu kategorizace obrázků vrací podrobné informace o obrázku.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 50942634bd50974453c242d1980db9fc589bd47e
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579205"
---
# <a name="detecting-domain-specific-content"></a>Rozpoznávání obsahu specifického pro doménu

Kromě toho kategorizace značek a nejvyšší úrovně, pro počítačové zpracování obrazu také podporuje speciální (nebo specifický pro doménu) informací. Tyto zvláštní informace je možné implementovat jako samostatnou metodu nebo společně s kategorií nejvyšší úrovně. Funguje jako prostředek, který dále zpřesňuje 86 taxonomických kategorií o modely určitých domén.

Existují dvě možnosti, jak používat modely určité domény:

* S vymezeným oborem analýzy  
  Analyzujte pouze zvolené model vyvoláním volání rozhraní HTTP POST. Pokud víte, které model, který chcete použít, zadejte název modelu. Můžete získat informace jenom relevantní k tomuto modelu. Tuto možnost můžete použít, když třeba hledáte jenom rozpoznávání celebrit. V odpovědi bude seznam možných shodných celebrit doplněný o skóre spolehlivosti.
* Rozšířené analýzy  
  Při této analýze můžete zadat další podrobnosti týkající se kategorií z 86 taxonomických kategorií. Tuto možnost můžete použít v aplikacích, ve kterých uživatelé kromě podrobností z jednoho nebo několika doménových modelů chtějí získat i obecnou analýzu obrázku. Při volání této metody se nejprve volá klasifikátor taxonomie, která obsahuje 86 kategorií. Pokud některou z kategorií odpovídat známé nebo odpovídající modelů, následuje překontrolovat třídění volání. Například pokud `details` parametr volání HTTP POST je nastavena na "vše" nebo "celebrit" zahrnuje, metoda se volá třídění celebrit po volání třídění 86 kategorie. Pokud na obrázku je klasifikován tak `people_` nebo podkategorie dané kategorie, a pak třídění celebrit je volána.

## <a name="listing-domain-specific-models"></a>Výpis doménově specifické modely

Můžete vytvořit seznam doménově specifické modely podporované pro počítačové zpracování obrazu. Počítačové zpracování obrazu v současné době podporuje následující doménově specifické modely pro zjišťování obsahu specifického pro doménu:

| Název | Popis |
|------|-------------|
| celebrit | Rozpoznávání celebrit, podporované pro klasifikaci obrázků v `people_` kategorie |
| Zajímavá | Rozpoznávání památek, podporované pro klasifikaci obrázků v `outdoor_` nebo `building_` kategorie |

### <a name="domain-model-list-example"></a>Příklad seznamu modelu domény

Následující odpověď JSON obsahuje seznam doménově specifické modely podporované pro počítačové zpracování obrazu.

```json
{
    "models": [
        {
            "name": "celebrities",
            "categories": ["people_", "人_", "pessoas_", "gente_"]
        },
        {
            "name": "landmarks",
            "categories": ["outdoor_", "户外_", "屋外_", "aoarlivre_", "alairelibre_",
                "building_", "建筑_", "建物_", "edifício_"]
        }
    ]
}
```

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [kategorizace obrázků](concept-categorizing-images.md).
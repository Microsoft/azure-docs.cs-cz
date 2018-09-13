---
title: Zjišťování obsahu specifického pro doménu
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Koncepty související s popisem obrázků s využitím Azure Cognitive Services pro počítačové zpracování obrazu.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 699698d8235d8fa53050a67491ee4f0e1c336d0e
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725336"
---
# <a name="detecting-domain-specific-content"></a>Zjišťování obsahu specifického pro doménu

Kromě toho kategorizace značek a nejvyšší úrovně, pro počítačové zpracování obrazu také podporuje speciální (nebo specifický pro doménu) informací. Specializované informace je možné implementovat jako samostatnou metodu nebo pomocí kategorizace vysoké úrovně. Funguje jako prostředky pro další upřesnění taxonomie kategorií 86 prostřednictvím přidání doménově specifické modely.

Existují dvě možnosti použití doménově specifické modely:

* S vymezeným oborem analýzy  
  Analyzujte pouze zvolené model vyvoláním volání rozhraní HTTP POST. Pokud víte, které model, který chcete použít, zadejte název modelu. Můžete získat informace jenom relevantní k tomuto modelu. Například můžete použít tuto možnost se díval jenom pro rozpoznávání celebrit. Odpověď obsahuje seznam potenciál odpovídající celebrit doplněny jejich skóre spolehlivosti.
* Rozšířené analýzy  
  Analýza poskytnout další podrobnosti související s kategorií podle kategorie 86 taxonomie. Tato možnost je k dispozici pro použití v aplikacích, kde uživatelé chtějí získat analýzy obecný obrázek kromě podrobností z jednoho nebo více doménově specifické modely. Po vyvolání tato metoda je volána nejprve taxonomie kategorií 86 třídění. Pokud některou z kategorií odpovídat známé nebo odpovídající modelů, následuje překontrolovat třídění volání. Například pokud `details` parametr volání HTTP POST je nastavena na "vše" nebo "celebrit" zahrnuje, metoda se volá třídění celebrit po volání třídění 86 kategorie. Pokud na obrázku je klasifikován tak `people_` nebo podkategorie dané kategorie, a pak třídění celebrit je volána.

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
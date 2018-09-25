---
title: Rozbalte položku transformace formátu JSON pomocí Azure Machine Learning Workbench
description: Referenční dokument pro transformaci 'rozbalte JSON.
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 0a5cbca114b220686d656f93edb00a199e3cbeeb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989817"
---
# <a name="expand-json-transformation"></a>Rozbalte transformace JSON

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


**Rozbalte JSON** transformace umožňuje rozšířit existující sloupec, který obsahuje platný text JSON do více sloupců.

## <a name="how-to-perform-this-transformation"></a>Jak provést tuto transformaci

Postupujte podle těchto kroků k provedení této transformace:
1. Vyberte zdrojový sloupec, který obsahuje JSON text.
2. Vyberte **rozbalte JSON** z **transformuje** nabídky. Nebo klikněte pravým tlačítkem na záhlaví zdrojového sloupce a vyberte **rozbalte JSON** v místní nabídce. 
3. Klikněte na **OK**. 
 
U zdrojového sloupce jsou přidány nové sloupce. Tyto sloupce obsahují vlastnosti z další úroveň hierarchie v textu JSON. Klíč vlastnosti, pokud jsou k dispozici, se používá k vytvoření názvu odpovídajícího sloupce. Vnořené vlastnosti jsou zachovány jako text JSON pro tohoto uživatele můžete zavádět postupně rozbalte nebo zrušit podle potřeby.

## <a name="examples"></a>Příklady

Zdrojový sloupec *zákazníka* rozbalen do dvou sloupců *Customer.Name* a *Customer.Phone*.

| Zákazník                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Name": "Carrie Dodson", "Phone": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Name": "Leonard Robledo", "Phone": "456 7890 123"} | Leonard Robledo | 456-7890-123   |


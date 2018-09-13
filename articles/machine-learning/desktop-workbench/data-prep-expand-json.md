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
ms.openlocfilehash: dbda4b7b6d82e8cf1e89dc78ce82efbac08b9933
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35644498"
---
# <a name="expand-json-transformation"></a>Rozbalte transformace JSON
**Rozbalte JSON** transformace umožňuje rozšířit existující sloupec, který obsahuje platný text JSON do více sloupců.

## <a name="how-to-perform-this-transformation"></a>Jak provést tuto transformaci

Postupujte podle těchto kroků k provedení této transformace:
1. Vyberte zdrojový sloupec, který obsahuje JSON text.
2. Vyberte **rozbalte JSON** z **transformuje** nabídky. Nebo klikněte pravým tlačítkem na záhlaví zdrojového sloupce a vyberte **rozbalte JSON** v místní nabídce. 
3. Klikněte na **OK**. 
 
U zdrojového sloupce jsou přidány nové sloupce. Tyto sloupce obsahují vlastnosti z další úroveň hierarchie v textu JSON. Klíč vlastnosti, pokud jsou k dispozici, se používá k vytvoření názvu odpovídajícího sloupce. Vnořené vlastnosti jsou zachovány jako text JSON pro tohoto uživatele můžete zavádět postupně rozbalte nebo zrušit podle potřeby.

## <a name="examples"></a>Příklady

Zdroj požadován *zákazníka* rozbalen do dvou sloupců *Customer.Name* a *Customer.Phone*.

| Zákazník                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| {"Name": "Carrie Dodson", "Phone": "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| {"Name": "Leonard Robledo", "Phone": "456 7890 123"} | Leonard Robledo | 456-7890-123   |


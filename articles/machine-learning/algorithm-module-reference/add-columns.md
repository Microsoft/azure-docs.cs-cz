---
title: 'Přidáte sloupce: Odkaz na modul'
titleSuffix: Azure Machine Learning service
description: Další informace o použití modulu přidat sloupce ve službě Azure Machine Learning zřetězit dvě datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029352"
---
# <a name="add-columns-module"></a>Přidat modul sloupce

Tento článek popisuje modulu rozhraní visual (preview) pro službu Azure Machine Learning.

Tento modul slouží k řetězení dvě datové sady. Můžete kombinovat všech sloupců z obou datových sadách, které zadáte jako vstupy pro vytvoření jedné datové sady. Pokud je nutné zřetězit více než dvě datové sady, použijte několik instancí **přidat sloupce**.



## <a name="how-to-configure-add-columns"></a>Jak nakonfigurovat přidat sloupce
1. Přidat **přidat sloupce** modulu do experimentu.

2. Připojení, dvě datové sady, které chcete zřetězit. Pokud chcete zkombinovat více než dvě datové sady, můžete zřetězit dohromady několik kombinací **přidat sloupce**.

    - Je možné kombinovat dva sloupce, které mají různý počet řádků. Výstupní datová sada je vyplněna chybějící hodnoty pro každý řádek v menších zdrojový sloupec.

    - Nemůžete zvolit jednotlivé sloupce, které chcete přidat. Všechny sloupce z každé datové sady jsou zřetězeny při použití **přidat sloupce**. Proto pokud chcete přidat pouze podmnožina sloupců, použijte výběr sloupců v datové sadě s sloupce, které chcete vytvořit datovou sadu.

3. Spusťte experiment.

### <a name="results"></a>Výsledky
Po spuštění testu:

- Pokud chcete zobrazit prvních řádků novou datovou sadu, klikněte pravým tlačítkem na výstup **přidat sloupce** a vyberte vizualizovat.

Počet sloupců v nová datová sada se rovná součtu sloupce vstupní datové sady.

Pokud jsou dva sloupce se stejným názvem ve vstupní datové sady, číselnou příponou se přidá k názvu sloupce. Například, pokud existují dvě instance sloupec s názvem TargetOutcome, v levém sloupci by přejmenovat TargetOutcome_1 a pravý sloupec bude přejmenován TargetOutcome_2.

## <a name="next-steps"></a>Další postup

Zobrazit [sada modulů, které jsou k dispozici](module-reference.md) do služby Azure Machine Learning. 
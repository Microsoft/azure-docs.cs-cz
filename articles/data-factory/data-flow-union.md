---
title: Azure Data Factory mapování dat Flow novou větev transformace
description: Azure Data Factory mapování dat Flow novou větev transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 1eebc879ad56ba4f35e6a8a1b857ae877a6a2f01
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726256"
---
# <a name="mapping-data-flow-union-transformation"></a>Mapování typu union transformace toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Sjednocení bude kombinovat více datových proudů do jednoho se SQL sjednocení těchto datových proudů jako nový výstup z sjednocení transformace. Všechny schéma z každého vstupního datového proudu se budou kombinovat v rámci toku dat, aniž byste museli mít klíč spojení.

Můžete kombinovat n počet datových proudů v tabulce nastavení tak, že vyberete ikonu "+" vedle jednotlivých řádků nakonfigurované.

![Sjednocení transformace](media/data-flow/union.png "Union")

V takovém případě můžete kombinovat různorodých metadat z více zdrojů (v tomto příkladu tři různé zdrojové soubory) a je zkombinovat do jediného datového proudu:

![Přehled sjednocení transformace](media/data-flow/union111.png "sjednocení 1")

K dosažení tohoto cíle, přidáte další řádky v Union nastavení včetně všechny zdroje, které chcete přidat. Není nutné pro běžné vyhledávání nebo spojení klíč:

![Nastavení typu UNION transformace](media/data-flow/unionsettings.png "sjednocení nastavení")

Pokud nastavíte vyberte transformace po vaší sjednocení, bude možné přejmenovat překrývající se pole nebo polí, která nejsou s názvem z headerless zdrojů. Klikněte na "Zkontrolujte, jestli se" Pokud chcete zobrazit metadata kombinovat s 132 celkový počet sloupců v tomto příkladu ze tří různých zdrojů:

![Konečné sjednocení transformace](media/data-flow/union333.png "sjednocení 3")

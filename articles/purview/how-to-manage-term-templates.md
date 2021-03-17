---
title: Správa termínových šablon pro obchodní Glosář
description: Naučte se spravovat termíny šablon pro obchodní Glosář ve službě Azure dosah Data Catalog.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553345"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Správa termínových šablon pro obchodní Glosář

Azure dosah vám umožňuje vytvořit Glosář termínů, které jsou důležité pro obohacení vašich dat. Každý nový termín přidaný do vašeho katalogu dosah Data Catalog Glosář je založen na šabloně termínu, která určuje pole pro daný termín. Tento článek popisuje, jak vytvořit šablonu termínu a vlastní atributy, které se dají přidružit k termínům glosáře.

## <a name="manage-term-templates-and-custom-attributes"></a>Správa šablon termínů a vlastních atributů

Pomocí šablon termínů můžete vytvářet vlastní atributy, seskupovat je dohromady a při vytváření podmínek použít šablonu. Po vytvoření termínu nelze změnit šablonu přidruženou k danému termínu.

1. Na stránce **glosářové podmínky** vyberte **Spravovat šablony termínů**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Snímek obrazovky glosářových podmínek > tlačítko Spravovat šablony termínů":::

2. Stránka zobrazuje jak systém, tak i vlastní atributy. Vyberte **vlastní** kartu pro vytváření nebo úpravy šablon termínů.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Snímek obrazovky glosářových termínů > spravovat stránku šablon termínů.":::

3. Vyberte **+ Nová šablona termínu** a zadejte název šablony a popis.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Snímek obrazovky glosářových termínů > Správa šablon termínů > nových šablon termínů":::

4. Vyberte **+ Nový atribut** pro vytvoření nového vlastního atributu pro pojem šablonu. Zadejte název atributu, popis. Název vlastního atributu musí být v rámci šablony termínu jedinečný, ale může se jednat o stejný název, který lze použít v rámci šablon.

   Typ pole zvolte ze seznamu **text** možnosti, **jedna volba**, **Vícenásobný výběr** nebo  **Datum**. Můžete také zadat výchozí hodnotu řetězec pro typy textových polí.  Atribut může být také označen jako **povinný**.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Snímek obrazovky glosářových termínů > nové stránky atributu":::

5. Jakmile budou vytvořeny všechny vlastní atributy, vyberte **Náhled** a uspořádejte sekvenci vlastních atributů. V požadované sekvenci můžete přetahovat vlastní atributy.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Snímek obrazovky glosářových termínů > šablonou termínu Preview.":::

6. Po definování všech vlastních atributů vyberte **vytvořit** a vytvořte šablonu s vlastními atributy.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Snímek obrazovky glosářových termínů > novou šablonu termínu – tlačítko vytvořit.":::

7. Stávající vlastní atributy mohou být označeny jako neplatné zaškrtnutím **označení konec platnosti**. Po vypršení platnosti nelze atribut znovu aktivovat. Atribut s vypršenou platností bude pro existující výrazy šedý. Budoucí nové termíny vytvořené pomocí této šablony termínu již nebudou zobrazovat atribut, který byl označen jako prošlý.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Snímek obrazovky glosářových podmínek > Upravit atribut a označit ho jako neplatný.":::

## <a name="next-steps"></a>Další kroky

Postup najdete v tomto [kurzu: vytváření a import podmínek glosáře](tutorial-import-create-glossary-terms.md) a další informace.

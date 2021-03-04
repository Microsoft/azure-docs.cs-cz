---
title: Glosář sestav s daty pomocí dosah Insights
description: Tato příručka popisuje, jak zobrazit a používat vytváření sestav glosáře dosah Insights pro vaše data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095846"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Glosář přehledů vašich dat v Azure dosah

Tato příručka popisuje, jak získat přístup, zobrazit a filtrovat sestavy dosah Glosář pro vaše data.

V této příručce se dozvíte, jak:

> [!div class="checklist"]
> - Přejít na přehledy z účtu dosah
> - Získejte pohled na vaše data na základě perspektivy.

## <a name="prerequisites"></a>Požadavky

Než začnete s dosah přehledy, ujistěte se, že jste dokončili následující kroky:

- Nastavení prostředků Azure a naplnění účtu daty

- Nastavení a dokončení kontroly zdrojového typu

- Nastavení glosáře a připojení prostředků k glosářovým termínům

Další informace najdete v tématu [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Použití dosah glosáře Insights

V Azure dosah můžete vytvořit pojem Glosář a připojit je k assetům. Později můžete zobrazit distribuci glosáře v glosáři Insights. Tím se dozvíte o stavu glosáře podle termínů připojených k assetům. Také vám dává informace o stavu a distribuci rolí podle počtu uživatelů.

**Zobrazení glosáře Insights:**

1. Přejít na obrazovku **Azure dosah** [instance v Azure Portal](https://aka.ms/purviewportal) a vyberte svůj účet dosah.

1. Na stránce **Přehled** **v části Začínáme** vyberte dlaždici **spustit účet dosah** .

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Spustit dosah z Azure Portal":::

1. Na **domovské** stránce dosah vyberte dlaždici **Zobrazit přehledy** , abyste měli přístup k oblasti **Insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Podívejte se na přehledy v Azure Portal":::

1. V oblasti **přehledy** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: Vyberte **Glosář** , aby se zobrazila sestava dosah **Glosář Insights** .

**Glosář Insights** nabízí jako firemního uživatele cenné informace, které udržují dobře definovaný Glosář pro vaši organizaci.

1. Sestava začíná **klíčovými ukazateli výkonu** , které ***v účtu dosah* zobrazují _celkové výrazy_ _, za _schválené výrazy bez prostředků_*_ a _*_vypršení platnosti s assety_**. Každá z těchto hodnot vám pomůže identifikovat stav glosáře.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Zobrazit klíčový ukazatel výkonu Glosář Insights"::: 


2. Část **snímku podmínek** (zobrazená výše) zobrazuje termín " **_koncept_*_, _*_schválen_*_, _*_Výstraha_*_ a*"_vypršela platnost_** pro podmínky s prostředky a termíny bez assetů.

3. Klikněte na **Zobrazit více** a podívejte se na názvy s různými stavy a dalšími podrobnostmi o **_expertích_ _Stewards_*_ a _***. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Snímek podmínek s prostředky a bez nich":::  

4. Když kliknete na Zobrazit další pro ***schválené podmínky s prostředky** _, Insights vám umožní přejít na stránku *Glosář* _ * termín, kde můžete dál přejít na seznam prostředků s připojenými podmínkami. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Přehledy pro Glosář"::: 

4. Na stránce Glosář Insights Zobrazte distribuci **neúplných podmínek** podle typu chybějící informace. Graf zobrazuje počet podmínek s **_chybějící definicí_*_, _*_chybějících znalců_*_, _*_chybějících Steward_*_ a _*_chybějících více_** polí.

1. Kliknutím na ***Zobrazit další** _ z _ * neúplné výrazy * * zobrazíte výrazy, které obsahují chybějící informace. Chcete-li zadat chybějící informace a zajistit, aby byl termín Glosář dokončen, můžete přejít na stránku podrobností o termínu Glosář.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak vytvořit Glosář pojmu pomocí [glosáře](./how-to-create-import-export-glossary.md) .
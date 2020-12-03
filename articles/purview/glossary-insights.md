---
title: Glosář sestav s daty pomocí dosah Insights
description: Tato příručka popisuje, jak zobrazit a používat vytváření sestav glosáře dosah Insights pro vaše data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 9f7d81423c75c3e1a51f5b5d5f37c54307488eb3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552750"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Glosář přehledů vašich dat v Azure dosah

Tato příručka popisuje, jak získat přístup, zobrazit a filtrovat sestavy dosah Glosář pro vaše data.

V této příručce se dozvíte, jak:

> - Přejít na přehledy z účtu dosah
> - Získejte pohled na vaše data na základě perspektivy.

## <a name="prerequisites"></a>Předpoklady

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

Stránka **Glosář Insights** obsahuje následující oblasti:
1. **Klíčové ukazatele výkonu** pro zobrazení pojmů Glosář a uživatelů katalogu

2. V **hlavních glosářích a počtu prostředků** se zobrazuje prvních 5 pojmů s připojenými prostředky k nim. Všechny ostatní prostředky se v grafu účtují v kategorii jiné.

3. **Glosář termínů podle termínu** zobrazuje distribuci termínů glosáře podle stavu, jako je například "koncept", "Schváleno", "Výstraha" a "vypršela platnost". 

1. Najeďte myší nebo klikněte na řez grafu se stavem a poznamenejte si počet podmínek s tímto stavem.

1. **Distribuce rolí podle počtu uživatelů** zobrazuje distribuci rolí podle počtu uživatelů na roli v dosah.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Zobrazit Glosář Insights":::

## <a name="next-steps"></a>Další kroky

Další informace o sestavách Azure dosah Insight prostřednictvím [Asset Insights](./asset-insights.md)
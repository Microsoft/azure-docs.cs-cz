---
title: Vytváření sestav o příponách souborů pomocí dosah Insights
description: V této příručce se dozvíte, jak zobrazit a používat sestavy rozšíření souborů dosah Insights pro vaše data.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 222106f5838a1eb237987afaa93924f1efe4a1f4
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746280"
---
# <a name="file-extension-insights-about-your-data-from-azure-purview"></a>Přehled o rozšířeních souborů z Azure dosah 

Tato příručka popisuje, jak získat přístup, zobrazit a filtrovat informace o příponách souborů nebo typech souborů, které se nacházejí ve vašich datech.

Mezi podporované zdroje dat patří: Azure Blob Storage, Azure Data Lake Storage (ADLS) fin 1, Azure Data Lake Storage (ADLS) GEN 2.

V této příručce se dozvíte, jak:
> [!div class="checklist"]
> * Spusťte účet dosah z Azure. 
> - Zobrazit informace o rozšířeních souborů pro vaše data
> - Přechod k podrobnostem o dalších rozšířeních souborů pro vaše data

## <a name="prerequisites"></a>Předpoklady 

Než začnete s dosah přehledy, ujistěte se, že jste dokončili následující kroky:

- Nastavte prostředky Azure a vyplníte příslušné účty testovacími daty

- Nastavení a dokončení kontroly dat testu v každém zdroji dat

Další informace najdete v tématu [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md).

## <a name="use-purview-file-extension-insights"></a>Použití přehledů přípon souborů dosah

Při kontrole prostředků dokáže Azure dosah rozpoznat typy souborů nalezené v datovém majetku a zadat další podrobnosti o jednotlivých typech souborů. Podrobnosti zahrnují počet souborů každého typu, které máte, kde jsou tyto soubory, a to, jestli jsou scannable pro citlivá data.

> [!NOTE]
> Až provedete prohledání vašich typů zdrojů, poskytněte novému zdroji přehled o **rozšířeních souborů** o několik hodin.

**Zobrazení rozšíření souboru – přehled:**

1. Přejít na obrazovku **Azure dosah** [instance v Azure Portal](https://aka.ms/purviewportal) a vyberte svůj účet dosah.

1. Na stránce **Přehled** **v části Začínáme** vyberte dlaždici **spustit účet dosah** .

1. V dosah vyberte položku nabídky **přehledy** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: na levé straně, abyste měli přístup k oblasti **Insights** .
    
1. V části **přehledy** vyberte kartu **přípony souborů** .

    V sestavě se zobrazí souhrn toho, kolik jedinečných přípon souborů se našlo, a také graf prvních 10 rozšíření, která se nacházejí ve vybraném časovém období (výchozí hodnota: 30 dnů).

    :::image type="content" source="media/file-extension-insights/file-extension-overview-small.png" alt-text="Sestava přípony souboru – přehled" lightbox="media/file-extension-insights/file-extension-overview.png":::

    Chcete-li zjistit více, proveďte některou z následujících akcí:

    - V horní části sestavy vyberte selektor **času** pro změnu časového rozsahu, pro který byly nalezeny přípony souborů.
    
    - Pokud chcete zobrazit úplný seznam přípon souborů, vyberte **Zobrazit více** pod grafem. Další informace najdete v tématu [Podrobnosti o rozšíření souborů – přehled](#file-extension-insights-drilldown). 

### <a name="file-extension-insights-drilldown"></a>Podrobnosti o rozšíření souborů – přehled

Až si zobrazíte informace vysoké úrovně o typech souborů, které se nacházejí v datovém majetku, můžete přejít k podrobnostem o tom, kde se nacházejí, a zda je možné prohledávat citlivá data.

Příklad:

:::image type="content" source="media/file-extension-insights/file-extension-drilldown-small.png" alt-text="Sestava přípony souboru – podrobné procházení" lightbox="media/file-extension-insights/file-extension-drilldown.png":::

V mřížce se zobrazí podrobnosti o každé nalezené příponě souboru, včetně:

- **Počet souborů**. Počet souborů se zadaným rozšířením.
- **Kontrola obsahu**. Určuje, jestli je přípona souboru podporovaná pro vyhledávání citlivých dat.
- **Odběry**. Počet odběrů, ve kterých bylo nalezeno zadané rozšíření.
- **Zdroje**. Počet nalezených zdrojů se zadanou příponou souboru.



Pomocí filtrů nad mřížkou vyfiltrujte zobrazená data:

|Možnost  |Popis  |
|---------|---------|
|**Filtrovat podle klíčového slova**     |    Zadáním textu do pole **filtrovat podle klíčového slova**  můžete zobrazit filtrování typů souborů podle názvu. Chcete-li například zobrazit pouze soubory PDF, zadejte `PDF` .     |
|**Čas**        | Tuto možnost vyberte, pokud chcete filtrovat podle konkrétního časového rozsahu, kdy se data vytvořila. <br>**Výchozí:** 30 dní  |
|**Přípona souboru**     |Tuto možnost vyberte, pokud chcete mřížku filtrovat podle jednoho nebo více typů souborů.        |
|**Prostředky**    |Tuto možnost vyberte, pokud chcete mřížku filtrovat podle konkrétních zdrojů dat. |
|**Kontrola obsahu**     |Tuto **možnost vyberte** , pokud chcete zobrazit jenom typy souborů, které se dají dále vyskenovat pro citlivá **data, nebo** data, která se nedají kontrolovat, třeba soubory **. CERT** nebo **. jpg** . |
| | |

Nad filtry vyberte **Upravit sloupce** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: a zobrazte více nebo méně sloupců v mřížce nebo změňte pořadí. 

Pokud chcete mřížku seřadit, vyberte záhlaví sloupce, které chcete seřadit podle daného sloupce.
## <a name="next-steps"></a>Další kroky

Další informace o sestavách Azure dosah Insight
> [!div class="nextstepaction"]
> [Glosář přehledů](glossary-insights.md)

> [!div class="nextstepaction"]
> [Vyhledat přehledy](scan-insights.md)

> [!div class="nextstepaction"]
> [Přehledy klasifikace](./classification-insights.md)

> [!div class="nextstepaction"]
> [Přehledy označení citlivosti](sensitivity-insights.md)

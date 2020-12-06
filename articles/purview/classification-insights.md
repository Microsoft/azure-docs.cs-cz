---
title: Vytváření sestav klasifikace pro vaše data s využitím dosah Insights (Preview)
description: Tato příručka popisuje, jak zobrazit a používat vytváření sestav klasifikace dosah Insights pro vaše data.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 553c33b3d5ea2e3f1ee81503cb69fe15db387af6
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745022"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Přehledy klasifikace vašich dat z Azure dosah

V této příručce se dozvíte, jak získat přístup k sestavám přehledu klasifikace dosah a jejich zobrazení a filtrování.

Mezi podporované zdroje dat patří: Azure Blob Storage, Azure Data Lake Storage (ADLS) fin 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure synapse Analytics (dřív SQL DW), Azure SQL Database, Azure SQL Managed instance SQL Server

V této příručce se dozvíte, jak:

> [!div class="checklist"]
> - Spuštění účtu dosah z Azure
> - Zobrazit přehledy o klasifikacích pro vaše data
> - Přejít k podrobnostem o dalších podrobnostech klasifikace vašich dat

## <a name="prerequisites"></a>Předpoklady

Než začnete s dosah přehledy, ujistěte se, že jste dokončili následující kroky:

- Nastavte prostředky Azure a vyplníte příslušné účty testovacími daty

- Nastavení a dokončení kontroly dat testu v každém zdroji dat 

Další informace najdete v tématu [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Použití přehledů klasifikace dosah

Ve službě Azure dosah se klasifikace podobají značkám předmětu a používají se k označení a identifikaci dat určitého typu, který se nachází v rámci vašich datových nemovitostí během kontroly.

Dosah používá stejné typy citlivých informací jako Microsoft 365, což vám umožní roztáhnout stávající zásady zabezpečení a ochranu mezi celou datovou nemovitost.

> [!NOTE]
> Až provedete prohledání vašich typů zdrojů, pojmenujte **klasifikaci s popisem** a provedeme několik hodin, aby odrážely nové prostředky.

**Zobrazení přehledů klasifikace:**

1. Přejít na obrazovku **Azure dosah** [instance v Azure Portal](https://aka.ms/purviewportal) a vyberte svůj účet dosah.

1. Na stránce **Přehled** **v části Začínáme** vyberte dlaždici **spustit účet dosah** .

1. V dosah vyberte položku nabídky **přehledy** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: na levé straně, abyste měli přístup k oblasti **Insights** .

1. V oblasti **přehledy** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: Vyberte možnost **klasifikace** , aby se zobrazila sestava dosah **Classification Insights** .

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Sestava přehledů klasifikace" lightbox="media/insights/select-classification-labeling.png":::

   Hlavní stránka **Přehled klasifikace** zobrazuje následující oblasti:

   |Oblast  |Popis  |
   |---------|---------|
   |**Přehled zdrojů s klasifikací**     |Zobrazí dlaždice, které poskytují: <br>– Počet odběrů nalezených ve vašich datech <br>– Počet jedinečných klasifikací nalezených ve vašich datech <br>– Počet nalezených klasifikovaných zdrojů <br>– Počet nalezených klasifikovaných souborů <br>– Počet nalezených klasifikovaných tabulek         |
   |**Hlavní zdroje s utajovanými daty (posledních 30 dní)**     |Zobrazuje trend, za posledních 30 dní, počtu zdrojů nalezených s utajovanými daty.            |
   |**Hlavní kategorie klasifikace podle zdrojů**     |Zobrazuje počet zdrojů nalezených podle kategorie klasifikace, jako je **finanční** nebo **státní správa**.      |
   |**Horní klasifikace souborů**     |Zobrazuje nejvyšší klasifikace použité u souborů ve vašich datech, jako jsou čísla kreditních karet nebo národní identifikační čísla.         |
   |**Horní klasifikace pro tabulky**     | Zobrazuje nejvyšší klasifikace použité u tabulek ve vašich datech, jako jsou osobní identifikační údaje. |   
   |  **Aktivita klasifikace** <br>(soubory a tabulky) |  Zobrazuje samostatné grafy pro soubory a tabulky, z nichž každý zobrazuje počet souborů nebo tabulek klasifikovaných v rámci vybraného časového období. <br>**Výchozí**: 30 dní<br>Vyberte filtr **času** nad grafy a vyberte jiný časový rámec, který chcete zobrazit.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Podrobnosti o klasifikacích přehledů

V některém z následujících grafů **klasifikace klasifikace** vyberte odkaz **Zobrazit další** a přejděte k podrobnostem, kde najdete další podrobnosti:

- **Hlavní kategorie klasifikace podle zdrojů**
- **Horní klasifikace souborů**
- **Horní klasifikace pro tabulky**
- **Klasifikační aktivita > klasifikační data**

Příklad:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Zobrazit všechny klasifikace" lightbox="media/insights/view-classifications.png":::

Další informace najdete v následujících informacích:

|Možnost  |Popis  |
|---------|---------|
|**Filtrování dat**     |  Pomocí filtrů nad mřížkou můžete filtrovat zobrazená data, včetně názvu klasifikace, názvu předplatného nebo typu zdroje. <br><br>Pokud si nejste jisti přesným názvem klasifikace, můžete zadat část nebo celý název do pole **filtrovat podle klíčového slova** .       |
|**Seřadit mřížku** |Vyberte záhlaví sloupce pro řazení mřížky podle daného sloupce. | 
|**Upravit sloupce**     |  Chcete-li zobrazit více nebo méně sloupců v mřížce, vyberte možnost **Upravit sloupce** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: a potom vyberte sloupce, které chcete zobrazit nebo změnit pořadí.   |
|**Přejít k podrobnostem**     | Chcete-li přejít k podrobnostem konkrétní klasifikace, vyberte název ve sloupci **klasifikace** , abyste zobrazili sestavu **klasifikace podle zdrojové** sestavy. <br><br>Tato sestava zobrazí data vybrané klasifikace, včetně názvu zdroje, typu zdroje, ID předplatného a počtu klasifikovaných souborů a tabulek.      |
|**Procházet prostředky**     |  Pokud chcete procházet prostředky nalezené s konkrétní klasifikací nebo zdrojem, vyberte klasifikaci nebo zdroj v závislosti na sestavě, kterou jste zobrazili, a pak vyberte **Procházet assety** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: nad filtry. <br><br>Ve výsledcích hledání se zobrazí všechny klasifikované assety, které jsou pro vybraný filtr nalezeny.  Další informace najdete v tématu [hledání Data Catalog v Azure dosah](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Další kroky

Další informace o sestavách Azure dosah Insight
> [!div class="nextstepaction"]
> [Glosář přehledů](glossary-insights.md)

> [!div class="nextstepaction"]
> [Vyhledat přehledy](scan-insights.md)

> [!div class="nextstepaction"]
> [Přehledy označování citlivosti](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Přehledy přípon souborů](file-extension-insights.md)
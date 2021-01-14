---
title: Označení citlivosti vytváření sestav pro vaše data v Azure dosah pomocí dosah Insights
description: V této příručce se dozvíte, jak zobrazit a použít označení citlivosti dosah při vytváření sestav pro vaše data.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: dffecb48a8faa869cb3df450cc220e86195bbc87
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98199372"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Přehledy o vašich datech v Azure dosah s popisem citlivosti

Tato příručka popisuje, jak získat přístup, zobrazit a filtrovat přehledy zabezpečení poskytované na základě popisků citlivosti použitých pro vaše data.

Mezi podporované zdroje dat patří: Azure Blob Storage, Azure Data Lake Storage (ADLS) fin 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, spravovaná instance Azure SQL.

V této příručce se dozvíte, jak:

> [!div class="checklist"]
> - Spusťte účet dosah z Azure.
> - Zobrazení citlivosti popisků při označování dat
> - Přechod k podrobnostem o dalších citlivostech týkajících se označování dat

> [!NOTE]
> Popisky citlivosti nalezené v [Power BIch prostředcích](register-scan-power-bi-tenant.md) , které jsou prohledávány pomocí dosah, nejsou aktuálně zobrazeny v sestavě citlivostní označování. 
>
> Chcete-li zobrazit popisky citlivosti Power BI assety, zobrazte Asset v [Data Catalog dosah](how-to-search-catalog.md).
> 
## <a name="prerequisites"></a>Požadavky

Než začnete s dosah přehledy, ujistěte se, že jste dokončili následující kroky:

- Nastavte prostředky Azure a vyplníte příslušné účty testovacími daty

- [Rozšířená Microsoft 365 popisky citlivosti na assety v Azure dosah](create-sensitivity-label.md)a vytvořily nebo vybraly popisky, které chcete použít pro vaše data.

- Nastavení a dokončení kontroly dat testu v každém zdroji dat

- Přihlásili jste se k dosah pomocí účtu s [rolí data Reader nebo data kurátor](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles).

Další informace najdete v tématech [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md) a [Automatické označování dat v Azure dosah](create-sensitivity-label.md).

## <a name="use-purview-sensitivity-labeling-insights"></a>Použití dosah s popisem citlivosti

V dosah se klasifikace podobají značkám předmětu a používají se k označení a identifikaci dat určitého typu, který se nachází v rámci vašich datových nemovitostí během kontroly.

Popisky citlivosti umožňují určit, jak citlivá data jsou ve vaší organizaci. Konkrétní název projektu může být například vysoce důvěrný v rámci vaší organizace, zatímco stejný termín není důvěrný pro jiné organizace. 

Klasifikace se shodují přímo, jako je číslo sociálního pojištění, které má klasifikaci **čísla sociálního pojištění**. 

Na rozdíl od jsou popisky citlivosti aplikovány, když se jedna nebo více klasifikací a podmínek společně nacházejí. V tomto kontextu [podmínky](/microsoft-365/compliance/apply-sensitivity-label-automatically) odkazují na všechny parametry, které můžete definovat pro nestrukturovaná data, jako je například **blízkost jiné klasifikace**, a **spolehlivost%**. 

Dosah používá stejné klasifikace, označované také jako [typy citlivých informací](/microsoft-365/compliance/sensitive-information-type-entity-definitions), jak Microsoft 365. To vám umožní roztáhnout stávající popisky citlivosti napříč prostředky Azure dosah.

> [!NOTE]
> Po prohledání vašich typů zdrojů popište **citlivostní označování** o několik hodin, aby odrážely nové prostředky.

**Chcete-li zobrazit přehledy označování citlivosti:**

1. Přejít na domovskou stránku **Azure dosah** .

1. Na stránce **Přehled** **v části Začínáme** vyberte dlaždici **spustit účet dosah** .

1. V dosah vyberte položku nabídky **přehledy** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: na levé straně, abyste měli přístup k oblasti **Insights** .

1. V oblasti **přehledy** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: Vyberte možnost **popisky citlivosti** , aby se zobrazila sestava dosah **citlivostní analýzy popisků** .

    > [!NOTE]
    > Pokud je tato sestava prázdná, možná jste nerozšířili popisky citlivosti na Azure dosah. Další informace najdete v tématu [automatické označení dat v Azure dosah](create-sensitivity-label.md).

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="Přehledy označování citlivosti" lightbox="media/insights/sensitivity-labeling-insights.png":::

   Hlavní stránka s **popisem citlivosti popisky** zobrazuje následující oblasti:

   |Plošný  |Description  |
   |---------|---------|
   |**Přehled zdrojů s popisky citlivosti**     |Zobrazí dlaždice, které poskytují: <br>– Počet odběrů nalezených ve vašich datech. <br>– Počet jedinečných popisků citlivosti použitých na vaše data <br>– Počet zdrojů s použitými popisky citlivosti <br>– Počet souborů a tabulek nalezených s použitými popisky citlivosti|
   |**Hlavní zdroje s popisky dat (posledních 30 dní)**     | Zobrazuje trend, za posledních 30 dní, z počtu zdrojů s použitými popisky citlivosti.       |
   |**Horní popisky aplikované napříč zdroji**     |Zobrazuje horní popisky aplikované napříč všemi Dosahmi datovými zdroji. |
   |**Horní popisky použité u souborů**     |Zobrazuje horní popisky citlivosti použité u souborů ve vašich datech.          |
   |**Horní popisky použité v tabulkách**     | Zobrazuje horní popisky citlivosti použité pro tabulky databáze ve vašich datech. |   
   |  **Aktivita označování**  |  Zobrazuje samostatné grafy pro soubory a tabulky, z nichž každý zobrazuje počet souborů nebo tabulek označených přes vybraný časový rámec. <br>**Výchozí**: 30 dní<br>Vyberte filtr **času** nad grafy a vyberte jiný časový rámec, který chcete zobrazit.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>Citlivostní označování podrobností – přehled

V některém z níže uvedených **citlivostních grafů s popisem přehledů** vyberte odkaz **Zobrazit další** a přejděte k podrobnostem, kde najdete další podrobnosti:

- **Horní popisky aplikované napříč zdroji**
- **Horní popisky použité u souborů**
- **Horní popisky použité v tabulkách**
- **Označení aktivity označování > s popisky dat**

Příklad:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="Podrobnosti popisku citlivosti" lightbox="media/insights/sensitivity-label-drilldown.png":::

Další informace najdete v následujících informacích:

|Možnost  |Popis  |
|---------|---------|
|**Filtrování dat**     |  Pomocí filtrů nad mřížkou můžete filtrovat zobrazená data, včetně názvu štítku, názvu předplatného nebo typu zdroje. <br><br>Pokud si nejste jisti přes přesný název popisku, můžete zadat část nebo celý název do pole **filtrovat podle klíčového slova** .       |
|**Seřadit mřížku** |Vyberte záhlaví sloupce pro řazení mřížky podle daného sloupce. | 
|**Upravit sloupce**     |  Chcete-li zobrazit více nebo méně sloupců v mřížce, vyberte možnost **Upravit sloupce** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: a potom vyberte sloupce, které chcete zobrazit nebo změnit pořadí.    <br><br>Vyberte záhlaví sloupce pro řazení mřížky podle daného sloupce.   |
|**Přejít k podrobnostem**     | Chcete-li přejít k podrobnostem konkrétního popisku, vyberte název ve sloupci **popisek citlivosti** , aby se zobrazil **popisek podle zdrojové** sestavy. <br><br>Tato sestava zobrazí data pro vybraný popisek, včetně názvu zdroje, typu zdroje, ID předplatného a počtu klasifikovaných souborů a tabulek.      |
|**Procházet prostředky**     |  Pokud chcete procházet prostředky nalezené pomocí konkrétního popisku nebo zdroje, vyberte jeden nebo více popisků nebo zdrojů v závislosti na sestavě, kterou jste právě prohlíželi, a pak vyberte **Procházet assety** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: nad filtry. <br><br>Ve výsledcích hledání se zobrazí všechny nalezené prostředky označené pro vybraný filtr.  Další informace najdete v tématu [hledání Data Catalog v Azure dosah](how-to-search-catalog.md).       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Integrace označení citlivosti s Microsoft 365 dodržování předpisů

Integrace se službou [Microsoft Information Protection](/microsoft-365/compliance/information-protection) nabídnutou v Microsoft 365 znamená, že dosah umožňuje přímým způsobům rozšiřování viditelnosti na vaši datovou plochu a klasifikace a označování dat.

Aby byly popisky citlivosti Microsoft 365 rozšířeny na vaše assety ve službě Azure dosah, musíte v centru dodržování předpisů Microsoft 365 aktivně zapnout Information Protection pro Azure dosah.

Další informace najdete v tématu [automatické označení dat v Azure dosah](create-sensitivity-label.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o těchto sestavách Azure dosah Insight:

- [Glosář přehledů](glossary-insights.md)
- [Vyhledat přehledy](scan-insights.md)
- [Přehledy klasifikace](./classification-insights.md)
- [Přehledy přípon souborů](file-extension-insights.md)

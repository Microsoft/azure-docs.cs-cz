---
title: Přehledy assetů na vašich datech v Azure dosah (Preview)
description: Tato příručka popisuje, jak zobrazit a používat vytváření sestav assetů dosah Insights pro vaše data.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 5045a13bb932f5907584bae23e956374a5757a68
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553099"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Přehledy assetů na vašich datech v Azure dosah

Tato příručka popisuje, jak získat přístup k sestavám dosah Asset Insights pro vaše data, zobrazit je a filtrovat.

V této příručce se dozvíte, jak:

* Podívejte se na přehledy z vašeho účtu dosah.
* Získejte pohled na vaše data v pohledech na oči.
* Podrobnosti o dalších podrobnostech o počtu prostředků najdete v podrobnostech.

## <a name="prerequisites"></a>Předpoklady

Než začnete s dosah přehledy, ujistěte se, že jste dokončili následující kroky:

* Nastavte prostředky Azure a naplňte účet daty.

* Nastavte a dokončete kontrolu zdrojového typu.

Další informace najdete v tématu [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md).

## <a name="use-purview-asset-insights"></a>Použití dosah Asset Insights

V Azure dosah můžete registrovat a kontrolovat zdrojové typy. Po dokončení kontroly si můžete prohlédnout distribuci assetů v Asset Insights, která vám oznamuje stav majetku podle klasifikace a sad prostředků. Také vás upozorní, pokud dojde ke změně velikosti dat.

> [!NOTE]
> Po prohledání vašich typů zdrojů poskytněte Asset Insights až hodinu, aby odrážela nové prostředky.

1. V Azure Portal přejděte do svého prostředku Azure dosah.

1. Na stránce **Přehled** **v části Začínáme** vyberte dlaždici **otevřít dosah Studio** .

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Spustit dosah z Azure Portal":::

1. Na **domovské** stránce dosah vyberte dlaždici **Zobrazit přehledy** , abyste měli přístup k oblasti **Insights** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Podívejte se na přehledy v Azure Portal":::

1. V oblasti **přehledy** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: Vyberte **assety** . zobrazí se sestava dosah **Asset Insights** .

### <a name="view-asset-insights"></a>Zobrazit přehledy Asset Insights

1. Na stránce hlavní informace o službě **Asset Insights** se zobrazí následující oblasti:

2. Klíčový ukazatel výkonu na nejvyšší úrovni pro zobrazení typů zdrojů, klasifikovaných prostředků a zjištěných prostředků
 
3. První graf znázorňuje **prostředky na zdrojový typ**.

4. Zobrazení distribuce prostředků podle typu zdroje. Vyberte klasifikaci nebo celou kategorii klasifikace, abyste viděli distribuci prostředků podle typu zdroje. 
 
5. Pokud chcete zobrazit další informace, vyberte **Zobrazit další**, které zobrazí tabelární formu typů zdrojů a počet assetů. Do této stránky se přenesou klasifikační filtry.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Zobrazení klíčových ukazatelů výkonu a grafu v Asset Insights":::
 
6. Vyberte konkrétní zdroj, pro který chcete zobrazit horní složky s počty assetů. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="Vybrat typ zdroje":::
 
7. Vyberte celkové prostředky v horní složce v rámci vybraného typu zdroje.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="Zobrazení cest k souborům":::

8. Zobrazení seznamu souborů v rámci složky. Přejděte zpět na přehledy pomocí crumbsu chleba.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="Zobrazit seznam assetů":::  

### <a name="file-based-source-types"></a>Souborové typy zdrojů
Další pár grafů v Asset Insights ukazuje distribuci typů zdrojů založených na souborech. První graf označovaný jako **trend velikosti (GB) typu souboru v rámci typů zdrojů** zobrazuje trend velikosti jednotlivých typů souborů v posledních 30 dnech. 
 
1. Vyberte zdrojový typ pro zobrazení typu souboru v rámci zdroje. 
 
1. Výběrem **Zobrazit další** zobrazíte aktuální velikost dat, změnu velikosti, aktuální počet assetů a změnu v počtu assetů.
 
   > [!NOTE]
   > Pokud se kontrola spustila jenom jednou za posledních 30 dní, nebo se kterákoli změna v katalogu, jako je přidání nebo odebrání klasifikace, stala jenom jednou za 30 dní, pak se výše uvedené informace o změně zobrazí jako prázdné.

1. Pokud kliknete na typ zdroje, podívejte se na horní složky se změnou počtu prostředků na nejvyšší úrovni.

1. Vyberte cestu, pro kterou chcete zobrazit seznam assetů.

Druhý graf v typech zdrojových souborů je **_soubory, které nejsou přidruženy k sadě prostředků_**. Pokud očekáváte, že se všechny soubory budou zavádět do sady prostředků, tento graf vám může porozumět tomu, které prostředky nebyly zahrnuty. Chybějící prostředky může být označením chybného vzoru souboru ve složce. Chcete-li zobrazit další podrobnosti o souborech, postupujte podle stejných kroků jako v jiných grafech.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="Zobrazení prostředků založených na souborech":::  

## <a name="next-steps"></a>Další kroky

Další informace o sestavách Azure dosah Insight s [přehledem vyhledávání](./scan-insights.md)

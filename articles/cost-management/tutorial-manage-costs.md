---
title: "Kurz – Správa nákladů pomocí služby Azure Cost Management | Microsoft Docs"
description: "V tomto kurzu zjistíte, jak spravovat náklady s využitím přidělování nákladů a sestav metod showback a chargeback."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/27/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 7e9f11e702dbbe8864f8a0c307777c922e5f028d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-manage-costs-by-using-azure-cost-management"></a>Kurz: Správa nákladů pomocí služby Azure Cost Management

Ve službě Azure Cost Management od Cloudyn spravujete náklady a vytváříte sestavy metody showback přidělováním nákladů na základě značek. Proces přidělování nákladů přiřazuje náklady ke spotřebovaným cloudovým prostředkům. K úplnému přidělení nákladů dojde, když jsou všechny prostředky uspořádané do kategorií pomocí značek. Po přidělení nákladů můžete svým uživatelům prostřednictvím řídicích panelů a sestav poskytnout metodu showback nebo chargeback. Když však začnete používat službu Cost Management, řada prostředků nemusí být označených nebo označení nemusí podporovat.

Například si můžete chtít nechat uhradit náklady na vytváření. Musíte být schopni svému technickému týmu ukázat, že potřebujete konkrétní částku v závislosti na nákladech na prostředky. Můžete jim ukázat sestavu všech spotřebovaných prostředků označených značkou *engineering* (vytváření).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Používat vlastní značky k přidělování nákladů.
> * Vytvářet sestavy metod showback a chargeback.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít zaregistrovanou zkušební verzi nebo placené předplatné služby Azure Cost Management.

## <a name="use-custom-tags-to-allocate-costs"></a>Použití vlastních značek k přidělování nákladů

Když začnete s přidělováním nákladů, první věc, kterou je potřeba udělat, je definovat rozsah s použitím modelu nákladů. Model nákladů náklady nemění, ale distribuuje je. Při vytváření modelu nákladů rozdělíte svá data podle entity nákladů, účtu nebo předplatného a několika značek. Mezi běžné příklady značek může patřit kód pro fakturaci, nákladové středisko nebo název skupiny. Značky pomáhají také provádět showback a chargeback do jiných částí organizace.

Pokud chcete vytvořit vlastní model přidělování nákladů, vyberte v nabídce sestavy **Cost** (Náklady) &gt; **Cost Management** (Správa nákladů) &gt; **Cost Allocation 360°** (360° přidělování nákladů).

![Výběr možnosti Cost Allocation 360° (360° přidělování nákladů)](./media/tutorial-manage-costs/cost-allocation-360.png)

Na stránce **Cost Allocation 360°** (360° přidělování nákladů) vyberte **Add** (Přidat) a pak zadejte název a popis modelu nákladů. Vyberte všechny účty nebo jednotlivé účty. Pokud chcete použít jednotlivé účty, můžete vybrat několik účtů od několika poskytovatelů cloudových služeb. Dále klikněte na **Categorization** (Kategorizace) a ze zjištěných značek zvolte ty, které kategorizují vaše data nákladů. Zvolte značky (kategorie), které chcete zahrnout do svého modelu. V následujícím příkladu je vybraná značka **Unit** (Jednotka).

![Příklad kategorizace modelu nákladů](./media/tutorial-manage-costs/cost-model01.png)



Příklad ukazuje, že 14 444 USD není zařazeno do kategorií (nemá značku).

Dále vyberte **Uncategorized Resources** (Prostředky nezařazené do kategorií) a vyberte služby s nepřidělenými náklady. Pak definujte pravidla pro přidělení prostředků.

Můžete například vzít náklady na úložiště Azure a rovnoměrně je distribuovat na virtuální počítače Azure. Pokud to chcete provést, vyberte službu **Azure/Storage**, pak možnost **Proportional to Categorized** (Proporční na kategorizované) a pak vyberte **Azure/VM**. Potom vyberte **Create** (Vytvořit).

![Příklad pravidla přidělování v modelu nákladů pro rovnoměrnou distribuci](./media/tutorial-manage-costs/cost-model02.png)



V jiném příkladu můžete chtít přidělit veškeré náklady na síť Azure ke konkrétní obchodní jednotce v rámci organizace. Pokud to chcete provést, vyberte službu **Azure/Network** a pak vyberte možnost **Explicit Distribution** (Explicitní distribuce). Potom nastavte podíl distribuce v procentech na 100 a vyberte obchodní jednotku – na následujícím obrázku je to **G&amp;A**:

![Příklad pravidla přidělování v modelu nákladů pro konkrétní obchodní jednotku](./media/tutorial-manage-costs/cost-model03.png)



Pro všechny zbývající prostředky nezařazené do kategorií vytvořte další pravidla přidělování.

Pokud máte nějaké nepřidělené rezervované instance Amazon Web Services (AWS), můžete je přiřadit k označeným kategoriím s **rezervovanými instancemi**.

Pokud chcete zobrazit informace o provedených volbách pro přidělování nákladů, vyberte **Summary** (Souhrn). Pokud chcete uložit informace a pokračovat v práci na dalších pravidlech později, vyberte **Save As Draft** (Uložit jako koncept). Případně pokud chcete uložit informace a nechat Cloudyn zahájit zpracování modelu přidělování nákladů, vyberte **Save and Activate** (Uložit a aktivovat).

V seznamu modelů nákladů se zobrazí váš nový model nákladů a jeho **Processing status** (Stav zpracování). Aktualizace databáze Cloudyn o váš model nákladů může nějakou dobu trvat. Po dokončení zpracování se stav aktualizuje na **Completed** (Dokončeno). Pak můžete zobrazit data ze svého modelu nákladů v sestavě analýzy nákladů v části **Extended Filters** (Rozšířené filtry) &gt; **Cost Model** (Model nákladů).

### <a name="category-manager"></a>Category Manager (Správce kategorií)

Category Manager (Správce kategorií) je nástroj pro čištění dat, který pomáhá slučováním hodnot v několika kategoriích (značkách) vytvářet nové kategorie. Je to jednoduchý nástroj založený na pravidlech, ve kterém vyberete kategorii a vytvoříte pravidla pro sloučení stávajících hodnot. Například můžete mít existující kategorie **R&amp;D** a **dev**, které obě představují vývojovou skupinu.

Na portálu Cloudyn klikněte na symbol ozubeného kolečka v pravém horním rohu a vyberte možnost **Category Manager** (Správce kategorií). Pokud chcete vytvořit novou kategorii, vyberte symbol plus (**+**). Zadejte název kategorie a pak v části **Keys** (Klíče) zadejte klíče kategorií, které chcete do nové kategorie zahrnout.

Při definování pravidla můžete přidat více hodnot s použitím podmínky OR. Můžete také provádět několik základních operací s řetězci. V každém případě klikněte na symbol tří teček (**...**) napravo od položky **Rule** (Pravidlo).

Pokud chcete definovat nové pravidlo, v oblasti **Rules** (Pravidla) vytvořte nové pravidlo. Zadejte například **dev** v části **Rules** (Pravidla) a pak zadejte **R&amp;D** v části **Actions** (Akce). Jakmile budete hotovi, uložte novou kategorii.

Následující obrázek ukazuje příklad pravidel vytvořených pro novou kategorii **Work-Load**:

![Příklad kategorie](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Označování zdrojů a sestav

Data značek, která se zobrazují v sestavách Cloudyn, pocházejí ze tří míst:

- Rozhraní API poskytovatele cloudu pro práci s prostředky
- Rozhraní API poskytovatele cloudu pro fakturaci
- Ručně vytvořené značky z následujících zdrojů:
    - Značky entit Cloudyn – uživatelsky definovaná metadata použitá na entity Cloudyn.
    - Category Manager (Správce kategorií) – nástroj pro čištění dat, který vytváří nové značky na základě pravidel použitých na existující značky.

Pokud chcete v sestavách nákladů Cloudyn zobrazit značky poskytovatele cloudu, musíte vytvořit vlastní model přidělování nákladů pomocí možnosti Cost Allocation 360° (360° přidělování nákladů). Pokud to chcete provést, přejděte do **Cost** (Náklady) > **Cost Management** (Správa nákladů) > **Cost Allocation 360** (360° přidělování nákladů), vyberte požadované značky a pak definujte pravidla pro zpracování neoznačených nákladů. Pak vytvořte nový model nákladů. Následně můžete podle značek prostředků Azure zobrazit, filtrovat a řadit sestavy v části Cost Allocation Analysis (Analýza přidělování nákladů).

Značky prostředků Azure se zobrazí pouze v sestavách **Cost Allocation Analysis** (Analýza přidělování nákladů).

Značky fakturace poskytovatele cloudu se zobrazí ve všech sestavách nákladů.

Značky entit Cloudyn a ručně vytvořené značky se zobrazí ve všech sestavách nákladů.


## <a name="create-showback-and-chargeback-reports"></a>Vytváření sestav metod showback a chargeback

Metody, pomocí kterých organizace provádějí showback a chargeback, se výrazně liší. Pro oba účely však můžete jako základ použít jakékoli řídicí panely a sestavy na portálu Cloudyn. Komukoli v rámci organizace můžete poskytnout uživatelský přístup, aby mohl řídicí panely a sestavy zobrazit na vyžádání. Všechny sestavy analýzy nákladů podporují showback, protože uživatelům ukazují prostředky, které spotřebovali. Navíc umožňují uživatelům zobrazit podrobné informace o nákladech nebo využití specifické pro jejich skupinu v rámci organizace.

Pokud chcete zobrazit výsledky přidělování nákladů, otevřete sestavu analýzy nákladů a vyberte model nákladů, který jste vytvořili. Pak přidejte seskupení podle jedné nebo několika značek vybraných v modelu nákladů.

![Sestava analýzy nákladů](./media/tutorial-manage-costs/cost-analysis.png)

Můžete snadno vytvářet a ukládat sestavy zaměřené na konkrétní služby spotřebované konkrétními skupinami. Například můžete mít oddělení, které ve velké míře využívá virtuální počítače Azure. Můžete vytvořit sestavu s filtrem na virtuální počítače Azure, ve které se zobrazí spotřeba a náklady.

Pokud potřebujete poskytnout data snímků jiným týmům, můžete jakoukoli sestavu exportovat ve formátu PDF nebo CSV.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Používat vlastní značky k přidělování nákladů.
> * Vytvářet sestavy metod showback a chargeback.



V dalším kurzu se dozvíte, jak řídit přístup k datům.

> [!div class="nextstepaction"]
> [Řízení přístupu k datům](tutorial-user-access.md)

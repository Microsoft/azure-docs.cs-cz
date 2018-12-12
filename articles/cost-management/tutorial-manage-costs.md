---
title: Kurz – Správa nákladů pomocí Cloudyn v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak spravovat náklady s využitím přidělování nákladů a sestav metod showback a chargeback.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/07/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: 02ff1822bab0abb4850541135a6ea51ea0e7119f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083559"
---
# <a name="tutorial-manage-costs-by-using-cloudyn"></a>Kurz: Správa nákladů pomocí Cloudyn

Ve službě Cloudyn spravujete náklady a vytváříte sestavy metody showback přidělováním nákladů na základě značek. Proces přidělování nákladů přiřazuje náklady ke spotřebovaným cloudovým prostředkům. K úplnému přidělení nákladů dojde, když jsou všechny prostředky uspořádané do kategorií pomocí značek. Po přidělení nákladů můžete svým uživatelům prostřednictvím řídicích panelů a sestav poskytnout metodu showback nebo chargeback. Když ale začnete používat Cloudyn, řada prostředků nemusí být označených nebo označení nemusí podporovat.

Například si můžete chtít nechat uhradit náklady na vytváření. Musíte být schopni svému technickému týmu ukázat, že potřebujete konkrétní částku v závislosti na nákladech na prostředky. Můžete jim ukázat sestavu všech spotřebovaných prostředků označených značkou *engineering* (vytváření).

V tomto článku se značky a kategorie někdy používají jako synonyma. Kategorie jsou rozsáhlé kolekce, které mohou představovat spoustu věcí. Mohou sem patřit obchodní jednotky, nákladová centra, webové služby nebo cokoli, co je opatřené značkou. Značky jsou dvojice název/hodnota, pomocí kterých můžete kategorizovat prostředky a zobrazovat a spravovat souhrnné fakturační údaje. Stačí, když u několika prostředků a skupin prostředků použijete stejnou značku. Ve starších verzích webu Azure Portal se *název značky* označoval jako *klíč*. Značky se vytvářejí pro jedno předplatné Azure, ve kterém jsou uložené. V AWS se značky skládají z dvojic klíč/hodnota. Protože v Azure i AWS se používá termín *klíč*, používá tento termín také Cloudyn. Správce kategorií používá klíče (názvy značek) ke sloučení značek.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Používat vlastní značky k přidělování nákladů.
> * Vytvářet sestavy metod showback a chargeback.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít buď zaregistrovanou zkušební verzi, nebo placené předplatné Cloudyn.
- [Neaktivované účty se musí aktivovat](activate-subs-accounts.md) na portálu Cloudyn.
- Na virtuálních počítačích musí být povolené [monitorování na úrovni hosta](azure-vm-extended-metrics.md).


## <a name="use-custom-tags-to-allocate-costs"></a>Použití vlastních značek k přidělování nákladů

Cloudyn získává data značek skupin prostředků z Azure a automaticky předává informace o značkách prostředkům. V přidělení nákladů vidíte náklady podle značek prostředků.

Při použití tohoto modelu přidělování nákladů definujete kategorie (značky), které se interně aplikují na nekategorizované (neoznačené) prostředky a umožňují seskupit náklady a definovat pravidla pro nakládání s neoznačenými náklady. Pravidla přidělování nákladů jsou vámi uložené pokyny, jak se mají náklady na službu rozúčtovat na nějakou jinou službu. Při výběru vytvořeného modelu se následně u těchto prostředků zobrazí značky/kategorie v sestavách *přidělení nákladů*.

Mějte na paměti, že informace o značkách se u těchto prostředků nezobrazují v sestavách *analýzy nákladů*. Značky použité v Cloudyn pomocí přidělování nákladů se navíc neodesílají do Azure, takže je neuvidíte na webu Azure Portal.

Když začnete s přidělováním nákladů, první věc, kterou je potřeba udělat, je definovat rozsah s použitím modelu nákladů. Model nákladů náklady nemění, ale distribuuje je. Při vytváření modelu nákladů rozdělíte svá data podle entity nákladů, účtu nebo předplatného a několika značek. Mezi běžné příklady značek může patřit kód pro fakturaci, nákladové středisko nebo název skupiny. Značky pomáhají také provádět showback a chargeback do jiných částí organizace.

Pokud chcete vytvořit vlastní model přidělování nákladů, vyberte v nabídce sestavy **Costs (Náklady)** &gt; **Cost Management (Správa nákladů)** &gt; **Cost Allocation 360° (360° přidělování nákladů)**.

![Příklad zobrazující řídicí panel, kde vyberete Cost Allocation 360](./media/tutorial-manage-costs/cost-allocation-360.png)

Na stránce **Cost Allocation 360°** (360° přidělování nákladů) vyberte **Add** (Přidat) a pak zadejte název a popis modelu nákladů. Vyberte všechny účty nebo jednotlivé účty. Pokud chcete použít jednotlivé účty, můžete vybrat několik účtů od několika poskytovatelů cloudových služeb. Dále klikněte na **Categorization** (Kategorizace) a ze zjištěných značek zvolte ty, které kategorizují vaše data nákladů. Zvolte značky (kategorie), které chcete zahrnout do svého modelu. V následujícím příkladu je vybraná značka **Unit** (Jednotka).

![Kategorizace modelu nákladů zobrazující příklad](./media/tutorial-manage-costs/cost-model01.png)

Příklad ukazuje, že 19 680 USD není zařazeno do kategorií (nemá značku).

Dále vyberte **Uncategorized Resources** (Prostředky nezařazené do kategorií) a vyberte služby s nepřidělenými náklady. Pak definujte pravidla pro přidělení prostředků.

Můžete například vzít náklady na úložiště Azure a rovnoměrně je distribuovat na virtuální počítače Azure. Pokud to chcete provést, vyberte službu **Azure/Storage**, pak možnost **Proportional to Categorized** (Proporční na kategorizované) a pak vyberte **Azure/VM**. Potom vyberte **Create** (Vytvořit).

![Příklad pravidla přidělování v modelu nákladů pro rovnoměrnou distribuci](./media/tutorial-manage-costs/cost-model02.png)



V jiném příkladu můžete chtít přidělit veškeré náklady na síť Azure ke konkrétní obchodní jednotce v rámci organizace. Uděláte to tak, že vyberete službu **Azure/Network** a pak v oblasti **Define Allocation Rule** (Definovat pravidlo přidělování) vyberete **Explicit Distribution** (Explicitní rozúčtování). Potom nastavte podíl distribuce v procentech na 100 a vyberte obchodní jednotku – na následujícím obrázku je to **G&amp;A**:

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

![Příklad zobrazující novou kategorii pracovní zátěže](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Označování zdrojů a sestav

Data značek, která se zobrazují v sestavách Cloudyn, pocházejí ze tří míst:

- Rozhraní API poskytovatele cloudu pro práci s prostředky
- Rozhraní API poskytovatele cloudu pro fakturaci
- Ručně vytvořené značky z následujících zdrojů:
    - Značky entit Cloudyn – uživatelsky definovaná metadata použitá na entity Cloudyn.
    - Category Manager (Správce kategorií) – nástroj pro čištění dat, který vytváří nové značky na základě pravidel použitých na existující značky.

Pokud chcete v sestavách nákladů Cloudyn zobrazit značky poskytovatele cloudu, musíte vytvořit vlastní model přidělování nákladů pomocí možnosti Cost Allocation 360° (360° přidělování nákladů). Uděláte to tak, že přejdete na **Costs (Náklady)** > **Cost Management (Správa nákladů)** > **Cost Allocation 360 (360° přidělování nákladů)**, vyberete požadované značky a pak definujete pravidla pro nakládání s neoznačenými náklady. Pak vytvořte nový model nákladů. Následně můžete podle značek prostředků Azure zobrazit, filtrovat a řadit sestavy v části Cost Allocation Analysis (Analýza přidělování nákladů).

Značky prostředků Azure se zobrazují jen v sestavách **Costs (Náklady)** > **Cost Allocation Analysis (Analýza přidělování nákladů)**.

Značky fakturace poskytovatele cloudu se zobrazí ve všech sestavách nákladů.

Značky entit Cloudyn a ručně vytvořené značky se zobrazí ve všech sestavách nákladů.


## <a name="create-showback-and-chargeback-reports"></a>Vytváření sestav metod showback a chargeback

Metody, pomocí kterých organizace provádějí showback a chargeback, se výrazně liší. Pro oba účely však můžete jako základ použít jakékoli řídicí panely a sestavy na portálu Cloudyn. Komukoli v rámci organizace můžete poskytnout uživatelský přístup, aby mohl řídicí panely a sestavy zobrazit na vyžádání. Všechny sestavy analýzy nákladů podporují showback, protože uživatelům ukazují prostředky, které spotřebovali. Navíc umožňují uživatelům zobrazit podrobné informace o nákladech nebo využití specifické pro jejich skupinu v rámci organizace.

Pokud chcete zobrazit výsledky přidělování nákladů, otevřete sestavu analýzy nákladů a vyberte model nákladů, který jste vytvořili. Pak přidejte seskupení podle jedné nebo několika značek vybraných v modelu nákladů.

![Sestava analýzy nákladů ukazuje příklad data z nového nákladů](./media/tutorial-manage-costs/cost-analysis.png)

Můžete snadno vytvářet a ukládat sestavy zaměřené na konkrétní služby spotřebované konkrétními skupinami. Například můžete mít oddělení, které ve velké míře využívá virtuální počítače Azure. Můžete vytvořit sestavu s filtrem na virtuální počítače Azure, ve které se zobrazí spotřeba a náklady.

Pokud potřebujete poskytnout data snímků jiným týmům, můžete jakoukoli sestavu exportovat ve formátu PDF nebo CSV.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Používat vlastní značky k přidělování nákladů.
> * Vytvářet sestavy metod showback a chargeback.



V dalším kurzu se dozvíte, jak řídit přístup k datům.

> [!div class="nextstepaction"]
> [Řízení přístupu k datům](tutorial-user-access.md)

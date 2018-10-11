---
title: Kurz – Optimalizace nákladů na rezervované instance pomocí Cloudyn v Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak optimalizovat náklady na rezervované instance pro Azure a Amazon Web Services (AWS).
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/18/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: c50259f0df955c3a22edc979dfebc8bfb2059e16
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987750"
---
<!-- Intent: As a cloud-consuming administrator, I need to ensure that my reserved instances are optimized for cost and usage
-->

# <a name="tutorial-optimize-reserved-instances"></a>Kurz: Optimalizace rezervovaných instancí

V tomto kurzu zjistíte, jak vám Cloudyn může pomoct optimalizovat náklady na rezervované instance Azure a Amazon Web Services (AWS) a jejich využití. Rezervovaná instance u jakéhokoli poskytovatele cloudových služeb představuje dlouhodobý smluvní závazek, kdy se předem zavazujete k budoucímu využití virtuálního počítače. Potenciálně může také nabízet výrazné úspory oproti standardnímu cenovému modelu virtuálních počítačů s platbami za použití. Potenciálních úspor dosáhnete pouze při plném využití kapacity rezervovaných instancí.

Tento kurz popisuje, jakým způsobem Cloudyn podporuje rezervované instance Azure a AWS. Popisuje také možnosti optimalizace nákladů na rezervované instance. Hlavně tím, že se vaše rezervace budou využívat naplno. V tomto kurzu provedete následující:

> [!div class="checklist"]
> * Porozumění nákladům na rezervované instance Azure
> * Seznámení s výhodami rezervovaných instancí
> * Optimalizace nákladů na rezervované instance Azure
> * Zobrazení nákladů na rezervované instance
> * Posouzení nákladové efektivity rezervovaných instancí Azure
> * Optimalizace nákladů na rezervované instance AWS
> * Nákup doporučených rezervovaných instancí
> * Úprava nevyužitých rezervací

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít buď zaregistrovanou zkušební verzi, nebo placené předplatné Cloudyn.
- Musíte mít zakoupené rezervované instance v Azure nebo AWS.

## <a name="understand-azure-ri-costs"></a>Porozumění nákladům na rezervované instance Azure

Při nákupu rezervovaných instancí virtuálních počítačů Azure platíte předem za jejich budoucí využití. Tato platba předem pokrývá náklady na budoucí využití virtuálních počítačů:

- konkrétního typu,
- v konkrétní oblasti,
- na jeden rok nebo tři roky a
- až do zakoupeného množství virtuálních počítačů.

Zakoupené rezervované instance virtuálních počítačů Azure můžete zobrazit na webu Azure Portal v části [Rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).

Termín _rezervovaná instance virtuálního počítače Azure_ se týká pouze cenového modelu. Vaše spuštěné virtuální počítače se nijak nezmění. Tento termín je specifický pro Azure a obecněji se pro něj používá název _rezervovaná instance_ nebo _rezervace_. Zakoupené rezervované instance se nevztahují na konkrétní virtuální počítače, ale na jakýkoli odpovídající virtuální počítač. Příkladem je rezervace typu virtuálního počítače spuštěného v oblasti, kterou jste pro svou zakoupenou rezervaci zvolili.

Zakoupené rezervované instance se vztahují pouze na základní hardware. Nezahrnují licence na software virtuálního počítače. Můžete si například rezervovat instanci a získáte odpovídající virtuální počítač s Windows. Rezervovaná instance pokrývá pouze základní náklady na virtuální počítač. V tomto příkladu platíte plnou cenu za všechny požadované licence Windows. Pokud chcete získat slevu na operační systém nebo jiný software spuštěný na virtuálních počítačích, měli byste zvážit využití [Zvýhodněného hybridního využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Zvýhodněné hybridní využití nabízí podobný typ slevy na licence na software jako rezervované instance v případě základních virtuálních počítačů.

Využití rezervovaných instancí nemá přímý vliv na náklady. Jinými slovy, provoz virtuálního počítače se 100% využitím procesoru má stejný vliv jako provoz virtuálního počítače s 0% využitím procesoru – platíte předem za přidělení virtuálního počítače, ne za jeho skutečné využití.

Podívejme se na vztah mezi náklady na využití standardního virtuálního počítače na vyžádání v porovnání s rezervovanými instancemi, jak je znázorněno na následujícím obrázku:

![Náklady na vyžádání vs. náklady na rezervovanou instanci](./media/tutorial-optimize-reserved-instances/azure01.png)



Červené pruhy ukazují celkové náklady na nákup rezervované instance. Platíte jenom jednorázový poplatek. Využití virtuálního počítače je bezplatné. Modré pruhy ukazují celkové náklady na stejný virtuální počítač spuštěný s použitím cenového modelu s průběžnými platbami nebo platbami za vyžádání. Někde mezi sedmým a osmým měsícem využívání virtuálního počítače se nachází *hranice rentability*. V tomto příkladu začnete od osmého měsíce šetřit peníze.

## <a name="benefits-of-ris"></a>Výhody rezervovaných instancí

Každý nákup rezervované instance se vztahuje na virtuální počítač konkrétní velikosti v konkrétním umístění. Příkladem je virtuální počítač D2s\_v3 spuštěný v umístění Západní USA, jak je znázorněno na následujícím obrázku:

![Podrobnosti o rezervované instanci Azure](./media/tutorial-optimize-reserved-instances/azure02.png)

Nákup rezervované instance se začne vyplácet, jakmile bude virtuální počítač spuštěný dostatečný počet hodin pro dosažení hranice rentability rezervace. Velikost a umístění virtuálního počítače musí odpovídat vaší rezervované instanci. Například v předchozím grafu se hranice rentability nachází přibližně v půlce osmého měsíce. Nákup tedy bude výhodný, když bude virtuální počítač odpovídající rezervaci spuštěný alespoň 7,5 měsíce \* 30 dnů \* 24 hodin = 5 400 hodin. Pokud bude odpovídající virtuální počítač spuštěný méně než 5 400 hodin, rezervace bude dražší než průběžné platby.

Hranice rentability se u jednotlivých velikostí a umístění virtuálních počítačů může lišit. Závisí také na vaší vyjednané ceně při průběžných platbách za virtuální počítače. Než provedete nákup, měli byste zkontrolovat hranici rentability pro váš případ.

Dalším aspektem, který byste při nákupu rezervace měli zvážit, je rozsah rezervované instance. Rozsah určuje, jestli se výhody rezervace sdílí nebo jestli platí pro konkrétní předplatné. Sdílené rezervované instance se náhodně používají ve všech vašich předplatných pro první nalezené odpovídající virtuální počítače.

Nákup sdíleného rozsahu je nejflexibilnější a doporučuje se, kdykoli je to možné. Pravděpodobnost využití všech rezervovaných instancí je se sdíleným rozsahem výrazně vyšší. Pokud však za rezervovanou instanci platí vlastník předplatného, může se stát, že jedinou možností bude její nákup s rozsahem jednoho předplatného.

## <a name="optimize-azure-ri-costs"></a>Optimalizace nákladů na rezervované instance Azure

Cloudyn podporuje rezervované instance a Zvýhodněné hybridní využití prostřednictvím:

- Zobrazení nákladů souvisejících s cenovými modely
- Sledování využití rezervovaných instancí
- Vyhodnocování dopadu rezervovaných instancí
- Přidělování nákladů na rezervované instance podle vašich zásad

První, co byste před nákupem rezervované instance měli provést, je vyhodnotit, jaký bude dopad nákupu rezervované instance:

- Kolik vás to bude stát?
- Kolik ušetříte?
- Jaká je hranice rentability?

Tyto otázky vám může pomoct zodpovědět sestava dopadu nákupu rezervované instance.

## <a name="assess-azure-ri-cost-effectiveness"></a>Posouzení nákladové efektivity rezervovaných instancí Azure

Na portálu Cloudyn přejděte do části **Optimizer** (Optimalizátor) > **RI Comparison** (Porovnání rezervovaných instancí) a vyberte **Reserved Instance Purchase Impact** (Dopad nákupu rezervované instance).

V sestavě dopadu nákupu rezervované instance vyberte velikost virtuálního počítače (typ instance), umístění (oblast), období rezervace, množství a očekávanou dobu běhu. Pak můžete vyhodnotit, jestli nákupem ušetříte.

Pokud například zakoupíte rezervaci virtuálního počítače typu DS1\_v2 v umístění Východní USA, který bude celý rok neustále spuštěný, pak můžete za rok ušetřit 369,48 USD. Hranice rentability nastane v pátém měsíci. Viz následující obrázek:

![Hranice rentability rezervované instance Azure](./media/tutorial-optimize-reserved-instances/azure03.png)

Pokud však bude spuštěný pouze 50 % času, hranice rentability nastane v 10. měsíci a roční úspory budou pouze 49,74 USD. Nákup rezervace takového typu instance v tomto příkladu pro vás nemusí být výhodný. Viz následující obrázek:

![Hranice rentability v Azure](./media/tutorial-optimize-reserved-instances/azure04.png)

## <a name="view-ri-costs"></a>Zobrazení nákladů na rezervované instance

Při nákupu rezervace provedete jednorázovou platbu. V Cloudyn můžete tuto platbu zobrazit dvěma způsoby:

- Skutečné náklady
- Amortizované náklady

### <a name="actual-reserved-instance-cost"></a>Skutečné náklady na rezervovanou instanci

Sestavy analýzy skutečných nákladů a dlouhodobé analýzy ukazují celou částku, kterou jste za rezervaci zaplatili od měsíce nákupu. Pomáhají získat přehled o skutečné útratě za určité období.

Na portálu Cloudyn přejděte do části **Cost** (Náklady) > **Cost Analysis** (Analýza nákladů) a pak vyberte **Actual Cost Analysis** (Analýza skutečných nákladů) nebo **Actual Cost Over Time** (Dlouhodobá analýza skutečných nákladů). Dále nastavte filtry. Můžete například vyfiltrovat pouze službu Azure/VM a provést seskupení podle typu prostředku (Resource Type) a cenového modelu (Price Model). Viz následující obrázek:

![Skutečné náklady na rezervovanou instanci](./media/tutorial-optimize-reserved-instances/azure05.png)

Můžete provést filtrování podle služby, v tomto případě **Azure/VM**, a seskupení podle cenového modelu (**Price Model**) a typu prostředku (**Resource Type**), jak je znázorněno na následujícím obrázku:

![Skupiny a filtry sestavy skutečných nákladů](./media/tutorial-optimize-reserved-instances/azure06.png)

Můžete také analyzovat typ provedených plateb, jako jsou jednorázové poplatky, poplatky za využití a poplatky za licence.

### <a name="amortized-reserved-instance-cost"></a>Amortizované náklady na rezervovanou instanci

Při nákupu rezervované instance zaplatíte počáteční poplatek, který se zobrazí v měsíci nákupu. V dalších fakturách se nezobrazí. Proto může být pohled na měsíční využití zavádějící. Vaše skutečné měsíční náklady se skládají z měsíčního využití a poměrné (amortizované) části všech předchozích jednorázových poplatků. Sestava amortizovaných nákladů vám může pomoct získat pravdivou představu.

Amortizované náklady na rezervovanou instanci se počítají postupným odepisováním jednorázového poplatku za rezervaci v průběhu období rezervace. V sestavách skutečných nákladů se jednorázové poplatky zobrazí v měsíci nákupu rezervace. Denní a měsíční útrata se ve skutečných nákladech na nasazení nezobrazí. Sestavy amortizovaných nákladů ukazují skutečné náklady na nasazení v průběhu času.  Sestava amortizovaných nákladů představuje jediný způsob, jak zobrazit trendy skutečných nákladů. Je to také jediný způsob, jak předvídat budoucí útratu.

V sestavě skutečných nákladů jste viděli špičku způsobenou nákupem rezervované instance 16. listopadu za 747 USD. Sestava amortizovaných nákladů (viz následující obrázek) obsahuje pro den 16. listopadu částečný náklad. Od 17. listopadu vidíte amortizované náklady na rezervovanou instanci ve výši 747 USD / 365 = 2,05 USD. Mimochodem si všimněte, že je zakoupená rezervace nevyužitá, takže ji můžete optimalizovat přepnutím na jinou velikost virtuálního počítače.

Pokud chcete sestavu zobrazit, přejděte do části **Cost** (Náklady) > **Cost Analysis** (Analýza nákladů) a vyberte **Amortized Cost Analysis** (Analýza amortizovaných nákladů) nebo **Amortized Cost Over Time** (Dlouhodobá analýza amortizovaných nákladů).

![Amortizované náklady na rezervovanou instanci](./media/tutorial-optimize-reserved-instances/azure07.png)

## <a name="optimize-aws-ri-costs"></a>Optimalizace nákladů na rezervované instance AWS

Rezervované instance představují volný závazek. Jsou užitečné v případě stabilního využití virtuálních počítačů, protože rezervované instance jsou levnější než instance na vyžádání. Musí však být dostatečně využité. Závazek spočívá v používání prostředků, obvykle virtuálních počítačů, po definované období – jeden rok nebo tři roky. Když se zavážete k nákupu, v rámci rezervace si předplatíte prostředky. Ne vždy však naplno využijete, k čemu jste se v rámci rezervace zavázali.

Můžete například vyhodnotit své prostředí a určit, že jste za poslední rok měli neustále spuštěných 20 instancí D2 úrovně Standard. Můžete pro ně zakoupit rezervaci a potenciálně tak výrazně ušetřit. V jiném příkladu se můžete zavázat k ročnímu používání deseti instancí MA4. Až dosud jste však mohli používat pouze pět. Oba příklady ilustrují neefektivní využití rezervovaných instancí. S využitím sestav optimalizace Cloudyn můžete optimalizovat náklady na rezervované instance dvěma způsoby:

- Kontrola doporučení k nákupu na základě historického využití
- Úprava nevyužitých rezervací

Náklady na rezervované instance a jejich využití můžete zlepšit pomocí sestav _EC2 RI Buying Recommendations_ (Doporučení k nákupu rezervovaných instancí EC2) a _EC2 Currently Unused Reservations_ (Aktuálně nevyužité rezervace EC2).

## <a name="buy-recommended-ris"></a>Nákup doporučených rezervovaných instancí

Cloudyn porovnává využití instancí na vyžádání s potenciálními rezervovanými instancemi. Když najde možné úspory, zobrazí doporučení v sestavě doporučení k nákupu EC2.

V nabídce sestav v horní části portálu klikněte na **Optimizer** (Optimalizátor) > **Pricing Optimization** (Optimalizace cen) > **EC2 RI Buying Recommendations** (Doporučení k nákupu rezervovaných instancí EC2).

Následující obrázek ukazuje doporučení k nákupu ze sestavy.

![Doporučení k nákupu](./media/tutorial-optimize-reserved-instances/aws01.png)

V tomto příkladu obsahuje účet Cloudyn\_A 32 doporučení k nákupu rezervovaných instancí. Pokud se budete řídit doporučeními k nákupu, můžete potenciálně ušetřit 137 770 USD ročně. Mějte na paměti, že doporučení k nákupu, která poskytuje Cloudyn, předpokládají zachování konzistentního využití vašich spuštěných úloh.

Pokud chcete zobrazit podrobnosti s vysvětlením jednotlivých doporučení k nákupu, klikněte na symbol plus (**+**) v části **Justifications** (Odůvodnění). Tady je příklad pro první doporučení v seznamu.

![Odůvodnění nákupu](./media/tutorial-optimize-reserved-instances/aws02.png)

Předchozí příklad ukazuje, že spouštění úlohy na vyžádání by ročně stálo 90 456 USD. Pokud však zakoupíte rezervaci předem, stejná úloha bude stát 56 592 USD a vy tak ušetříte 33 864 USD ročně.

Kliknutím na symbol plus vedle textu **EC2 RI Purchase Impact** (Dopad nákupu rezervované instance EC2) zobrazíte hranici rentability v průběhu roku, abyste přibližně viděli, kdy se vaše investice do nákupu začne vyplácet. V následujícím příkladu začnou přibližně osm měsíců po zakoupení celkové náklady na vyžádání převyšovat celkové náklady na rezervovanou instanci:

![Dopad nákupu](./media/tutorial-optimize-reserved-instances/aws03.png)

V tomto okamžiku začnete šetřit peníze.

Pokud chcete ověřit přesnost navrhovaného doporučení k nákupu, můžete zkontrolovat sestavu **Instances over Time** (Instance v průběhu času). V tomto příkladu vidíte, že se pro úlohu za posledních 30 dnů využívalo průměrně šest instancí.

![Instance v průběhu času](./media/tutorial-optimize-reserved-instances/aws04.png)

## <a name="modify-unused-reservations"></a>Úprava nevyužitých rezervací

Nevyužité rezervace se běžně vyskytují ve výpočetních prostředích řady uživatelů cloudových prostředků. Zajištěním plného využití nevyužitých rezervací můžete ušetřit, pokud rezervace upravíte tak, aby splňovaly vaše aktuální požadavky. Například můžete mít předplatné obsahující instance D3 úrovně Standard spuštěné v Linuxu. Pokud rezervaci nevyužijete naplno, můžete změnit typ instance. Případně můžete nevyužité prostředky přesunout do jiné rezervace nebo jiného účtu.

AWS prodává rezervované instance pro konkrétní zóny dostupnosti a oblasti. Pokud jste zakoupili rezervované instance pro konkrétní zónu dostupnosti, pak není možný přesun rezervací mezi zónami. Regionální rezervované instance však můžete jednoduše přesouvat mezi zónami s využitím sestavy **EC2 Currently Unused Reservations** (Aktuálně nevyužité rezervace EC2). Alternativně je můžete upravit tak, aby měly regionální rozsah, a pak budou používat odpovídající instance ve všech zónách dostupnosti.

V nabídce sestav v horní části portálu klikněte na **Optimizer** (Optimalizátor) > **Inefficiencies** (Neefektivity) > **EC2 Currently Unused Reservations** (Aktuálně nevyužité rezervace EC2).

Následující obrázky ukazují sestavu s nevyužitými rezervovanými instancemi.

![Nevyužité rezervace](./media/tutorial-optimize-reserved-instances/unused-ri01.png)

Kliknutím na symbol plus v části **Details** (Podrobnosti) zobrazíte podrobnosti o konkrétní rezervaci.

![Podrobnosti o nevyužitých rezervacích](./media/tutorial-optimize-reserved-instances/unused-ri02.png)

V předchozím příkladu je celkem 77 nevyužitých rezervací v různých zónách dostupnosti. První rezervace obsahuje 51 nevyužitých instancí. Když se v seznamu podíváte níže, uvidíte potenciální úpravy rezervovaných instancí, které můžete provést s použitím typu instance **m3.2xlarge** v zóně dostupnosti **us-east-1c**.

Kliknutím na **Modify** (Upravit) u první rezervace v seznamu otevřete stránku **Modify RI** (Úprava rezervované instance), na které se zobrazí data o příslušné rezervaci.

![Úprava rezervované instance](./media/tutorial-optimize-reserved-instances/unused-ri03.png)

V seznamu se zobrazí rezervované instance, které můžete upravit. Na následujícím obrázku je 51 nevyužitých rezervací, které můžete upravit, ale obě rezervace vyžadují dohromady 54 rezervací. Pokud upravíte nevyužité rezervace tak, aby se použily všechny, čtyři instance se budou i nadále spouštět na vyžádání. Pro účely tohoto příkladu rozdělte nevyužité rezervace tak, že první rezervace jich bude používat 30 a druhá rezervace jich bude používat 21.

Klikněte na symbol plus u první položky rezervace a nastavte **Reservation quantity** (Množství rezervací) na **30**. Pro druhou položku nastavte množství rezervací na **21** a pak klikněte na **Apply** (Použít).

![Změna množství rezervací](./media/tutorial-optimize-reserved-instances/unused-ri04.png)

Všechny nevyužité instance pro rezervace se využívají naplno a 51 instancí se již nespouští na vyžádání. V tomto příkladu šetříte své organizaci peníze výrazným omezením využití na vyžádání a používáním předplacených rezervací.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste úspěšně provedli následující úlohy:

> [!div class="checklist"]
> * Porozumění nákladům na rezervované instance Azure
> * Seznámení s výhodami rezervovaných instancí
> * Optimalizace nákladů na rezervované instance Azure
> * Zobrazení nákladů na rezervované instance
> * Posouzení nákladové efektivity rezervovaných instancí Azure
> * Optimalizace nákladů na rezervované instance AWS
> * Nákup doporučených rezervovaných instancí
> * Úprava nevyužitých rezervací


V dalším kurzu se dozvíte, jak řídit přístup k datům.

> [!div class="nextstepaction"]
> [Řízení přístupu k datům](tutorial-user-access.md)

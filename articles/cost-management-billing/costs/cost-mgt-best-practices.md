---
title: Optimalizace investic do cloudu se službou Azure Cost Management
description: Tento článek vám pomůže maximalizovat hodnotu svých investic do cloudu, snížit náklady a vyhodnotit způsob utrácení peněz.
author: bandersmsft
ms.author: banders
ms.date: 05/27/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18
ms.openlocfilehash: 4dd22427038bc7c4052ee714c3ec3ce45d7a1837
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132818"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Jak optimalizovat investice do cloudu se službou Azure Cost Management

Azure Cost Management nabízí nástroje na plánování, analýzu a snižování výdajů, abyste mohli maximalizovat hodnotu svých investic do cloudu. Tento dokument nabízí metodický přístup ke správě nákladů a vysvětluje dostupné nástroje, které můžete použít k řešení problémů vaší organizace souvisejících s náklady. Azure usnadňuje vytváření a nasazení cloudových řešení. Tato řešení je ale potřeba optimalizovat, abyste minimalizovali náklady organizace. Pokud se budete řídit principy uvedenými v tomto dokumentu a budete používat naše nástroje, vytvoříte tím předpoklady pro zajištění úspěchu organizace.

## <a name="methodology"></a>Metodologie

Správa nákladů organizace je problematická. Měla by to být nepřetržitá činnost, která začíná ještě před vynaložením peněz na cloudové prostředky. Aby organizace úspěšně implementovala správu nákladů a optimalizovala náklady, musí:

- Mít připravené správné nástroje, které jí zajistí úspěch.
- Být zodpovědná, co se svých nákladů.
- Podniknout příslušné akce, které optimalizují výdaje.

Abyste mohli úspěšně spravovat náklady, je potřeba v organizaci zajistit soulad těchto tří klíčových skupin:

- **Finance** – lidé, kteří v organizaci zodpovídají za schvalování rozpočtových požadavků na základě prognózy cloudových výdajů. Proplácejí příslušná vyúčtování a přiřazují náklady různým týmům, které za ně odpovídají.
- **Manažeři** – pracovníci organizace s rozhodovací pravomocí, kteří potřebují rozumět cloudovým nákladům, aby je dokázali vynaložit co nejúčelněji.
- **Aplikační týmy** – technici, kteří se věnují každodenní správě cloudových prostředků a vyvíjejí služby, které vyhovují potřebám organizace. Tyto týmy potřebují být flexibilní, aby mohly v mezích rozpočtu přinášet co největší hodnotu.

### <a name="key-principles"></a>Hlavní principy

Následující principy zajistí vaší organizaci úspěch při správě cloudových nákladů.

Další informace najdete ve videu věnovaném [nastavení služby Cost Management pro dosažení úspěchu](https://www.youtube.com/watch?v=dVuwITdSAZ4). Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/dVuwITdSAZ4]

#### <a name="planning"></a>Plánování

Komplexní předběžné plánování vám umožní přizpůsobit využití cloudu tak, aby vyhovovalo konkrétním požadavkům vaší firmy. Zeptejte se sami sebe:

- Jaký obchodní problém potřebuji vyřešit?
- Jaký očekávám způsob využití prostředků?

Odpovědi vám pomůžou vybrat vhodné nabídky. Vyplyne z nich, jakou infrastrukturu použít a také způsob jejího použití, abyste Azure využívali co nejefektivněji.

#### <a name="visibility"></a>Viditelnost

Pokud máte správnou strukturu, pomůže vám Cost Management informovat uživatele o nákladech na Azure, za které zodpovídají, nebo o vynaložených peněžních prostředcích. Služby Azure jsou navržené tak, aby vám umožnily získat přehled o tom, *kde* své peníze utrácíte. Využijte tyto nástroje. Pomůžou vám najít nevyužité prostředky, vyloučí plýtvání a maximalizují příležitosti k úspoře nákladů.

#### <a name="accountability"></a>Odpovědnost

Přiřaďte náklady organizace tak, aby byli lidé odpovědní za výdaje svých týmů. K plnému pochopení výdajů organizace za Azure je potřeba prostředky uspořádat tak, abyste měli co největší přehled o přiřazených nákladech. Dobrá organizace pomáhá řídit a snižovat náklady a nutí lidi, aby odpovídali za účinné vynakládání prostředků organizace.

#### <a name="optimization"></a>Optimalizace

Je to činnost zaměřená na snižování výdajů. Maximalizujte její účinek na základě poznatků shromážděných při plánování a zpřehledněte co nejvíce náklady. Kromě změn nasazené infrastruktury, o kterých si povíme v další části tohoto dokumentu, možná budete chtít optimalizovat i nákup a licencování.

#### <a name="iteration"></a>Iterace

Všichni členové organizace se musí účastnit životního cyklu správy nákladů. Je potřeba je trvale zapojit do optimalizace nákladů. Tento opakující se proces je potřeba důsledně dodržovat a zavést ho jako hlavní princip odpovědného řízení cloudu vaší organizace.

![Diagram klíčových principů: přehlednost, odpovědnost a optimalizace](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Plánování s důrazem na náklady

Než nasadíte cloudové prostředky, zvažte následující věci:

- Nabídku Azure, která nejlépe vyhovuje vašim potřebám.
- Prostředky, které plánujete použít.
- Kolik můžou stát.

Azure nabízí nástroje, které vám pomůžou provést vyhodnocení: S těmito nástroji si uděláte přehled o investici potřebné ke zprovoznění vašich úloh. Pak můžete vybrat nejlepší konfiguraci, která odpovídá vaší situaci.

### <a name="azure-onboarding-options"></a>Možnosti nasazení Azure

Prvním krokem, který vám umožní maximálně využít prostředí služby Cost Management, je průzkum nabídek Azure a rozhodnutí, která je pro vás nejlepší. Rozmyslete si své plány, jak chcete Azure používat v budoucnosti. Také si rozmyslete, jak chcete konfigurovat fakturační model. Při rozhodování si položte následující otázky:

- Jak dlouho plánuji používat Azure? Zkouším nebo plánuji vytvořit dlouhodobější infrastrukturu?
- Jak budu Azure platit? Mám platit předem, abych získal/a sníženou cenu, nebo si nechám cenu fakturovat na konci měsíce?

Další informace o různých možnostech najdete v tématu o [možnostech nákupu Azure](https://azure.microsoft.com/pricing/purchase-options/). Tady jsou některé nejčastější fakturační modely:

#### <a name="free"></a>[Free](https://azure.microsoft.com/free/)

- 12 měsíců oblíbených služeb zdarma
- Kredit 200 USD k průzkumu služeb na 30 dní
- 25 a více služeb je vždy zdarma

#### <a name="pay-as-you-go"></a>[Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p)

- Bez minimálního využití a závazků
- Konkurenční ceny
- Platíte jenom za to, co využijete
- Můžete zrušit kdykoli

#### <a name="enterprise-agreement"></a>[Smlouva Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Možnosti předběžných peněžních závazků
- Přístup k nižším cenám Azure

#### <a name="azure-in-csp"></a>[Azure v CSP](https://azure.microsoft.com/offers/ms-azr-0145p/)

- Partneři CSP jsou primárním kontaktem pro potřeby svých zákazníků a středem vztahů se zákazníky
- Partneři CSP zřizují nové zákazníky, objednávají předplatná, spravují předplatná a provádí úlohy správy jménem svých zákazníků
- Partneři CSP dodávají služby a jedinečná řešení v sadě nebo prodávají Azure a současně mají pod kontrolou ceny, podmínky a fakturaci

## <a name="estimate-the-cost-of-your-solution"></a>Odhad nákladů na řešení

Před nasazením infrastruktury je potřeba vyhodnotit, kolik vás řešení bude stát. Toto vyhodnocení vám pomůže předem vytvořit v organizaci rozpočet dané úlohy. Pak můžete rozpočet průběžně používat k ověřování platnosti původního odhadu. A můžete ho porovnat se skutečnými náklady nasazeného řešení.

### <a name="azure-pricing-calculator"></a>Cenová kalkulačka Azure

Cenová kalkulačka Azure umožňuje míchat a porovnávat různé kombinace služeb Azure, abyste si mohli prohlédnout odhadované náklady. Řešení můžete v Azure implementovat různým způsobem, aby všichni mohli ovlivnit celkové výdaje. Když si předem rozmyslíte celou infrastrukturu potřebnou k nasazení cloudu, pomůže vám to používat nástroj co nejúčinněji. Pomůže vám to také dostatečně přesně odhadnout výdaje na Azure.

Další informace najdete v tématu [Cenová kalkulačka Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate je služba, která vyhodnotí aktuální úlohy vaší organizace v místních datacentrech. Pomůže vám získat přehled o prostředcích potřebných pro náhradní řešení v Azure. Služba Migrate nejprve analyzuje místní počítače, aby zjistila, jestli je migrace proveditelná. Pak doporučí velikost virtuálních počítačů v Azure, aby maximalizovala výkon. Nakonec také odhadne náklady na řešení založené na Azure.

Další informace najdete v tématu o službě [Azure Migrate](../../migrate/migrate-services-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analýza a správa nákladů

Nechte se informovat o vývoji nákladů organizace v čase. Následující techniky vám umožní správně porozumět výdajům, abyste je mohli řídit.

### <a name="organize-resources-to-maximize-cost-insights-and-accountability"></a>Uspořádání prostředků pro zajištění maximálních přehledů o nákladech a zodpovědnosti

Dobře naplánovaná organizační struktura pro hierarchii prostředků a fakturaci Azure vám pomůže zajistit dobrý přehled a kontrolu nad náklady při vytváření cloudové infrastruktury. Podívejte se na video věnované [nastavení hierarchií entit](https://www.youtube.com/watch?v=n3TLRaYJ1NY), kde získáte přehled o dostupných organizačních nástrojích a možnostech jejich využití. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/n3TLRaYJ1NY]

Při vyhodnocování a vytváření hierarchie, která splňuje vaše požadavky, si položte následující otázky.

*Jakou hierarchii fakturace mám k dispozici a jaké rozsahy mohou využít?*

Určete fakturační uspořádání vaší organizace určením typu nabídky Azure. Dostupné rozsahy pro jednotlivé fakturační ujednání v Azure jsou popsané v tématu [Vysvětlení a práce s rozsahy](understand-work-scopes.md).

*Jak mám uspořádat předplatná a skupiny prostředků, když mám víc týmů?*

Běžným postupem je vytvoření předplatného nebo skupiny prostředků pro každý tým. Pomohou vám rozlišit náklady a zajistit zodpovědnost jednotlivých týmů. Náklady jsou ale vázané na předplatné nebo skupinu prostředků.

Pokud už máte týmy s několika předplatnými, zvažte seskupení předplatných do skupin pro správu, aby se náklady analyzovaly dohromady. Skupiny pro správu, předplatná i skupiny prostředků jsou součástí hierarchie Azure RBAC. Používejte je dohromady pro řízení přístupu ve vašich týmech.

Prostředky mohou být rozložené mezi několik rozsahů, zejména pokud je sdílí více týmů nebo úloh. Zvažte identifikaci prostředků pomocí značek. Značky jsou podrobněji popsány v následující části.

*Mám vývojové a provozní prostředí?*

Pro vývojová prostředí zvažte vytvoření předplatných pro vývoj a testování, abyste využili snížené ceny. Pokud úlohy zasahují do několika týmů nebo rozsahů Azure, zvažte jejich identifikaci pomocí značek.

### <a name="tag-shared-resources"></a>Označení sdílených prostředků pomocí značek

Značky představují efektivní způsob, jak porozumět nákladům, které jsou rozložené mezi několik týmů a rozsahů Azure. Můžete například mít prostředek, jako je e-mailový server, který využívá hodně týmů. Sdílený prostředek, jako je e-mailový server, můžete umístit do předplatného, které je vyhrazené pro sdílené prostředky, nebo do už existujícího předplatného. Pokud ho umístíte do existujícího předplatného, vlastník předplatného pravděpodobně nebude chtít, aby se náklady na tento prostředek naúčtovaly jeho týmu každý měsíc. V tomto příkladu můžete použít značku k identifikaci prostředku jako sdíleného.

Podobně můžete mít také webové aplikace nebo prostředí, jako je například testovací nebo provozní, které využívají prostředky napříč několika předplatnými, která vlastní různé týmy. Pro lepší pochopení kompletních nákladů na úlohy můžete označit prostředky, které používají. Pokud jsou značky nastavené správně, můžete je v analýzách nákladů použít jako filtr pro lepší pochopení trendů.

Po naplánování označování prostředků můžete nakonfigurovat zásady Azure tak, aby toto označování prostředků vynutily. Podívejte se na video o [kontrole zásad značek s využitím služby Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) a seznamte se s nástroji, které jsou k dispozici pro vynucení škálovatelného označování prostředků. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/nHQYcYGKuyw]

### <a name="use-cost-analysis"></a>Použití nákladové analýzy

Nákladová analýza umožňuje podrobně analyzovat náklady organizace tím, že je rozdělíte podle standardních vlastností prostředků. Při analýze se můžete nechat vést následujícími nejčastějšími dotazy. Pravidelné odpovědi na tyto otázky vám pomůžou zůstat informovanější a umožní vám rozhodovat se podle nákladů.

- **Odhadované náklady v aktuálním měsíci** – kolik jsme v tomto měsíci zatím utratili? Podaří se mi dodržet rozpočet?
- **Zkoumání odchylek** – provádějte pravidelné kontroly, abyste měli jistotu, že náklady přiměřeně odpovídají normálnímu využití. Jaké jsou trendy? Existují nějaké výkyvy?
- **Odsouhlasení faktur** – jsou moje nejnovější fakturované náklady vyšší než předchozí měsíc? Jak se vyvíjejí měsíční výdaje?
- **Interní přeúčtování** – když znám účtované výdaje, jak je můžu ve své organizaci rozdělit?

Další informace najdete v [nákladové analýze](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Export fakturačních dat podle plánu

Potřebujete fakturační data importovat do nějakého externího systému, jako je řídicí panel nebo finanční systém? Nastavte automatické exporty do Azure Storage, abyste nemuseli každý měsíc stahovat soubory ručně. Pak můžete jednoduše nastavit automatickou integraci do jiných systémů, aby byla fakturační data synchronizovaná.

Další informace o exportu fakturačních dat najdete v tématu o [vytvoření a správě exportovaných dat](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Tvorba rozpočtů

Když jste identifikovali výdaje a analyzovali jejich strukturu, je důležité začít sobě i svým týmům nastavovat limity. Rozpočty v Azure umožňují při jejich nastavování vycházet z nákladů nebo využití a nabízejí různé prahové hodnoty a výstrahy. Nezapomeňte vytvořené rozpočty pravidelně kontrolovat, abyste viděli průběžné čerpání rozpočtu a mohli případně provést změny. Rozpočty v Azure také umožňují konfigurovat automatické triggery v případě dosažení rozpočtového prahu. Můžete například nakonfigurovat službu tak, virtuální počítače vypnula. Nebo můžete službu nastavit tak, aby se v reakci na aktivační událost rozpočtu infrastruktura přesunula do jiné cenové úrovně.

Další informace najdete v tématu o [rozpočtech Azure](tutorial-acm-create-budgets.md).

Další informace o automatizaci založené na rozpočtech najdete v tématu o [automatizaci založené na rozpočtech](../manage/cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Optimalizační akce
K optimalizaci výdajů můžete použít následující možnosti.

### <a name="cut-out-waste"></a>Zastavit plýtvání

Po nasazení infrastruktury do Azure je důležité se ujistit, jestli se používá. Nejjednodušší způsob, jak okamžitě začít šetřit, je zkontrolovat vaše prostředky a odebrat ty, které se nepoužívají. Potom byste se měli pokusit zjistit, jestli se vaše prostředky používají co nejúčelněji.

#### <a name="azure-advisor"></a>Azure Advisor

Služba Azure Advisor (kromě jiného) podle využití procesoru a sítě identifikuje málo používané virtuální počítače. Pak se na základě odhadovaných nákladů můžete rozhodnout, jestli počítače necháte běžet nebo je vypnete, případně změníte jejich velikost. Advisor také nabízí doporučení týkající se nákupů rezervovaných instancí. Doporučení vycházejí z využití virtuálního počítače za posledních 30 dní. Pokud se podle nich budete řídit, pomůžou vám snížit výdaje.

Další informace najdete v tématu o službě [Azure Advisor](../../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Nastavení správné velikosti virtuálních počítačů

Velikost virtuálního počítače má velký vliv na celkové náklady na Azure. Počet virtuálních počítačů potřebných v Azure nemusí odpovídat virtuálním počítačům aktuálně nasazeným v místním datacentru. Přesvědčte se, že pro úlohy, které plánujete spustit, zvolíte správnou velikost.

Další informace najdete v tématu [Azure IaaS: správné nastavení velikosti a nákladů](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Použití nákupních slev

Azure nabízí řadu slev. Pokud je vaše organizace využije, může ušetřit.

#### <a name="azure-reservations"></a>Rezervace Azure

Služba Azure Reservations umožňuje předplatit si výpočetní kapacitu virtuálního počítače nebo databáze SQL na jeden nebo na tři roky. Díky předplacení získáte slevu na používané prostředky. Rezervace Azure můžou při jednoletém nebo tříletém závazku s platbou předem výrazně snížit náklady na výpočetní výkon virtuálního počítače nebo databáze SQL, a to až o 72 procent oproti cenám při průběžných platbách. Rezervace poskytují slevu z faktury a neovlivňují běhový stav virtuálních počítačů nebo databází SQL.

Další informace najdete v tématu o službě [Azure Reservations](../reservations/save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

Pokud máte místně nasazené licence Windows Serveru nebo SQL Serveru, můžete díky programu Zvýhodněné hybridní využití Azure ušetřit i v Azure. V případě systému Windows Server každá licence pokrývá náklady na operační systém (až na dvou virtuálních počítačích) a platíte jenom základní výpočetní náklady. Stávající licence na SQL Server můžete využít k ušetření až 55 procent nákladů u databází SQL založených na virtuálních jádrech. Mezi možnosti patří SQL Server v Azure Virtual Machines a služba SSIS (SQL Server Integration Services).

Další informace najdete tady: [Kalkulačka úspor při zvýhodněném hybridním využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Další prostředky

Azure také nabízí službu, která umožňuje vytvářet služby využívající přebytečnou kapacitu Azure za nižší tarif. Další informace najdete v tématu o [použití virtuálních počítačů s nízkou prioritou ve službě Batch](../../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Další kroky
- Pokud se službou Cost Management teprve začínáte, přečtěte si téma [Co je Azure Cost Management](../cost-management-billing-overview.md), ze kterého se dozvíte, jak monitorovat a kontrolovat výdaje za Azure a jak optimalizovat využití prostředků.
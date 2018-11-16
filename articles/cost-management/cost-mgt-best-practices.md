---
title: Optimalizovat vaše cloudové investice ve službě Azure Cost Management | Dokumentace Microsoftu
description: Tento článek pomáhá využít na maximum ze svých investic do cloudu, snížení nákladů a vyhodnotit, kde je využita vaše peníze.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/09/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 8958d2cde9f40aa1a7370894614a54bbd604bce7
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706326"
---
# <a name="how-to-optimize-your-cloud-investment-with-azure-cost-management"></a>Jak optimalizovat vaše cloudové investice ve službě Azure Cost Management

Azure Cost Management poskytuje nástroje pro plánování, analyzovat a omezit útratu k maximalizaci vaší investice do cloudu. Tento dokument vám poskytne metodický přístup do služby cost management a zvýrazní nástroje, které jsou k dispozici, protože řeší problémy náklady na vaší organizace. Azure usnadňuje sestavování a nasazování cloudových řešení. Je ale důležité, že těchto řešení jsou optimalizována pro minimalizaci nákladů pro vaši organizaci. Následující zásady uvedené v tomto dokumentu a používání, že naše nástroje pomůže zajistit, že vaše organizace je připraven k dosažení úspěchu.

## <a name="methodology"></a>Metodologie

Služba Cost management je organizační problému a musí být probíhající praxe, která začíná před cloudové prostředky můžete utratit peníze. K úspěšné implementaci správy nákladů a optimalizovat náklady, musí vaše organizace:

- Provést přípravu pomocí správných nástrojů k dosažení úspěchu
- Za náklady
- Přijmout vhodná opatření k optimalizaci výdajů

Tři klíčové skupiny uvedené dále, musí být zarovnány ve vaší organizaci, abyste měli jistotu, že jste úspěšně Správa nákladů.

- **Finance** -zodpovědný za schvalování požadavků rozpočtu celé organizace založené na cloudu útraty prognózy. Jejich platit odpovídající faktury a náklady na přiřadit různé týmy tak podpořit přebírání odpovědnosti.
- **Správci** – obchodní uživatelé s rozhodovací pravomocí v organizaci, potřebujete pochopit cloudových výdajů za účelem vyhledání nejlepší útraty výsledky.
- **Aplikační týmy** – technici Správa cloudových prostředků na základě každodenní vývoj služeb podle potřeb organizace. Tyto týmy potřebují flexibilně dodávat v jejich definované rozpočet na maximum.

### <a name="key-principles"></a>Klíč zásad

Použijte zásady uvedené níže na pozici vaší organizaci k dosažení úspěchu v cloudu, Správa nákladů.

#### <a name="planning"></a>Plánování

Komplexní, počáteční plánování umožňuje přizpůsobit využití cloudu pro vaše konkrétní požadavky. Položte si otázku:

- Jaký problém obchodní jsem řešení?
- Jaké vzorce používání očekávat od Moje prostředky?

Vaše odpovědi vám pomůže vybrat nabídek, které jsou pro vás nejvhodnější. Určují infrastrukturu k použití a jak se používá pro maximalizaci efektivity Azure.

#### <a name="visibility"></a>Viditelnost

Když dobře strukturovaných, Cost Management pomáhá uživatelům poskytnout informace o nákladech na Azure, které jsou odpovědné za nebo za peníze, kterou lidé tráví. Azure nabízí služby navržené tak, aby vám poskytnou přehled o *kde* byl stráven vaše peníze. Využijte výhod těchto nástrojů. Pomohou vám najít prostředky, které jsou nedostatečný, odeberte plýtvání a maximalizovat možnosti úspory nákladů.

#### <a name="accountability"></a>Odpovědnosti

Atribut nákladů ve vaší organizaci, abyste měli jistotu, že zodpovědný zodpovídají za jejich tým útraty. Abyste úplně pochopili útraty Azure vaší organizace, by měl uspořádání prostředků pro maximalizaci přehled o přidělení nákladů. Dobré organizaci pomáhá spravovat a snížit náklady a uložení uživatelé zodpovídají za účinné útraty ve vaší organizaci.

#### <a name="optimization"></a>Optimalizace

Slouží ke snížení vaší útraty. Využijte naplno na poznatky získané pomocí plánování a zvýší viditelnost náklady na základě. Můžete zvážit nákupu a licencování optimalizace spolu s změny nasazení infrastruktury, které jsou detailně popsány dále v tomto dokumentu.

#### <a name="iteration"></a>Iterace

Všichni uživatelé v organizaci musíte zapojit do životního cyklu správy nákladů. Potřebují k zajištění zahrnutých průběžně provádět pro zajištění optimalizace nákladů. Být přísné o tento iterativní proces a nastavte ji klíčovým principem zásad správného řízení zodpovědná cloudu ve vaší organizaci.

![Klíč zásad](./media/cost-mgt-best-practices/principles.png)

## <a name="plan-with-cost-in-mind"></a>Plánování nákladů v paměti

Před nasazením cloudové prostředky, zhodnoťte následující položky:

- Nabídka Azure, která nejlépe vyhovuje vašim potřebám
- Prostředky, které plánujete použít
- Kolik může stát

Azure poskytuje nástroje, které vám pomohou při posouzení procesu. Nástroje vám může poskytnout představu o investici vyžadovaného k povolení úloh. Pak můžete vybrat optimální konfiguraci pro konkrétní situaci.

### <a name="azure-onboarding-options"></a>Možnosti registrace Azure

Prvním krokem při maximalizaci prostředí v rámci Cost Management je pro prozkoumání a rozhodnout, která nabídka Azure je pro vás nejvhodnější. Představte si, jak máte v úmyslu používat Azure v budoucnu. Zvažte také, jak mají být váš model fakturace nakonfigurované. Při rozhodování, zvažte následující otázky:

- Jak dlouho je plánujete používat Azure? Testují můžu, nebo kteří plánují k vytvoření dlouhodobější infrastruktury?
- Jak je chcete platit za Azure? By měl můžu předem za sníženou cenu nebo získat fakturovány na konci měsíce?

Další informace o různých možnostech najdete v tématu [jak koupit Azure](https://azure.microsoft.com/pricing/purchase-options/). Níže jsou uvedené některé z nejběžnějších modely fakturace.

#### <a name="freehttpsazuremicrosoftcomfree"></a>[Free](https://azure.microsoft.com/free/)

- 12 měsíců oblíbených služeb zdarma
- 200 USD v kreditech, které prozkoumání služeb po dobu 30 dnů
- více než 25 služeb jsou vždycky zdarma

#### <a name="pay-as-you-gohttpsazuremicrosoftcomoffersms-azr-0003p"></a>[Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p)

- Bez minimálních částek a závazků
- Výhodné ceny
- Platíte jenom za to, co využijete
- Možnost kdykoli zrušit

#### <a name="enterprise-agreementhttpsazuremicrosoftcompricingenterprise-agreement"></a>[Smlouvy Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/)

- Možnosti pro počáteční peněžní závazky
- Přístup k nižší ceny Azure

## <a name="estimate-the-cost-of-your-solution"></a>Odhad nákladů na řešení

Před nasazením jakékoli infrastruktury posuďte, kolik vaše řešení bude nákladů. Posouzení vám pomůžou vytvořit rozpočtu vaší organizace pro pracovní vytížení, počáteční. Pak můžete použít rozpočtu v čase do testu výkonnosti platnosti počáteční odhad. A porovnejte ji s skutečné náklady na nasazené řešení.

### <a name="azure-pricing-calculator"></a>Cenovou kalkulačku Azure

Cenovou kalkulačku Azure umožňuje kombinovat a párovat různé kombinace služeb Azure, pokud chcete zobrazit odhad nákladů. Můžete implementovat řešení pomocí různých způsobů, jak v Azure – každý by mohly ovlivnit vaše celkové výdaje. Přemýšlení o všech požadavků na infrastrukturu na vaším cloudovým nasazením již v rané fázi vám pomůže používat nástroj nejefektivněji. Pomůže vám zjistit solid odhad vaší odhad útraty v Azure.

Další informace najdete v tématu [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator).

### <a name="azure-migrate"></a>Azure Migrate

Azure Migrate je služba, která posuzuje aktuální úlohy vaší organizace v místních datových centrech. Poskytuje přehled o tom, co potřebujete z Azure náhradní řešení. Nejprve analyzuje migrace vašich místních počítačů k určení, zda migrace je to možné. Potom doporučené velikosti virtuálních počítačů v Azure pro zajištění maximálního výkonu. Nakonec se vytvoří také odhad nákladů pro řešení založené na Azure.

Další informace najdete v tématu [Azure Migrate](../migrate/migrate-overview.md).

## <a name="analyze-and-manage-your-costs"></a>Analýza a Správa nákladů

Udržujte si přehled o jak v čase vyvíjí vaše organizace náklady. Pomocí následujících postupů správně pochopit a spravovat svoji útratu.

### <a name="organize-and-tag-your-resources"></a>Uspořádání a označení prostředků

Uspořádání prostředků s náklady na paměti. Při vytváření skupiny prostředků a předplatná, představte si týmy, které jsou zodpovědné za související náklady. Ujistěte se, že sestav udržuje vaše organizace v úvahu. Skupiny prostředků a předplatná poskytují dobrou intervalů k uspořádání a atribut útraty napříč vaší organizací. Značky poskytují dobrý způsob, jak atribut náklady. Značky můžete použít jako filtr. A můžete je seskupit podle při analýze dat a prozkoumat náklady. Zákazníci se smlouvou Enterprise můžete také vytvořit oddělení a umístit předplatná pod nimi. Náklady na základě organizace v Azure pomáhá zachovat relevantní osoby ve vaší organizaci accountable pro snížení jejich tým útraty.

### <a name="use-cost-analysis"></a>Použití analýzy nákladů

Analýza nákladů vám umožní analyzovat vaše organizace náklady na podrobné podle segmentování a analyzování náklady na používání vlastností standardních prostředků. Vezměte v úvahu následující běžné otázky a jako vodítko pro analýzu. Odpovědi na tyto otázky v pravidelných intervalech, pomůže vám Udržujte si přehled více a povolit další rozhodnutí hledí na cenu.

- **Odhadované náklady pro aktuální měsíc** – kolik máte účtují zatím tento měsíc? Bude Moje rozpočtu dodrželi?
- **Prozkoumat anomálie** – rutiny kontrolu, abyste měli jistotu, že náklady na zůstanou v rozumné rozsahu normálního využití. Co jsou trendy? Existují jakékoli odlehlé hodnoty?
- **Vyrovnání faktury** – je Moje nejnovější invoiced nákladů více než předchozí měsíc? Jak útraty návyky změnila měsíce na měsíc?
- **Interní vrácení peněz** – teď, když mi vědět, kolik se mi účtuje, jak by tyto poplatky rozdělit pro moji organizaci?

Další informace najdete v tématu [analýza nákladů](quick-acm-cost-analysis.md).

### <a name="export-billing-data-on-a-schedule"></a>Export fakturačních dat podle plánu

Je potřeba pro import vašich fakturačních dat do externího systému, jako je řídicí panel nebo finanční systému? Můžete naplánovat automatické sestavy, každý den, aby se zabránilo ručního stahování souborů každý měsíc. A můžete exportovat fakturačních dat do účtu služby Azure storage a dostanete oznámení pomocí [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md). Pak můžete použít Azure data zkombinovat s vlastními daty, které můžete použít ve vlastním systémům.

Další informace o exportu fakturačních dat najdete v tématu [vytvořit a spravovat exportovaná data](tutorial-export-acm-data.md).

### <a name="create-budgets"></a>Vytvoření rozpočtů

Po identifikovat a analyzovat vaše útraty postupy, je důležité začít nastavení omezení pro vás a vašich týmů. Azure rozpočty poskytují možnost nastavit náklady nebo podle využití rozpočtu s mnoha prahové hodnoty a výstrahy. Nezapomeňte zkontrolovat rozpočty, které vytvoříte pravidelně sledujte své pokroky vypalování dolů rozpočtu a provést změny podle potřeby. Azure rozpočty také umožňují konfigurovat aktivační událost automatizace při dosažení prahové hodnoty daného rozpočtu. Můžete například nakonfigurovat svoji službu pro vypnutí virtuálních počítačů. Nebo infrastrukturu můžete přesunout do jiné cenové úrovni v reakci na aktivační událost rozpočtu.

Další informace najdete v tématu [Azure rozpočty](tutorial-acm-create-budgets.md).

Další informace o automatizaci v rozpočtu, naleznete v tématu [automatizace na základě rozpočtu](../billing/billing-cost-management-budget-scenario.md).

## <a name="act-to-optimize"></a>Jednejte a optimalizace
Použijte tyto způsoby optimalizovat výdaje.

### <a name="cut-out-waste"></a>Vystřihněte plýtvání

Po nasazení infrastruktury v Azure, je důležité zajistit, aby že se používá. Nejjednodušší způsob, jak začít, ukládají se okamžitě je ke kontrole vašich prostředků a odeberte všechny, které nejsou používány. Tady byste měli určit, pokud vaše prostředky se používají jako efektivně.

#### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor je služba, která, mimo jiné identifikuje virtuální počítače s nízkou úrovní využití z hlediska využití procesoru nebo sítě. Odtud můžete vypnout nebo změnit velikost počítače podle odhadované náklady na provoz bude moct být spuštěná na počítačích. Advisor také poskytuje doporučení k nákupu rezervovaných instancí. Doporučení jsou založené na vašich za posledních 30 dní využití virtuálních počítačů. Pokud datová služba vykoná, doporučení můžete vám pomůže snížit svoji útratu.

Další informace najdete v tématu [Azure Advisoru](../advisor/advisor-overview.md).

### <a name="size-your-vms-properly"></a>Správné velikosti virtuálních počítačů

Velikosti virtuálních počítačů má významný dopad na celkové Azure nákladů. Počet virtuálních počítačů, je potřeba v Azure nemusí odpovídá aktuálně nasazené v místním datovém centru. Ujistěte se, že váš výběr správné velikosti pro úlohy, které chcete spustit.

Další informace najdete v tématu [Azure IaaS: určení správné velikosti a nákladů](https://azure.microsoft.com/resources/videos/azurecon-2015-azure-iaas-proper-sizing-and-cost/).

### <a name="use-purchase-discounts"></a>Použití slevy nákupu

Azure nabízí mnoho slevy, které vaše organizace by měly využít výhod ještě ušetříte peníze.

#### <a name="azure-reservations"></a>Azure rezervace

Azure rezervace umožňují Předplatíte jeden rok nebo tři roky virtuální počítač nebo databázi SQL výpočetní kapacitu. Platíte předem vám umožní získat slevu na prostředky, které používáte. Azure rezervace může výrazně snížit náklady na výpočetní výkon databáze SQL vaší virtuálního počítače – až 72 procent u průběžných plateb s jeden rok nebo tři roky předem k ničemu zavázat. rezervace poskytovat fakturační slevy a neovlivní jejich běhový stav virtuálního počítače nebo databáze SQL.

Další informace najdete v tématu [co jsou Azure rezervace?](../billing/billing-save-compute-costs-reservations.md).

#### <a name="use-azure-hybrid-benefit"></a>Použít zvýhodněné hybridní využití Azure

Pokud již máte licence Windows serveru nebo SQL Server v místních nasazení, můžete program zvýhodněné hybridní využití Azure pro uložení v Azure. S využitím Windows serveru každá licence pokrývá náklady na operační systém (až dva virtuální počítače) a platíte jenom základní výpočetní náklady. Můžete použít stávající licence SQL serveru a ušetřete až 55 procent na založený na virtuálních jádrech možnosti služby SQL Database. mezi možnosti patří SQL Server v Azure Virtual Machines a služby SQL Server Integration Services.

Další informace najdete v tématu [Kalkulačka úspor pro program zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

### <a name="other-resources"></a>Další prostředky

Azure má také služba, která umožňuje vytvářet služby, které využívají nadbytečnou kapacitu v Azure pro nižší sazby. Další informace najdete v tématu [s nízkou prioritou virtuálních počítačů pomocí služby Batch](../batch/batch-low-pri-vms.md).

## <a name="next-steps"></a>Další postup
- Pokud do služby Cost Management začínáte, přečtěte si [co je Azure Cost Management?](overview-cost-mgt.md) zjistěte, jak pomáhá monitorovat a kontrolovat útraty Azure a optimalizovat využití prostředků.

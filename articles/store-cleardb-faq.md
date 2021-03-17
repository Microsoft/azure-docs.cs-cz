---
title: Nejčastější dotazy k databázím MySql v ClearDB s Azure App Service
description: Odpovědi na běžné dotazy týkající se použití ClearDB databází MySQL s Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: d9d4ae746c192e8b4afd1781c1e072fc0dc70422
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599797"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Nejčastější dotazy k databázím MySql v ClearDB s Azure App Service
Tato Nejčastější dotazy vám kladou odpovědi na nejčastější dotazy týkající se používání a nákupu databází MySQL ClearDB pro Azure Web Apps.

> [!IMPORTANT]
> Od 13. června 2018 ClearDB přechod zákazníků založených na Azure, který se v současnosti účtuje Microsoftem, na přímý fakturační model s ClearDB. Informace v tomto článku jsou teď zastaralé. Už nebudete moct vytvářet ani upgradovat databázi ClearDB, která se vytvořila v Azure.
>
> Další podrobnosti a další kroky najdete v tématu [změny v plánech služby ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jaké možnosti mám pro MySQL v Azure?
Nejnovější informace o této službě najdete v tématu [ClearDB](https://w2.cleardb.net/) . ClearDB je hostingová služba MySQL a spravuje infrastrukturu MySQL za vás. 

Pro hostování MySQL v Azure máte několik dalších možností:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Cluster MySQL běžící na virtuálním počítači Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Jedna instance MySQL spuštěná na virtuálním počítači Azure](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Potřebuji pro šablonu Web App + MySQL v Azure Marketplace platební kartu?
To závisí na typu předplatného, které používáte. Tady jsou některé běžně používané typy předplatného:

* [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/): vyžaduje platební kartu a při nákupu placené databáze MySQL se vám bude účtovat vaše kreditní karta.
* [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/): zahrnuje kredity pro použití s Microsoft Azure službami, ale neumožňuje nákup prostředků třetích stran. Pokud chcete koupit služby třetích stran nebo placená databáze MySQL, musíte použít předplatné s povolenými kreditními kartami. Pro Web Apps můžete vytvořit bezplatnou ClearDB databázi MySQL.
* [Předplatné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) a **průběžné testování MSDN pro vývoj za** použití: podobně jako bezplatné zkušební období vyžaduje předplatné MSDN, abyste si koupili placené řešení MySQL z ClearDB.
* [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): zákazníci se smlouvou EA se účtují za každé čtvrtletí smlouvy EA pro všechny své Azure Marketplace (nákup třetích stran) na samostatné konsolidované faktuře. Účtují se za všechny nákupy na webu Marketplace za rámec předplacení Azure (dříve označované jako peněžní závazek). Upozorňujeme, že v tuto chvíli není Azure Storu k dispozici pro zákazníky, kteří se zaregistrovali v Ázerbájdžánu, Chorvatsku, Norsku a Portoriko. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Proč se mi u webové aplikace + MySQL z Azure Marketplace účtují $3,50?
Výchozí možnost databáze je Titan, což je $3,50. Neúčtujeme vám náklady během vytváření databáze a můžete si omylem koupit databázi, kterou jste nechtěli zavádět. Snažíme se najít způsob, jak vylepšit prostředí, ale až potom musíte zaškrtnout všechny vybrané cenové úrovně pro webovou aplikaci a databázi před tím, než kliknete na **vytvořit** a spustit nasazení prostředků.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Spouštím MySQL na mém vlastním virtuálním počítači Azure. Můžu připojit svou webovou aplikaci Azure k databázi?
Yes. Webovou aplikaci můžete připojit k databázi, pokud váš virtuální počítač Azure má vzdálený přístup k vaší webové aplikaci. Další informace najdete v tématu [Instalace MySQL na virtuálním počítači](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Ve kterých zemích nebo oblastech jsou podporované clustery ClearDB Premium MySQL?
Clustery ClearDB Premium MySQL jsou dostupné ve všech oblastech Azure po celém světě s výjimkou Indie, Austrálie, Brazílie – jih a Číny.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Je možné vytvořit nový cluster před vytvořením databáze s řešením clusteru ClearDB Premium?
Ne, vytváření prázdných clusterů ClearDB se nepodporuje. Azure Portal umožňuje vytvářet databáze v clusteru, což může vytvořit nový cluster současně.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Zobrazí se mi upozornění, když se pokouším odstranit databázi MySQL ClearDB, kterou používá jedna z mých aplikací?
Ne, Azure vám neupozorní na odstranění nákupu na webu Marketplace, na kterém vaše aplikace závisí.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Ve kterých oblastech můžu vytvářet databáze ClearDB?
Azure Marketplace není k dispozici pro zákazníky, kteří se zaregistrovali v Ázerbájdžánu, Chorvatsku, Norsku nebo Portoriko. ClearDB není v těchto oblastech k dispozici.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jakou cenovou úroveň mám zvolit pro produkční webovou aplikaci a databázi?
Pro Web Apps použijte základní nebo vyšší cenovou úroveň. Pro ClearDB doporučujeme buď plán Saturn, nebo Jupiter. Přečtěte si o funkcích & omezeních každé cenové úrovně pro databáze [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) a [ClearDB MySQL](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) , abyste vybrali tu, která vyhovuje vašim potřebám.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Návody upgradovat moji databázi ClearDB z jednoho plánu na jiný?
V [Azure Portal](https://portal.azure.com)můžete škálovat ClearDB sdílenou hostující databázi. Přečtěte si tento [článek](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) , kde najdete další informace. V tuto chvíli v Azure Portal Nepodporujeme upgrade pro clustery ClearDB Premium.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nemohu v Azure Portal zobrazit databázi ClearDB?
Pokud jste databázi ClearDB vytvořili v klasickém prostředí, nebudete moci zobrazit vaši databázi v [Azure Portal](https://portal.azure.com). Pro tento scénář neexistuje žádné řešení.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Na koho se mám obrátit, když je moje databáze mimo provoz?
Kontaktujte [podporu ClearDB](https://www.cleardb.com/developers/help/support) pro všechny problémy související s databází. Připravte se na poskytování informací o předplatném Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Můžu vytvořit další uživatele pro moje řešení clusteru databáze MySQL ClearDB?
No. Nemůžete vytvořit další uživatele, ale v clusteru databáze ClearDB můžete vytvořit další databáze.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Můžou se databáze řady Basic/pro na portálu ClearDB upgradovat na místě, podobně jako plány Planetary.
Ano, základní databáze řady lze upgradovat místně (Basic 60 až Basic 500). Řadu sady pro je možné upgradovat místně (pro 125 až pro 1000) s výjimkou pro 60 pro. Momentálně nepodporujeme upgrade databáze pro 60. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Při migraci prostředků z jednoho předplatného na jiný se mi migruje i moje ClearDB databáze MySQL?
Při provádění migrace prostředků mezi předplatnými platí některá [omezení](azure-resource-manager/management/move-support-resources.md) . ClearDB MySQL je služba třetí strany, která se proto nemigruje během migrace předplatného Azure. Pokud před migrací prostředků Azure nespravujete migraci vaší databáze MySQL, ClearDB databáze MySQL je možné zakázat. Nejdřív ručně migrujte svoje databáze a pak u své webové aplikace proveďte migraci předplatného Azure. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Dosáhli jste limitu útraty u svého předplatného. Odebrali jste limit a moje App Service je online, ale databáze není přístupná. Návody znovu povolit databázi ClearDB?
Pokud chcete databázi znovu povolit, obraťte se na [podporu ClearDB](https://www.cleardb.com/developers/help/support) . Poskytněte jim informace o vašem předplatném Azure a název databáze.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Můžu přenést databázi ClearDB z předplatného platební karty do předplatného EA?
Existující databáze ClearDB používají platební kartu přidruženou ke stávajícím předplatným. Pokud chcete použít předplatné EA, potřebujete migrovat data do nové databáze:

* Zakupte novou databázi ClearDB s předplatným EA.
* Migrujte data do nové databáze.
* Aktualizujte aplikaci tak, aby používala novou databázi.
* Odstraňte starou databázi ClearDB.

Když vytvoříte novou webovou aplikaci s MySQL (ClearDB) nebo vytvoříte databázi MySQL (ClearDB), předplatné, které zvolíte, určuje, jak budete platit za službu. V rámci předplatného EA nebudeme zablokovat nákup služeb třetích stran, jako je ClearDB v Azure Portal. Předplatná EA se účtují mimo předplatné Azure a účtují se čtvrtletně a v nedoplatkech. Zákazník EA by musel nastavit způsob platby, jako je platební karta, která bude platit pro jakékoli služby tržiště třetích stran.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Kde můžu zobrazit poplatky za prostředky ClearDB v předplatném EA?
Pro zákazníky s přímým přístupem EA se Azure Marketplace poplatky na Enterprise Portal zobrazit. Všimněte si, že všechny nákupy a spotřebu na webu Marketplace se účtují mimo předplacenou platbu Azure a účtují se čtvrtletně a v nedoplatkech. Zákazníci se smlouvou EA musí platit přímo poskytovatelům služeb třetích stran, a to tak, že umožňují způsob platby, jako je platební karta s jejich účtem EA.

Nepřímí zákazníci se smlouvou EA můžou najít své Azure Marketplace předplatná na stránce **spravovat předplatná** Enterprise Portal, ale ceny jsou skryté. Pokud zákazníci chtějí získat informace o poplatcích za marketplace, měli by se obrátit na svého poskytovatele LSP.

Přístup k Azure Marketplace pro služby třetích stran může spravovat správce registrace EA Azure. Můžou zakázat nebo znovu povolit nákupy z obchodu třetích stran v části Správa účtů a předplatných v části účty v Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Na koho se mám obrátit s dotazy ohledně služby faktura za službu ClearDB ve svém předplatném EA?
Kontaktujte [zákaznickou podporu](https://aka.ms/AzureEntSupport) s ohledem na fakturaci v rámci jejich registrace EA. Tým podpory pro portál EA odpoví na vaši otázku nebo vám pomůže vyřešit váš problém.

## <a name="more-information"></a>Další informace
[Nejčastější dotazy k Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)
---
title: Nejčastější dotazy k databázím ClearDB MySql se službou Azure App Service
description: Odpovědi na běžné otázky týkající se používání databází ClearDB MySQL se službou Azure App Service.
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
ms.openlocfilehash: ddbf1eb960a24cfaa8d09ab45b6febca07e6d504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979914"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Nejčastější dotazy k databázím ClearDB MySql se službou Azure App Service
Tyto nejčastější dotazy odpovídají na běžné otázky týkající se používání a nákupu databází ClearDB MySQL pro Azure Web Apps.

> [!IMPORTANT]
> června 2018 přešla ClearDB na zákazníky azure, kterým microsoft momentálně účtuje, na model přímé fakturace s ClearDB. Informace v tomto článku jsou nyní zastaralé. Už nebudete moct vytvořit nebo upgradovat databázi ClearDB, která byla vytvořena v Azure.
>
> Další podrobnosti a další kroky naleznete [v tématu Změny plánů služeb ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jaké mám možnosti pro MySQL v Azure?
Nejnovější informace o této službě naleznete v [části ClearDB.](https://w2.cleardb.net/) ClearDB je hostingová služba MySQL a spravuje infrastrukturu MySQL za vás. 

Máte několik dalších možností pro hostování MySQL v Azure:
* [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/)
* [Cluster MySQL spuštěný na virtuálním počítači Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Jedna instance MySQL spuštěná na virtuálním počítači Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Potřebuji platební kartu pro webovou aplikaci + šablonu MySQL na Azure Marketplace?
To závisí na typu předplatného, které používáte. Tady jsou některé běžně používané typy předplatného:

* [Pay as you Go](https://azure.microsoft.com/offers/ms-azr-0003p/): Vyžaduje kreditní kartu, a když si koupíte zaplacenou databázi MySQL vaše kreditní karta je účtována.
* [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/): Zahrnuje kredity pro použití se službami Microsoft Azure, ale neumožňuje nákup prostředků třetích stran. Chcete-li zakoupit služby třetích stran nebo placenou databázi MySQL, musíte použít předplatné s povolenou kreditní kartou. Pro webové aplikace můžete vytvořit bezplatnou databázi ClearDB MySQL.
* [Předplatné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) a **msdn dev test průběžně**: Podobně jako bezplatná zkušební verze, předplatné MSDN vyžaduje, abyste měli kreditní kartu k nákupu placeného řešení MySQL od společnosti ClearDB.
* [Smlouva Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): Zákazníkům eA se každé čtvrtletí účtuje vůči eA za všechny nákupy na Azure Marketplace (třetí strany) na samostatné konsolidované faktuře. Za nákupy na trhu se vám účtuje mimo peněžní závazek. Upozorňujeme, že v tuto chvíli není Azure Store k dispozici zákazníkům zaregistrovaná v Ázerbájdžánu, Chorvatsku, Norsku a Portoriku. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Proč mi z Azure Marketplace byla účtována částka 3,50 USD za webovou aplikaci + MySQL?
Výchozí možnost databáze je Titan, což je $ 3.50. Nezobrazujeme náklady během vytváření databáze a můžete omylem zakoupit databázi, kterou jste neměli v úmyslu. Snažíme se najít způsob, jak zlepšit prostředí, ale do té doby je nutné zkontrolovat všechny vybrané cenové úrovně pro webovou aplikaci a databázi před kliknutím na **vytvořit** a spuštění nasazení prostředků.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Používám MySQL na svém vlastním virtuálním počítači Azure. Můžu svou webovou aplikaci Azure připojit k databázi?
Ano. Webovou aplikaci můžete připojit k databázi, pokud váš virtuální počítač Azure udělil vzdálený přístup k vaší webové aplikaci. Další informace naleznete [v tématu Instalace MySQL na virtuálním počítači](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Ve kterých zemích nebo oblastech jsou podporovány clustery ClearDB Premium MySQL?
Clustery ClearDB Premium MySQL jsou dostupné ve všech oblastech Azure po celém světě s výjimkou Indie, Austrálie, Brazílie – jih a Číny.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Je možné vytvořit nový cluster před vytvořením databáze pomocí prémiového clusteru ClearDB řešení?
Ne, vytváření prázdných clusterů ClearDB není podporováno. Portál Azure umožňuje vytvářet databáze v clusteru, které mohou vytvořit nový cluster ve stejnou dobu.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Budu upozorněn, pokud se pokusím odstranit databázi ClearDB MySQL, která je používána jednou z mých aplikací?
Ne, Azure vás neupozorní, pokud odstraníte nákup na marketplace, na kterém závisí vaše aplikace.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Ve kterých oblastech mohu vytvářet databáze ClearDB?
Azure Marketplace není k dispozici zákazníkům zaregistrovaná v Ázerbájdžánu, Chorvatsku, Norsku nebo Portoriku. ClearDB není v těchto oblastech k dispozici.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jakou cenovou úroveň si mám vybrat pro produkční webovou aplikaci a databázi?
Pro webové aplikace použijte základní nebo vyšší cenovou úroveň. Pro ClearDB doporučujeme plán Saturn nebo Jupiter. Zkontrolujte funkce & omezení jednotlivých cenových úrovní pro [webové aplikace](https://azure.microsoft.com/pricing/details/app-service/) i [databáze ClearDB MySQL](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) a vyberte si ten, který vyhovuje vašim potřebám.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Jak lze upgradovat databázi ClearDB z jednoho plánu do druhého?
Na [webu Azure Portal](https://portal.azure.com)můžete vertikálně navýšit kapacitu sdílené hostingové databáze ClearDB. Přečtěte si tento [článek](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) se dozvíte více. Momentálně nepodporujeme upgrade pro clustery ClearDB Premium na webu Azure Portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nevidím svou databázi ClearDB na webu Azure Portal?
Pokud jste vytvořili databázi ClearDB v klasickém, nebudete moci zobrazit databázi na [webu Azure Portal](https://portal.azure.com). Neexistuje žádné řešení pro tento scénář.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Na koho se mám obrátit kvůli podpoře, když je moje databáze vypnutá?
Obraťte se na [podporu ClearDB](https://www.cleardb.com/developers/help/support) pro všechny problémy související s databází. Buďte připraveni poskytnout jim informace o předplatném Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Mohu vytvořit další uživatele pro řešení clusteru databáze ClearDB MySQL?
Ne. Nelze vytvořit další uživatele, ale můžete vytvořit další databáze v databázovém clusteru ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Mohou být databáze řady Basic/Pro upgradovány na místě podobně jako planetární plány dnes na portálu ClearDB?
Ano, základní řady databází lze upgradovat na místě (Basic 60 až Basic 500). Řada Pro může být upgradována na místě (Pro 125 až Pro 1000) s výjimkou Pro 60. V současné době nepodporujeme upgrade databáze Pro 60. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Migruji prostředky z jednoho předplatného do druhého, migruje se i moje databáze ClearDB MySQL?
Při provádění migrace prostředků mezi předplatnými platí určitá [omezení.](azure-resource-manager/management/move-support-resources.md) Databáze ClearDB MySQL je služba třetí strany, a proto se během migrace předplatného Azure nemigruje. Pokud migraci prostředků Azure nespravujete migraci databáze MySQL, mohou být databáze ClearDB MySQL zakázány. Nejdřív migrujte databáze ručně a pak proveďte migraci předplatného Azure pro webovou aplikaci. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Dosáhl jsem limitu útraty u předplatného. Odstranil jsem limit a moje služba App Service je online, ale databáze není přístupná. Jak znovu povolím databázi ClearDB?
Obraťte se na [podporu ClearDB](https://www.cleardb.com/developers/help/support) znovu povolit databázi. Poskytněte jim informace o předplatném Azure a název databáze.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Mohu přenést databázi ClearDB z předplatného platební karty na předplatné EA?
Existující databáze ClearDB používají platební kartu přidruženou k existujícím předplatným. Chcete-li používat předplatné EA, musíte migrovat data do nové databáze:

* Kupte si novou databázi ClearDB s předplatným EA.
* Migrujte data do nové databáze.
* Aktualizujte aplikaci tak, aby používala novou databázi.
* Odstraňte starou databázi ClearDB.

Když vytvoříte novou webovou aplikaci s MySQL (ClearDB) nebo vytvoříte databázi MySQL (ClearDB), zvolené předplatné určuje, jak budete za službu platit. S předplatným EA nebudeme blokovat zadávání veřejných zakázek na služby třetích stran, jako je ClearDB na portálu Azure. Předplatné EA se účtuje mimo peněžní závazek a účtuje se čtvrtletně a v prodlení. Zákazník EA by musel nastavit způsob platby, jako je kreditní karta, aby mohl platit za jakékoli služby na trhu třetích stran.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Kde mohu zobrazit poplatky za prostředky ClearDB v předplatném EA?
Pro zákazníky Direct EA jsou poplatky azure marketplace viditelné na portálu Enterprise Portal. Upozorňujeme, že všechny nákupy a spotřeba na trhu se účtují mimo peněžní závazek a účtují se čtvrtletně a v prodlení. Zákazníci EA musí platit přímo poskytovatelům služeb třetích stran a mohou tak učinit povolením způsobu platby, jako je kreditní karta, se svým účtem EA.

Nepřímí zákazníci EA můžou svoje předplatná azure marketplace najít na stránce **Spravovat předplatná** na portálu Enterprise Portal, ale ceny jsou skryté. Pokud zákazníci chtějí získat informace o poplatcích za marketplace, měli by se obrátit na svého poskytovatele LSP.

Přístup na Azure Marketplace pro služby třetích stran můžou spravovat vaši správci registrace EA Azure. V části Účty na portálu Enterprise Portal můžou zakázat nebo znovu povolit přístup k nákupům třetích stran ze služby Store in Manage Accounts and Subscriptions.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Na koho se mám obrátit s dotazy ohledně mého účtu za služby ClearDB v mém předplatném EA?
Obraťte se na [podnikovou zákaznickou podporu](https://aka.ms/AzureEntSupport) s ohledem na fakturaci v rámci registrace ea. Tým podpory portálu EA vám odpoví nebo vám pomůže problém vyřešit.

## <a name="more-information"></a>Další informace
[Nejčastější dotazy k Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)


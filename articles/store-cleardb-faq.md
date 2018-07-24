---
title: Nejčastější dotazy k databázím ClearDB MySql pomocí Azure App Service | Dokumentace Microsoftu
description: Odpovědi na běžné dotazy týkající se použití databází ClearDB MySQL pomocí Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.workload: data-management
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f078cad95d39c18a8391bc32100d08accc53c37c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215199"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Nejčastější dotazy k databázím ClearDB MySQL ve službě Azure App Service
Tyto nejčastější dotazy odpovědi na běžné dotazy týkající se použití a nakupování databází ClearDB MySQL pro Azure Web Apps.

> [!IMPORTANT]
> Od 13. června 2018 se ClearDB přešla společnost Microsoft aktuálně účtují přímé fakturační model s ClearDB zákazníkům založené na Azure. Informace v tomto článku je nyní zastaralá. Už bude možné vytvořit nebo upgradovat databázi ClearDB, který byl vytvořen v Azure.
>
> Další podrobnosti a další kroky najdete v tématu [změní na plány služeb ClearDB](http://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).


## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jaké jsou možnosti pro MySQL v Azure?
Máte několik možností:

* [Databáze ClearDB MySQL sdílené](/marketplace/partners/cleardb/databases/)
* [Clustery ClearDB MySQL Premium](/marketplace/partners/cleardb-clusters/cluster/)
* [MySQL clusteru běžícího na Virtuálním počítači Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Jednu instanci MySQL běžící na Virtuálním počítači Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB MySQL, který je hostitelem služby je a spravuje MySQL infrastruktury za vás. Když spustíte vlastní MySQL cluster nebo databáze ve virtuálním počítači Azure, budete muset nastavit MySQL server a zachování jejich aktualizace s opravami.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Potřebuji platební kartu pro webová aplikace a MySQL šablony na webu Azure Marketplace?
To závisí na typu vašeho předplatného, který používáte. Tady jsou některé běžně používané předplatné:

* [Průběžné platby](/offers/ms-azr-0003p/): vyžaduje platební karta a při nákupu placené databáze MySQL se účtuje vaší platební karty.
* [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/): zahrnuje kredity pro použití s Microsoft Azure services, ale neumožňuje nákup materiály třetích stran. K nákupu služeb třetích stran nebo placené databázi MySQL, budete muset použít platební kartu povolené předplatné. Pro webové aplikace můžete vytvořit bezplatný ClearDB MySQL database.
* [Předplatné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) a **MSDN pro vývoj testování průběžných plateb**: podobně jako v případě bezplatné zkušební verze předplatného MSDN vyžaduje, abyste platební kartu zakoupit placené řešení MySQL od ClearDB.
* [Smlouvy Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): zákazníky se smlouvou EA se účtují podle příslušné smlouvy EA čtvrtletně pro všechny své nákupy na Azure Marketplace (třetí strany) na samostatné, konsolidované faktury. Bude se vám účtovat mimo peněžní závazek pro nákupy na marketplace. Mějte prosím na paměti, že v tuto chvíli není Azure Store dostupný pro zákazníky, které jsou zaregistrované v Ázerbájdžán, Chorvatsko, Norsko a Portoriko. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Proč se mi naúčtovala platba 3.50 pro webové aplikace a MySQL v Azure Marketplace?
Výchozí možnost databáze je Titan, což je 3.50 $. Jsme nezobrazovat náklady při vytváření databáze, a chybně jste si můžete koupit databázi, kterou jste neměli v úmyslu. Snažíme se najít způsob, jak vylepšit, ale do té doby je nutné zkontrolovat všechny vaše vybrané cenové úrovně pro webovou aplikaci a databázi před kliknutím na tlačítko **vytvořit** a spouští se nasazování prostředků.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Spouštím MySQL na vlastním virtuálním počítači Azure. Je Moje webové aplikace Azure připojit k databázi?
Ano. Webové aplikace může připojit k vaší databázi, tak dlouho, dokud váš virtuální počítač Azure udělil vzdálený přístup do vaší webové aplikace. Další informace najdete v tématu [instalace MySQL na virtuálním počítači](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Ve které země jsou podporované clustery ClearDB Premium MySQL?
[ClearDB Premium MySQL clustery](/marketplace/partners/cleardb-clusters/cluster/) jsou k dispozici ve všech oblastech Azure po celém světě s výjimkou Indie, Austrálie, Brazílie – jih a Čína.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Můžete vytvořit nový cluster před vytvořením databáze ClearDB řešení cluster úrovně premium?
Ne, vytváření clusterů prázdný ClearDB se nepodporuje. Na webu Azure portal umožňuje vytvářet databáze v clusteru, který může vytvořit nový cluster ve stejnou dobu.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Budou mi se pokusím-li odstranit databázi ClearDB MySQL, který se používá pomocí jedné z našich aplikací?
Ne, Azure nezobrazí upozornění je-li odstranit nákup na marketplace, které vaše aplikace závisí.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Jaké oblasti je možné vytvářet databáze ClearDB v?
Azure Marketplace není dostupná pro zákazníky, které jsou zaregistrované v Ázerbájdžán, Chorvatsko, Norsko a Portoriko. ClearDB není k dispozici v těchto oblastech.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jaké cenové úrovně je nejvhodnější pro produkční webovou aplikaci a databázi?
Použijte Basic nebo vyšší cenovou úroveň pro webové aplikace. ClearDB doporučujeme Saturn nebo Jupiter plánu. Kontrola funkce a omezení každá cenová úroveň pro obě [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) a [databází ClearDB MySQL](/marketplace/partners/cleardb/databases/) zvolit ten, který nejlépe vyhovuje vašim potřebám.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Jak můžu upgradovat Moje databáze ClearDB z jednoho plánu do jiného?
V [webu Azure portal](https://portal.azure.com), je možné škálovat i databázi ClearDB sdíleného hostingu. Přečtěte si tento [článku](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) Další informace. Aktuálně Nepodporujeme upgrade ClearDB Premium clusterů na portálu Azure portal.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nejde Zobrazit Moje databáze ClearDB na webu Azure portal?
Pokud jste databázi ClearDB vytvořili v klasickém, nebudete moci zobrazit vaše databáze v [webu Azure Portal](https://portal.azure.com). Neexistuje žádné alternativní pro tento scénář.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Koho se mám obrátit pro podporu při Moje databáze je mimo provoz?
Kontakt [podporu ClearDB](https://www.cleardb.com/developers/help/support) pro některé databázi související problémy. Připravte se na poskytují informace o vašem předplatném Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Můžete vytvořit další uživatele pro moje řešení clusteru databází ClearDB MySQL?
Ne. Nelze vytvořit další uživatele, ale můžete vytvořit další databází ve vašem clusteru databázi ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Můžete řady Basic/Pro databáze být místně upgradovány podobný dnes planetární plánů na portálu ClearDB?
Ano, základní řadu, kterou databáze může být upgradována místě (až 500 základní základní 60). Seriál pro profesionály může být místně upgradovány (Pro 125 až Pro 1000) s výjimkou Pro 60. Probíhá upgrade databáze Pro 60 aktuálně nepodporujeme. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Když Moje prostředky můžu migrovat z jednoho předplatného do druhého, se Moje databáze ClearDB MySQL migrují také?
Když provádíte migraci prostředků napříč předplatnými, některé [omezení](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) použít. Databáze ClearDB MySQL je služba třetí strany a proto nejsou migrována během migrace předplatného Azure. Pokud nespravujete migraci databáze MySQL před migrací prostředků Azure, je možné zakázat databází ClearDB MySQL. Nejprve ručně migrovat vaše databáze a pak proveďte migraci předplatného Azure pro vaši webovou aplikaci. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>V rámci mého předplatného dosáhnu limitu útraty. Po odebrání limitu a Moje služby App Service je online, ale databáze není dostupná. Jak znovu povolit databázi ClearDB?
Kontakt [podporu ClearDB](https://www.cleardb.com/developers/help/support) databázi znovu povolit. Jim poskytnout vaše předplatné Azure informace a název databáze.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Je možné převést databázi ClearDB z platební karty předplatného na předplatné EA?
Stávajících databází ClearDB použití platební karty přidružené stávajících předplatných. Chcete-li použití předplatného EA migrovat data do nové databáze:

* Kupte si novou databázi ClearDB v rámci předplatného EA.
* Migrace dat do nové databáze.
* Aplikaci aktualizujte, aby používal novou databázi.
* Odstraňte staré databáze ClearDB.

Když vytvoříte novou webovou aplikaci s MySQL (ClearDB) nebo vytvořit databázi MySQL (ClearDB), předplatné si zvolíte Určuje, jak můžete platit za služby. S předplatným EA jsme nezablokuje zajišťování služeb třetích stran, jako je například ClearDB na webu Azure Portal. Předplatných EA se účtují mimo peněžní závazek a se fakturují čtvrtletně a za uplynulé čtvrtletí. Úrovni zákazníka EA musel nastavit způsob platby, jako je například platební kartou platit za služby webu marketplace třetích stran.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Kde lze zobrazit poplatky za ClearDB prostředky v rámci předplatného EA?
Pro zákazníky Direct EA poplatky za Azure Marketplace jsou viditelné na podnikovém portálu. Poznámka: všechny nákupy na marketplace a využití se účtují mimo peněžní závazek se fakturují čtvrtletně a za uplynulé čtvrtletí. Zákazníci programu EA si muset zaplatit přímo poskytovatelům služeb třetích stran a to tak, že povolení způsob platby, jako je například platební karty se svým účtem EA.

Nepřímí zákazníci EA svá předplatná Azure Marketplace najdete na **spravovat předplatná** stránky na Enterprise Portal, ale ceny je skrytá. Zákazníci by kontaktovat LSP informace o poplatcích za marketplace.

Správce registrací EA Azure můžete spravovat přístup k webu Azure Marketplace pro služby třetích stran. Mohou zakázat nebo znovu získat přístup k 3. stran nákupy z Store Správa účtů a předplatných na webu Enterprise Portal v části účty.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Koho se mám obrátit pro otázky k faktuře služby ClearDB ve svém předplatném EA?
Kontakt [Enterprise zákaznickou podporu](http://aka.ms/AzureEntSupport) s ohledem na fakturaci podle jejich registraci smlouvy Enterprise. Tým podpory portál EA se odpověď na vaši otázku nebo váš problém vyřešit.

## <a name="more-information"></a>Další informace
[Nejčastější dotazy k Azure Marketplace](/marketplace/faq/)


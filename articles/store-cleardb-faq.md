---
title: Nejčastější dotazy týkající se databáze ClearDB MySql službou Azure App Service | Microsoft Docs
description: Odpovědi na časté otázky týkající se používání databází ClearDB MySQL službou Azure App Service.
documentationcenter: php
services: ''
author: sunbuild
manager: yochayk
editor: ''
tags: mysql
ms.assetid: c2ed5e78-6d7d-4d0c-b7ee-a52ae41ceab8
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ms.openlocfilehash: 8186e86bd7a441fcefb0759d75ded6f063a4722f
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28948032"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>Nejčastější dotazy k databázím ClearDB MySQL ve službě Azure App Service
Tyto nejčastější dotazy odpovědi na běžné dotazy týkající se použití a nákup databáze MySQL cleardb – pro webové aplikace Azure.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Jaké možnosti jsou pro databázi MySQL v Azure?
Máte několik možností:

* [Databáze MySQL cleardb – sdílené](/marketplace/partners/cleardb/databases/)
* [ClearDB MySQL Premium clustery](/marketplace/partners/cleardb-clusters/cluster/)
* [Cluster MySQL běžící na virtuálním počítači Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Jedna instance databáze MySQL spuštěna na virtuálním počítači Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB je MySQL, který je hostitelem služby a spravuje infrastrukturu MySQL za vás. Při spouštění vlastní MySQL cluster nebo databáze na virtuální počítač Azure, budete muset nastavit MySQL server a pravidelně ho aktualizujte s opravy.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Potřebuji platební karty pro Web app + šablony MySQL v Azure Marketplace?
To závisí na typu předplatné, které používáte. Zde jsou některé typy běžně používané předplatného:

* [Platím průběžně](/offers/ms-azr-0003p/): vyžaduje platební kartu, a při nákupu placené databáze MySQL je účtován platební karty.
* [Bezplatná zkušební verze](https://azure.microsoft.com/pricing/free-trial/): obsahuje kredity pro použití se službou Microsoft Azure, služby, ale neumožňuje nákup prostředků třetích stran. Chcete-li zakoupit služeb třetích stran nebo placené databáze MySQL, budete muset použít platební karty povolené předplatné. Pro webové aplikace můžete vytvořit databázi volné ClearDB MySQL.
* [Předplatné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) a **MSDN pro vývoj testování platím průběžně**: podobně jako u bezplatnou zkušební verzi, předplatné MSDN vyžaduje, abyste k nákupu placené řešení MySQL ClearDB platební karty.
* [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): EA zákazníků se účtují před jejich EA každé čtvrtletí pro všechny svůj nákup Azure Marketplace (třetí strany) na samostatné, konsolidované faktury. Fakturuje se mimo peněžních závazků pro všechny nákupy marketplace. Upozorňujeme, že v tomto okamžiku Azure úložiště není k dispozici zákazníkům zaregistrovaná Ázerbájdžán, Chorvatska, Norsko a Portoriko. 
* [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): je možné vytvořit jenom volné ClearDB databází pro webové aplikace. Neexistuje žádné omezení počtu bezplatné databáze MySQL cleardb – databáze, které můžete vytvořit. Všimněte si, že bezplatných databází není určen k použití pro provozní webové aplikace, protože tato služba je určena pouze pro zkušební verzi.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Proč se mi strhla platba 3.50 pro webové aplikace + MySQL v Azure Marketplace?
Výchozí možnost databáze je Titan, což je $3.50. Jsme nezobrazovat náklady během vytváření databáze a můžete zakoupit databázi, kterou jste neměli v úmyslu omylem. Snažíme se najít způsob, jak vylepšit ale do té doby je nutné před kliknutím na tlačítko Zkontrolovat všechny vaše vybrané cenové úrovně pro webovou aplikaci a databázi **vytvořit** a spuštění nasazení prostředků.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Používám MySQL na vlastní virtuální počítač Azure. Můžete Moje Azure webovou aplikaci připojit k databázi?
Ano. Webové aplikace můžete připojit k databázi, dokud svého virtuálního počítače Azure udělil vzdálený přístup do webové aplikace. Další informace najdete v tématu [nainstalovat MySQL na virtuálním počítači](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Ve kterém jsou jednotlivé země clustery ClearDB Premium MySQL podporované?
[Databáze MySQL ClearDB Premium clustery](/marketplace/partners/cleardb-clusters/cluster/) jsou k dispozici ve všech oblastech Azure po celém světě s výjimkou Indie, Austrálie, Brazílie – jih a Číně.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Můžete vytvořit nový cluster před vytvořením databázi ClearDB premium clusteru řešení?
Ne, vytváření clusterů prázdný ClearDB není podporován. Portál Azure umožňuje vytváření databází v clusteru, který může vytvoření nového clusteru ve stejnou dobu.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Zobrazí I upozornění, pokud pokusu o odstranění databáze ClearDB MySQL, která je používána jedním z mé aplikace?
Ne, Azure nebude varovat, pokud je odstranit nákupu marketplace, které závisí aplikace na.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Oblasti, které lze vytvořit ClearDB databází v?
Není k dispozici zákazníkům registrovaná v Ázerbájdžán, Chorvatsko, Norsko nebo Portoriko Azure Marketplace. ClearDB není k dispozici v těchto oblastech.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Jaké cenová úroveň by měl vybrat pro provozní webové aplikace a databáze?
Použijte základní nebo používat vyšší cenová úroveň pro webové aplikace. Pro cleardb – doporučujeme, abyste buď Saturn nebo Jupiter plán. Zkontrolujte funkce a omezení jednotlivých cenových úrovní pro obě [webové aplikace](https://azure.microsoft.com/pricing/details/app-service/) a [databáze ClearDB MySQL](/marketplace/partners/cleardb/databases/) zvolit ten, který vyhovuje vašim potřebám.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Jak se dá upgradovat databázi ClearDB z jeden plán do jiné?
V [portál Azure](https://portal.azure.com), můžete postupně škálovat sdílenou databázi ClearDB hostování. Přečtěte si to [článku](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) Další informace. Aktuálně nepodporujeme aktualizace pro clustery ClearDB Premium na portálu Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Nelze zobrazit databázi ClearDB na portálu Azure?
Když vytvoříte databázi ClearDB v klasickém, nebudete moci zobrazit vaše databáze v [portálu Azure](https://portal.azure.com). Neexistuje žádné řešení pro tento scénář.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Kdo I požádat o podporu Pokud databáze je dolů?
Obraťte se na [ClearDB podporu](https://www.cleardb.com/developers/help/support) pro všechny databáze související problémy. Připravte se na jim poskytnout informace o vašem předplatném Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Můžete vytvořit další uživatele pro moje řešení clusteru databáze ClearDB MySQL?
Ne. Nelze vytvořit další uživatele, ale můžete vytvořit další databáze v databázi ClearDB clusteru.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Může být databáze řady Basic/Pro upgradována místní podobná dnes planetární plány na portálu ClearDB?
Ano, základní řady, který může být databáze upgradovat na místě (základní 60 prostřednictvím základní 500). Pro řadu lze upgradovat na místě (Pro 125 prostřednictvím Pro 1000) s výjimkou Pro 60. Upgrade databáze Pro 60 aktuálně nepodporujeme. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Při migraci mé zdroje z jedno předplatné do jiného, databáze ClearDB MySQL migrována také?
Když provádíte migraci prostředků ve předplatných, některé [omezení](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) použít. Databáze ClearDB MySQL je službu třetí strany a proto nejsou migrována během migrace předplatného Azure. Pokud spravujete není migraci databáze MySQL před migrací prostředků Azure, je vaše databáze MySQL cleardb – zakázat. Nejprve ručně migrovat své databáze a pak proveďte migraci předplatného Azure pro vaši webovou aplikaci. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Můžu na Moje předplatné dosáhl limitu útraty. Po odebrání limit a Moje App Service je online, ale databáze je nedostupná. Jak znovu povolit databázi ClearDB?
Obraťte se na [ClearDB podporu](https://www.cleardb.com/developers/help/support) databázi znovu zapnout. Zadejte pro ně vašeho předplatného Azure informace a název databáze.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Lze přenést databázi ClearDB z platební karty odběru k předplatnému EA?
Existující databáze ClearDB pomocí platební karty spojené s existující odběry. Chcete-li použít předplatné EA migrace dat pro novou databázi:

* Kupte si novou databázi ClearDB s předplatným EA.
* Migrace dat na novou databázi.
* Aktualizace aplikace k používání nové databáze.
* Odstraňte starou databázi ClearDB.

Při vytváření nové webové aplikace s MySQL (cleardb –) nebo vytvoření databáze MySQL cleardb (–), určuje odběr, který zvolíte, jak bude platit pro službu. EA předplatné jsme neblokuje nákup služeb třetích stran, jako je ClearDB na portálu Azure. Odběry EA se účtují mimo peněžních závazků a fakturují se čtvrtletně a zpětně. Zákazník EA by měla mít nastavit způsob platby, jako je například platební karty platit pro všechny služby třetích stran marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Kde lze zobrazit poplatky za ClearDB prostředků v předplatném služby EA?
Pro zákazníky přímé EA Azure Marketplace poplatky jsou viditelné na podnikovém portálu. Všimněte si, že všechny nákupy marketplace a spotřeba se účtují mimo peněžních závazků a fakturují se čtvrtletně a zpětně. Zákazníci, EA platit přímo na poskytovatele služeb třetích stran a k tomu povolení platby například platební karty pomocí svého účtu EA.

Nepřímý zákazníkům EA najdete na svých předplatných Azure Marketplace **Spravovat odběry** stránce portálu Enterprise ale ceny skryt. Zákazníci získáte jejich LSP informace na webu marketplace poplatky.

Správci registrace EA Azure lze spravovat přístup k Azure Marketplace pro služby třetích stran. Mohou zakázat nebo znovu povolit přístup ke 3. stran nákupy z úložiště v Správa účtů a odběry části účty v podnikovém portálu.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Kdo I contact pro dotazy o Moje fakturovaná částka u ClearDB služby v Moje předplatné EA?
Obraťte se na [Enterprise zákaznickou podporu](http://aka.ms/AzureEntSupport) s ohledem na fakturace pod jejich EA registrace. Tým podpory portál EA bude odpovídající vaší otázce nebo váš problém vyřešit.

## <a name="more-information"></a>Další informace
[Nejčastější dotazy k Azure Marketplace](/marketplace/faq/)


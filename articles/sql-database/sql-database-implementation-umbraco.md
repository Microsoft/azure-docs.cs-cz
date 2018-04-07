---
title: Azure SQL databáze Azure Případová studie - Umbraco | Microsoft Docs
description: Další informace o tom, jak Umbraco používá SQL Database k rychlému zřízení a škálování služby pro tisíce klienty v cloudu
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 1079adb6ef8a206506823fdee6721aabbf857b4d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco používá Azure SQL Database k rychlému zřizování a škálování služby pro tisíce klienty v cloudu
![Umbraco Logo](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco je oblíbených open-source obsahu – systém správy (CMS), můžete spustit nic z malé weby kampaně nebo – příručka pro komplexní aplikace pro žádnou 500 společnosti a globální média weby. 

> "Máme poměrně velké Komunita vývojářů, kteří používají systém, s více než 100 000 vývojáři na našich fórech a více než 350,000 lokalit, které jsou nasazeny, systémem Umbraco."
> 
> – Mortena Christensen, technické realizace, Umbraco
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

Pro zjednodušení implementace zákazníků, Umbraco přidat Umbraco jako-Service (UaaS): nabídku softwaru jako služba (SaaS), která eliminuje potřebu místní nasazení poskytuje integrované škálování a odebere správu režie tím, že umožňuje vývojářům zaměřit na inovace spíše než řešení pro správu produktu. Umbraco je schopný poskytnout všechny tyto výhody podle flexibilní modelu platforma jako služba (PaaS) nabízí Microsoft Azure.

UaaS umožňuje zákazníkům SaaS použít Umbraco CMS funkce, které byly dříve mimo jejich reach. Těchto zákazníků se zřídí pomocí pracovního prostředí CMS, která obsahuje provozní databáze. Zákazníci můžete přidat až dva další databáze pro vývoj a testovací prostředí, v závislosti na jejich požadavky. Pokud se požaduje nového prostředí, automatizovaného procesu přiřadí tohoto zákazníka databáze automaticky. Nové databáze je připraven v sekundách, protože databáze již předem zřízená podle Umbraco z Azure elastickém fondu k dispozici databází (viz obrázek 1).

![Zřizování životního cyklu Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Obrázek 1. Zřizování životní cyklus Umbraco jako služba (UaaS)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure elastické fondy a automatizace zjednodušit nasazení
S Azure SQL Database a jinými službami Azure Umbraco zákazníci mohou samoobslužně zřizovat jejich prostředí a Umbraco můžete snadno sledovat a spravovat databáze v rámci intuitivní pracovního postupu:

1. Zřídit
   
   Umbraco udržuje kapacitou 200 dostupné předem zřízená databáze z elastické fondy. Pokud nové zákazník zaregistruje UaaS, Umbraco poskytuje zákazník s nového prostředí CMS skoro v reálném čase přiřazením databázi z fondu dostupnosti.
   
   Když fond dostupnosti dosáhne prahové hodnoty, se vytvoří nový elastický fond a nové databáze jsou předem zřízená přiřazen zákazníků podle potřeby.
   
   Implementace je plně automatizovat pomocí správy knihovny jazyka C# a fronty Azure Service Bus.
2. Využívat
   
   Zákazníci používat jednu až tři prostředí (pro produkční, pracovní nebo vývoj), každý s svou vlastní databázi. V elastické fondy, které umožňuje Umbraco zajistit efektivní škálování bez nutnosti přepsání zřídit jsou databáze zákazníka.
   
   ![Přehled Umbraco projektu](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Umbraco podrobností projektu](./media/sql-database-implementation-umbraco/figure3.png)
   
   Obrázek 2. Web zákazníka Umbraco jako-Service (UaaS), zobrazující projektu přehled a podrobnosti
   
   Azure SQL Database jednotky transakcí databáze (Dtu) používá k reprezentaci relativní výkon požadované pro skutečné databázové transakce. Pro zákazníky UaaS databáze obvykle fungují v asi 10 Dtu, ale každá z nich má pružnost škálování na vyžádání. To znamená, že UaaS můžete zajistit, že zákazníci potřebné prostředky, vždy mají i během špiček. Například během poslední události sportu nedělní píků jednu databázi zkušeného zákazníka UaaS až 100 Dtu po dobu trvání hra. Azure elastické fondy umožněno Umbraco pro podporu tohoto vysokého zatížení bez snížení výkonu.
3. Monitorování
   
   Umbraco monitorování databáze aktivity používat řídicí panely v rámci portálu Azure, spolu s vlastní e-mailové výstrahy.
4. Zotavení po havárii
   
   Azure nabízí dvě možnosti zotavení po havárii (DR): aktivní geografickou replikaci a geografické obnovení. Možnost zotavení po Havárii, která společnost měli vybrat závisí na jeho [kontinuity podnikových procesů cíle](sql-database-business-continuity.md).
   
   aktivní geografickou replikací poskytuje nejrychlejší úroveň odpovědi v případě výpadku. Používá aktivní geografickou replikaci, můžete vytvořit až čtyři sekundární databáze čitelných na serverech v různých oblastech, a potom můžete spustit převzetí služeb při selhání do jakéhokoli sekundárních databází v případě selhání.
   
   Umbraco nevyžaduje geografická replikace, ale je využít výhod Azure geografické obnovení k zajištění minimální výpadku v případě výpadku. geografické obnovení spoléhá na zálohování databáze v geograficky redundantní úložiště Azure. Umožňuje uživatelům obnovit ze záložní kopie, když dojde k výpadku v primární oblasti.
5. Deaktivace přidělení
   
   Při odstranění prostředí projektu, se odeberou všechny přidružené databáze (vývoj, přípravné nebo za provozu) při čištění fronty Azure Service Bus. Tento proces automatické obnoví nepoužívané databází do fondu elastické databáze dostupnosti Umbraco, aby byly k dispozici pro budoucí zřizování při zachování maximální využití.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Elastické fondy umožňují UaaS ke škálování se usnadnění
Využitím Azure elastické fondy, Umbraco, můžete optimalizovat výkon pro své zákazníky bez nutnosti over nebo snížení zřizování. Umbraco má v současnosti téměř 3000 databází mezi 19 elastické fondy, umožňující snadno škálovat podle potřeby pro přizpůsobení jejich stávající zákazníky služby 325,000 nebo nové zákazníky, kteří jsou připraveny k nasazení systému CMS v cloudu.

Ve skutečnosti podle Mortena Christensen, technické vést v Umbraco, "UaaS nyní dochází k růstu asi 30 nové zákazníky za den. Naše zákazníky jsou ctí s výhodou moct zřídit nové projekty v sekundách, okamžitě publikování aktualizací na svůj web za provozu z prostředí pro vývoj pomocí 'nasazení jedním kliknutím a změnit tak, jak rychle, pokud najdou chyby."

Pokud zákazník nevyžaduje druhý a třetí prostředí už, ho můžete jednoduše odebrat tato prostředí. Který uvolní prostředky, které můžete využít jako součást fondu elastické databáze dostupnosti Umbraco ostatních zákazníků.

![Architektura nasazení služby Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Obrázek 3. Architektura UaaS nasazení v Microsoft Azure

## <a name="the-path-from-datacenter-to-cloud"></a>Cesta z datacentra do cloudu
Když vývojáři Umbraco původně rozhodnutí o přesun modelu SaaS, znal by, že potřebují cenově výhodné a škálovatelné způsob, jak sestavit na službu.

> "elastické fondy jsou vzájemně přizpůsobit pro naše SaaS nabídky vzhledem k tomu, že jsme vytočit kapacity nahoru a dolů podle potřeby. Zřizování je snadné a naše instalačního programu, abychom využití na maximální."
> 
> – Mortena Christensen, technické realizace, Umbraco
> 
> 

"Jsme chtěli věnovat naše času na řešení problémů s našich zákazníků, není správu infrastruktury. Niels Hartvig, zakladatele Umbraco jsme chtěli usnadňují naše zákazníky, získat maximální hodnotu"říká. "Původně za hostitelské servery, na sebe, ale plánování kapacity by byl při důvodů." Shodou okolností Umbraco není využívat všechny správce databáze, které podtržítka nabídky hodnota klíče pro použití UaaS.

Jeden cíl důležité pro vývojáře Umbraco bylo poskytují způsob, jak zákazníci UaaS ke zřízení prostředí rychle a bez omezení kapacity. Ale poskytnutí vyhrazené hostovanou službu v datových centrech Umbraco by vyžadovaly spoustu přebytečnou kapacitou pro zpracování shluky ve zpracování. Který by chtěl přidání významnou výpočetní infrastrukturu, která by mít byla pravidelně nedostatečně využité.

Kromě toho vývojový tým Umbraco chtěli řešení, které by mohly opakovaně používat co nejvíc svůj existující kód nejblíže. Jako vývojář Umbraco stavy Mikkel Madsen, "nám radostí s vývojové nástroje společnosti Microsoft, které nám již obeznámeni s, jako je Microsoft SQL Server, Microsoft Azure SQL Database, ASP.net a Internetová informační služba (IIS). Než začne investovat IaaS nebo PaaS cloudové řešení, jsme chtěli Ujistěte se, že ho by podporují naše nástroje společnosti Microsoft a platformy, takže jsme nebude muset provést velkých změn naše kódu základní. "

Pro všechna její kritéria splňují, hledá Umbraco pro partnera cloudu s kvalifikací následující:

* Dostatečnou kapacitu a spolehlivost
* Podpora pro vývojové nástroje společnosti Microsoft, aby technici Umbraco by se vynutilo úplně reinvent jejich vývojového prostředí
* Přítomnosti ve všech zeměpisné trhy, ve kterých UaaS bojuje (podnikům třeba zajistit, že se můžete rychle přístup k datům a uložení svých dat v umístění, které splňuje jejich místní zákonné požadavky)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Proč Umbraco zvolili Azure pro UaaS
Podle Mortena Christensen "po zvážení všech našich možností, jsme vybrali Azure vzhledem k tomu, že se splní všechny naše kritéria, z možností správy a škálovatelnost znalosti a efektivity nákladů. Nastavení prostředí na virtuálních počítačích Azure a každé prostředí má svou vlastní instanci databáze SQL Azure s všechny instance elastické fondy. Oddělením databází mezi vývoj, pracovní a za provozu prostředí můžete nabízí svým zákazníkům izolaci výkonu robustní namapovat na škálování – velký win. "

Pokračuje Mortena, "před, jsme měl zřídit na serverech pro webové databáze ručně. Nemáme teď myslíte o něm. Všechno, co je automatizováno – od zřízení až po vyčištění. "

Mortena je také radostí s možnosti škálování poskytovaný platformou Azure. "elastické fondy jsou vzájemně přizpůsobit pro naše SaaS nabídky vzhledem k tomu, že jsme vytočit kapacity nahoru a dolů podle potřeby. Zřizování je snadné a naše instalačního programu, abychom využití na maximální." Stavy Mortena, "jednoduchost elastické fondy, společně s ujištění na základě služeb vrstvy Dtu dává nám zřídit nové fondy zdrojů na vyžádání. Nedávno, jeden z našich zákazníků větší Špičatá 100 Dtu ve svém prostředí za provozu. Použití Azure, naše elastické fondy zadat zákazníka databáze s prostředky, které budou potřebné v reálném čase bez nutnosti k předvídání požadavků na DTU. Jednoduše řečeno, naše zákazníky můžete získat čas očekávané a jsme naplňují našich smluv o úrovni služeb výkonu."

Mikkel Madsen shrnuje ho: "Jsme jste kterých je založena výkonné Azure algoritmus, který připojí běžný scénář SaaS (nové zákazníky pro registrace v reálném čase ve velkém měřítku) na našem vzor aplikací (předem zřizování databáze, i vývoj a za provozu) nad základní technologii (pomocí fronty Azure Service Bus ve spojení s Azure SQL Database)."

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>S Azure UaaS přesahuje očekávání zákazníka
Od vyberete Azure jako jeho partnerský server pro cloud, byl Umbraco moci poskytnout zákazníkům UaaS optimálního výkonu správy obsahu, bez investice IT prostředků vyžaduje z vlastním hostováním řešení. Jako Mortena informacemi o tom, "jsme rádi důvodu usnadnění práce vývojářů a škálovatelnost, která nám dává Azure a našich zákazníků jsou thrilled s funkcemi a spolehlivosti. Celkově platí byla skvělé win pro nás!"

## <a name="more-information"></a>Další informace
* Další informace o Azure elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).
* Další informace o Azure Service Bus, najdete v části [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Další informace o virtuální sítě najdete v tématu [virtuální sítě](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Další informace o zálohování a obnovení najdete v tématu [kontinuity podnikových procesů](sql-database-business-continuity.md).    
* Další informace o monitorování ppols najdete v tématu [monitorování fondy](sql-database-elastic-pool-manage-portal.md).    
* Další informace o Umbraco jako služba, najdete v části [Umbraco](https://umbraco.com/cloud).


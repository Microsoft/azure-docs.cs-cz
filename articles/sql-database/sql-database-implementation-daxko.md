---
title: Azure SQL Database, Azure případovou studii – Daxko/CSI | Dokumentace Microsoftu
description: Další informace o tom, jak Daxko/CSI používá SQL Database, ke zrychlení jeho vývojový cyklus a ke zvýšení jeho zákaznické a výkon
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: carlrab
ms.openlocfilehash: 10720f42f7a9b10b42ccaaaad81acca369592f6a
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731206"
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI používá Azure ke zrychlení jeho vývojový cyklus a ke zvýšení jeho zákaznické a výkon
![Daxko/CSI Logo](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Software Daxko/CSI ve složité: zákaznické základny vhodnosti a rekreace Center byla narůstá, díky úspěch své řešení komplexní podnikového softwaru ale udržení s potřebami infrastruktury IT pro tento rostoucí zákazníka základní byl testování vaší společnosti pracovníků IT. Společnost se stále omezeny zvyšující režii operací, zejména pro správu jejich rostoucí databáze. Horší byl tento režijní náklady na operace vyjmutí do zdroje pro vývoj nové iniciativ, jako jsou nové funkce mobility pro software společnosti.

Podle Davida Molina, ředitel produktu vývoje v Daxko/CSI Azure dostupný CSI Software pomocí modelu platforma jako služba (PaaS), která je potřeba k zjednodušení správy databáze, zvýšení škálovatelnosti a uvolnit tak prostředky a zaměřte se na software místo ops. "Azure SQL Database je skvělou možností pro nás. Nemusíte se starat o údržbu systému SQL Server, cluster převzetí služeb při selhání a všechno, co potřebuje další infrastrukturu je ideální pro nás."

Od migrace do Azure, potřebuje CSI softwaru provozní personál pouze dva ke správě více než 600 databáze zákazníků. Společnost používá k přesunutí databáze zákazníků na základě velikosti elastických fondů Azure SQL Database a potřebujete.

Pokračuje Molina "našim zákazníkům popisovač změny okamžitě. Před elastické fondy někdy měli vypršení časového limitu a další potíže během období burst. Pomocí Azure elastických fondů mohou navyšovat výkon podle potřeby a používat software bez problémů."

Kromě vylepšení výkonu pro zákazníky, elastické fondy Azure uvolnit CSI softwarové prostředky a zaměřte se na vývoj nových služeb a funkcí, namísto zpracování operace a správa. Tyto prostředky IT pomohly CSI softwaru vylepšit svůj software enterprise nabízí SpectrumNG k zapojení posilovně členů, zvyšují efektivitu zaměstnanců a poskytují zaměstnanci a členy mobilní přístup pro interaktivní úlohy a oznámení v reálném čase.

Azure má také pomohl CSI softwaru zrychlení a zlepšení vývoje a kontroly kvality (dotazů a odpovědí) cyklu povolením možnosti automatizace. S Azure implementací vaší společnosti můžou správci sestavení zabalit součásti kliknutím na tlačítko. Podle popisu Molina "jako součást cyklu vydávání verzí, dotazů a odpovědí je teď možné nasadit do testovacího prostředí v Azure, které úzce napodobuje naše portfolio produkčního prostředí. Můžeme okamžitě nasadit sestavení do našich vývoj prostředí ověřte změny. To je velkou výhrou pro us, protože nebyly k dispozici parita pro testování před inicializací."

## <a name="offloading-to-the-cloud"></a>Snižování zátěže úloh do cloudu
Před přechodem do cloudu, měli CSI softwaru úspěšně sestaveny svou vlastní infrastrukturu víceklientské v místním datovém centru v Houstonu. Jak je rozvedeno společnost čelí rostoucí důsledně Kvetoucí od nákupu, zřizování a správě všech hardwaru a softwaru, které jsou potřeba k podpoře svých zákazníků. IT pracovníky na operace se stal jiný kritickým bodem, která vedla k zpomalení zřizování nových prostředků a zavádí nové služby zákazníkům.

CSI Software prostudování cloudové možnosti pro odstranění nároky, tak, aby se mohli zaměřit na jeho kód, namísto jeho operace. Společnost zjistí, že mnoho poskytovatelů nejvíce nabízejí pouze řešení infrastruktury jako služby (IaaS), která stále vyžadují velké pracovníci IT ke správě zásobníku IaaS. Nakonec CSI softwaru zjištěno, že řešení Azure PaaS pro své potřeby nejvhodnější. Vysvětluje Molina "Azure pomůže hardwaru a systému softwaru eliminuje, takže se můžeme zaměřit na naše nabídky softwaru a snižuje nutnost naše režijní náklady na IT."

## <a name="making-the-transition-to-azure"></a>Přechodu do Azure
Po výběru Azure pro svoje řešení PaaS, CSI softwaru zahájení migrace jeho back-endové infrastruktury a databází do cloudu. Před Azure zákazníci SpectrumNG potřebný k instalaci klientské aplikace, které se předávají pomocí služby Windows Communication Foundation (WCF) na back-endu. Podle Molina "i když někteří zákazníci hostované všechno, co v jejich vlastních datových center jsme vytvořili produktu, který má být víceklientské. Jsme hostované všechno, co v datacentru Houston, použití jako úložiště dat serveru SQL Server.

"Náš produkt nabízí také zahrnuté přístupných členů webového portálu (napsané v ASP.net), která byla navržena jako prázdné s názvem bez přípony tak, aby odpovídaly zákazníka webové prezentace a rozhraní API protokolu SOAP pro podporu stránky online a integrace jakékoli třetí strany."

Migrace do cloudu nebere dlouhé pro architekturu. Podle Molina "Většinu úsilí se mění způsob, jsme si přečetli informace o konfiguračním souboru, změna centralizované connection-string a automatizace balení, odesílání a nasazení naše vydané verze."

Pro vývoj, automatizaci sestavování, CSI softwarové inženýry používá prostředí Azure PowerShell a rozhraní REST API pro vytváření balíčků a k jejich nahrávání do přípravného prostředí pro verzi každou noc.
Celkové přechod na cloudové nasazení Azure se nepovedlo rychle a plynule pro CSI Software IT tým. Vysvětluje Molina ", jsme měli beta prostředí v cloudu v rámci tři až čtyři týdny s ohledem na projekt. To bylo překvapivé win pro nás."

Po konfiguraci a testování prostředí, začal CSI softwaru migrace zákazníky. Zákazníci, kteří již používají hostování CSI softwaru bylo téměř spojité přechodu. Zákazníci, kteří migrují z místního nasazení, migrace do cloudu trvalo některé další čas, ale byla stále primárně bezproblémové pro zákazníky a CSI softwaru.

Pro nové zákazníky, CSI softwaru pro pracovníky IT, použijte následující postup připojit je na Azure:

1. Aktivovat novou databázi pro zákazníka; se používají skripty Azure Powershellu Všichni zákazníci začínají na úrovni premium zajistit dostatek počáteční propustnosti na přechod.
2. Pokud je to možné, CSI Software používá Průvodce migrací služby Azure SQL pro přesun existujících dat na instanci Azure SQL Database.
3. Nakonec Microsoft Integrace služby SSIS (SQL Server) se používají ke sjednocení nesrovnalostí v datech nebo vyčistěte data podle potřeby.

V současné době přibližně 99 % CSI softwaru zákazníků jsou hostované v Azure, v čtyři místních datových centrech (USA – střed, Jižní střední, – východ a – západ). Tím, že datová centra v geografické oblasti každého zákazníka, latence omezeny na minimum.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Elastické fondy Azure osvoboďte IT zdroje
Několik funkcí Azure pomohly CSI softwaru odklon od infrastruktury a operací, zaměřuje na funkce a vývoj, zaměřuje. Možná největší výhody byl z elastických fondů.

CSI Software aktuálně poskytuje přibližně 550 databází pro zákazníky. Před elastické fondy bylo obtížné spravovat daný počet databází v rámci úrovně struktury. OPS manažeři se museli přiřaďte úrovně služeb a výpočet velikosti na základě potřeb burst zákazníků, které vyžadují významné IT prostředků režie. S elastickými fondy se správci můžete přiřadit tenantů premium nebo standard fondu podle potřeby a potom přesunutí zákazníků na základě velikosti a potřebujete. Zákazníci téměř okamžitě; popisovač účinky elastických fondů před elastické fondy zákazníci došlo k vypršení časového limitu a další potíže během období využití rozšíření, ale s elastickými fondy mohou zákazníci nárůstům aktivity podle potřeby a může i nadále bez problémů používat SpectrumNG.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Aktivní geografickou replikaci Azure zrychlí vytváření sestav
Několik zákazníků CSI Software se také využít Azure aktivní geografickou replikaci. S aktivní geografickou replikaci až čtyři čitelné sekundární databáze se dá nakonfigurovat v oblastech stejných nebo různých datových center. CSI Software používá aktivní geografickou replikaci dvěma způsoby: první, sekundární databáze jsou k dispozici v případě výpadku datacentra nebo neschopnost připojení na primární databázi. a za druhé, čitelné sekundární databáze a slouží k přesměrování zpracování úlohy jen pro čtení jako úlohy vytváření sestav. Zákazníci, kteří CSI softwaru použijte tuto výhodu k urychlení pracovní postupy vytváření sestav.

## <a name="csi-software-application-logic-and-architecture"></a>Aplikace logiky CSI softwaru a architekturu
SpectrumNG používá webové role. Protože je víceklientské aplikace, služby WCF se používá ke zpracování původní požadavek na připojení od zákazníků. Jak uvádí Molina "požadavek identifikuje každého zákazníka, který umožňuje nám vytvořit připojovací řetězec si do své databáze provést cokoli, co musíme udělat."

Pro webovou vrstvu její služba CSI softwaru využívá výhod platformy Azure automatické škálování podle den a čas. Prostředky jsou automaticky skutečnost zohlednit zvýšením většího využití během pracovní doby v časovém pásmu z každé místní datové centrum. Prostředky jsou nastaveny také můžete vertikálně snížit kapacitu o víkendech, když jsou nižší potřeb zákazníků.

![Architektura Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Obrázek 1. Role pracovního procesu cloud services kreslení strukturovaná data z Azure SQL Database a částečně strukturovaná data z tabulky úložiště. SpectrumNG uživatelé možnost zasahovat, že data přes cloud services – webové role.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Pomocí webových aplikací a vrstvou plán webu pro mobilní aplikace
Používání Azure SQL Database uvolnit prostředky pro CSI softwaru povolit nové iniciativy, včetně kompletní mobilní platformu podle vlastního rozhraní API hostované ve službě Azure web apps. Platforma umožňuje posilovně členů a zaměstnanci používat mobilní zařízení zkontrolujte plány, knihy třídy a přijímat zprávy.

Platformu používá architektura orientovaná na služby (SOA) provést jednotlivé součásti – POS systému (POS) nebo prodejnímu systému, jako jsou – přesunout v reálném čase na jiný webový plán a aktivujte službu pro podporu této komponenty, a nechat všechno ostatní na uložené původní plán web. Tato možnost poskytuje značnou flexibilitu CSI softwaru, a pomáhá snížili náklady.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure umožňuje vývojářům zaměřit CSI softwarových aplikací a služeb
Azure SQL Database není právě boon SpectrumNG zákazníky, kteří mají rychlé a spolehlivé služby, je také velkou výhrou pro CSI softwaru pracovníky IT a vývojáře. Přesměrováním zátěže ops do Azure v cloudu, softwaru CSI snížit jeho režijní náklady na prostředky a infrastruktury, výrazně accelerated svůj vývojové cykly a už se potřebuje k databázím micromanage za účelem optimalizace výkonu pro své tenanty.

## <a name="more-information"></a>Další informace
* Další informace o Azure elastických fondech najdete v tématu [elastické fondy](sql-database-elastic-pool.md).
* Další informace o nástrojích databáze a elastické škálování najdete v tématu [nástrojů pro elastické databáze a elastické škálování](sql-database-elastic-scale-get-started.md).
* Další informace o migraci databáze SQL serveru najdete v tématu [migrace databáze SQL serveru do Azure](sql-database-cloud-migrate.md).
* Další informace o aktivní geografickou replikaci, najdete v článku [aktivní geografickou replikaci](sql-database-geo-replication-overview.md).
* Další informace o Azure Service Bus, najdete v článku [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Další informace o automatickém škálování najdete v tématu [škálování cloudové služby](../cloud-services/cloud-services-how-to-scale-portal.md).


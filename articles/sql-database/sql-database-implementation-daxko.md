---
title: Azure SQL databáze Azure Případová studie - Daxko/CSI | Microsoft Docs
description: Další informace o tom, jak Daxko/CSI používá databáze SQL pro zvýšení jeho cyklu vývoje a ke zvýšení jeho zákaznické a výkonu
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 3779b32aa1397b2ca0e05e2627241c0bfb7a8622
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI používá Azure pro zvýšení jeho cyklu vývoje a ke zvýšení jeho zákaznické a výkonu
![Daxko/CSI Logo](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Software Daxko/CSI potýkají výzvu: zákaznické základny vhodnosti a rekonstrukce Center byl narůstá, díky úspěch jeho enterprise-software s komplexní řešení, ale ponechat stále s potřebami infrastruktury IT pro rostoucí zákazníka základní byl testování společnosti zaměstnanců IT. Společnost se omezené stále rostoucí operations režie, hlavně pro správu její rostoucí databáze. Horší byl tento režijní náklady na operace vyjímání do vývoj prostředky pro nové iniciativy, jako jsou nové funkce mobility pro software společnosti.

Podle David Molina, ředitel produktu vývoj v Daxko/CSI poskytovaná Azure s modelem platforma jako služba (PaaS), který je potřebný pro zjednodušení správy databáze, zvýšení škálovatelnosti a uvolní prostředky a zaměřit se na software místo ops CSI softwaru. "Azure SQL Database je skvělou možnost pro nás. Nemusí se starat o údržbu systému SQL Server, clusteru s podporou převzetí služeb při selhání a všechny, které potřebuje další infrastrukturou je ideální pro nás."

CSI softwaru od migraci na Azure, musí provozní personál pouze dva ke správě více než 600 databáze zákazníka. Společnost používá elastické fondy Azure SQL Database k přesunutí databází zákazníka na základě velikosti a potřebují.

Pokračuje Molina "naše zákazníky popisovač změny okamžitě. Před elastické fondy někdy měly vypršení časových limitů a další potíže během období shluků. S Azure elastické fondy můžou burst podle potřeby a používání tohoto softwaru bez problémů."

Kromě zlepšení výkonu pro zákazníky, Azure elastické fondy uvolní prostředky CSI softwaru umožňuje zaměřit se na vývoji nových služeb a funkcí, místo práci s operace a správa. Tyto prostředky IT pomohl CSI softwaru zlepšit jeho podnikového softwaru nabídky, SpectrumNG se chcete zapojit sebou členy, zlepšit efektivitu zaměstnancům a poskytují zaměstnanci a členy mobilní přístup pro interaktivní úlohy a oznámení v reálném čase.

Azure má také pomohl CSI softwaru urychlit a zlepšovat vývoj a zajištění kvality (QA) cyklus povolením možnosti automatizace. S Azure implementací společnosti můžou správci sestavení zabalit komponenty s klepnutím na tlačítko. Jak popisuje Molina, "jako součást cyklus vydání QA je teď možné nasadit do testovacího prostředí v Azure, které úzce napodobuje naše produkční zásobníku. Můžeme okamžitě nasadit sestavení pro naše prostředí dev ověřte změny. Který je velký win pro nás, protože nebyly k dispozici parita pro testování před."

## <a name="offloading-to-the-cloud"></a>Snižování zátěže do cloudu
Před přesunutím do cloudu, měl CSI softwaru úspěšně vytvářet vlastní víceklientské infrastruktury v datacentru místní v Houston. Společnost rozbalené, potýkají roste důsledně Kvetoucí z nákupu, zřizování a údržbě všechny hardwaru a softwaru, které jsou potřeba k podpoře svým zákazníkům. IT pracovníky pro zpracování operací se aktivovala jiné problémové místo, která vedla k zpomalení zřizování nových prostředků a zavádí nové služby zákazníkům.

CSI Software prostudování možností cloudu k odstranění režie, tak, aby se může soustředit na jeho kód, namísto jeho operací. Společnost zjistit mnoho poskytovatelů nejvyšší cloudu pouze nabízejí řešení infrastruktura jako služba (IaaS), které stále vyžadují velký pracovníky IT spravovat v zásobníku IaaS. V části end CSI softwaru určit, že Azure PaaS řešením bylo nejlepší pro své potřeby. Vysvětluje Molina "Azure získá hardwaru a systému softwaru stranou, tak jsme mohli soustředit na našem nabídek softwaru při současném snížení naše režii IT oddělení."

## <a name="making-the-transition-to-azure"></a>Provedení přechodu do Azure
Po výběru Azure pro své řešení PaaS, CSI softwaru začal migrace jeho infrastruktury back-end a databáze do cloudu. Před Azure SpectrumNG zákazníci potřebné pro instalaci klientskou aplikaci, která oznamovat službou Windows Communication Foundation (WCF) na back-end. Podle Molina "i když někteří zákazníci hostované všechno ve svých vlastních datových center jsme vytvořili na produktu, který má být víceklientské. Jsme hostované vše v datacentru v Houston, pomocí systému SQL Server jako úložiště dat.

"Náš produkt nabídka také zahrnuty člen směřujících webového portálu (napsané v ASP.net), který byl navržený tak, aby se s označením white tak, aby odpovídaly webová služba zákazníka a protokolu SOAP API pro podporu online stránek a nějakou integraci třetích stran."

Migrace na cloud není trvat pro architekturu. Podle Molina, "Většině úsilí vyřešit mění způsob jsme přečetli informace o konfiguračním souboru, změna centralizované připojovacího řetězce a automatizaci balení, odesílání a nasazení naše verzí."

K vývoji automatizace sestavení, technici CSI softwaru použít Azure PowerShell a rozhraní REST API k vytváření balíčků a k jejich nahrávání do pracovního prostředí pro verzi každou noc.
Celkové přechod na Azure cloudové nasazení se rychle a bezproblémově pro CSI softwaru IT tým. Vysvětluje Molina "ve všech, jsme měli prostředí beta v cloudu v rámci tři až čtyři týdny pořízení na projekt. Který byl překvapivé win pro nás."

Po konfiguraci a testování prostředí, CSI softwaru začal migrace zákazníků. Pro zákazníky už používá hostování softwaru CSI byla téměř bezproblémové přechodu. Pro zákazníky migraci z místního nasazení, trvalo některé další dobu migrace do cloudu, ale byla stále především problémové bez pro zákazníky a CSI softwaru.

Pro nové zákazníky, CSI softwaru pro pracovníky IT použijte následující postup integrovaného je do Azure:

1. Azure skriptů prostředí PowerShell se používají k začne pracovat novou databázi pro zákazníka; všem zákazníkům spustí na úrovni premium zajistit dostatek počáteční propustnost pro přechod.
2. Pokud je to možné, CSI Software používá Průvodce migrací SQL Azure pro přesun existujících dat do Azure SQL Database instance.
3. Nakonec Microsoft integrační služby SSIS (SQL Server) se používají sjednotit případné nesrovnalosti v datech nebo provádět vyčištění dat podle potřeby.

V současné době o 99 procent zákazníků CSI softwaru jsou hostované v Azure, napříč čtyři regionální datová centra (centrální – sever, Jižní střední, – východ a – západ). Latence je tak, že datových centrech v zeměpisné oblasti každého zákazníka, omezit na minimum.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Azure elastické fondy uvolněte IT zdroje
Některé funkce služby Azure pomohly CSI softwaru shift nebudou infrastruktury a operací zaměřuje stal vývoj zaměřuje a funkce. Možná největších benefit byl z elastické fondy.

CSI Software aktuálně poskytuje o 550 databáze pro zákazníky. Před elastické fondy bylo obtížné spravovat tolika databáze v rámci struktury vrstvy. OPS správci museli přiřadit úrovně výkonu na základě potřeb shluků zákazníků, které vyžaduje významné IT prostředky režie. S elastické fondy správce můžete přiřadit klienty premium nebo standardní fond, podle potřeby a potom přesunout zákazníkům na základě velikosti a potřebují. Zákazníci popisovač důsledky elastické fondy téměř okamžitě; před elastické fondy zákazníků měla vypršení časových limitů a další potíže během období shluků využití, ale s elastické fondy, zákazníků může zaznamenat shluky aktivity podle potřeby a může i dál používat SpectrumNG bez problémů.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure aktivní geografickou replikací zrychluje vytváření sestav
Několik zákazníků CSI softwaru jsou také využívat výhod Azure aktivní geografickou replikací. S aktivní geografickou replikací až čtyři čitelný sekundární databáze se dá nakonfigurovat v oblastech datacenter stejný nebo jiný. CSI softwaru využívá aktivní geografickou replikací dvěma způsoby: nejdřív sekundární databáze jsou k dispozici v případě výpadku datového centra nebo neschopnost se připojit k primární databázi; a druhý, sekundární databáze načíst a slouží k přesměrování zpracování úlohy jen pro čtení, například úlohy sestav. Někteří zákazníci CSI softwaru pomocí této výhody urychlit vytváření sestav pracovních postupů.

## <a name="csi-software-application-logic-and-architecture"></a>CSI Software aplikační logiku a architektura
SpectrumNG používá webové role. Protože aplikace je více klientů, služby WCF se používá pro zpracování požadavku na počáteční připojení od zákazníků. Jako Molina stavy, "žádost identifikuje každého zákazníka, který umožňuje nám vytvořit připojovací řetězec se do své databáze uděláte, ať je potřeba udělat."

Pro webovou vrstvu svou službu CSI softwaru využívá Azure automatické škálování podle den a čas. Dostupné prostředky jsou automaticky skutečnost zohlednit zvýšením vyšší využití během pracovní doby, podle časového pásma každý místní datacentra. Prostředky jsou nastaveny také pro škálování směrem dolů na víkendy, kdy jsou nižší požadavky zákazníka.

![Daxko/CSI Architecture](./media/sql-database-implementation-daxko/figure1.png)

Obrázek 1. Cloudové služby rolí pracovního procesu nevykresluje strukturovaných dat z Azure SQL Database a částečně strukturovaných dat z úložiště tabulek. SpectrumNG uživatelé komunikovat s že dat přes cloud services webové role.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Pomocí webové aplikace a vrstvy webové plán pro mobilní aplikace
Používání Azure SQL Database uvolní prostředky pro CSI softwaru povolit nové iniciativy, včetně kompletní mobilní platformu podle vlastního rozhraní API hostované ve službě Azure web apps. Platforma umožňuje sebou členové a zaměstnanci používat mobilní zařízení Pokud chcete zkontrolovat plány, sešit třídy a přijímat zprávy.

Platformu používá architekturu orientované na služby (SOA) provést jedinou komponentu – jako POS systému (POS) nebo prodejní systému – ho přesunout do jiného plánu webové za chodu a pak začne pracovat podporují tuto součást a všechny ostatní nechat původní plán webové služby. Tato schopnost poskytuje značnou flexibilitu CSI softwaru, a pomáhá snížení nákladů.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure umožňuje zaměřit se vývojáři softwaru CSI na aplikace a služby
Azure SQL Database není právě velké zvýšení SpectrumNG zákazníkům, kteří mají rychlé, spolehlivé služby, je také velký win CSI softwaru pracovníky IT a vývojářům. Přesměrováním zátěže ops do Azure v cloudu, CSI Software omezuje jeho režii prostředků a infrastruktury, výrazně accelerated svůj vývoj cyklů a už se potřebuje k databázím micromanage za účelem optimalizace výkonu pro své klienty.

## <a name="more-information"></a>Další informace
* Další informace o Azure elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).
* Další informace o nástrojích databáze a elastické škálování najdete v tématu [nástroje elastické databáze a elastické škálování](sql-database-elastic-scale-get-started.md).
* Další informace o migraci databázi systému SQL Server, najdete v tématu [migrace databáze SQL serveru do Azure](sql-database-cloud-migrate.md).
* Další informace o aktivní geografickou replikaci, najdete v části [aktivní geografickou replikací](sql-database-geo-replication-overview.md).
* Další informace o Azure Service Bus, najdete v části [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Další informace o automatické škálování, najdete v části [škálování cloudové služby](../cloud-services/cloud-services-how-to-scale-portal.md).


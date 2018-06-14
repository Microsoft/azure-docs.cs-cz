---
title: Vzor víceklientské webových aplikací | Microsoft Docs
description: Vyhledá architektury přehledy a vzory návrhu, které popisují, jak implementovat víceklientské webovou aplikaci na platformě Azure.
services: ''
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: ''
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: 57ba0e46139bda2d74c9f7db0ffab2f2122b0df2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850711"
---
# <a name="multitenant-applications-in-azure"></a>Víceklientské aplikace v Azure
Víceklientské aplikace je sdílený prostředek, který umožňuje samostatné uživatelům, nebo "klienty," Zobrazit aplikace, jako kdyby byla svoje vlastní. Typický scénář, který slouží k víceklientské aplikace je jedním ve které všichni uživatelé aplikace může přát přizpůsobit činnost koncového uživatele, ale jinak mají stejné základní obchodní požadavky. Příkladem velké víceklientské aplikace jsou Office 365, Outlook.com a visualstudio.com.

Z hlediska poskytovatele aplikace výhod víceklientská většinou týkají provozní náklady a náklady efektivitu. Jednu verzi vaší aplikace můžete vyhovovaly mnoho klientů nebo zákazníků, povolení úlohy správy, jako jsou monitorování, optimalizace výkonu, údržba softwaru a zálohování dat konsolidace systému.

Následující část obsahuje seznam nejdůležitější cíle a požadavky z hlediska zprostředkovatele.

* **Zřizování**: musí být možné zřídit nové klienty pro aplikaci.  Pro víceklientské aplikace s velký počet klientů je obvykle potřeba tento proces automatizovat povolením samoobslužné zřizování.
* **Udržovatelnost**: musí být schopna upgradu aplikace a provádět další úlohy údržby v okamžiku, kdy několik klientů používají.
* **Monitorování**: musí být schopen monitorovat aplikace za všech okolností zjistit případné problémy a jejich řešení. To zahrnuje monitorování, jak je každého klienta pomocí aplikace.

Správně implementovaná víceklientské aplikace nabízí následující výhody pro uživatele.

* **Izolace**: aktivity jednotlivých klientů, které nemají vliv na použití aplikace ostatních klientů. Klienty nelze přistupovat k datům uživatele toho druhého. Zobrazí se klientovi jako v případě, že má výhradní použití aplikace.
* **Dostupnost**: jednotlivých klientů chcete aplikaci, která bude stále k dispozici, případně s záruky, které jsou definované v SLA. Aktivity ostatních klientů znovu, by neměla vliv na dostupnost aplikace.
* **Škálovatelnost**: aplikace škáluje poptávku jednotlivých klientů. Přítomnosti a akce ostatních klientů by neměla mít vliv na výkon aplikace.
* **Náklady na**: náklady jsou nižší než spuštěna aplikace typu vyhrazené, jednoho klienta, protože víceklientská architektura umožňuje sdílení prostředků.
* **Možnosti přizpůsobení**. Možnost přizpůsobit aplikaci pro jednoho klienta různými způsoby, například přidání nebo odebrání funkcí, změna barvy a loga nebo i přidání vlastní kód nebo skriptu.

Stručně řečeno existuje mnoho aspekty, které musí vzít v úvahu při poskytování vysoce škálovatelné služby, existují také určité cíle a požadavky, které jsou společné pro mnoho víceklientské aplikací. Nemusí být některá relevantní v konkrétních scénářů, a význam jednotlivé cíle a požadavky se budou lišit v každém scénáři. Jako zprostředkovatel víceklientské aplikace budete také mít cíle a požadavky, jako splňuje s klienty cíle a požadavky, ziskovosti, fakturace, více úrovní služeb, zřizování, udržovatelnosti monitorování a automatizace.

Další informace o další aspekty návrhu víceklientské aplikace najdete v tématu [hostování víceklientské aplikace na platformě Azure][Hosting a Multi-Tenant Application on Azure]. Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure poskytuje řadu funkcí, které vám umožní adres klíče problémy vzniklé při návrhu víceklientské systému.

**Izolace**

* Segment webu klienty podle hlavičky hostitele s nebo bez komunikaci prostřednictvím protokolu SSL
* Segment webu klienty podle parametrů dotazu
* Webové služby v rolí pracovního procesu
  * Role pracovního procesu. která se obvykle zpracovávají data v back-end aplikace.
  * Webové role, které obvykle fungují jako front-endu pro aplikace.

**Úložiště**

Správa dat, jako je například Azure SQL Database nebo Azure Storage services, například službu tabulku, která poskytuje služby pro ukládání velkého objemu nestrukturovaných dat a objektů Blob službu, která poskytuje služby k ukládání velkých objemů nestrukturovaných textu nebo binárních data, jako je například video, zvuk a obrázků.

* Zabezpečení víceklientské Data z databáze SQL odpovídající přihlášení serveru SQL Server za klienta.
* Pomocí tabulky Azure pro aplikace prostředky tak, že zadáte zásadu úrovně přístupu kontejneru, můžete možnost Upravit oprávnění bez nutnosti vydávat nové adresy URL pro prostředky chráněné s podpisy sdíleného přístupu.
* Front Azure pro fronty aplikace prostředky Azure se často používá ke zpracování jednotky jménem klienty, ale může také použít k rozložení práce požadované pro zřizování nebo správu.
* Fronty služby Service Bus pro prostředky aplikace, který by vložil spolupracovat na sdílenou službu, můžete použít jediné fronty kde každý odesílatele klienta má pouze oprávnění (jako je odvozen od deklarace identity vystavené ze serveru ACS) tak, aby nabízel do této fronty, i když má jenom příjemci ze služby oprávnění načítat z fronty dat pocházejících od víc klientů.

**Připojení a zabezpečení služeb**

* Azure Service Bus, která se nachází mezi aplikací, což jim k výměně zpráv volně párované způsobem pro lepší škálování a odolnost proti chybám infrastrukturu zasílání zpráv.

**Síťové služby**

Azure poskytuje několik síťové služby, které podporují ověřování a zlepšit možnosti správy hostovaných aplikací. Tyto služby patří:

* Azure umožňuje virtuální sítě můžete zřizovat a spravovat virtuální privátní sítě (VPN) v Azure stejně jako bezpečně propojit je s místní infrastrukturu IT.
* Virtuální síť Traffic Manager umožňuje načíst vyrovnávání příchozí provoz napříč více službami Azure hostované, zda jejich používáte ve stejném datovém centru nebo v různých datových centrech po celém světě.
* Azure Active Directory (Azure AD) je moderní, založené na REST služba, která poskytuje funkce řízení správy a přístupu identit pro cloudové aplikace. Pomocí služby Azure AD pro prostředky aplikace Azure AD poskytuje snadný způsob ověřování a autorizace uživatelů pro přístup do webové aplikace a služby při povolení funkce ověřování a autorizaci se promítnou z vašeho kódu.
* Azure Service Bus poskytuje zabezpečené zasílání zpráv a funkce toku dat pro distribuované a hybridní aplikace, jako je komunikace mezi Azure hostované aplikace a místní aplikace a služby, bez nutnosti komplexní brány firewall a zabezpečení infrastruktury. Pomocí předávání přes Service Bus prostředky aplikace služby, které jsou zveřejněné jako koncové body mohou patřit do klienta (například hostované mimo systému, jako je například místní) nebo mohou být službám zřizovaným speciálně pro klienta (protože konkrétního klienta, citlivá data přenášena mezi nimi).

**Zřizování prostředků**

Azure poskytuje několik způsobů zřídit nové klienty pro aplikaci. Pro víceklientské aplikace s velký počet klientů je obvykle potřeba tento proces automatizovat povolením samoobslužné zřizování.

* Role pracovního procesu umožňují zřídit a deaktivace zřízení každého klienta prostředky (například když nový klient přihlásí up nebo zruší), shromažďování metrik pro měření používat a spravovat škálování následující určité plánu nebo v reakci na k překročení prahové hodnoty klíče výkonu indikátory. Tento stejný atribut role může být rovněž k nabízení se aktualizace a upgrady k řešení.
* Azure BLOB můžete použít k přidělení výpočetní nebo předem inicializovat prostředky úložiště pro nové klienty při současném poskytování zásady kontejneru úrovně přístupu k ochraně výpočetní služby balíčky, bitové kopie virtuálního pevného disku a dalším prostředkům.
* Možnosti pro zřizování prostředků databáze SQL pro klienta:
  
  * DDL ve skriptech nebo vložené jako prostředky v rámci sestavení
  * SQL Server 2008 R2 balíčky DAC nasazené prostřednictvím kódu programu.
  * Kopírování z hlavní referenční databáze
  * Používání databáze Import a Export zřídit nové databáze ze souboru.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716

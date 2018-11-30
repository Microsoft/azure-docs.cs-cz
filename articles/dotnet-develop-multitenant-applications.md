---
title: Vzoru aplikace s více Tenanty webové | Dokumentace Microsoftu
description: Najdete architektury přehledy a vzorů návrhu, které popisují, jak implementovat víceklientské webová aplikace v Azure.
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
ms.openlocfilehash: 342c7903e58a5c3bc41278152630187fa0c63b7b
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425035"
---
# <a name="multitenant-applications-in-azure"></a>Víceklientské aplikace v Azure
Víceklientské aplikace je sdílený prostředek, který umožňuje samostatné uživatele, nebo "klienti", chcete-li zobrazit aplikace, jako by byl své vlastní. Typický scénář, který slouží k víceklientské aplikaci je jedním ve kterém všichni uživatelé aplikace chtít přizpůsobit uživatelské prostředí, ale jinak mají stejný základní obchodní požadavky. Příkladem velké víceklientské aplikace jsou visualstudio.com, Office 365 a Outlook.com.

Z pohledu zprostředkovatele aplikace výhody víceklientskou architekturu nejčastěji týkají provozní a nákladové efektivity. Jednu verzi vaší aplikace lze splnit potřeby mnoha tenantů/zákazníků, umožňuje konsolidaci systému úlohy správy, jako je monitorování, optimalizace výkonu, údržby softwaru nebo data záloh.

Následuje seznam nejvýznamnější cíle a požadavky z hlediska poskytovatele.

* **Zřizování**: musíte být schopni zřízení nových tenantů pro aplikaci.  Pro víceklientské aplikace s velkým množstvím tenantů je obvykle nezbytná k automatizaci tohoto procesu tím, že umožňuje samoobslužné služby zřizování.
* **Udržovatelnost**: musíte být schopni upgradovat aplikaci a provádět další úlohy údržby, zatímco se vytvářejí pomocí více tenantů.
* **Monitorování**: musí být schopen monitorovat aplikace za všech okolností zjistit případné problémy a jejich řešení. To zahrnuje monitorování, jak každý klient používá aplikace.

Správně implementovaná víceklientské aplikaci poskytuje následující výhody pro uživatele.

* **Izolace**: aktivity jednotlivých tenantů nemají vliv na použití aplikace podle jiných tenantů. Klienty nelze přistupovat k datům uživatele toho druhého. Zobrazí se do tenanta jako kdyby všichni mají výhradní použití aplikace.
* **Dostupnost**: jednotlivé tenanty chcete aplikaci, která bude stále k dispozici, například záruky, které jsou definovány v smlouvu SLA. Znovu aktivity z jiných tenantů nemělo ovlivnit dostupnost aplikace.
* **Škálovatelnost**: aplikace se škáluje podle potřeby jednotlivých tenantů. Přítomnost a konání ostatních tenantů by neměla vliv na výkon aplikace.
* **Náklady na**: náklady jsou nižší než systémem vyhrazené jednoho tenanta aplikaci, protože více tenantů umožňuje sdílení prostředků.
* **Přizpůsobitelnost**. Možnost přizpůsobení aplikace pro jednoho klienta různými způsoby, například přidávání nebo odebírání funkcí, změna barev a loga nebo dokonce přidat vlastní kód nebo skript.

Stručně řečeno i když existují mnoho důležité informace, které musí vzít v úvahu k poskytování vysoce škálovatelná služba, existují také řadu cíle a požadavky, které jsou společné pro mnoho víceklientské aplikace. Některé nemusí být v určitých situacích relevantní a závažnost jednotlivých cíle a požadavky se budou lišit v každém scénáři. Jako poskytovatel víceklientské aplikaci budete také mít cíle a požadavky, jako splnění klienty cíle a požadavky, ziskovosti, fakturace, více úrovní služeb, zřizování, udržovatelnost monitorování a automatizace.

Další informace o další aspekty návrhu víceklientské aplikace, najdete v části [hostování aplikace v Azure s více Tenanty][Hosting a Multi-Tenant Application on Azure]. Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure poskytuje mnoho funkcí, které umožňují věnovat důležitým problémům došlo při navrhování víceklientském systému.

**Izolace**

* Segment webu klienty podle hlavičky hostitele s nebo bez něj komunikaci prostřednictvím protokolu SSL
* Segment webu klienty podle parametrů dotazu
* Webové služby v rolích pracovního procesu
  * Role pracovního procesu. který se obvykle zpracovávají data v back-endové aplikace.
  * Webové role, které obvykle fungují jako front-endu pro aplikace.

**Storage**

Správa dat, jako je například Azure SQL Database nebo Azure Storage services, jako jsou služby Table service, která poskytuje služby pro ukládání velkých objemů nestrukturovaných dat a služby Blob service, která poskytuje služby pro ukládání velkých objemů nestrukturovaných textových nebo binárních data, jako jsou videa, zvukové soubory a obrázky.

* Zabezpečení Multitenant Data ve službě SQL Database odpovídající přihlášení serveru SQL Server za klienta.
* Použití tabulek Azure pro aplikace prostředků tak, že určíte zásady přístupu na úrovni kontejneru, můžete možnost Upravit oprávnění, aniž byste museli vydávat nové adresy URL pro prostředky chráněné pomocí signatur sdíleného přístupu.
* Fronty Azure pro fronty Azure prostředků aplikace se běžně používají k jednotky zpracování jménem tenantů, ale může také sloužit k distribuci práce potřebné pro zřizování nebo správy.
* Fronty služby Service Bus pro prostředky aplikace, který by vložil pracujeme, abychom sdílené služby, můžete použít jednou frontou kde odesílatele každý tenant má pouze oprávnění (jak je odvozen z deklarací identity vystavených ze služby ACS) tak, aby nabízel do této fronty, i když má pouze příjemci ze služby oprávnění k načítání z fronty dat přicházejících z více tenantů.

**Připojení a zabezpečení služeb**

* Azure Service Bus, infrastruktura zasílání zpráv umístěná mezi aplikacemi, kterým umožňuje výměnu zpráv volně způsobem lepší škálovatelnost a odolnost proti chybám.

**Síťové služby**

Azure poskytuje několik síťových služeb, které podporují ověřování a zlepšit možnosti správy hostovaných aplikací. Tyto služby patří:

* Azure Virtual Network umožňuje můžete zřizovat a spravovat virtuální privátní sítě (VPN) v Azure také bezpečně je propojíte s místní IT infrastrukturu.
* Virtuální síť Traffic Manager umožňuje vyrovnávat zatížení příchozího provozu napříč několika hostovanými službami Azure spuštěnými ve stejném datacentru nebo i v různých datacentrech po celém světě.
* Azure Active Directory (Azure AD) je moderní služba založená na protokolu REST, která poskytuje identitu Správa a řízení přístupu určené pro cloudové aplikace. Používání služby Azure AD pro prostředky aplikace Azure AD a poskytuje snadný způsob ověřování a autorizace uživatelů, jak získat přístup k webovým aplikacím a službám při povolení funkce ověřování a autorizace promítnout z kódu.
* Azure Service Bus poskytuje zabezpečené zasílání zpráv a funkci toku dat pro distribuované a hybridní aplikace, jako je komunikace mezi Azure a místní aplikace služeb a aplikací hostovaných, bez nutnosti složité brány firewall a zabezpečení infrastruktury. Pomocí propojovací Service Bus pro prostředky aplikací ke službám, které jsou zveřejněné jako koncové body mohou patřit tenantovi (například hostování mimo systém, například v místním prostředí), nebo může být služby poskytované speciálně pro příslušného tenanta (protože specifickým pro tenanta, citlivá data se přenáší prostřednictvím jejich).

**Zřizování prostředků**

Azure nabízí celou řadu způsobů, zřízení nových tenantů pro aplikaci. Pro víceklientské aplikace s velkým množstvím tenantů je obvykle nezbytná k automatizaci tohoto procesu tím, že umožňuje samoobslužné služby zřizování.

* Role pracovního procesu vám umožní zřizování a rušení zřízení na tenanta prostředky (například pokud nového tenanta zaregistruje do služby nebo zruší), shromažďování metrik pro měření použití a správa škálovací po určité plánu nebo v reakci na překročení prahové hodnoty pro výkonu ukazatele. Tento stejný atribut role může také sloužit k odesílání aktualizací a upgradů do řešení.
* Objekty BLOB Azure, můžete použít ke zřízení výpočetních nebo předem inicializovaná prostředky úložiště pro nové tenanty současně kontejneru zásad úrovně přístupu k ochraně výpočetní služby balíčky, bitové kopie virtuálního pevného disku a další prostředky.
* Možnosti zřizování prostředků SQL Database pro tenanta zahrnují:
  
  * DDL ve skriptech nebo vložený jako prostředky v rámci sestavení
  * SQL Server 2008 R2 balíčky DAC nasazené prostřednictvím kódu programu.
  * Kopírování z hlavní odkaz databáze
  * Pomocí databáze Import a Export zřídit nové databáze ze souboru.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716

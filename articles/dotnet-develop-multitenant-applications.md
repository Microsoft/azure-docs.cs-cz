---
title: Vícenásobný tenant – vzor webové aplikace | Microsoft Docs
description: Najděte si přehledy architektury a vzory návrhu, které popisují implementaci víceklientské webové aplikace v Azure.
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
ms.custom: devx-track-dotnet
ms.openlocfilehash: 71114b6663d010437e5778d288eacf4e120b3da5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91395970"
---
# <a name="multitenant-applications-in-azure"></a>Multitenantové aplikace v Azure
Víceklientské aplikace je sdílený prostředek, který umožňuje uživatelům v samostatných klientech zobrazit aplikaci, jako by byla vlastní. Typický scénář, který se sám zapůjčuje do víceklientské aplikace, je ten, ve kterém všichni uživatelé aplikace z různých tenantů můžou chtít přizpůsobit uživatelské prostředí, ale v opačném případě mají stejné základní obchodní požadavky. Příklady rozsáhlých víceklientské aplikací jsou Microsoft 365, Outlook.com a visualstudio.com.

Z perspektivy poskytovatele aplikace se výhody víceklientské architektury týkají zejména provozní a nákladové efektivity. Jedna verze vaší aplikace může splňovat potřeby mnoha klientů nebo zákazníků, což umožňuje konsolidaci úloh správy systému, jako je monitorování, optimalizace výkonu, údržba softwaru a zálohování dat.

Níže najdete seznam nejvýznamnějších cílů a požadavků z perspektivy poskytovatele.

* **Zřizování**: musíte být schopni zřídit pro aplikaci nové klienty.  Pro víceklientské aplikace s velkým počtem klientů je obvykle potřeba automatizovat tento proces povolením samoobslužného zřizování.
* **Udržovatelnost**: musíte být schopni provést upgrade aplikace a provádět další úlohy údržby, zatímco je používá více klientů.
* **Monitorování**: aplikace musí být schopná kdykoli monitorovat, aby bylo možné identifikovat případné problémy a řešit je. Zahrnuje monitorování, jak každý tenant používá aplikaci.

Správně implementovaná víceklientské aplikace poskytuje uživatelům následující výhody.

* **Izolace**: aktivity jednotlivých tenantů neovlivňují použití aplikace jinými klienty. Klienti nemají přístup k datům ostatních. Klient se zobrazí, jako by se jednalo o výhradní použití aplikace.
* **Dostupnost**: jednotliví klienti chtějí nepřetržitě dostupný aplikaci, třeba se zárukami definovanými v rámci smlouvy SLA. V důsledku toho by aktivity jiných tenantů neměly mít vliv na dostupnost aplikace.
* **Škálovatelnost**: aplikace se škáluje tak, aby splňovala požadavky jednotlivých tenantů. Přítomnost a akce jiných tenantů by neměly mít vliv na výkon aplikace.
* **Náklady**: náklady jsou nižší než spuštění vyhrazené aplikace s jedním klientem, protože víceklientská architektura umožňuje sdílení prostředků.
* **Úpravou**. Možnost přizpůsobit aplikaci pro jednotlivé klienty různými způsoby, jako je například přidávání nebo odebírání funkcí, změna barev a log nebo dokonce přidání vlastního kódu nebo skriptu.

V krátké době existuje mnoho důležitých informací, které je třeba vzít v úvahu, aby bylo možné zajistit vysoce škálovatelnou službu, existuje také řada cílů a požadavků, které jsou společné pro mnoho víceklientské aplikací. Některé nemusí být relevantní v konkrétních scénářích a význam jednotlivých cílů a požadavků se v každém scénáři liší. Jako poskytovatel víceklientské aplikace budete mít také cíle a požadavky, jako je třeba splnění cílů a požadavků klienta, ziskovosti, fakturace, více úrovní služeb, zřizování, monitorování údržby a automatizace.

Další informace o dalších doporučeních pro návrh víceklientské aplikace najdete v tématu [hostování aplikace s více klienty v Azure][Hosting a Multi-Tenant Application on Azure]. Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure poskytuje mnoho funkcí, které vám umožní řešit klíčové problémy zjištěné při navrhování víceklientského systému.

**Oddělení**

* Segmentovat klienty webu podle hlaviček hostitele s komunikací TLS nebo bez něj
* Segmentovat klienty webu podle parametrů dotazu
* Webové služby v rolích pracovního procesu
  * Role pracovních procesů, které obvykle zpracovávají data v back-endu aplikace.
  * Webové role, které obvykle fungují jako front-end pro aplikace.

**Storage**

Správa dat, jako jsou například Azure SQL Database nebo Azure Storage služby, jako je Table service, která poskytuje služby pro ukládání velkých objemů nestrukturovaných dat a Blob service, které poskytují služby pro ukládání velkých objemů nestrukturovaných textových nebo binárních dat, jako je video, zvuk a obrázky.

* Zabezpečují se data ve více klientech v SQL Database přihlašovacích údajů pro každého tenanta SQL Server.
* Použití tabulek Azure pro prostředky aplikace zadáním zásad přístupu na úrovni kontejneru můžete mít možnost Upravit oprávnění bez nutnosti vydávat nové adresy URL pro prostředky chráněné pomocí sdílených přístupových podpisů.
* Fronty Azure pro prostředky aplikací Azure Queues se běžně používají k řízení zpracování v zastoupení klientů, ale můžou se používat i k distribuci práce, která se vyžaduje pro zřizování nebo správu.
* Service Bus fronty pro prostředky aplikací, které přidávají práci do sdílené služby, můžete použít jednu frontu, ve které má každý odesílatel tenanta oprávnění pouze (odvozený od služby ACS), aby se do této fronty nastavila, zatímco jenom příjemci ze služby mají oprávnění k vyžádání z fronty z dat přicházejících z více tenantů.

**Služby připojení a zabezpečení**

* Azure Service Busová infrastruktura pro zasílání zpráv, která se nachází mezi aplikacemi a umožňuje jim volně vyměňovat zprávy pro lepší škálování a odolnost.

**Síťové služby**

Azure poskytuje několik síťových služeb, které podporují ověřování, a vylepšuje možnosti správy hostovaných aplikací. Mezi tyto služby patří následující:

* Azure Virtual Network umožňuje zřizovat a spravovat virtuální privátní sítě (VPN) v Azure a také je bezpečně propojit s místní IT infrastrukturou.
* Virtual Network Traffic Manager umožňuje vyrovnávat zatížení příchozího provozu napříč několika hostovanými službami Azure bez ohledu na to, jestli běží ve stejném datovém centru nebo napříč různými datacentry po celém světě.
* Azure Active Directory (Azure AD) je moderní služba založená na REST, která poskytuje funkce pro správu identit a řízení přístupu pro vaše cloudové aplikace. Použití Azure AD pro prostředky aplikací poskytuje snadný způsob, jak ověřovat a autorizovat uživatele, aby měli přístup k vašim webovým aplikacím a službám, a současně umožňuje převzít funkce ověřování a autorizace z vašeho kódu.
* Azure Service Bus poskytuje zabezpečenou funkci zasílání zpráv a toků dat pro distribuované a hybridní aplikace, jako je komunikace mezi aplikacemi hostovanými v Azure a místními aplikacemi a službami, aniž by bylo potřeba složitou bránu firewall a bezpečnostní infrastrukturu. Použití Service Bus Relay pro prostředky aplikace pro přístup ke službám, které jsou vystaveny jako koncové body, mohou patřit do tenanta (například hostovaného mimo systém, jako je například místní), nebo mohou být služby zřízené speciálně pro klienta (protože mezi nimi jsou přenášena data specifická pro tenanta).

**Zřizování prostředků**

Azure poskytuje několik způsobů, jak pro aplikaci zřídit nové klienty. Pro víceklientské aplikace s velkým počtem klientů je obvykle potřeba automatizovat tento proces povolením samoobslužného zřizování.

* Role pracovního procesu umožňují zřídit a zrušit zřízení prostředků klientů (například když se nový tenant zaregistruje nebo zruší), shromažďovat metriky pro měření využití a spravovat škálování podle určitého plánu nebo v reakci na překročení prahových hodnot klíčových ukazatelů výkonu. Tato stejná role se dá použít taky k odeslání aktualizací a upgradů do řešení.
* Objekty blob Azure je možné použít ke zřízení výpočetních nebo předem inicializovaných prostředků úložiště pro nové klienty a zároveň poskytovat zásady přístupu na úrovni kontejneru pro ochranu balíčků výpočetních služeb, imagí VHD a dalších prostředků.
* Mezi možnosti zřízení SQL Database prostředků pro tenanta patří:
  
  * DDL ve skriptech nebo vložených jako prostředky v rámci sestavení.
  * Balíčky DAC SQL Server 2008 R2 nasazené programově.
  * Kopírování z hlavní referenční databáze.
  * Použití importu a exportu databáze k zřizování nových databází ze souboru.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716

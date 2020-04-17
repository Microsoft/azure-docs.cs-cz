---
title: Vzor webové aplikace pro více klientů | Dokumenty společnosti Microsoft
description: Najděte přehledy architektury a návrhové vzory, které popisují, jak implementovat víceklientské webové aplikace v Azure.
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
ms.openlocfilehash: d1441ede9f448b3e6ffb0726c2ee92f192369e9a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81481839"
---
# <a name="multitenant-applications-in-azure"></a>Multitenantové aplikace v Azure
Víceklientská aplikace je sdílený prostředek, který umožňuje "uživatelům v samostatných tenantů" zobrazit aplikaci, jako by to bylo jejich vlastní. Typický scénář, který se hodí pro víceklientské aplikace je ten, ve kterém všichni uživatelé aplikace z různých klientů může chtít přizpůsobit uživatelské prostředí, ale jinak mají stejné základní obchodní požadavky. Příklady velkých víceklientských aplikací jsou Office 365, Outlook.com a visualstudio.com.

Z pohledu poskytovatele aplikací se výhody víceklientské většinou vztahují k provozní a nákladové efektivitě. Jedna verze aplikace může splňovat potřeby mnoha klientů/zákazníků, což umožňuje konsolidaci úloh správy systému, jako je monitorování, optimalizace výkonu, údržba softwaru a zálohování dat.

Následující obsahuje seznam nejvýznamnějších cílů a požadavků z pohledu zprostředkovatele.

* **Zřizování**: Musíte být schopni zřídit nové klienty pro aplikaci.  Pro víceklientské aplikace s velkým počtem klientů je obvykle nutné automatizovat tento proces povolením samoobslužného zřizování.
* **Udržovatelnost**: Musíte být schopni upgradovat aplikaci a provádět další úlohy údržby, zatímco ji používá více klientů.
* **Monitorování**: Musíte být schopni sledovat aplikaci po celou dobu identifikovat případné problémy a jejich řešení. To zahrnuje sledování, jak každý klient používá aplikaci.

Správně implementovaná víceklientská aplikace poskytuje uživatelům následující výhody.

* **Izolace**: Aktivity jednotlivých klientů nemají vliv na použití aplikace jinými klienty. Klienti nemají přístup k datům ostatních. Zdá se, že nájemce, jako by mají výhradní použití aplikace.
* **Dostupnost**: Jednotliví klienti chtějí, aby aplikace byla neustále dostupná, například se zárukami definovanými v sla. Opět platí, že aktivity jiných klientů by neměly mít vliv na dostupnost aplikace.
* **Škálovatelnost**: Aplikace se škáluje tak, aby splňovala požadavky jednotlivých klientů. Přítomnost a akce jiných klientů by neměla mít vliv na výkon aplikace.
* **Náklady**: Náklady jsou nižší než spuštění vyhrazené aplikace s jedním tenantem, protože víceklientské umožňuje sdílení prostředků.
* **Přizpůsobitelnost**. Možnost přizpůsobit aplikaci pro jednotlivého klienta různými způsoby, jako je přidání nebo odebrání funkcí, změna barev a lognebo dokonce přidání vlastního kódu nebo skriptu.

Stručně řečeno, zatímco existuje mnoho aspektů, které je třeba vzít v úvahu k poskytování vysoce škálovatelné služby, existuje také řada cílů a požadavků, které jsou společné pro mnoho víceklientských aplikací. Některé nemusí být relevantní v konkrétních scénářích a význam jednotlivých cílů a požadavků se bude lišit v každém scénáři. Jako poskytovatel víceklientské aplikace budete mít také cíle a požadavky, jako je splnění cílů a požadavků klienta, ziskovost, fakturace, více úrovní služeb, zřizování, monitorování udržovatelnosti a automatizace.

Další informace o dalších aspektech návrhu víceklientské aplikace najdete v [tématu hostování víceklientské aplikace v Azure][Hosting a Multi-Tenant Application on Azure]. Informace o běžných vzorech architektury dat databázových aplikací softwaru s více tenanty jako služby (SaaS) naleznete v části [Vzory návrhu pro aplikace SaaS s více tenanty s databází Azure SQL Database](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure poskytuje mnoho funkcí, které umožňují řešit klíčové problémy, ke kterým došlo při navrhování víceklientského systému.

**Izolace**

* Segmentovat klienty webových stránek podle záhlaví hostitelských stránek s komunikací TLS nebo bez ní
* Segmentovat klienty webových stránek podle parametrů dotazu
* Webové služby v rolích pracovního procesu
  * Role pracovních procesů, které obvykle zpracovávají data v back-endu aplikace.
  * Webové role, které obvykle fungují jako front-end pro aplikace.

**Storage**

Správa dat, jako je Azure SQL Database nebo služby Azure Storage, jako je služba Table, která poskytuje služby pro ukládání velkého množství nestrukturovaných dat a služby Blob, která poskytuje služby pro ukládání velkého množství nestrukturovaného textu nebo binárních dat, jako je video, zvuk a obrázky.

* Zabezpečení víceklientských dat v databázi SQL Database na klienta přihlášení SQL Serveru.
* Pomocí Azure Tabulky pro prostředky aplikací zadáním zásadpřístupu na úrovni kontejneru, můžete mít možnost upravit oprávnění bez nutnosti vydávat nové adresy URL pro prostředky chráněné sdílenými přístupovými podpisy.
* Fronty Azure pro aplikační prostředky Azure fronty se běžně používají k řízení zpracování jménem klientů, ale může být také použit k distribuci práce potřebné pro zřizování nebo správu.
* Fronty služby Service Bus pro aplikační prostředky, které tlačí práci do sdílené služby, můžete použít jednu frontu, kde každý odesílatel klienta má pouze oprávnění (odvozené z deklarací vydaných ze služby ACS) k nabízení do této fronty, zatímco pouze příjemci ze služby mají oprávnění k vytažení dat z fronty z více klientů.

**Služby připojení a zabezpečení**

* Azure Service Bus, infrastruktura zasílání zpráv, která je mezi aplikacemi, která jim umožňuje volně spřažené zprávy pro lepší škálování a odolnost proti chybám.

**Síťové služby**

Azure poskytuje několik síťových služeb, které podporují ověřování a zlepšují možnosti správy hostovaných aplikací. Mezi tyto služby patří následující:

* Virtuální síť Azure umožňuje zřizování a správu virtuálních privátních sítí (VNU) v Azure a také je bezpečně propojit s místní IT infrastrukturou.
* Správce provozu virtuální sítě umožňuje vyvažovat příchozí provozy napříč několika hostovanými službami Azure bez ohledu na to, zda běží ve stejném datovém centru nebo v různých datových centrech po celém světě.
* Azure Active Directory (Azure AD) je moderní služba založená na REST, která poskytuje možnosti správy identit a řízení přístupu pro vaše cloudové aplikace. Použití Azure AD pro prostředky aplikací poskytuje snadný způsob ověřování a autorizace uživatelů získat přístup k webovým aplikacím a službám a zároveň umožňuje funkce ověřování a autorizace, které mají být faktored z vašeho kódu.
* Azure Service Bus poskytuje zabezpečené zasílání zpráv a tok dat pro distribuované a hybridní aplikace, jako je komunikace mezi hostitelskými aplikacemi Azure a místními aplikacemi a službami, aniž by bylo nutné složité brány firewall a infrastruktury zabezpečení. Použití service bus relay pro aplikační prostředky pro přístup ke službám, které jsou vystaveny jako koncové body mohou patřit do tenanta (například hostované mimo systém, jako je například místní), nebo mohou být služby zřízené speciálně pro klienta (protože citlivá data specifická pro klienta cestuje přes ně).

**Zřizování prostředků**

Azure poskytuje řadu způsobů, jak zřídit nové klienty pro aplikaci. Pro víceklientské aplikace s velkým počtem klientů je obvykle nutné automatizovat tento proces povolením samoobslužného zřizování.

* Role pracovního procesu umožňují zřídit a zrušit poskytování na prostředky tenanta (například když se nový klient zaregistruje nebo zruší), shromažďovat metriky pro použití měření a spravovat škálování podle určitého plánu nebo v reakci na překročení prahových hodnot klíčových ukazatelů výkonu. Stejná role může být také použita k vysazení aktualizací a upgradů řešení.
* Objekty BLOB Azure se můžou zřizovat výpočetní nebo předem inicializované prostředky úložiště pro nové klienty a zároveň poskytovat zásady přístupu na úrovni kontejnerů k ochraně balíčků výpočetních služeb, ibi obrazových míst virtuálního pevného disku a dalších prostředků.
* Možnosti pro zřizování prostředků databáze SQL pro klienta zahrnují:
  
  * DDL ve skriptech nebo vložené jako prostředky v rámci sestavení.
  * Balíčky DAC serveru SQL Server 2008 R2 byly nasazeny programově.
  * Kopírování z hlavní referenční databáze.
  * Použití databáze Import a export zřídit nové databáze ze souboru.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: https://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: https://msdn.microsoft.com/library/windowsazure/hh689716

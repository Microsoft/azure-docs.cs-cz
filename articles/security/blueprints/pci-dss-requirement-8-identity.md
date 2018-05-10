---
title: Azure platebních zpracování plán, podle kterého – požadavky na Identity
description: PCI DSS požadavek 8
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 1a398601-8c48-4f8e-b3d4-eba94edad61c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: a83040a6b5174307ea73e5473165835458d217f6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="identity-requirements-for-pci-dss-compliant-environments"></a>Požadavky identity kompatibilní se standardem PCI DSS prostředí 
## <a name="pci-dss-requirement-8"></a>PCI DSS požadavek 8

**Identifikovat a ověřovat přístup k součástem systému**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Přiřazení k každá osoba s přístupem k jedinečné identifikaci (ID) zajišťuje, že každého uživatele jedinečné odpovědné za provedené akce. Pokud takové odpovědnosti za je na místě, akce prováděné na důležitá data a systémy provádí a daly sledovat až známé a autorizovaným uživatelům a procesy.
Efektivita hesla je do značné míry návrhu a implementace systému ověřování – zvlášť, jak často pokusy o zadání hesla můžete provést tak, že útočník a metody zabezpečení k ochraně uživatelská hesla v okamžiku vstupu, během přenosu a při v úložišti.

> [!NOTE]
> Tyto požadavky platí pro všechny účty, včetně POS účty s možnostmi správy a všechny účty použité k zobrazení nebo přístupu držitele karty dat nebo k přístupu k systémům daty držitele karty. To zahrnuje účty používané dodavatele a jiných třetích stran (například pro podporu nebo údržby). Tyto požadavky se nevztahují na účty, které používají příjemci (například služba). Však nejsou určeny požadavky 8.1.1, 8.2, 8.5, 8.2.3 prostřednictvím 8.2.5 a 8.1.6 prostřednictvím 8.1.8 chcete použít pro uživatelské účty v rámci aplikace POS platby, které pouze mít přístup k jedno číslo karty současně pro usnadnění (jedné transakce například pokladní účty).
 
## <a name="pci-dss-requirement-81"></a>PCI DSS požadavek 8.1

**8.1** definovat a implementujte zásady a postupy, které zajistí identifikaci správu správné uživatelů jiný příjemce uživatelům a správcům na všechny součásti systému následujícím způsobem.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje případ použití a popis pro správné použití správců pro ukázkové nasazení.|



### <a name="pci-dss-requirement-811"></a>PCI DSS požadavek 8.1.1

**8.1.1** přiřadit jedinečné ID všichni uživatelé předtím, než je součástí systému přístup nebo data držitele karty.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore implementuje Azure Active Directory a řízení přístupu Azure Active Directory Role-Based (RBAC) aby všichni uživatelé mít jedinečné ID. Další informace najdete v tématu [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-812"></a>PCI DSS požadavek 8.1.2

**8.1.2** řízení přidání, odstranění a úpravy ID uživatele, přihlašovací údaje a další objekty identifikátor.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore implementuje Azure Active Directory a řízení přístupu Azure Active Directory Role-Based (RBAC) aby všichni uživatelé mít jedinečné ID. Další informace najdete v tématu [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-813"></a>PCI DSS požadavek 8.1.3

**8.1.3** okamžitě ukončena odvolání přístupu pro všechny uživatele.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Active Directory pro správu uživatelů. Odvolání uživatelů můžete udělat ve službě Active Directory.|



### <a name="pci-dss-requirement-814"></a>PCI DSS požadavek 8.1.4

**8.1.4** odebrat ani vypnout neaktivní uživatelské účty do 90 dnů.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Active Directory pro správu uživatelů. `-enableADDomainPasswordPolicy` Možnost lze nastavit zajistit hesla vyprší za 90 dní.|



### <a name="pci-dss-requirement-815"></a>PCI DSS požadavek 8.1.5

**8.1.5** spravovat ID používaných třetích stran k přístupu, podporu nebo Udržovat komponenty systému prostřednictvím vzdáleného přístupu následujícím způsobem:
- Povolit pouze během časového období potřebné a zakázané, když není používán.
- Monitorovat v použití.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure přijal zásady použít podnikové a organizační zabezpečení, včetně zásad zabezpečení informací. Zásady byla schválena, publikována a přenést do Microsoft Azure. Zásady zabezpečení informací vyžaduje, aby přístup k prostředkům Microsoft Azure udělit oprávnění podle obchodního oprávnění s vlastník majetku autorizace a omezené na základě zásady "potřeba vědět" a "minimálními oprávněními". Kromě toho zásady také řeší požadavky na přístup správy životního cyklu, včetně zřizování přístupu, ověřování, autorizaci přístupu, odebrání přístupová práva a pravidelně přístup ke recenze. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Ukázkové společnosti Contoso Webstore implementovala Azure Active Directory a řízení přístupu Azure Active Directory Role-Based spravovat přístup uživatelů k instalaci. Další informace najdete v tématu [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-816"></a>PCI DSS požadavek 8.1.6

**8.1.6** limit opakuje podle uzamykání ID uživatele po více než šest pokusy pokouší o přístup.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore implementovala zrušte oddělení povinností (ZATRAVNIT) pro všechny uživatele ukázce. Další informace najdete v tématu ""Azure Active Directory identitu ochrany"v [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-817"></a>PCI DSS požadavek 8.1.7

**8.1.7** nastavte doba trvání uzamčení minimálně 30 minut, dokud správce povolí ID uživatele.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření, vynucení a monitorování zásad hesel je kompatibilní s PCI DSS požadavky zákazníků.|



### <a name="pci-dss-requirement-818"></a>PCI DSS požadavek 8.1.8

**8.1.8** Pokud relaci bylo nečinné po dobu delší než 15 minut, vyžadovat, aby uživatel znovu provést ověření znovu aktivovat Terminálové nebo relace.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření, vynucení a monitorování zásad hesel je kompatibilní s PCI DSS požadavky zákazníků.|



## <a name="pci-dss-requirement-82"></a>PCI DSS požadavek 8.2

**8.2** kromě přiřazení jedinečné ID, zajištění správy správné ověření uživatele pro jiný příjemce uživatelům a správcům na všechny součásti systému díky využití architektury alespoň jeden z následujících metod k ověření všech uživatelů:
- Něco, co jste si jisti, například heslo nebo přístupové heslo
- Něco, které máte, jako jsou čipové karty nebo tokenu zařízení
- Něco, co jste, jako je například biometrické

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | K poskytování snadného použití pro ukázku byla zakázána Contoso Webstore implementace pro službu Multi-Factor authentication. Služba Multi-Factor authentication lze implementovat pomocí [Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/).|



### <a name="pci-dss-requirement-821"></a>PCI DSS požadavek 8.2.1

**8.2.1** používání silného šifrování vykreslení všechny ověřovací pověření (například hesla nebo fráze) nečitelná během přenosu a uložení na všechny součásti systému.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zavedla postupy správy klíčů, můžete spravovat kryptografické klíče v průběhu jejich životního cyklu (např. generace, distribuční, odvolání). Microsoft Azure pomocí podnikové infrastruktury PKI společnosti Microsoft. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore vynucuje silná hesla, popsané v Průvodci nasazením. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



### <a name="pci-dss-requirement-822"></a>PCI DSS požadavek 8.2.2

**8.2.2** ověřit identitu uživatele před změnou žádné pověření ověřování – například provádění resetování hesla, zřizování nové tokeny nebo generování nových klíčů.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zavedla postupy správy klíčů, můžete spravovat kryptografické klíče v průběhu jejich životního cyklu (např. generace, distribuční, odvolání). Microsoft Azure pomocí podnikové infrastruktury PKI společnosti Microsoft. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore vynucuje silná hesla, popsané v Průvodci nasazením. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).|



### <a name="pci-dss-requirement-823"></a>PCI DSS požadavek 8.2.3

**8.2.3** hesla nebo přístupová hesla musí splňovat následující:
- Vyžadovat minimální délku alespoň 7 znaků.
- Obsahují číselné i abecední znaky.
Alternativně hesla přístupová hesla musí mít složitosti a síly ekvivalentní minimálně na parametry zadané výše.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore vynucuje silná hesla, popsané v Průvodci nasazením.|



### <a name="pci-dss-requirement-824"></a>PCI DSS požadavek 8.2.4

**8.2.4** změnit přístupová hesla uživatele nebo hesla alespoň jednou za 90 dní.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure Active Directory pro správu uživatelů. `-enableADDomainPasswordPolicy` Možnost lze nastavit zajistit hesla vyprší alespoň jednou za 90 dní.|



### <a name="pci-dss-requirement-825"></a>PCI DSS požadavek 8.2.5

**8.2.5** neumožňují konkrétního k odeslání nové heslo nebo přístupové heslo, je stejný jako kterákoli ze se poslední čtyři hesla nebo přístupová hesla se má použít.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore vynucuje silná hesla, popsané v Průvodci nasazením. Další informace najdete v tématu [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



### <a name="pci-dss-requirement-826"></a>PCI DSS požadavek 8.2.6

**8.2.6** nastavte přístupová hesla nebo hesla pro první použití a po resetování jedinečnou hodnotu pro každého uživatele a změnit okamžitě po prvním použití.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore vynucuje silná hesla, popsané v Průvodci nasazením. Další informace najdete v tématu [pokyny PCI - Identity Management](payment-processing-blueprint.md#identity-management).<br /><br />|



## <a name="pci-dss-requirement-83"></a>PCI DSS požadavek 8.3

**8.3** zabezpečit všechny jednotlivé mimo konzolu pro správu přístupu a všechny vzdálený přístup k prostředí držitele karty data (CDE) pomocí služby Multi-Factor authentication.

> [!NOTE]
> Vícefaktorové ověřování vyžaduje, že minimálně dvě metody tři ověřování (viz popis metod ověřování požadavek 8.2) používat pro ověřování. Použití jediný faktor dvakrát (např. pomocí dvou samostatných hesel) není považováno za služby Multi-Factor authentication.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure správci jsou nutné k použití vícefaktorového ověřování pro přístup k při provádění údržby a správy Azure systémů a serverů. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Během nasazení Contoso Webstore vytvoří tři účty: admin, sqladmin a edna (výchozí uživatelská přihlášení k webové aplikaci během provádění ukázku). Služba Multi-Factor authentication není implementována pro ukázku.|



### <a name="pci-dss-requirement-831"></a>PCI DSS požadavek 8.3.1

**8.3.1** začlenění vícefaktorového ověřování pro veškerý přístup mimo konzolu do CDE pro pracovníky s přístupem pro správu.

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure správci jsou nutné k použití vícefaktorového ověřování pro přístup k při provádění údržby a správy Azure systémů a serverů. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Během nasazení Contoso Webstore vytvoří tři účty: admin, sqladmin a edna (výchozí uživatelská přihlášení k webové aplikaci během provádění ukázku). Služba Multi-Factor authentication není implementována pro ukázku.|



### <a name="pci-dss-requirement-832"></a>PCI DSS požadavek 8.3.2

**8.3.2** začlenění vícefaktorového ověřování pro všechny vzdálený přístup k síti (uživatele a správce i včetně přístupu třetích stran pro podporu nebo údržby) pocházející z mimo síť entity.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Azure správci jsou nutné k použití vícefaktorového ověřování pro přístup k při provádění údržby a správy Azure systémů a serverů. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Během nasazení Contoso Webstore vytvoří tři účty: admin, sqladmin a edna (výchozí uživatelská přihlášení k webové aplikaci během provádění ukázku). Služba Multi-Factor authentication není implementována pro ukázku.|



## <a name="pci-dss-requirement-84"></a>PCI DSS požadavek 8.4

**8.4** dokumentu a komunikovat zásady ověřování a postupy pro všechny uživatele, včetně:
- Pomoc s výběrem silné ověřování pověření
- Pokyny, jak uživatelé měli chránit svá pověření ověřování
- Pokyny, které nechcete použít dříve použitých hesel
- Pokyny pro změnu hesla je jakékoli podezření heslo může dojít k ohrožení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za následující pokyny a dokumentace a komunikaci procedury ověřování a zásady pro všechny uživatele.|



## <a name="pci-dss-requirement-85"></a>PCI DSS požadavek na 8.5

**8.5** nepoužívejte skupiny, které jsou sdíleny, nebo obecný ID, hesla nebo jiných metod ověřování, následujícím způsobem:
- ID obecného uživatelů se zakážou nebo odeberou.
- Sdílené uživatelské ID pro správu systému a další důležité funkce neexistují.
- Sdílené a obecné uživatelské ID nepoužívají ke správě všech komponent systému.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Během nasazení Contoso Webstore vytvoří tři účty: admin, sqladmin a edna (výchozí uživatelská přihlášení k webové aplikaci během provádění ukázku). Služba Multi-Factor authentication není implementována pro ukázku.|



### <a name="pci-dss-requirement-851"></a>PCI DSS požadavek 8.5.1

**8.5.1** **další požadavek pro poskytovatele služeb pouze:** poskytovatelé vzdálený přístup k zákazníka (například pro podporu systémy POS nebo serverů), musíte použít je jedinečný ověřování (přihlašovací údaje například heslo nebo frázi) pro každého zákazníka. 

> [!NOTE]
> Tento požadavek není určen pro použití sdílené poskytovatelé hostingu přístup k vlastní hostitelské prostředí, kde jsou hostované prostředí s více zákazníků.

**Odpovědnosti:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici pro zákazníky, kteří Microsoft Azure. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici pro zákazníky, kteří Microsoft Azure.|



## <a name="pci-dss-requirement-86"></a>PCI DSS požadavek 8.6

**8.6** Pokud další ověřovací mechanismy, které se používají (pro příklad, tokeny zabezpečení fyzické nebo logické, čipové karty, certifikáty, atd.), použijte tyto mechanismy musí být přiřazena následujícím způsobem:
- Ověřovací mechanismy, které musí být přiřazen k účtu jednotlivých a není sdílen více účtů.
- K zajištění, že pouze určený účet můžete použít tento mechanismus pro získání přístupu musí být fyzické nebo logické ovládací prvky.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Během nasazení Contoso Webstore vytvoří tři účty: admin, sqladmin a edna (výchozí uživatelská přihlášení k webové aplikaci během provádění ukázku). Služba Multi-Factor authentication není implementována pro ukázku. Veškerý přístup ke správě [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), která pomáhá chránit kryptografické klíče a tajné klíče používané cloudovými aplikacemi a službami. |



## <a name="pci-dss-requirement-87"></a>PCI DSS požadavek 8.7

**8.7** veškerý přístup k jakékoli databázi obsahující data držitele karty (včetně přístupu aplikace, správce a ostatní uživatelé) je omezen následujícím způsobem:
- Všechny přístup uživatelů k, uživatel dotazy pro a akce uživatelů u databáze se pomocí programování.
- Pouze databáze Správci mají možnost přímý přístup k nebo dotazy na databáze.
- ID aplikace pro databázové aplikace lze použít pouze pomocí aplikací (a ne podle jednotlivých uživatelů nebo jiné procesy mimo aplikaci).

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore chrání všechna data držitele karty s Azure Key Vault a šifrování záznamů popsané v dokumentaci k nasazení. Další informace najdete v tématu [pokyny PCI - šifrování](payment-processing-blueprint.md#encryption-and-secrets-management).<br /><br />|



## <a name="pci-dss-requirement-88"></a>PCI DSS požadavek 8.8

**8.8** zajistěte, aby zásady zabezpečení a provozních postupů k identifikaci a ověření jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za zajištění, že zásady zabezpečení a provozních postupů k identifikaci a ověření jsou dokumentovány používán a ví, že všechny dotčené strany.|





---
title: Zřizování správy pro podnikové aplikace v Azure Active Directory uživatelů | Dokumentace Microsoftu
description: Další informace o správě zřizování uživatelských účtů pro podnikové aplikace pomocí Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6bf265f51f2fea16f90dd0bcf2891bd9bed5cef8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963568"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Správa uživatelského účtu zřizování pro podnikové aplikace na webu Azure Portal

Tento článek popisuje způsob použití [webu Azure portal](https://portal.azure.com) ke správě automatické účet zřizování uživatelů pro aplikace, které se na ně a jeho rušení. Další informace o zřizování automatické uživatelských účtů a jak to funguje, najdete v článku [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](user-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Vyhledání aplikace na portálu

Pomocí portálu Azure Active Directory pro zobrazení a Správa všech aplikací, které jsou nakonfigurovány pro jednotné přihlašování v adresáři. Podnikové aplikace jsou aplikace, které se nasazují a používané ve vaší organizaci. Použijte následující postup umožňuje zobrazit a spravovat vaše firemní aplikace:

1. Otevřít [portálu Azure Active Directory](https://aad.portal.azure.com).

1. Vyberte **podnikové aplikace** v levém podokně. Se zobrazí seznam všech nakonfigurovaných aplikací, včetně aplikací, které byly přidány z galerie.

1. Vyberte některou aplikaci načíst prostředek podokno, kde můžete zobrazit sestavy a spravovat nastavení aplikace.

1. Vyberte **zřizování** ke správě nastavení pro vybranou aplikaci pro vytváření uživatelského účtu.

   ![Podokno prostředků aplikace](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Režim zřizování

**Zřizování** podokně začíná **režimu** nabídky, která zobrazuje režim zřizování pro podniková aplikace nepodporuje a umožňuje jejich konfigurace. Mezi dostupné možnosti patří:

* **Automatické** – tato možnost je zobrazena, pokud Azure AD podporuje automatické zřizování založené na rozhraní API nebo zrušení zřizování uživatelských účtů do této aplikace. Vyberte tento režim k zobrazení rozhraní, který pomáhá správcům:

  * Konfigurace služby Azure AD pro připojení k rozhraní API pro správu uživatelů vaší aplikace
  * Vytvoření účtu mapování a pracovní postupy, které definují, jak dat účtu uživatele jakým způsobem se předávají mezi službami Azure AD a aplikace
  * Správa zřizování služby Azure AD

* **Ruční** – tato možnost je zobrazena, pokud Azure AD nepodporuje automatické zřizování uživatelských účtů do této aplikace. V takovém případě uživatelský účet se záznamy ukládají v aplikaci musí být spravovány pomocí externího procesu, na základě uživatele Správa a zřizování možností dostupném v příslušné aplikaci (včetně možnosti zřizování za běhu SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurace zřizování automatické uživatelských účtů

Vyberte **automatické** můžete zadat nastavení pro synchronizaci, mapování, spuštění a zastavení a přihlašovací údaje správce.

### <a name="admin-credentials"></a>Přihlašovací údaje správce

Rozbalte **přihlašovacích údajů správce** k zadání přihlašovacích údajů potřebných pro službu Azure AD pro připojení k rozhraní API pro správu uživatelů vaší aplikace. Vyžaduje se vstup se liší v závislosti na aplikaci. Další informace o požadavcích pro konkrétní aplikace a typy přihlašovacích údajů, najdete v článku [kurz ke konfiguraci pro konkrétní aplikaci](user-provisioning.md).

Vyberte **Test připojení** otestovat přihlašovací údaje tím, že Azure AD pokuste se připojit k aplikaci je zřizování aplikace s použitím zadané přihlašovací údaje.

### <a name="mappings"></a>Mapování

Rozbalte **mapování** zobrazit a upravit atributy uživatele, které budou plout mezi službami Azure AD a cílové aplikace, když jsou uživatelské účty zřízen nebo aktualizován.

Existuje sada předem nakonfigurované mapování mezi objekty uživatelů Azure AD a uživatelských objektů příslušné aplikace SaaS. Některé aplikace spravovat další typy objektů, jako jsou skupiny nebo kontakty. Vyberte v tabulce, čímž otevřete editor mapování doprava, kde můžete zobrazit a přizpůsobit je mapování.

![Podokno prostředků aplikace](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Podporovaná vlastní nastavení patří:

* Povolení a zakázání mapování pro konkrétní objekty, například objekt uživatele Azure AD k objektu uživatele aplikace SaaS.
* Úprava atributů, které vedou z objektu uživatele Azure AD do objektu uživatele aplikace. Další informace o mapování atributů najdete v tématu [Principy mapování typů atributů](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrování zřizování akce, Azure AD, na kterých běží cílové aplikace. Namísto toho, aby plně synchronizovat objekty služby Azure AD, můžete omezit akce spuštění. 

  Například vybrat jenom **aktualizace** a Azure AD pouze aktualizace stávajících uživatelských účtů v aplikaci, ale nevytvoří nové. Vybrat pouze **vytvořit** a Azure pouze vytvoří nové uživatelské účty, ale neprovede aktualizaci existující aplikace. Tato funkce umožňuje správcům vytvořit jiné mapování pro vytváření účtů a aktualizovat pracovní postupy.

* Přidání nové mapování atributů. Vyberte **přidat nové mapování** v dolní části **mapování atributů** podokně. Vyplňte **Upravit atribut** formulář a vyberte **Ok** do seznamu přidat nové mapování. 

### <a name="settings"></a>Nastavení

Můžete spustit a zastavit zřizování služby pro vybranou aplikaci v Azure AD **nastavení** oblasti **zřizování** obrazovky. Můžete také vymazat mezipaměť zřizování a restartujte službu.

Pokud zřizování se povoluje první přihlášení pro aplikaci, zapněte službu tak, že změníte **stavu zřizování** k **na**. Tato změna způsobí, že služba zřizování Azure AD spustit počáteční synchronizaci. Načte v přiřazených uživateli **uživatelů a skupin** oddílu dotazuje cílové aplikace je a pak spustí zřizování akce definované ve službě Azure AD **mapování** části. Během tohoto procesu zřizovací služba ukládá data uložená v mezipaměti o uživatelské účty, které spravuje, tak nespravované účty v cílové aplikace, které nebyly nikdy v oboru pro přiřazení nejsou ovlivněny zrušení zřizování operace. Po počáteční synchronizaci zřizovací služba automaticky synchronizuje objekty uživatelů a skupin na deset minut.

Změnit **stavu zřizování** k **vypnout** pozastavit službu zřizování. V tomto stavu není Azure vytvářet, aktualizovat nebo odebrat všechny uživatele nebo skupiny objektů v aplikaci. Změna stavu zpět do **na** a služby převezme tam, kde skončila.

Vyberte **Vymazat aktuální stav a restartovat synchronizaci** zaškrtávací políčko a vyberte **Uložit** na:

* Zastavit službu zřizování
* Výpis dat uložených v mezipaměti o účty, které spravuje Azure AD
* Restartujte služby a spusťte znovu počáteční synchronizaci

Tato možnost umožňuje správcům zahájit proces zřizování nasazení znovu.

### <a name="synchronization-details"></a>Podrobnosti synchronizace

Tato část obsahuje další podrobnosti o operaci služby zřizování, včetně služby zřizování spustili před aplikace a kolik uživatelů a skupin objektů spravuje první a poslední časy.

Je k dispozici odkaz **sestavu aktivit zřizování**, který poskytuje protokol všichni uživatelé a skupiny vytvořený, aktualizovaná a odebrané mezi Azure AD a cílovou aplikací. Je také k dispozici odkaz **zřizování zprávu o chybách**, která poskytuje další podrobné chybové zprávy pro uživatele a skupiny, které se nepodařilo přečíst, vytvořit, aktualizovat nebo odebrat.

---
title: Zřizování správy pro podnikové aplikace v Azure Active Directory uživatelů | Dokumentace Microsoftu
description: Další informace o správě zřizování uživatelských účtů pro podnikové aplikace pomocí Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 2b2b44deacf8278b8e8c2554cc873c5d0b75f6f9
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366184"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Správa uživatelského účtu zřizování pro podnikové aplikace na webu Azure Portal
Tento článek popisuje způsob použití [webu Azure portal](https://portal.azure.com) ke správě automatické účet zřizování uživatelů pro aplikace, které jej podporují, zejména těch, které jsou přidané z "doporučené" kategorie ajehorušení[ Galerie aplikací Azure Active Directory](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery). Další informace o zřizování automatické uživatelských účtů a jak to funguje, najdete v článku [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="finding-your-apps-in-the-portal"></a>Vyhledání aplikace na portálu
Všechny aplikace, které jsou nakonfigurované pro jednotné přihlašování v adresáři, správcem adresáře pomocí [Galerie aplikací Azure Active Directory](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery), můžete zobrazit a spravovat [webu Azure portal](https://portal.azure.com). Aplikace lze nalézt v **všechny služby** &gt; **podnikové aplikace** části portálu. Podnikové aplikace jsou aplikace, které se nasazují a používané ve vaší organizaci.

![Podokno podnikových aplikací](./media/configure-automatic-user-provisioning-portal/enterprise-apps-pane.png)

Výběr **všechny aplikace** odkaz na levé straně zobrazí seznam všech aplikací, které jste nakonfigurovali, včetně aplikací, přidané z galerie. V podokně prostředků pro tuto aplikaci, ve kterém lze sestavy prohlížet pro tuto aplikaci a je možné spravovat řadu nastavení výběrem aplikace načte.

Nastavení pro vytváření uživatelského účtu můžete spravovat tak, že vyberete **zřizování** na levé straně.

![Podokno prostředků aplikace](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Režim zřizování
**Zřizování** podokně začíná **režimu** nabídky, která ukazuje, jaké režim zřizování jsou podporovány pro podnikové aplikace a umožňuje jejich konfiguraci. Mezi dostupné možnosti patří:

* **Automatické** – tato možnost se zobrazí, pokud Azure AD podporuje automatické zřizování založené na rozhraní API a/nebo zrušení zřizování uživatelských účtů do této aplikace. Tento režim výběru zobrazí rozhraní, které vede správce konfigurace služby Azure AD pro připojení k rozhraní API pro správu uživatelů vaší aplikace, vytváření mapování účtů a pracovních postupů, které definují, jak dat účtu uživatele jakým způsobem se předávají mezi službami Azure AD a aplikace a správa zřizování služby Azure AD.
* **Ruční** – tato možnost je zobrazena, pokud Azure AD nepodporuje automatické zřizování uživatelských účtů do této aplikace. Tato možnost znamená, že záznamů uživatelského účtu uložených v aplikaci se musí spravovat pomocí externího procesu, na základě uživatele Správa a zřizování možností dostupném v příslušné aplikaci (včetně možnosti zřizování za běhu SAML).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurace zřizování automatické uživatelských účtů
Výběr **automatické** možnost otevře obrazovku, která je rozdělena do čtyř oddílů:

### <a name="admin-credentials"></a>Přihlašovací údaje správce
Tato část se kdy vyžaduje přihlašovací údaje pro službu Azure AD pro připojení k rozhraní API jsou zadány pro správu uživatelů vaší aplikace. Vyžaduje se vstup se liší v závislosti na aplikaci. Další informace o požadavcích pro konkrétní aplikace a typy přihlašovacích údajů, najdete v článku [kurz ke konfiguraci pro konkrétní aplikaci](../active-directory-saas-app-provisioning.md).

Výběr **Test připojení** tlačítko využijete k otestování přihlašovací údaje tím, že Azure AD pokuste se připojit k aplikaci prvku zřizování aplikace s použitím zadané přihlašovací údaje.

### <a name="mappings"></a>Mapování
Tato část je, kde můžou správci zobrazit a upravit jaké atributy toku uživatele mezi službami Azure AD a cílové aplikace, když jsou uživatelské účty zřízen nebo aktualizován.

Existuje sada předem nakonfigurované mapování mezi objekty uživatelů Azure AD a uživatelských objektů příslušné aplikace SaaS. Některé aplikace spravovat další typy objektů, jako jsou skupiny nebo kontakty. Výběrem jedné z těchto mapování v tabulka uvádí mapování editor doprava, kde mohou být zobrazit a upravit.

![Podokno prostředků aplikace](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Podporovaná vlastní nastavení patří:

* Povolení a zakázání mapování pro konkrétní objekty, například objekt uživatele Azure AD k objektu uživatele aplikace SaaS.
* Úprava atributů, které vedou z objektu uživatele Azure AD do objektu uživatele aplikace. Další informace o mapování atributů najdete v tématu [Principy mapování typů atributů](../active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).
* Filtrovat zřizování akce, které Azure AD provádí na cílové aplikace. Namísto toho, aby plně synchronizovat objekty služby Azure AD, můžete omezit akce provést. Například výběrem pouze **aktualizace**, Azure AD pouze aktualizace stávajících uživatelských účtů v aplikaci a ne vytvořit nové. Pouze výběrem **vytvořit**, pouze vytvoří nové uživatelské účty ale neaktualizuje existující aplikace Azure. Tato funkce umožňuje správcům vytvořit jiné mapování pro vytváření účtů a aktualizovat pracovní postupy.

### <a name="settings"></a>Nastavení
Tato část umožňuje správcům spuštění a zastavení zřizování služby pro vybranou aplikaci Azure AD a volitelně vymazat mezipaměť zřizování a restartujte službu.

Pokud zřizování se povoluje první přihlášení pro aplikaci, zapněte službu tak, že změníte **stavu zřizování** k **na**. Tato změna způsobí, že služba zřizování Azure AD k provedení počáteční synchronizace, kde ho načte uživatelé přiřazení v **uživatelů a skupin** oddílu dotazuje cílové aplikace je a pak provede akce zřizování definované ve službě Azure AD **mapování** oddílu. Během tohoto procesu zřizovací služba ukládá data uložená v mezipaměti o uživatelské účty, které spravuje, tak nespravované účty v cílové aplikace, které nebyly nikdy v oboru pro přiřazení nejsou ovlivněny zrušení zřizování operace. Po počáteční synchronizaci zřizovací služba automaticky synchronizuje objekty uživatelů a skupin na deset minut.

Změna **stavu zřizování** k **vypnout** jednoduše pozastaví službu zřizování. V tomto stavu Azure není vytvářet, aktualizovat nebo odebrat všechny uživatele nebo skupiny objektů v aplikaci. Změna stavu zpět do způsobí, že služba pokračovat tam, kde skončila.

Výběr **Vymazat aktuální stav a restartovat synchronizaci** zaškrtávací políčko a ukládání zastaví službu zřizování, výpisy dat uložených v mezipaměti o účty, které Azure AD spravuje, restartování služby a provede počáteční synchronizaci znovu. Tato možnost umožňuje správcům zahájit proces zřizování nasazení znovu.

### <a name="synchronization-details"></a>Podrobnosti synchronizace
Tato část obsahuje další podrobnosti o operaci služby zřizování, včetně časy první a poslední zřizovací služba spustili před aplikace a kolik uživatelů a skupin objektů jsou spravovány.

Jsou uvedeny odkazy na **sestavu aktivit zřizování** , který poskytuje protokol všichni uživatelé a skupiny, vytvoření, aktualizace a odebrání mezi Azure AD a cílovou aplikaci a **zřizování zprávu o chybách** , který obsahuje další podrobné chybové zprávy pro uživatele a skupinu objektů, které se nepodařilo přečíst, vytvořit, aktualizovat ani odebrat. 

## <a name="feedback"></a>Váš názor

Nechte prosím už zpětnou vazbu! Publikovat vaše názory a návrhy pro zlepšení **portál pro správu** část naší [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Technický tým je nadšený, vytváření nových co skvělého každý den a používat vaše pokyny na obrazec a definujte, co chcete sestavit jako další.


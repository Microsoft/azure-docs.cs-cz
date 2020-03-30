---
title: Jak nakonfigurovat jednotné přihlašování hesla pro aplikace Azure AD | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat jednotné přihlašování hesla (SSO) k podnikovým aplikacím Azure AD v platformě microsoftových identit (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063522"
---
# <a name="configure-password-single-sign-on"></a>Konfigurace jednotného přihlašování hesla

Když do aplikací Azure AD Enterprise [připřidáte aplikaci galerie](add-gallery-app.md) nebo [webovou aplikaci,](add-non-gallery-app.md) která není galerie, je jednou z možností jednotného přihlášení, které máte k dispozici, [jednotné přihlašování založené na heslech](what-is-single-sign-on.md#password-based-sso). Tato možnost je k dispozici pro všechny weby s přihlašovací stránkou HTML. Zabezpečení založené na heslech, označované také jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je to také užitečné pro scénáře, kde několik uživatelů potřebuje sdílet jeden účet, například s účty aplikací sociálních médií vaší organizace. 

Zabezpečení založené na heslech je skvělý způsob, jak začít rychle integrovat aplikace do Azure AD a umožňuje:

-   Povolení **jednotného přihlašování pro uživatele** bezpečným ukládáním a přehráním uživatelských jmen a hesel pro aplikaci, kterou jste integrovali do Azure AD

-   **Podpora aplikací, které vyžadují více polí pro přihlášení** pro aplikace, které vyžadují více než jen pole uživatelského jména a hesla pro přihlášení

-   **Přizpůsobení popisků** polí pro zadávání uživatelského jména a hesla, která se uživatelům zobrazí na [panelu Pro přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) při zadávání přihlašovacích údajů

-   Umožněte **uživatelům** zadat vlastní uživatelská jména a hesla pro všechny existující účty aplikací, které zadávají ručně na [panelu Přístupkové zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Povolit **členovi obchodní skupiny** zadat uživatelská jména a hesla přiřazená uživateli pomocí funkce [Samoobslužný přístup k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Povolit **správci** zadat uživatelské jméno a heslo, které mají jednotlivci nebo skupiny používat při přihlašování k aplikaci pomocí funkce Aktualizovat pověření 

## <a name="before-you-begin"></a>Než začnete

Pokud aplikace nebyla přidána do vašeho klienta Azure AD, přečtěte si informace [o přidání aplikace galerie](add-gallery-app.md) nebo přidání [aplikace, která není galerie.](add-non-gallery-app.md)

## <a name="open-the-app-and-select-password-single-sign-on"></a>Otevření aplikace a výběr jednotného přihlašování hesla

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce cloudových aplikací nebo správce aplikace pro vašeho klienta Azure AD.

2. Přejděte k**aplikacím** **Azure Active Directory** > Enterprise . Zobrazí se náhodný vzorek aplikací v tenantovi Azure AD. 

3. V nabídce **Typ aplikace** vyberte **Všechny aplikace**a pak vyberte **Použít**.

4. Do vyhledávacího pole zadejte název aplikace a vyberte ji z výsledků.

5. V části **Správa** vyberte **Jednotné přihlašování**. 

6. Vyberte **možnost Založené na heslech**.

7. Zadejte adresu URL webové přihlašovací stránky aplikace. Tento řetězec musí být stránka, která obsahuje vstupní pole uživatelského jména.

   ![Jednotné přihlašování založené na heslech](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Vyberte **Uložit**. Azure AD se pokusí analyzovat přihlašovací stránku pro vstup uživatelského jména a zadání hesla. Pokud se pokus uspěje, je po všem. 
 
> [!NOTE]
> Dalším krokem je [přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md). Po přiřazení uživatelů a skupin můžete zadat pověření, která budou použita jménem uživatele při přihlášení k aplikaci. Vyberte **Uživatelé a skupiny**, zaškrtněte políčko pro řádek uživatele nebo **skupiny**a klepněte na tlačítko Aktualizovat pověření . Poté zadejte uživatelské jméno a heslo, které chcete použít jménem uživatele nebo skupiny. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.
 

## <a name="manual-configuration"></a>Ruční konfigurace

Pokud se nezdaří pokus o analýzu služby Azure AD, můžete nakonfigurovat přihlášení ručně.

1. V ** \<** části název aplikace> Konfigurace vyberte **Konfigurovat \<název aplikace> nastavení jednotného přihlášení hesla,** **chcete-li** zobrazit stránku Konfigurovat přihlášení. 

2. Vyberte **Ručně rozpoznat přihlašovací pole**. Zobrazí se další pokyny popisující ruční zjišťování polí pro přihlášení.

   ![Ruční konfigurace jednotného přihlašování založeného na heslech](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Vyberte **Zachytit přihlašovací pole**. Na nové kartě se otevře stránka se stavem zachycení, která zobrazuje **právě probíhající sběr metadat**zprávy .

4. Pokud se na nové kartě zobrazí pole **Povinné rozšíření přístupového panelu,** vyberte Nainstalovat **a** nainstalujte rozšíření rozšíření rozšíření zabezpečení přihlášení **moje aplikace.** (Rozšíření prohlížeče vyžaduje Microsoft Edge, Chrome nebo Firefox.) Potom nainstalujte, spusťte a povolte rozšíření a aktualizujte stránku stavu sběru.

   Rozšíření prohlížeče pak otevře další kartu, která zobrazuje zadanou adresu URL.
5. Na kartě se zadanou adresou URL projděte procesem přihlášení. Vyplňte pole uživatelského jména a hesla a zkuste se přihlásit. (Není třeba zavést správné heslo.)

   Zobrazí se výzva s výzvou k uložení zachycených přihlašovacích polí.
6. Vyberte **OK**. Rozšíření prohlížeče aktualizuje stránku stavu sběru zpráv **Metadata byla aktualizována pro aplikaci**. Karta prohlížeče se zavře.

7. Na stránce **konfigurace služby** Azure AD vyberte Ok, podařilo se mi **úspěšně se přihlásit k aplikaci**.

8. Vyberte **OK**.

Po zachycení přihlašovací stránky můžete přiřadit uživatele a skupiny a můžete nastavit zásady pověření stejně jako běžné [aplikace s přihlašování heslem](what-is-single-sign-on.md).

> [!NOTE]
> Logo dlaždice pro aplikaci můžete nahrát pomocí tlačítka **Nahrát logo** na kartě **Konfigurovat** pro aplikaci.

## <a name="next-steps"></a>Další kroky

- [Přiřazení uživatelů nebo skupin k aplikaci](methods-for-assigning-users-and-groups.md)
- [Konfigurace automatického zřizování uživatelských účtů](../app-provisioning/configure-automatic-user-provisioning-portal.md)

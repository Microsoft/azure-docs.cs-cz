---
title: 'Kurz: Konfigurace izolovaného prostoru Salesforce pro Automatické zřizování uživatelů s Azure Active Directory | Microsoft Docs'
description: Seznamte se s kroky, které je třeba provést v izolovaném prostoru (sandbox) Salesforce a Azure AD pro automatické zřízení a zrušení zřízení uživatelských účtů z Azure AD do služby Salesforce Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7e3f8e5e975468b468712ae8907cdca0e80a5f9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94352604"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Kurz: Konfigurace izolovaného prostoru Salesforce pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které musíte provést v izolovaném prostoru (sandbox) Salesforce a Azure AD a automaticky zřizovat a zrušit zřízení uživatelských účtů z Azure AD do izolovaného prostoru služby Salesforce.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

*   Tenant Azure Active Directory.
*   Platný tenant pro služby Salesforce Sandbox for Work nebo Salesforce Sandbox pro vzdělávání. Pro kteroukoli službu můžete použít bezplatný zkušební účet.
*   Uživatelský účet v izolovaném prostoru Salesforce s oprávněními správce týmu.

## <a name="assigning-users-to-salesforce-sandbox"></a>Přiřazování uživatelů do izolovaného prostoru Salesforce

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte určit, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k vaší aplikaci Salesforce Sandbox. Po provedení tohoto rozhodnutí můžete těmto uživatelům přiřadit aplikaci pro sandboxu Salesforce podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) .

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Důležité tipy pro přiřazení uživatelů k izolovanému prostoru Salesforce

* Doporučujeme, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD k izolovanému prostoru Salesforce. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele do izolovaného prostoru služby Salesforce je nutné vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

> [!NOTE]
> Tato aplikace importuje vlastní role z izolovaného prostoru (sandbox) Salesforce jako součást procesu zřizování, který může zákazník chtít vybrat při přiřazování uživatelů.

## <a name="enable-automated-user-provisioning"></a>Povolit automatizované zřizování uživatelů

V této části se seznámíte s připojením k rozhraní API pro zřizování uživatelských účtů v izolovaném prostoru (sandbox) Azure AD a nakonfigurujete službu zřizování k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v izolovaném prostoru Salesforce na základě přiřazení uživatelů a skupin ve službě Azure AD.

>[!Tip]
>V případě izolovaného prostoru (sandbox) založeného na protokolu SAML můžete také povolit Single Sign-On založená na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů

Cílem této části je vysvětlit, jak povolit uživatelům zřizování uživatelských účtů služby Active Directory do izolovaného prostoru (sandbox) Salesforce.

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

1. Pokud jste již nakonfigurovali izolovaný prostor Salesforce pro jednotné přihlašování, vyhledejte instanci izolovaného prostoru Salesforce pomocí vyhledávacího pole. V opačném případě vyberte možnost **Přidat** a vyhledat **izolovaný prostor Salesforce** v galerii aplikací. Z výsledků hledání vyberte izolovaný prostor Salesforce a přidejte ho do seznamu aplikací.

1. Vyberte instanci izolovaného prostoru (sandbox) Salesforce a pak vyberte kartu **zřizování** .

1. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky ukazuje stránku zřizování izolovaného prostoru (sandbox) s režimem zřizování nastaveným na automatické a jiné hodnoty, které můžete nastavit.](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. V části **přihlašovací údaje správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **správce uživatelské jméno** zadejte název účtu izolovaného prostoru (sandbox), který má přiřazený profil **správce systému** v Salesforce.com.
   
    b. Do textového pole **heslo správce** zadejte heslo pro tento účet.

1. Pokud chcete získat token zabezpečení izolovaného prostoru Salesforce, otevřete novou kartu a přihlaste se ke stejnému účtu správce izolovaného prostoru (Salesforce). V pravém horním rohu stránky klikněte na své jméno a pak klikněte na **Nastavení**.

     ![Snímek obrazovky se zobrazuje vybraný odkaz nastavení.](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Povolit automatické zřizování uživatelů")

1. V levém navigačním podokně klikněte na **osobní informace** a rozbalte související část a potom klikněte na **resetovat můj token zabezpečení**.
  
    ![Snímek obrazovky s vybraným osobním údajem resetování tokenu zabezpečení](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Povolit automatické zřizování uživatelů")

1. Na stránce **resetovat token zabezpečení** klikněte na tlačítko **resetovat token zabezpečení** .

    ![Snímek obrazovky se stránkou s tokenem zabezpečení REST s vysvětlujícím textem a možností resetovat token zabezpečení](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Povolit automatické zřizování uživatelů")

1. Ověřte e-mailové doručené pošty přidružené k tomuto účtu správce. Vyhledejte e-mail ze služby Salesforce Sandbox.com, který obsahuje nový token zabezpečení.

1. Zkopírujte token, otevřete okno Azure AD a vložte ho do pole **tajný token** .

1. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci izolovaného prostoru Salesforce.

1. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

1. Klikněte na **Uložit.**  
    
1.  V části mapování vyberte možnost **synchronizovat Azure Active Directory uživatele do izolovaného prostoru Salesforce.**

1. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do služby Salesforce Sandbox. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v izolovaném prostoru Salesforce pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

1. Pokud chcete povolit službu zřizování Azure AD pro izolovaný prostor Salesforce, změňte **stav zřizování** na **zapnuto** v části nastavení.

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených k izolovanému prostoru Salesforce v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci v izolovaném prostoru (sandbox) Salesforce.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](./salesforce-sandbox-tutorial.md)
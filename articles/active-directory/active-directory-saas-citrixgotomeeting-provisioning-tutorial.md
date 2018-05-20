---
title: 'Kurz: Konfigurace GoToMeeting pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f59fedb-2cf8-48d2-a5fb-222ed943ff78
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: cbf53904c5fbf4ddfeb8c9d72efcf4549b1d7a7a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Kurz: Konfigurace GoToMeeting pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v GoToMeeting a Azure AD a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do GoToMeeting.

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klienta služby Azure Active directory.
*   GoToMeeting jednotné přihlašování povolené předplatné.
*   Uživatelský účet v GoToMeeting s oprávněními správce týmu.

## <a name="assigning-users-to-gotomeeting"></a>Přiřazení uživatelů k GoToMeeting

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu se synchronizují pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování služby, musíte rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci GoToMeeting. Jakmile se rozhodli, můžete přiřadit těmto uživatelům aplikace GoToMeeting podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Důležité tipy pro přiřazování uživatelů do GoToMeeting

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke GoToMeeting a otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazování GoToMeeting uživatele, musíte vybrat platné uživatelské role. Roli "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit automatické zřizování uživatelů

Tato část vás provede připojení k GoToMeeting na uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakažte přiřazené uživatelské účty v GoToMeeting podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také pro GoToMeeting povoleno na základě SAML jednotné přihlašování, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurace automatického uživatele zřizování účtu:

1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste již nakonfigurovali GoToMeeting pro jednotné přihlašování, vyhledejte instanci GoToMeeting pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **GoToMeeting** v galerii aplikací. Vyberte GoToMeeting ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci GoToMeeting a pak vyberte **zřizování** kartě.

4. Nastavte **zřizování** režim **automatické**. 

    ![zřizování](./media/active-directory-saas-citrixgotomeeting-provisioning-tutorial/provisioning.png)

5. V části přihlašovací údaje správce proveďte následující kroky:
   
    a. V **uživatelské jméno správce GoToMeeting** textovému poli, zadejte uživatelské jméno správce.

    b. V **heslo správce GoToMeeting** textovému poli, heslo správce.

6. Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k aplikaci GoToMeeting. Pokud připojení nezdaří, zkontrolujte účtu GoToMeeting má oprávnění správce týmu a zkuste to **"Přihlašovací údaje správce"** krok opakujte.

7. Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole a zaškrtnutím políčka.

8. Klikněte na tlačítko **uložit.**

9. V části mapování vyberte **synchronizaci Azure Active Directory uživatelům GoToMeeting.**

10. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizované z Azure AD GoToMeeting. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v GoToMeeting pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

11. Povolit zřizování služby pro GoToMeeting Azure AD, změňte **Stav zřizování** k **na** v části Nastavení

12. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k GoToMeeting v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud se službou provést. Můžete použít **podrobnosti synchronizace** oddílu monitorovat průběh a odkazech na protokoly aktivity, které popisují všechny akce prováděné při zřizování služby ve vaší aplikaci GoToMeeting zřizování.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)



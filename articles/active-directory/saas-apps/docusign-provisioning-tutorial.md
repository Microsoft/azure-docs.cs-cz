---
title: 'Kurz: Konfigurace DocuSign pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 18004d7b7f40d8461346f7f529f54ef5f982d8de
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213623"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Kurz: Konfigurace DocuSign pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v DocuSign a Azure AD a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do DocuSign.

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klienta služby Azure Active directory.
*   DocuSign jednotné přihlašování povolené předplatné.
*   Uživatelský účet v DocuSign s oprávněními správce týmu.

## <a name="assigning-users-to-docusign"></a>Přiřazení uživatelů k DocuSign

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování služby, musíte rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci DocuSign. Jakmile se rozhodli, můžete přiřadit těmto uživatelům aplikace DocuSign podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Důležité tipy pro přiřazování uživatelů do DocuSign

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke DocuSign a otestovat konfiguraci zřizování. Další uživatelé mohou přiřadit později.

*   Při přiřazování DocuSign uživatele, musíte vybrat platné uživatelské role. Roli "Výchozí přístup" nefunguje pro zřizování.

> [!NOTE]
> Azure AD nepodporuje skupiny zřizování s Docusign aplikace, může být zřízen jenom uživatelé.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojení k DocuSign na uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakažte přiřazené uživatelské účty v DocuSign podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip]
> Můžete také pro DocuSign povoleno na základě SAML jednotné přihlašování, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-user-account-provisioning"></a>Ke konfiguraci zřizování účtu uživatele:

Cílem této části se popisují postup povolení zřizování uživatelů z uživatelských účtů služby Active Directory pro DocuSign.

1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste již nakonfigurovali DocuSign pro jednotné přihlašování, vyhledejte instanci DocuSign pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **DocuSign** v galerii aplikací. Vyberte DocuSign ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci DocuSign a pak vyberte **zřizování** kartě.

4. Nastavte **režimu zřizování** k **automatické**. 

    ![zřizování](./media/docusign-provisioning-tutorial/provisioning.png)

5. V části **přihlašovací údaje správce** části, zadejte následující nastavení konfigurace:
   
    a. V **uživatelské jméno správce** textovému poli, zadejte název, který má účtu DocuSign **správce systému** profil v DocuSign.com přiřazen.
   
    b. V **heslo správce** textovému poli, zadejte heslo pro tento účet.

6. Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k aplikaci DocuSign.

7. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, kdo by měly dostávat oznámení zřizování chyby a zaškrtněte políčko.

8. Klikněte na tlačítko **uložit.**

9. V části mapování vyberte **synchronizaci Azure Active Directory uživatelům DocuSign.**

10. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizované z Azure AD DocuSign. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v DocuSign pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

11. Povolit zřizování služby pro DocuSign Azure AD, změňte **Stav zřizování** k **na** v části Nastavení

12. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů přidružených k DocuSign v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud se službou provést. Můžete použít **podrobnosti synchronizace** oddílu monitorovat průběh a odkazech na protokoly aktivity, které popisují všechny akce prováděné při zřizování služby ve vaší aplikaci DocuSign zřizování.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](docusign-tutorial.md)
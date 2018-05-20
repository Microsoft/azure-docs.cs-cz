---
title: 'Kurz: Konfigurace Concur pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: c439e6ca784bf031cd4102a6cb15b5e210130810
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Kurz: Konfigurace Concur pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v Concur a Azure AD a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do Concur.

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klienta služby Azure Active directory.
*   Concur jednotné přihlašování povolené předplatné.
*   Uživatelský účet v Concur s oprávněními správce týmu.

## <a name="assigning-users-to-concur"></a>Přiřazení uživatelů k Concur

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu se synchronizují pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování služby, musíte rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k vaší aplikaci Concur. Jakmile se rozhodli, můžete přiřadit těmto uživatelům aplikace Concur podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Důležité tipy pro přiřazování uživatelů do Concur

*   Dále je doporučeno jednoho uživatele Azure AD pro Concur přidělí otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazování Concur uživatele, musíte vybrat platné uživatelské role. Roli "Výchozí přístup" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojení k Concur na uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakažte přiřazené uživatelské účty v Concur podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip] 
> Můžete také pro Concur povoleno na základě SAML jednotné přihlašování, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="to-configure-user-account-provisioning"></a>Ke konfiguraci zřizování účtu uživatele:

Cílem této části se popisují postup povolení zřizování uživatelských účtů služby Active Directory do Concur.

K povolení služby náklady, že musí být správné nastavení a použití profilu Správce webu služby. Nepřidáte roli správce WS stávající profil správce, který používáte pro T & E funkce správy.

Vyústit konzultanty nebo správce klienta, musíte vytvořit profil odlišné správce webové služby a musí správce klienta použít tento profil pro funkce správce webu služby (například povolení aplikace). Tyto profily musí být udržovány odděleně od klienta denní T & E správce profil správce (Správce profil T & E by neměl mít přiřazenou roli WSAdmin).

Když vytvoříte profil, který se použije pro povolení aplikace, zadejte do pole profil uživatele jméno správce klienta. Tím se přiřadí vlastnictví k profilu. Po vytvoření jednoho nebo více profilů klient musí přihlásit pomocí tohoto profilu můžete kliknutím na "*povolit*" tlačítko pro partnera aplikaci v nabídce webové služby.

Z následujících důvodů by neměla provést tuto akci s profilem, které používají pro správu normální T & E.

* Klient musí být ten, který klikne na možnost "*Ano*" v dialogu okně, které se zobrazí po povolení aplikace. Klikněte na toto tlačítko uznává, že klient je ochotná partnera aplikace přistupovat ke svým datům, takže můžete nebo partnerovi nelze na toto tlačítko Ano.

* Pokud správce klienta, která povolila aplikaci pomocí Správce T & E profil společnost opustí (což je v profilu se deaktivovány), musí všechny aplikace povolit pomocí profilu dokud aplikace je povolená s jinou active profilu WS správce nebude fungovat. Z tohoto důvodu je mají odlišné správce WS vytváření profilů.

* Pokud správce ze společnosti odejde, název přidružené k profilu WS správce lze změnit správci nahrazení v případě potřeby bez dopadu, že aplikaci povoleno, protože není nutné tento profil deaktivovány.

**Pokud chcete konfigurovat, zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Concur** klienta.

2. Z **správy** nabídce vyberte možnost **webové služby**.
   
    ![Klienta Concur](./media/active-directory-saas-concur-provisioning-tutorial/IC721729.png "Concur klienta")

3. Na levé straně od **webové služby** podokně, vyberte **povolit aplikaci partnera**.
   
    ![Povolit aplikaci partnera](./media/active-directory-saas-concur-provisioning-tutorial/ic721730.png "povolit partnera aplikace")

4. Z **povolit aplikaci** seznamu, vyberte **Azure Active Directory**a potom klikněte na **povolit**.
   
    ![Microsoft Azure Active Directory](./media/active-directory-saas-concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klikněte na tlačítko **Ano** zavřete **potvrďte akci** dialogové okno.
   
    ![Akci potvrďte](./media/active-directory-saas-concur-provisioning-tutorial/ic721732.png "potvrzení akce")

6. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

7. Pokud jste již nakonfigurovali Concur pro jednotné přihlašování, vyhledejte instanci Concur pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **Concur** v galerii aplikací. Vyberte Concur ve výsledcích hledání a přidejte ji do seznamu aplikací.

8. Vyberte instanci Concur a pak vyberte **zřizování** kartě.

9. Nastavte **režimu zřizování** k **automatické**. 
 
    ![zřizování](./media/active-directory-saas-concur-provisioning-tutorial/provisioning.png)

10. V části **přihlašovací údaje správce** zadejte **uživatelské jméno** a **heslo** vaše Concur správce.

11. Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k aplikaci Concur. Pokud se nepovede připojit, zajistěte, aby byl váš účet Concur oprávnění správce týmu.

12. Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole a zaškrtnutím políčka.

13. Klikněte na tlačítko **uložit.**

14. V části mapování vyberte **synchronizaci Azure Active Directory uživatelům Concur.**

15. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizované z Azure AD Concur. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Concur pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

16. Povolit zřizování služby pro Concur Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části

17. Klikněte na tlačítko **uložit.**

Nyní můžete vytvořit testovací účet. Chcete-li ověřit, že účet byly synchronizovány Concur Počkejte až 20 minut.

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](active-directory-saas-tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](active-directory-saas-concur-tutorial.md)


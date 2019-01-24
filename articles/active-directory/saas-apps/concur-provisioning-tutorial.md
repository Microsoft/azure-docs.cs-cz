---
title: 'Kurz: Konfigurace Concur pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Concur.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: df47f55f-a894-4e01-a82e-0dbf55fc8af1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: e6e6d0d51174250954f886790dccc650064a6f45
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821885"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Kurz: Konfigurace Concur pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je potřeba provést Concur a Azure AD a automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do Concur.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory.
*   Concur jednotného přihlašování povolená předplatného.
*   Účet uživatele s oprávněními správce týmu v Concur.

## <a name="assigning-users-to-concur"></a>Přiřazování uživatelů k Concur

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují, kteří potřebují přístup k vaší aplikaci Concur. Po se rozhodli, můžete přiřadit tito uživatelé do vaší aplikace Concur podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Důležité tipy pro přiřazování uživatelů k Concur

*   Dále je doporučeno jednoho uživatele Azure AD pro Concur přidělí k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Concur, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

## <a name="enable-user-provisioning"></a>Povolit zřizování uživatelů

Tato část vás provede připojení k rozhraní API zřizování Concur pro uživatelský účet služby Azure AD a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v Concur podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip] 
> Můžete také povolena založené na SAML jednotného přihlašování pro Concur, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-user-account-provisioning"></a>Postup konfigurace zřizování uživatelských účtů:

Cílem této části se popisují, jak povolit zřizování uživatelských účtů služby Active Directory k Concur.

K povolení aplikace ve službě výdajů, že musí být správné nastavení a použití webových služeb pro správu profilu. Role správce WS nepřidávejte do existující profil správce, který používáte pro T & E funkce správy.

Concur konzultanty nebo správce klienta, musíte vytvořit různé profil správce webové služby a správce klienta, musíte použít tento profil pro funkce webové služby správce (například povolení aplikace). Tyto profily musí být udržovány odděleně od správce klienta denní T & E správce profilu (T & E profil Správce by neměly mít přiřazenou roli WSAdmin).

Když vytvoříte profil, který chcete použít pro povolení aplikace, zadejte do polí profilu uživatele správce klienta název. Toto vlastnictví přiřadí k profilu. Po vytvoření jednoho nebo více profilů klienta musíte přihlásit pomocí tohoto profilu klikněte "*povolit*" tlačítko pro aplikace s partnerem v rámci nabídky webové služby.

Z následujících důvodů by neměla provést tuto akci s profilem, které používají pro správu na normální T & E.

* Klient musí být ten, který klikne na tlačítko "*Ano*" v dialogu okně, které se zobrazí po povolení aplikace. Klikněte na tlačítko bere na vědomí, že klient ochotni aplikace partnera pro přístup k datům, takže můžete nebo partnera nemůže klikněte na tlačítko Ano.

* Pokud správce klienta, která povolila aplikaci pomocí Správce T & E profilu odejde ze společnosti (což v profilu se deaktivovala), všechny aplikace povolit pomocí profilu do aplikace je povolená s jinou aktivní profilu WS správce není funkční. To je důvod, proč je mají odlišné správce WS vytváření profilů.

* Pokud správce odejde ze společnosti, název přidružený k profilu WS správce můžete změnit na náhradního správce v případě potřeby bez dopadu na, že povoleno app, protože není nutné tento profil deaktivovala.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **Concur** tenanta.

2. Z **správu** nabídce vyberte možnost **webových služeb**.
   
    ![Concur tenanta](./media/concur-provisioning-tutorial/IC721729.png "Concur tenanta")

3. Na levé straně od **webových služeb** vyberte **povolit partnerské aplikace**.
   
    ![Povolit aplikaci partnera](./media/concur-provisioning-tutorial/ic721730.png "umožňují aplikaci partnera")

4. Z **povolit aplikace** seznamu vyberte **Azure Active Directory**a potom klikněte na tlačítko **povolit**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Klikněte na tlačítko **Ano** zavřete **potvrďte akci** dialogového okna.
   
    ![Potvrdit akci](./media/concur-provisioning-tutorial/ic721732.png "Potvrdit akci")

6. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

7. Pokud jste už nakonfigurovali Concur pro jednotné přihlašování, vyhledejte svoji instanci služby Concur pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Concur** v galerii aplikací. Ve výsledcích hledání vyberte Concur a přidat do seznamu aplikací.

8. Vyberte instanci Concur a potom **zřizování** kartu.

9. Nastavte **režim zřizování** k **automatické**. 
 
    ![zřizování](./media/concur-provisioning-tutorial/provisioning.png)

10. V části **přihlašovacích údajů správce** části, zadejte **uživatelské jméno** a **heslo** Concur správce.

11. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Concur. Pokud se nepovede, ujistěte se, že váš účet Concur má oprávnění správce týmu.

12. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko.

13. Klikněte na tlačítko **uložit.**

14. V oddílu mapování, vyberte **synchronizace Azure Active Directory uživatelům Concur.**

15. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do Concur. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Concur pro operace update. Vyberte tlačítko Uložit potvrďte změny.

16. Služba pro Concur zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu

17. Klikněte na tlačítko **uložit.**

Nyní můžete vytvořit zkušební účet. Chcete-li ověřit, že účet byl synchronizován do Concur čekat až 20 minut.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](concur-tutorial.md)


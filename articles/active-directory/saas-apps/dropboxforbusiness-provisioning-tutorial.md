---
title: 'Kurz: Konfigurace Dropboxu pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 0f3a42e4-6897-4234-af84-b47c148ec3e1
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 8e61fe4af83ee72df74027b2e52a3e81db486798
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347303"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Kurz: Konfigurace Dropbox for Business pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v Dropboxu pro firmy a Azure AD, aby automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD na Dropbox for Business.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory.
*   Dropbox pro obchodní jednotné přihlašování v předplatném povolené.
*   Účet uživatele s oprávněními správce týmu v Dropboxu pro firmy.

## <a name="assigning-users-to-dropbox-for-business"></a>Přiřazování uživatelů k Dropboxu pro firmy

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup do vaší schránky pro obchodní aplikace. Jakmile se rozhodli, můžete přiřadit tito uživatelé do vaší schránky pro obchodní aplikace podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Důležité tipy pro přiřazování uživatelů k Dropboxu pro firmy

*   Doporučujeme jeden je přiřazen Dropbox for Business pro testování konfigurace zřizování uživatele Azure AD. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazování uživatele na Dropbox for Business, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování...

## <a name="enable-automated-user-provisioning"></a>Povolit automatické zřizování uživatelů

Tato část vás provede připojením služby Azure AD na Dropbox firemní uživatelského účtu rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v Dropboxu pro firmy na základě uživatele a skupiny přiřazení ve službě Azure AD.

>[!Tip]
>Můžete také povolena založené na SAML jednotného přihlašování pro Dropbox for Business, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-account-provisioning"></a>Postup konfigurace zřizování automatické uživatelských účtů:

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste už nakonfigurovali Dropbox for Business pro jednotné přihlašování, vyhledávání pro vaši instanci Dropbox for Business, pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Dropbox for Business** v galerii aplikací. Vyberte Dropbox for Business ve výsledcích hledání a přidat do seznamu aplikací.

3. Vyberte instanci Dropbox for Business a potom **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**. 

    ![zřizování](./media/dropboxforbusiness-provisioning-tutorial/provisioning.png)

5. V části **přihlašovacích údajů správce** klikněte na tlačítko **Authorize**. Dropbox pro obchodní přihlašovací dialogové okno se otevře v novém okně prohlížeče.

6. Na **přihlásit se k Dropboxu, abyste mohli propojit s Azure AD** dialogové okno přihlášení do vaší schránky pro obchodní tenanta.

     ![Zřizování uživatelů](./media/dropboxforbusiness-provisioning-tutorial/ic769518.png "zřizování uživatelů")

7. Potvrďte, že chcete udělit oprávnění Azure Active Directory provádět změny do vaší schránky pro obchodní tenanta. Klikněte na tlačítko **povolit**.
    
      ![Zřizování uživatelů](./media/dropboxforbusiness-provisioning-tutorial/ic769519.png "zřizování uživatelů")

8. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k vaší schránky pro obchodní aplikace. Pokud se nepovede, zajistěte vašeho Dropboxu pro obchodní účet má oprávnění správce týmu a **"Ověřit"** krok znovu.

9. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko.

10. Klikněte na tlačítko **uložit.**

11. V oddílu mapování, vyberte **synchronizace Azure Active Directory Users na Dropbox for Business.**

12. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD na Dropbox for Business. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Dropboxu pro firmy pro operace update. Vyberte tlačítko Uložit potvrďte změny.

13. Chcete-li povolit služba pro Dropbox for Business zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

14. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Dropboxu pro firmy v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce prováděné na váš Dropbox zřizovací služba pro obchodní aplikace.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](dropboxforbusiness-tutorial.md)
---
title: 'Kurz: Konfigurace služby Infor CloudSuite pro automatické zřizování uživatelů pomocí služby Azure Active Directory | Dokumenty společnosti Microsoft'
description: Zjistěte, jak nakonfigurovat službu Azure Active Directory tak, aby automaticky zřašovala a odpočatou uživatelské účty do služby Infor CloudSuite.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 3ea430bb-86a7-4bb4-8315-95434a660e88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 7b91b8418580717afaf8ddf176f934b3ff1d0c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057535"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Kurz: Konfigurace zařízení Infor CloudSuite pro automatické zřizování uživatelů

Cílem tohoto kurzu je demonstrovat kroky, které mají být provedeny v Infor CloudSuite a Azure Active Directory (Azure AD) nakonfigurovat Azure AD automaticky zřídit a de-provision uživatelů a/nebo skupin infor CloudSuite.

> [!NOTE]
> Tento kurz popisuje konektor postavený na nad službou zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá, jak funguje, a nejčastější dotazy, najdete [v tématu Automatizace zřizování uživatelů a zrušení zřizování aplikací SaaS pomocí služby Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve verzi Public Preview. Další informace o obecných podmínkách použití Microsoft Azure pro funkce preview najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující požadavky:

* Klient Azure AD
* [Tenant Infor CloudSuite](https://www.infor.com/products/infor-os)
* Uživatelský účet v Infor CloudSuite s oprávněními správce.

## <a name="assigning-users-to-infor-cloudsuite"></a>Přiřazení uživatelů k Infor CloudSuite

Azure Active Directory používá koncept s názvem *přiřazení* k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatickézřižené zřizování uživatelů jsou synchronizovány pouze uživatelé nebo skupiny, které byly přiřazeny k aplikaci ve službě Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k Infor CloudSuite. Jakmile se rozhodnete, můžete přiřadit tyto uživatele a / nebo skupiny Infor CloudSuite podle pokynů zde:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Důležité tipy pro přiřazení uživatelů do Infor CloudSuite

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen a Infor CloudSuite k testování konfigurace automatického zřizování uživatelů. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k infor CloudSuite, musíte vybrat všechny platné role specifické pro aplikaci (pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s rolí **Výchozí přístup** jsou z zřizování vyloučeni.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Nastavení Infor CloudSuite pro zřizování

1. Přihlaste se ke [konzoli Infor CloudSuite Admin Console](https://www.infor.com/customer-center). Klikněte na ikonu uživatele a přejděte na **správu uživatelů**.

    ![Konzola pro správu Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  Klikněte na ikonu nabídky v levém horním rohu obrazovky. Klikněte na **Spravovat**.

    ![Infor CloudSuite Přidat SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Přejděte na **účty SCIM**.

    ![Účet Infor CloudSuite SCIM](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Přidejte správce kliknutím na ikonu plus. Zadejte **heslo SCIM** a zadejte stejné heslo v části **Potvrdit heslo**. Kliknutím na ikonu složky uložte heslo. Poté se zobrazí **identifikátor uživatele** generovaný pro uživatele správce.

    ![Uživatel správce Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Heslo infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    ![Identifikátor Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/identifier.png)

5. Chcete-li generovat token nosiče, zkopírujte **identifikátor uživatele** a **heslo SCIM**. Vložte je do poznámkového bloku++ odděleného dvojtečkou. Zakódujte hodnotu řetězce tak, že přejdete na **Pluginy > nástroje MIME > Basic64 Encode**. 

    ![Identifikátor Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/token.png)

3.  Zkopírujte žeton nosiče. Tato hodnota se zadá do pole Tajný token na kartě Zřizování aplikace Infor CloudSuite na webu Azure Portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Přidání Infor CloudSuite z galerie

Před konfigurací Infor CloudSuite pro automatické zřizování uživatelů pomocí Azure AD, je potřeba přidat Infor CloudSuite z galerie aplikací Azure AD do seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Infor CloudSuite z galerie aplikací Azure AD, proveďte následující kroky:**

1. Na **[webu Azure Portal](https://portal.azure.com)** vyberte na levém navigačním panelu **položku Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace**a vyberte **všechny aplikace**.

    ![Okno Aplikace Enterprise](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Infor CloudSuite**, vpanelu s výsledky vyberte **Infor CloudSuite** a pak klepnutím na tlačítko **Přidat** přidejte aplikaci.

    ![Infor CloudSuite v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Konfigurace automatického zřizování uživatelů do Služby Infor CloudSuite 

Tato část vás provede kroky konfigurace služby zřizování Azure AD k vytvoření, aktualizaci a zakázání uživatelů nebo skupin v Infor CloudSuite na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování na bázi SAML pro Infor CloudSuite podle pokynů uvedených v [kurzu pro jednotné přihlašování Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce kompliment navzájem.

> [!NOTE]
> Další informace o koncovém bodě SCIM aplikace Infor CloudSuite naleznete na [tomto](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#).

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Konfigurace automatického zřizování uživatelů pro Infor CloudSuite ve službě Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **podnikové aplikace**a pak vyberte **Všechny aplikace**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Infor CloudSuite**.

    ![Odkaz Infor CloudSuite v seznamu Aplikace](common/all-applications.png)

3. Vyberte kartu **Zřizování.**

    ![Karta Zřizování](common/provisioning.png)

4. Nastavte **režim zřizování** na **automatické**.

    ![Karta Zřizování](common/provisioning-automatic.png)

5. V části **Pověření správce** `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` zadejte adresu **URL klienta**. Zadejte hodnotu nosné tokenu načtenou dříve v **tajném tokenu**. Kliknutím na **Testovat připojení** zajistíte, že se Azure AD může připojit k Infor CloudSuite. Pokud se připojení nezdaří, ujistěte se, že váš účet Infor CloudSuite má oprávnění správce a zkuste to znovu.

    ![Adresa URL klienta + token](common/provisioning-testconnection-tenanturltoken.png)

6. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách při zřizování, a zaškrtněte políčko – **Odeslat e-mailové oznámení, když dojde k chybě**.

    ![E-mail s oznámením](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **Mapování** vyberte **Synchronizovat uživatele služby Azure Active Directory s aplikací Infor CloudSuite**.

    ![Mapování uživatelů Služby Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do Infor CloudSuite v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Infor CloudSuite pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Uživatelské atributy Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. V části **Mapování** vyberte **Synchronizovat skupiny Služby Active Directory Azure s aplikací Infor CloudSuite**.

    ![Mapování skupiny Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny, které jsou synchronizovány z Azure AD do Infor CloudSuite v části **Mapování atributů.** Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly skupinám v Infor CloudSuite pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko **Uložit.**

    ![Atributy skupiny Infor CloudSuite](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Chcete-li konfigurovat filtry oborů, naleznete v následujících pokynech uvedených v [kurzu filtru oborů](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Chcete-li povolit službu zřizování Azure AD pro Infor CloudSuite, změňte **stav zřizování** **na Zapnuto** v části **Nastavení.**

    ![Stav zřizování zapnutý](common/provisioning-toggle-on.png)

14. Definujte uživatele nebo skupiny, které chcete zřídit infor CloudSuite výběrem požadovaných hodnot v **oboru** v části **Nastavení.**

    ![Obor zřizování](common/provisioning-scope.png)

15. Až budete připraveni k zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení.** Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na Infor CloudSuite.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Přečtěte si, jak zkontrolovat protokoly a získat sestavy o aktivitě zřizování.](../app-provisioning/check-status-user-account-provisioning.md)
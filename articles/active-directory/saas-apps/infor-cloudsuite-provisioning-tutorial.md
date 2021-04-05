---
title: 'Kurz: Konfigurace informačního CloudSuiteu pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Naučte se, jak nakonfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro informační CloudSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 8fdd2c8a326fbdc68d1aec65377f4c465c5ee4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353897"
---
# <a name="tutorial-configure-infor-cloudsuite-for-automatic-user-provisioning"></a>Kurz: Konfigurace informačního CloudSuiteu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je předvést kroky, které je třeba provést v nástroji CloudSuite a Azure Active Directory (Azure AD) ke konfiguraci služby Azure AD pro Automatické zřizování a zrušení zřizování uživatelů a skupin pro informačního CloudSuite.

> [!NOTE]
> Tento kurz popisuje konektor založený na službě zřizování uživatelů Azure AD. Důležité podrobnosti o tom, co tato služba dělá a jak funguje, a odpovědi na nejčastější dotazy najdete v tématu [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS ve službě Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Tento konektor je aktuálně ve Public Preview. Další informace o obecných Microsoft Azure podmínek použití pro funkce ve verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)náhledy.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že už máte následující požadavky:

* Tenant Azure AD
* [Tenant CloudSuite pro informačního tenanta](https://www.infor.com/products/infor-os)
* Uživatelský účet v info CloudSuite s oprávněními správce.

## <a name="assigning-users-to-infor-cloudsuite"></a>Přiřazování uživatelů k informacím CloudSuite

Azure Active Directory používá koncept nazvaný *přiřazení* k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelů se synchronizují jenom uživatelé a skupiny, které jsou přiřazené k aplikaci v Azure AD.

Před konfigurací a povolením automatického zřizování uživatelů byste se měli rozhodnout, kteří uživatelé a skupiny ve službě Azure AD potřebují přístup k informacím CloudSuite. Jakmile se rozhodnete, můžete tyto uživatele a skupiny přiřadit do informačního CloudSuiteu podle pokynů uvedených tady:
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-infor-cloudsuite"></a>Důležité tipy pro přiřazování uživatelů k informacím CloudSuite

* Doporučuje se, aby k testování konfigurace automatického zřizování uživatelů byl přiřazený jeden uživatel Azure AD CloudSuite k informacím. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k informačnímu CloudSuite musíte v dialogovém okně přiřazení vybrat jakoukoli platnou roli specifickou pro aplikaci (Pokud je dostupná). Uživatelé s **výchozí rolí přístupu** se z zřizování vylučují.

## <a name="set-up-infor-cloudsuite-for-provisioning"></a>Nastavení CloudSuite pro zřizování

1. Přihlaste se ke [konzole Správce informací CloudSuite](https://www.infor.com/customer-center). Klikněte na ikonu uživatele a potom přejděte ke **správě uživatelů**.

    ![Konzola správce informací CloudSuite](media/infor-cloudsuite-provisioning-tutorial/admin.png)

2.  V levém horním rohu obrazovky klikněte na ikonu nabídky. Klikněte na **Spravovat**.

    ![Informace CloudSuite přidání SCIM](media/infor-cloudsuite-provisioning-tutorial/manage.png)

3.  Přejděte na **účty SCIM**.

    ![Účet informačního SCIM CloudSuite](media/infor-cloudsuite-provisioning-tutorial/scim.png)

4.  Kliknutím na ikonu Plus přidejte uživatele s právy pro správu. Zadejte **heslo SCIM** a do pole **Potvrdit heslo** zadejte stejné heslo. Klikněte na ikonu složky a uložte heslo. Zobrazí se **identifikátor uživatele** generovaný pro uživatele s právy pro správu.

    ![Uživatel s oprávněními správce CloudSuite](media/infor-cloudsuite-provisioning-tutorial/newuser.png)
    
    ![Heslo pro informační CloudSuite](media/infor-cloudsuite-provisioning-tutorial/password.png)

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/identifier.png" alt-text="Snímek obrazovky s konzolou správce CloudSuite, která zobrazuje zvýrazněný řádek tabulky Tento řádek obsahuje identifikátor uživatele, hesla a časové razítko." border="false":::

5. Pokud chcete vygenerovat nosný token, zkopírujte **identifikátor uživatele** a **heslo SCIM**. Vložte je do poznámkového bloku + + oddělené dvojtečkou. Zakódovat řetězcovou hodnotu tak, že přejdete na **moduly plug-in > nástroje MIME > Basic64 kódování**. 

    :::image type="content" source="media/infor-cloudsuite-provisioning-tutorial/token.png" alt-text="Snímek obrazovky s dokumentem Notepad + + V nabídce moduly plug-in se zvýrazní nástroje MIME. V nabídce nástroje MIME se zvýrazní kódování Base64." border="false":::

3.  Zkopírujte token nosiče. Tato hodnota se zadá do pole token tajného kódu na kartě zřizování aplikace CloudSuite, která se nachází v Azure Portal.

## <a name="add-infor-cloudsuite-from-the-gallery"></a>Přidání informačního CloudSuiteu z Galerie

Před konfigurací informačního CloudSuiteu pro Automatické zřizování uživatelů pomocí Azure AD je nutné přidat informace CloudSuite z Galerie aplikací Azure AD do svého seznamu spravovaných aplikací SaaS.

**Chcete-li přidat informační CloudSuite z Galerie aplikací Azure AD, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** v levém navigačním panelu vyberte možnost **Azure Active Directory**.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, vyberte tlačítko **Nová aplikace** v horní části podokna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **info CloudSuite**, vyberte **informační CloudSuite** na panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Informace CloudSuite v seznamu výsledků](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-infor-cloudsuite"></a>Konfigurace automatického zřizování uživatelů pro Správce informací CloudSuite 

V této části se seznámíte s postupem konfigurace služby zřizování Azure AD k vytváření, aktualizaci a zakázání uživatelů nebo skupin v informačním CloudSuite na základě přiřazení uživatelů nebo skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro CloudSuite, a to podle pokynů uvedených v [kurzu pro jednotné přihlašování v informačním CloudSuite](./infor-cloud-suite-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování uživatelů, i když se tyto dvě funkce navzájem doplňují.

> [!NOTE]
> Další informace o SCIM koncového bodu [CloudSuite najdete tady.](https://docs.infor.com/mingle/12.0.x/en-us/minceolh/jho1449382121585.html#)

### <a name="to-configure-automatic-user-provisioning-for-infor-cloudsuite-in-azure-ad"></a>Postup při konfiguraci automatického zřizování uživatelů pro Správce informací CloudSuite ve službě Azure AD:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **Podnikové aplikace** a pak vyberte **Všechny aplikace**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **informace CloudSuite**.

    ![Odkaz na informační CloudSuite v seznamu aplikací](common/all-applications.png)

3. Vyberte kartu **Zřizování**.

    ![Snímek obrazovky s možnostmi správy pomocí možnosti zřizování s názvem.](common/provisioning.png)

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky s rozevíracím seznamem režimu zřizování s možností automatického volání](common/provisioning-automatic.png)

5. V části **přihlašovací údaje správce** zadejte `https://mingle-t20b-scim.mingle.awsdev.infor.com/INFORSTS_TST/v2/scim` **adresu URL tenanta**. Zadejte hodnotu tokenu nosiče, která byla dříve načtena v **tajném tokenu**. Klikněte na **Test připojení** a ujistěte se, že se Azure AD může připojit k informacím CloudSuite. Pokud se připojení nepovede, ujistěte se, že váš CloudSuite účet má oprávnění správce, a zkuste to znovu.

    ![Adresa URL tenanta + token](common/provisioning-testconnection-tenanturltoken.png)

6. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko – **pošle e-mailové oznámení, když dojde k chybě**.

    ![Oznamovací e-mail](common/provisioning-notification-email.png)

7. Klikněte na **Uložit**.

8. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory uživatele s informacemi CloudSuite**.

    ![Mapování uživatelů CloudSuite k informacím](media/infor-cloudsuite-provisioning-tutorial/usermappings.png)

9. Zkontrolujte atributy uživatele synchronizované z Azure AD s informacemi o CloudSuite v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v nástroji info CloudSuite pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![Atributy uživatele CloudSuite informace o uživatelích](media/infor-cloudsuite-provisioning-tutorial/userattributes.png)

10. V části **mapování** vyberte možnost **synchronizovat Azure Active Directory skupiny s informacemi CloudSuite**.

    ![Mapování skupin CloudSuite informací](media/infor-cloudsuite-provisioning-tutorial/groupmappings.png)

11. Zkontrolujte atributy skupiny synchronizované z Azure AD s informacemi o CloudSuite v oddílu **mapování atributů** . Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování skupin v nástroji info CloudSuite pro operace aktualizace. Kliknutím na tlačítko **Uložit** potvrďte změny.

    ![CloudSuite – atributy skupiny informací](media/infor-cloudsuite-provisioning-tutorial/groupattributes.png)

12. Pokud chcete nakonfigurovat filtry rozsahu, postupujte podle pokynů uvedených v [kurzu k filtrům rozsahu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Pokud chcete povolit službu Azure AD Provisioning pro informace CloudSuite, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

    ![Zapnutý přepínač Stav zřizování](common/provisioning-toggle-on.png)

14. V části **Nastavení** definujte uživatele nebo skupiny, které chcete zřídit pro informace CloudSuite, a to tak, že vyberete požadované hodnoty v **rozsahu** .

    ![Rozsah zřizování](common/provisioning-scope.png)

15. Jakmile budete připraveni na zřízení, klikněte na **Uložit**.

    ![Uložení konfigurace zřizování](common/provisioning-configuration-save.png)

Tato operace spustí počáteční synchronizaci všech uživatelů nebo skupin definovaných v **oboru** v části **Nastavení** . Počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba zřizování Azure AD spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na sestavu aktivit zřizování, která popisuje všechny akce prováděné službou zřizování Azure AD na CloudSuiteu.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další kroky

* [Zjistěte, jak procházet protokoly a získat sestavy aktivit zřizování](../app-provisioning/check-status-user-account-provisioning.md).
---
title: 'Kurz: Konfigurace Samanage pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: v-wingf-msft
ms.openlocfilehash: e5a69fa2ee9a8c4baaeb6586627c7a9a3c9ba4a8
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44347949"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Kurz: Konfigurace Samanage pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v Samanage a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se Samanage.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že již máte následující:

*   Klient služby Azure AD
*   A [Samanage tenanta](https://www.samanage.com/pricing/) s profesionální balíčku
*   Uživatelský účet v Samanage s oprávněními správce

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [Samanage Rest API](https://www.samanage.com/api/), která je k dispozici pro vývojáře Samanage pro účty s profesionální balíčku.

## <a name="adding-samanage-from-the-gallery"></a>Přidání Samanage z Galerie
Před konfigurací Samanage pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat Samanage z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Samanage z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]

3. Chcete-li přidat Samanage, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **Samanage**.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/AppSearch.png)

5. Na panelu výsledků vyberte **Samanage**a potom klikněte na tlačítko **přidat** tlačítko pro přidání Samanage do seznamu aplikací SaaS.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/AppSearchResults.png)

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-samanage"></a>Přiřazování uživatelů k Samanage

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k Samanage. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny Samanage podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Důležité tipy pro přiřazování uživatelů k Samanage

*   Dále je doporučeno jednoho uživatele Azure AD, je přiřazená Samanage otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Samanage, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Konfigurace automatické zřizování uživatelů pro Samanage

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v Samanage podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro Samanage, postupujte podle pokynů uvedených v [Samanage jednotné přihlašování – kurz](samanage-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro Samanage ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte Samanage ze seznamu aplikací SaaS.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/AppInstanceSearch.png)

3. Vyberte **zřizování** kartu.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. V části **přihlašovacích údajů správce** části, zadejte **uživatelské jméno správce** a **heslo správce** Samanage účtu. Mezi tyto hodnoty patří:

    *   V **uživatelské jméno správce** pole, uživatelské jméno účtu správce ve svém tenantovi Samanage naplnit. Příklad: admin@contoso.com.

    *   V **heslo správce** pole, naplnění heslo účtu správce odpovídající uživatelské jméno správce.

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k Samanage. Pokud se nepovede, ujistěte se, že váš účet Samanage má oprávnění správce a zkuste to znovu.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/TestConnection.png)

7. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtněte políčko **odeslání e-mailové oznámení, když dojde k selhání**.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům Samanage**.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do Samanage v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Samanage pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. V části Povolit mapování skupin **mapování** vyberte **synchronizaci skupinám Azure Active Directory k Samanage**.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Nastavte **povoleno** k **Ano** synchronizace skupiny. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do Samanage v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Samanage pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Služba pro Samanage zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Definovat uživatele a/nebo skupiny, které chcete k poskytování Samanage výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![Samanage zřizování](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na Samanage zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png

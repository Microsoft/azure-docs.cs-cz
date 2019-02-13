---
title: 'Kurz: Konfigurace BlueJeans pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do BlueJeans.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c2006b025c4bcf54ee2ab131e0d6a105f9f4e9f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178171"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Kurz: Konfigurace BlueJeans pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v BlueJeans a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se BlueJeans.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že již máte následující:

*   Klient služby Azure AD
*   BlueJeans tenantovi se [moji firmu](https://www.BlueJeans.com/pricing) plán nebo lépe povoleno
*   Uživatelský účet v BlueJeans s oprávněními správce

> [!NOTE]
> Zřizování integrace Azure AD spoléhá na [BlueJeans API](https://BlueJeans.github.io/developer), což je k dispozici BlueJeans týmy využívající plán Standard nebo vyšší.

## <a name="adding-bluejeans-from-the-gallery"></a>Přidání BlueJeans z Galerie
Před konfigurací BlueJeans pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat BlueJeans z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat BlueJeans z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]
    
3. Chcete-li přidat BlueJeans, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **BlueJeans**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansAppSearch.png)

5. Na panelu výsledků vyberte **BlueJeans**a potom klikněte na tlačítko **přidat** tlačítko pro přidání BlueJeans do seznamu aplikací SaaS.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansAppSearchResults.png)

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansAppCreate.png)
    
## <a name="assigning-users-to-bluejeans"></a>Přiřazování uživatelů k BlueJeans

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k BlueJeans. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny BlueJeans podle zde uvedených pokynů:

*   [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Důležité tipy pro přiřazování uživatelů k BlueJeans

*   Dále je doporučeno jednoho uživatele Azure AD, je přiřazená BlueJeans otestovat automatické konfigurace zřizování uživatelů. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k BlueJeans, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Konfigurace automatické zřizování uživatelů pro BlueJeans

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v BlueJeans podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro BlueJeans, postupujte podle pokynů uvedených v [BlueJeans jednotné přihlašování – kurz](bluejeans-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro BlueJeans ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte BlueJeans ze seznamu aplikací SaaS.
 
    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/Bluejeans2.png)

3. Vyberte **zřizování** kartu.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Nastavte **režim zřizování** k **automatické**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. V části **přihlašovacích údajů správce** části, zadejte **uživatelské jméno správce**, a **heslo správce** BlueJeans účtu. Mezi tyto hodnoty patří:

    *   V **uživatelské jméno správce** pole, uživatelské jméno účtu správce ve svém tenantovi BlueJeans naplnit. Příklad: admin@contoso.com.

    *   V **heslo správce** pole, vyplnit odpovídající uživatelské jméno správce heslo.

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k BlueJeans. Pokud se nepovede, ujistěte se, že váš účet BlueJeans má oprávnění správce a zkuste to znovu.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům BlueJeans**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do BlueJeans v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v BlueJeans pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Konfigurace filtrů oborů, najdete v následující pokyny uvedené v [Scoping filtr kurzu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Služba pro BlueJeans zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definovat uživatele a/nebo skupiny, které chcete k poskytování BlueJeans výběrem požadované hodnoty do **oboru** v **nastavení** oddílu.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

    ![BlueJeans Provisioning](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na BlueJeans zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Omezení konektoru

* Bluejeans neumožňuje uživatelských jmen, která překročit 30 znaků.

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png

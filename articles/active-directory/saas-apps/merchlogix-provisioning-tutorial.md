---
title: 'Kurz: Konfigurace MerchLogix pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů do MerchLogix.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 9df4c7c5-9a58-478e-93b7-2f77aae12807
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8fecc5232b26c98c4027174454cf29b81b0ee41
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263587"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Kurz: Konfigurace MerchLogix pro automatické zřizování uživatelů

Cílem tohoto kurzu je předvést postup provést v MerchLogix a Azure Active Directory (Azure AD) ke konfiguraci Azure AD automaticky zřizovat a rušit zřízení uživatele a/nebo skupiny, které se MerchLogix.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že už máte splněné následující požadavky:

* Klient služby Azure AD
* MerchLogix tenanta
* Technický kontakt v MerchLogix, který můžete zadat adresu URL koncového bodu SCIM a token tajného kódu, které jsou požadované pro zřizování uživatelů

## <a name="adding-merchlogix-from-the-gallery"></a>Přidání MerchLogix z Galerie

Před konfigurací MerchLogix pro automatické zřizování uživatelů pomocí Azure AD, budete muset přidat MerchLogix z Galerie aplikací Azure AD na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat MerchLogix z Galerie aplikací Azure AD, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![Tlačítko Azure Active Directory][1]

2. Přejděte do **podnikové aplikace** > **všechny aplikace**.

    ![Podnikové aplikace oddílu][2]

3. Chcete-li přidat MerchLogix, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace][3]

4. Do vyhledávacího pole zadejte **MerchLogix**.

5. Na panelu výsledků vyberte **MerchLogix**a potom klikněte na tlačítko **přidat** tlačítko pro přidání MerchLogix do seznamu aplikací SaaS.

    ![MerchLogix zřizování][4]

## <a name="assigning-users-to-merchlogix"></a>Přiřazování uživatelů k MerchLogix

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V souvislosti s automatické zřizování uživatelů se synchronizují pouze uživatele a/nebo skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte automatické zřizování uživatelů, byste měli rozhodnout, které uživatele a/nebo skupiny ve službě Azure AD potřebují přístup k MerchLogix. Jakmile se rozhodli, můžete přiřadit tyto uživatele a/nebo skupiny MerchLogix podle zde uvedených pokynů:

* [Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Důležité tipy pro přiřazování uživatelů k MerchLogix

* Dále je doporučeno jednoho uživatele Azure AD je přiřazená MerchLogix k testování vašich počáteční automatické zřizování uživatelů konfigurace. Další uživatele a/nebo skupiny může být přiřazena vyšší Jakmile testy jsou úspěšné.

* Při přiřazení uživatele k MerchLogix, musíte vybrat libovolnou platnou roli specifické pro aplikaci (Pokud je k dispozici) v dialogovém okně přiřazení. Uživatelé s **výchozího přístupu k** role jsou vyloučené z zřizování.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Konfigurace automatické zřizování uživatelů pro MerchLogix 

Tato část vás provede kroky pro konfiguraci Azure AD služby zřizování a vytvářet, aktualizovat a zakázat uživatele a/nebo skupiny v MerchLogix podle přiřazení uživatele a/nebo skupiny ve službě Azure AD.

> [!TIP]
> Můžete také povolit založené na SAML jednotného přihlašování pro MerchLogix, postupujte podle pokynů uvedených v [MerchLogix jednotné přihlašování – kurz](merchlogix-tutorial.md). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování uživatelů, i když tyto dvě funkce návrzích mezi sebou.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Konfigurace automatické zřizování uživatelů pro MerchLogix ve službě Azure AD:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace**.

2. Vyberte MerchLogix ze seznamu aplikací SaaS.

3. Vyberte **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**.

    ![MerchLogix zřizování](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. V části **přihlašovacích údajů správce** části:

    * V **adresy URL Tenanta** zadejte adresu URL koncového bodu SCIM poskytované vaší MerchLogix technický kontakt.

    * V **tajný klíč tokenu** zadejte token tajného kódu poskytované vaší MerchLogix technický kontakt.

6. Po vyplnění polí zobrazených v kroku 5, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k MerchLogix. Pokud se nepovede, ujistěte se, že váš účet MerchLogix má oprávnění správce a zkuste to znovu.

7. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měla přijímat oznámení zřizování chyba a zaškrtnutím políčka - **odeslání e-mailové oznámení, když dojde k selhání**.

8. Klikněte na **Uložit**.

9. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelům MerchLogix**.

10. Zkontrolujte atributy uživatele, které se synchronizují ze služby Azure AD do MerchLogix v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v MerchLogix pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

11. V části **mapování** vyberte **synchronizaci skupinám Azure Active Directory k MerchLogix**.

12. Zkontrolujte skupiny atributů, které se synchronizují ze služby Azure AD do MerchLogix v **mapování atributů** oddílu. Atributy vybrané jako **odpovídající** vlastnosti se používají k vyhodnocení skupiny v MerchLogix pro operace update. Vyberte **Uložit** tlačítko potvrďte všechny změny.

13. Služba pro MerchLogix zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu.

14. Až budete připravení ke zřízení, klikněte na tlačítko **Uložit**.

Tato operace spustí počáteční synchronizaci všech uživatelů a/nebo skupiny definované v **oboru** v **nastavení** oddílu. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování sestava aktivity, která popisuje všechny akce, které provádí služba na MerchLogix zřizování Azure AD.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup

* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png

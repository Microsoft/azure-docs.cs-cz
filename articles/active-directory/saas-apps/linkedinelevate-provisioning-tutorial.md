---
title: 'Kurz: zřizování uživatelů pro LinkedIn úrovně LinkedIn – Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro zvýšení oprávnění LinkedInu.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: 5e972475530ad36a188f73990bb9eca35748c36c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358945"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Kurz: Konfigurace zvýšení oprávnění LinkedInu pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat postup, který je potřeba provést v povýšení a Azure AD a automaticky zřídit a zrušit zřízení uživatelských účtů z Azure AD až po zvýšení úrovně LinkedInu.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenanta Azure Active Directory.
* Tenant zvýšení oprávnění LinkedInu
* Přístup účtu správce ve LinkedInu se zvyšuje s přístupem k centru účtů LinkedIn.

> [!NOTE]
> Azure Active Directory se integruje se zvýšením úrovně LinkedInu pomocí protokolu [SCIM](http://www.simplecloud.info/) .

## <a name="assigning-users-to-linkedin-elevate"></a>Přiřazení uživatelů ke zvýšení oprávnění LinkedInu

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů budou synchronizováni pouze uživatelé a skupiny, které byly přiřazeny do aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte službu zřizování, budete se muset rozhodnout, co uživatelé a skupiny ve službě Azure AD reprezentují uživatelé, kteří potřebují přístup ke zvýšení oprávnění LinkedInu. Po rozhodnutí můžete těmto uživatelům přiřadit zvýšení oprávnění LinkedInu podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Důležité tipy pro přiřazení uživatelů ke zvýšení oprávnění LinkedInu

* Doporučujeme, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD ke zvýšení oprávnění LinkedInu. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k zvýšení oprávnění LinkedInu, musíte v dialogovém okně přiřazení vybrat roli **uživatele** . Role výchozí přístup nefunguje pro zřizování.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurace zřizování uživatelů na zvýšení úrovně LinkedInu

V této části se seznámíte s připojením k rozhraní API pro zřizování SCIM uživatelského účtu v Azure AD a konfigurací zřizovací služby k vytváření, aktualizaci a zakázání přiřazených uživatelských účtů ve službě Azure AD v závislosti na přiřazení uživatelů a skupin.

**Tip:** Můžete se také rozhodnout povolit pro zvýšení oprávnění LinkedInu jeden Sign-On založený na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů ve službě Azure AD zvýšení úrovně oprávnění LinkedIn:

Prvním krokem je načtení přístupového tokenu LinkedIn. Pokud jste podnikovým správcem, můžete přístupový token zřídit sami. V centru účtů, přejít na **nastavení &gt; globální nastavení** a otevřete panel **Nastavení SCIM** .

> [!NOTE]
> Pokud k centru účtů přistupujete přímo přes odkaz, můžete k němu přistupovat pomocí následujících kroků.

1. Přihlaste se do centra účtů.

2. Vyberte **&gt; Nastavení správce správy** .

3. Klikněte na **rozšířené integrace** na levém bočním panelu. Jste přesměrováni na centrum účtů.

4. Klikněte na **+ Přidat novou konfiguraci SCIM** a postupujte podle pokynů vyplněním jednotlivých polí.

    > [!NOTE]
    > Pokud není povoleno automatické přiřazování licencí, znamená to, že budou synchronizována pouze uživatelská data.

    ![Snímek obrazovky ukazuje globální nastavení centra účtů LinkedIn.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > Pokud je povoleno přiřazení autolicense, je nutné poznamenat instanci aplikace a typ licence. Licence se přiřazují k prvnímu, prvnímu a zajišťují, aby se všechny licence vycházely.

    ![Snímek obrazovky se zobrazí stránka Instalace S C I M.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Klikněte na **vygenerovat token**. V poli **přístupový token** byste měli vidět zobrazení přístupového tokenu.

6. Před zavřením stránky uložte váš přístupový token do schránky nebo počítače.

7. Potom se přihlaste k [Azure Portal](https://portal.azure.com)a přejděte do části **Azure Active Directory > podnikové aplikace > všechny aplikace**  .

8. Pokud jste již nakonfigurovali zvýšení oprávnění LinkedInu pro jednotné přihlašování, vyhledejte vaši instanci LinkedInu pomocí vyhledávacího pole. V opačném případě vyberte možnost **Přidat** a vyhledat **zvýšení oprávnění LinkedInu** v galerii aplikací. Ve výsledcích hledání vyberte možnost LinkedIn úrovně LinkedIn a přidejte ji do seznamu aplikací.

9. Vyberte svou instanci zvýšení oprávnění LinkedInu a pak vyberte kartu **zřizování** .

10. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky se stránkou zřizování pro stránku LinkedIn úrovně.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Do následujících polí zadejte v části **přihlašovací údaje správce** :

    * Do pole **Adresa URL tenanta** zadejte `https://api.linkedin.com` .

    * V poli **token tajného klíče** zadejte přístupový token, který jste vygenerovali v kroku 1, a klikněte na **Test připojení** .

    * Na straně upperright na portálu by se mělo zobrazit oznámení o úspěchu.

12. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko níže.

13. Klikněte na **Uložit**.

14. V části **mapování atributů** zkontrolujte atributy uživatelů a skupin, které se budou synchronizovat z Azure AD až po zvýšení úrovně LinkedInu. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity ke spárování uživatelských účtů a skupin ve službě LinkedIn se zvýšením oprávnění pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

    ![Snímek obrazovky ukazuje mapování, včetně mapování atributů.](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Pokud chcete povolit službu zřizování Azure AD pro zvýšení oprávnění LinkedInu, změňte **stav zřizování** na **zapnuto** v oddílu **Nastavení** .

16. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených ke zvýšení oprávnění LinkedInu v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než další synchronizace, ke kterým dojde přibližně každých 40 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci pro zvýšení oprávnění LinkedInu.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

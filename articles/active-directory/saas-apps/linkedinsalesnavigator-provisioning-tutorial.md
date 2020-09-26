---
title: 'Kurz: zřizování uživatelů – LinkedIn Sales Navigator, Azure AD'
description: Naučte se konfigurovat Azure Active Directory pro automatické zřízení a zrušení zřízení uživatelských účtů pro LinkedIn Sales Navigator.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: e908c8d19a4640995c536580b6584558212ada3e
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361565"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Kurz: Konfigurace programu LinkedIn Sales Navigator pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky, které je třeba provést v nástroji LinkedIn Sales Navigator a Azure AD, a automaticky zřizovat a zrušit zřizování uživatelských účtů z Azure AD až LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenanta Azure Active Directory.
* Tenant pro LinkedIn Sales Navigator 
* Účet správce v aplikaci LinkedIn Sales Navigator s přístupem k centru účtů LinkedIn

> [!NOTE]
> Azure Active Directory se integruje s prodejním navigátorem LinkedInu pomocí protokolu [SCIM](http://www.simplecloud.info/) .

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Přiřazování uživatelů k prodejnímu navigátoru LinkedIn

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů budou synchronizováni pouze uživatelé a skupiny, které byly přiřazeny do aplikace ve službě Azure AD.

Než nakonfigurujete a povolíte službu zřizování, budete se muset rozhodnout, co můžou uživatelé a skupiny v Azure AD zastupovat s uživateli, kteří potřebují přístup k aplikaci LinkedIn Sales Navigator. Po rozhodnutí můžete tyto uživatele přiřadit k prodejnímu navigátoru LinkedIn podle pokynů uvedených tady:

[Přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Důležité tipy pro přiřazování uživatelů k LinkedIn Sales Navigator

* Doporučuje se, aby se k otestování konfigurace zřizování přiřadil jeden uživatel Azure AD, který je přiřazený k prodejnímu navigátoru LinkedIn. Další uživatele a skupiny můžete přiřadit později.

* Když přiřadíte uživatele k prodejnímu navigátoru LinkedIn, musíte v dialogovém okně přiřazení vybrat roli **uživatele** . Role výchozí přístup nefunguje pro zřizování.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurace zřizování uživatelů na LinkedIn Sales Navigator

V této části se seznámíte s tím, že propojíte rozhraní API pro zřizování SCIM uživatelských účtů pro Azure AD a nakonfigurujete službu zřizování tak, aby vytvořila, aktualizovala a zakázala přiřazené uživatelské účty v oblasti LinkedIn Sales Navigator na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete se také rozhodnout povolit jednotné přihlašování založené na SAML pro LinkedIn Sales Navigator, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když tyto dvě funkce doplňují sebe.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Konfigurace automatického zřizování uživatelských účtů na LinkedIn Sales Navigator v Azure AD:

Prvním krokem je načtení přístupového tokenu LinkedIn. Pokud jste podnikovým správcem, můžete přístupový token zřídit sami. V centru účtů, přejít na **nastavení &gt; globální nastavení** a otevřete panel **Nastavení SCIM** .

> [!NOTE]
> Pokud k centru účtů přistupujete přímo přes odkaz, můžete k němu přistupovat pomocí následujících kroků.

1. Přihlaste se do centra účtů.

2. Vyberte ** &gt; Nastavení správce správy** .

3. Klikněte na **rozšířené integrace** na levém bočním panelu. Jste přesměrováni na centrum účtů.

4. Klikněte na **+ Přidat novou konfiguraci SCIM** a postupujte podle pokynů vyplněním jednotlivých polí.

    > [!NOTE]
    > Pokud není povoleno automatické přiřazování licencí, znamená to, že budou synchronizována pouze uživatelská data.

    ![Snímek obrazovky ukazuje globální nastavení centra účtů LinkedIn.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Pokud je povoleno přiřazení autolicense, je nutné poznamenat instanci aplikace a typ licence. Licence se přiřazují k prvnímu, prvnímu a zajišťují, aby se všechny licence vycházely.

    ![Snímek obrazovky se zobrazí stránka Instalace S C I M.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klikněte na **vygenerovat token**. V poli **přístupový token** byste měli vidět zobrazení přístupového tokenu.

6. Před zavřením stránky uložte váš přístupový token do schránky nebo počítače.

7. Potom se přihlaste k [Azure Portal](https://portal.azure.com)a přejděte do části **Azure Active Directory > podnikové aplikace > všechny aplikace**  .

8. Pokud jste již nakonfigurovali obchodní navigátor LinkedIn pro jednotné přihlašování, vyhledejte vaši instanci aplikace LinkedIn Sales Navigator pomocí vyhledávacího pole. V opačném případě vyberte možnost **Přidat** a hledat pro **LinkedIn Sales Navigator** v galerii aplikací. Ve výsledcích hledání vyberte LinkedIn Sales Navigator a přidejte ho do seznamu aplikací.

9. Vyberte instanci aplikace LinkedIn Sales Navigator a pak vyberte kartu **zřizování** .

10. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky se stránkou zřizování pro stránku LinkedIn úrovně.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Do následujících polí zadejte v části **přihlašovací údaje správce** :

    * Do pole **Adresa URL tenanta** zadejte https://developer.linkedin.com .

    * V poli **token tajného klíče** zadejte přístupový token, který jste vygenerovali v kroku 1, a klikněte na **Test připojení** .

    * Na straně upperright na portálu by se mělo zobrazit oznámení o úspěchu.

12. Zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování v poli **e-mail s oznámením** , a zaškrtněte políčko níže.

13. Klikněte na **Uložit**.

14. V části **mapování atributů** zkontrolujte atributy uživatelů a skupin, které se budou synchronizovat z Azure AD do služby LinkedIn Sales Navigator. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti budou použity ke spárování uživatelských účtů a skupin v nástroji LinkedIn Sales Navigator pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

    ![Snímek obrazovky ukazuje mapování, včetně mapování atributů.](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Pokud chcete povolit službu zřizování Azure AD pro LinkedIn Sales Navigator, změňte **stav zřizování** na **zapnuto** v části **Nastavení** .

16. Klikněte na **Uložit**.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených k prodejnímu navigátoru LinkedIn v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než další synchronizace, ke kterým dojde přibližně každých 40 minut, pokud je služba spuštěná. V části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci LinkedIn Sales Navigator.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

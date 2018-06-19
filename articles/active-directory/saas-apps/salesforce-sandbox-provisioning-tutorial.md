---
title: 'Kurz: Konfigurace služby Salesforce izolovaného prostoru pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a izolovaného prostoru služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 1c0b39a73de21c6296f09dbedefc3c84d39dfeea
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899264"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Salesforce izolovaného prostoru pro zřizování automatické uživatelů

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v izolovaného prostoru Salesforce a Azure AD a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do izolovaného prostoru služby Salesforce.

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klienta služby Azure Active directory.
*   Platný klient pro služby Salesforce izolovaného prostoru pro pracovní nebo Salesforce izolovaného prostoru pro vzdělávací organizace. Bezplatný zkušební účet můžete použít buď služby.
*   Uživatelský účet v izolovaném prostoru Salesforce s oprávněními správce týmu.

## <a name="assigning-users-to-salesforce-sandbox"></a>Přiřazování uživatelů do izolovaného prostoru Salesforce

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování služby, musíte rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k aplikaci Salesforce izolovaného prostoru. Po provedení tohoto rozhodnutí, můžete přiřadit tyto uživatele do izolovaného prostoru Salesforce aplikace podle pokynů v [přiřadit uživatele nebo skupinu enterprise aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Důležité tipy pro přiřazování uživatelů do izolovaného prostoru Salesforce

* Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke izolovaného prostoru Salesforce a otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k izolovanému prostoru služby Salesforce, musíte vybrat platné uživatelské role. Roli "Výchozí přístup" nefunguje pro zřizování.

> [!NOTE]
> Tato aplikace importuje vlastní role ze služby Salesforce izolovaného prostoru v rámci procesu zřizování, který může zákazník vyberte při přiřazování uživatelů.

## <a name="enable-automated-user-provisioning"></a>Povolit zřizování automatizované uživatelů

Tato část vás provede připojení k Salesforce izolovaného uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v izolovaném prostoru Salesforce podle přiřazení uživatelů a skupin ve službě Azure AD.

>[!Tip]
>Můžete také pro izolovaný prostor Salesforce povoleno na základě SAML jednotné přihlašování, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického uživatele zřizování účtu

Cílem této části se popisují postup povolení zřizování uživatelů služby Active Directory uživatelských účtů do izolovaného prostoru služby Salesforce.

1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste již nakonfigurovali Salesforce izolovaného prostoru pro jednotné přihlašování, vyhledávání pro instanci služby Salesforce izolovaného prostoru pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **izolovaného prostoru Salesforce** v galerii aplikací. Vyberte Salesforce izolovaného prostoru ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci služby Salesforce izolovaného prostoru a pak vyberte **zřizování** kartě.

4. Nastavte **režimu zřizování** k **automatické**.

    ![zřizování](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

5. V části **přihlašovací údaje správce** části, zadejte následující nastavení konfigurace:
   
    a. V **uživatelské jméno správce** textovému poli, zadejte název, který má účet služby Salesforce izolovaném prostoru **správce systému** profil v Salesforce.com přiřazen.
   
    b. V **heslo správce** textovému poli, zadejte heslo pro tento účet.

6. Se získat token zabezpečení izolovaného prostoru služby Salesforce, otevřete novou kartu a přihlášení do stejného účtu správce izolovaného prostoru služby Salesforce. V pravém horním rohu stránky klikněte na své jméno a potom klikněte **nastavení**.

     ![Povolit automatické uživatele zajišťování](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "povolit zřizování automatické uživatelů")

7. V levém navigačním podokně klikněte na tlačítko **Moje osobní údaje** rozbalte související část, a potom klikněte na **resetovat Moje zabezpečení tokenu**.
  
    ![Povolit automatické uživatele zajišťování](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "povolit zřizování automatické uživatelů")

8. Na **resetovat tokenu zabezpečení** klikněte na tlačítko **resetovat tokenu zabezpečení** tlačítko.

    ![Povolit automatické uživatele zajišťování](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "povolit zřizování automatické uživatelů")

9. Zkontrolujte e-mailovou schránku spojené s tímto účtem správce. Vyhledejte e-mailu ze služby Salesforce Sandbox.com, který obsahuje nový token zabezpečení.

10. Zkopírujte token, přejděte do okna vaší služby Azure AD a vložte ji do **tajný klíč tokenu** pole.

11. Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k aplikaci Salesforce izolovaného prostoru.

12. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, kdo by měly dostávat oznámení zřizování chyby a zaškrtněte políčko.

13. Klikněte na tlačítko **uložit.**  
    
14.  V části mapování vyberte **synchronizaci Azure Active Directory Users do izolovaného prostoru služby Salesforce.**

15. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizované z Azure AD izolovaného prostoru služby Salesforce. Atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Salesforce izolovaného prostoru pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

16. Povolit zřizování služby pro izolovaný prostor Salesforce Azure AD, změňte **Stav zřizování** k **na** v části Nastavení

17. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Salesforce izolovaného prostoru v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud se službou provést. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity, které popisují všechny akce, které provádí službu zřizování na aplikaci Salesforce izolovaného prostoru.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)

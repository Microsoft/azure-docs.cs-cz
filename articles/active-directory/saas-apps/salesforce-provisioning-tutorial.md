---
title: 'Kurz: Konfigurace služby Salesforce pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: be6e5fb83ea88ce30b64f5e1eb53422f0404410f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229617"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Salesforce pro zřizování automatické uživatelů

Cílem tohoto kurzu je zobrazit kroky potřebné k provedení v Salesforce a Azure AD pro automatické zřizování a deaktivace zřízení uživatelských účtů ze služby Azure AD do služby Salesforce.

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klienta služby Azure Active directory
*   Klientovi Salesforce.com

>[!IMPORTANT] 
>Pokud používáte zkušební účet Salesforce.com, pak nebude možné nakonfigurovat zřizování automatizované uživatelů. Zkušebními účty nemají potřebná přístupová oprávnění rozhraní API povoleno, dokud se zakoupili. Obejít toto omezení můžete získat pomocí bezplatného [vývojářský účet](https://developer.salesforce.com/signup) k dokončení tohoto kurzu.

Pokud používáte prostředí izolovaného prostoru služby Salesforce, přejděte na téma [izolovaného prostoru Salesforce integrace kurzu](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Přiřazování uživatelů do služby Salesforce

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu se synchronizují pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Před konfigurací a povolení zřizování služby, musíte rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k aplikaci Salesforce. Po provedení tohoto rozhodnutí, můžete přiřadit tyto uživatele k aplikaci Salesforce podle pokynů v [přiřadit uživatele nebo skupinu enterprise aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Důležité tipy pro přiřazování uživatelů do služby Salesforce

*   Dále je doporučeno jednoho uživatele Azure AD se přiřadí ke Salesforce a otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

*  Při přiřazování uživatele do služby Salesforce, musíte vybrat platné uživatelské role. Roli "Výchozí přístup" nefunguje pro zřizování

    > [!NOTE]
    > Tato aplikace importuje vlastní role ze služby Salesforce jako součást procesu zřizování, který může zákazník vyberte při přiřazování uživatelů

## <a name="enable-automated-user-provisioning"></a>Povolit zřizování automatizované uživatelů

Tato část vás provede připojení k Salesforce na uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakažte přiřazené uživatelské účty v Salesforce podle přiřazení uživatelů a skupin ve službě Azure AD.

>[!Tip]
>Můžete také pro Salesforce povoleno na základě SAML jednotné přihlašování, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického uživatele zřizování účtu

Cílem této části se popisují postup povolení zřizování uživatelů služby Active Directory uživatelských účtů do služby Salesforce.

1. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste již nakonfigurovali Salesforce pro jednotné přihlašování, vyhledávání pro instanci služby Salesforce pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **Salesforce** v galerii aplikací. Vyberte Salesforce ve výsledcích hledání a přidejte ji do seznamu aplikací.

3. Vyberte instanci služby Salesforce a pak vyberte **zřizování** kartě.

4. Nastavte **režimu zřizování** k **automatické**.

    ![zřizování](./media/salesforce-provisioning-tutorial/provisioning.png)

5. V části **přihlašovací údaje správce** části, zadejte následující nastavení konfigurace:
   
    a. V **uživatelské jméno správce** textovému poli, zadejte název, který má účtu Salesforce **správce systému** profil v Salesforce.com přiřazen.
   
    b. V **heslo správce** textovému poli, zadejte heslo pro tento účet.

6. Chcete-li získat token zabezpečení služby Salesforce, otevřete novou kartu a přihlaste na stejný účet správce služby Salesforce. V pravém horním rohu stránky klikněte na své jméno a potom klikněte **nastavení**.

     ![Povolit automatické uživatele zajišťování](./media/salesforce-provisioning-tutorial/sf-my-settings.png "povolit zřizování automatické uživatelů")

7. V levém navigačním podokně klikněte na tlačítko **Moje osobní údaje** rozbalte související část, a potom klikněte na **resetovat Moje zabezpečení tokenu**.
  
    ![Povolit automatické uživatele zajišťování](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "povolit zřizování automatické uživatelů")

8. Na **resetovat tokenu zabezpečení** klikněte na tlačítko **resetovat tokenu zabezpečení** tlačítko.

    ![Povolit automatické uživatele zajišťování](./media/salesforce-provisioning-tutorial/sf-reset-token.png "povolit zřizování automatické uživatelů")

9. Zkontrolujte e-mailovou schránku spojené s tímto účtem správce. Vyhledejte e-mailu z Salesforce.com, který obsahuje nový token zabezpečení.

10. Zkopírujte token, přejděte do okna vaší služby Azure AD a vložte ji do **tajný klíč tokenu** pole.

11. **URL klienta** musí být zadán, pokud je instance služby Salesforce na Cloud vlády Salesforce. Jinak je volitelné. Zadejte adresu URL klienta pomocí formátu https://your-instance.my.salesforce.com, nahraďte názvem vaší instance služby Salesforce vaše instance.

12. Na portálu Azure klikněte na tlačítko **Test připojení** zajistit Azure AD může připojit k aplikaci Salesforce.

13. V **e-mailové oznámení** pole, zadejte e-mailovou adresu uživatele nebo skupiny, který by měl zřizování chyba oznámení dostávat a zaškrtněte políčko níže.

14. Klikněte na tlačítko **uložit.**  
    
15.  V části mapování vyberte **synchronizaci Azure Active Directory Users do služby Salesforce.**

16. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizované z Azure AD do služby Salesforce. Všimněte si, že atributy vybrán jako **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Salesforce pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

17. Povolit Azure AD zřizování služby pro služby Salesforce, změňte **Stav zřizování** k **na** v části Nastavení

18. Klikněte na tlačítko **uložit.**

Tím se spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené do služby Salesforce v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud se službou provést. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity, které popisují všechny akce prováděné při zřizování služby ve vaší aplikaci Salesforce.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)

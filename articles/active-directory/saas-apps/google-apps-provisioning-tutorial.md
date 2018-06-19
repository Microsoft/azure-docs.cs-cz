---
title: 'Kurz: Konfigurace G Suite pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Naučte se automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 7bd2cc871300ca9aa9e7cccb5222bb003d199780
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35903147"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Kurz: Konfigurace G Suite pro zřizování automatické uživatelů

Cílem tohoto kurzu je ukazují, jak automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure Active Directory (Azure AD) do sady G.

> [!NOTE]
> Tento kurz popisuje spojnice postavená na službu zřizování uživatele Azure AD. Důležité informace o jaké této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS ve službě Azure Active Directory](./../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace Azure AD integrace se sadou G Suite, potřebujete následující položky:

- Předplatné služby Azure AD
- G Suite jednotné přihlašování povolené předplatné
- Google Apps předplatné nebo Google Cloud Platform předplatného.

> [!NOTE]
> K testování kroky v tomto kurzu, nedoporučujeme používání provozním prostředí.

Chcete-li otestovat kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte provozním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verze Azure AD, můžete [získat zkušební verzi jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Přiřazení uživatelů k G Suite

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD.

Než začnete konfigurovat a povolit zřizování služby, musíte rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebovat přístup k vaší aplikaci. Po provedení tohoto rozhodnutí, můžete přiřadit tyto uživatele do vaší aplikace podle pokynů v [přiřadit uživatele nebo skupinu enterprise aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Doporučujeme jednoho uživatele Azure AD pro testování zřizování konfigurace přidělí G Suite. Můžete později přiřadit dalších uživatelů a skupin.

> Když přiřadíte uživatele k G Suite, vyberte **uživatele** nebo **skupiny** role v dialogovém okně přiřazení. **Výchozího přístupu k** role nefunguje pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit zřizování automatizované uživatelů

Tato část vás provede procesem připojení k uživatelskému účtu zřizování Suite G rozhraní API služby Azure AD. Pomáhá také můžete konfigurovat službu zřizování vytvářet, aktualizovat a zakázat přiřazené uživatelské účty v sadě G na základě uživatele a přiřazení skupiny ve službě Azure AD.

>[!TIP]
>Můžete také zvolit povolení na základě SAML jednotné přihlašování pro sady G, postupujte podle pokynů v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce doplnění navzájem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického uživatele zřizování účtu

> [!NOTE]
> Jiné vhodným řešením pro automatizaci zřizování uživatelů G Suite je použití [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS zřídí identitami místní služby Active Directory k G Suite. Naproti tomu zřídí řešení v tomto kurzu uživatelů Azure Active Directory (cloud) a skupiny G Suite s povoleným e-mailu. 

1. Přihlaste se k [konzoly pro správu aplikace Google](http://admin.google.com/) pomocí účtu správce a pak vyberte **zabezpečení**. Pokud nevidíte odkaz, může být skrytý pod **více ovládacích prvků** nabídky v dolní části obrazovky.
   
    ![Vyberte zabezpečení.][10]

2. Na **zabezpečení** vyberte **referenční dokumentace rozhraní API**.
   
    ![Vyberte referenční dokumentace rozhraní API.][15]

3. Vyberte **povolit rozhraní API pro přístup**.
   
    ![Vyberte referenční dokumentace rozhraní API.][16]

    > [!IMPORTANT]
    > Pro každého uživatele, který máte v úmyslu zřídit Suite G, svoje uživatelské jméno ve službě Azure Active Directory *musí* být vázáno vlastní doménu. Například uživatel názvy této vypadají bob@contoso.onmicrosoft.com nejsou přijímány G Suite. Na druhé straně bob@contoso.com byla přijata. Existujícího uživatele domény můžete změnit úpravou jejich vlastnosti ve službě Azure AD. Jsme zahrnuli pokyny, jak nastavit vlastní doménu pro Azure Active Directory a G Suite v následujících krocích.
      
4. Pokud zatím jste nepřidali vlastního názvu domény do Azure Active Directory, proveďte následující kroky:
  
    a. V [portál Azure](https://portal.azure.com), na levém navigačním podokně, vyberte **služby Active Directory**. V seznamu adresáře vyberte svůj adresář. 

    b. Vyberte **název domény** na levém navigačním podokně a pak vyberte **přidat**.
     
     ![Doména](./media/google-apps-provisioning-tutorial/domain_1.png)

     ![Přidání domény](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Zadejte název domény do **název domény** pole. Tento název domény by měl být stejný jako název domény, kterou chcete použít pro sadu G. Vyberte **přidáním domény** tlačítko.
     
     ![Název domény](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Vyberte **Další** přejděte na stránku pro ověření. Chcete-li ověřit, že vlastníte tuto doménu, upravte záznamy DNS domény podle hodnoty, které jsou k dispozici na této stránce. Můžete ověřit pomocí buď **záznamů MX** nebo **záznamů TXT**podle toho, co jste vybrali pro **typ záznamu** možnost. 
    
    Komplexní pokyny o tom, jak ověřit názvy domén s Azure AD najdete v tématu [přidat vlastní název domény do Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Doména](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Opakujte předchozí kroky u všech domén, které chcete přidat do vašeho adresáře.

    > [!NOTE]
    Pro zřizování uživatelů, vlastní domény musí odpovídat názvu domény zdroje Azure AD. Pokud se neshodují, bude pravděpodobně možné vyřešit problém s implementací přizpůsobení mapování atributů.


5. Teď, když si ověříte, že všechny vaše domény s Azure AD, je nutné je ověřit znovu s Google Apps. Pro každou doménu, která již není registrován u služby Google proveďte následující kroky:
   
    a. V [konzoly pro správu aplikace Google](http://admin.google.com/), vyberte **domény**.
     
     ![Vyberte domény][20]

    b. Vyberte **přidejte k doméně nebo alias domény**.
     
     ![Přidání nové domény][21]

    c. Vyberte **přidat jiné domény**a pak zadejte název domény, který chcete přidat.
     
     ![Zadejte název domény][22]

    d. Vyberte **pokračovat a ověření vlastnictví domény**. Postupujte podle pokynů k ověření, že jste vlastníkem názvu domény. Komplexní pokyny o tom, jak ověřit doménu službou Google najdete v tématu [ověřit vlastnictví lokality s Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Opakujte předchozí kroky pro další domény, které chcete přidat do Google Apps.
     
     > [!WARNING]
     > Pokud změníte primární domény pro vašeho tenanta G Suite, a pokud již máte nakonfigurovat jednotné přihlašování s Azure AD, pak je nutné zopakovat krok #3 pod [krok 2: povolení jednotného přihlašování](#step-two-enable-single-sign-on).
       
6. V [konzoly pro správu aplikace Google](http://admin.google.com/), vyberte **rolí správce**.
   
     ![Vyberte Google Apps][26]

7. Určí, který účet správce, kterou chcete použít ke správě zřizování uživatelů. Pro **role správce** tohoto účtu, upravit **oprávnění** pro tuto roli. Ujistěte se, povolte všechny **oprávnění rozhraní API Správce** tak, aby tento účet slouží pro zřizování.
   
     ![Vyberte Google Apps][27]
   
    > [!NOTE]
    > Pokud konfigurujete provozním prostředí, osvědčeným postupem je vytvoření účtu správce v sadě G speciálně pro tento krok. Tyto účty musí mít roli správce s nimi spojených s potřebnými oprávněními rozhraní API.
     
8. V [portál Azure](https://portal.azure.com), vyhledejte **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** části.

9. Pokud jste již nakonfigurovali G Suite pro jednotné přihlašování, hledání instance sady G pomocí pole hledání. Jinak vyberte možnost **přidat**a poté vyhledejte **G Suite** nebo **Google Apps** v galerii aplikací. Ve výsledcích hledání vyberte svou aplikaci a poté jej přidejte do seznamu aplikací.

10. Vyberte instanci G Suite a pak vyberte **zřizování** kartě.

11. Nastavte **režimu zřizování** k **automatické**. 

     ![Zřizování](./media/google-apps-provisioning-tutorial/provisioning.png)

12. V části **přihlašovací údaje správce** vyberte **Authorize**. Otevře se dialogové okno Google autorizace v nové okno prohlížeče.

13. Potvrďte, že chcete poskytnout Azure Active Directory oprávnění k provádění změn ke klientovi G Suite. Vyberte **přijmout**.
    
     ![Zkontrolujte oprávnění.][28]

14. Na portálu Azure vyberte **Test připojení** zajistit, že Azure AD může připojit k vaší aplikace. Pokud se nepovede připojit, zajistěte, aby váš účet G Suite oprávnění správce týmu. Opakujte **Autorizovat** krok opakujte.

15. Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole. Potom zaškrtněte políčko.

16. Vyberte **uložit.**

17. V části **mapování** vyberte **synchronizaci Azure Active Directory uživatelům Google Apps**.

18. V **mapování atributů** , projděte si uživatelské atributy, které jsou synchronizované z Azure AD G Suite. Atributy, které jsou **párování** vlastnosti se používají tak, aby odpovídaly uživatelské účty v sadě G pro operace aktualizace. Vyberte **Uložit** potvrzení změny.

19. Povolit Azure AD zřizování služby pro sadu G, změňte **Stav zřizování** k **na** v **nastavení**.

20. Vyberte **Uložit**.

Tento proces se spustí počáteční synchronizaci všech uživatelů nebo skupin, které jsou přiřazeny k G Suite v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minutách, když je služba spuštěná. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity. Tyto protokoly popisují všechny akce, které se provádí pomocí služby zajišťování ve vaší aplikaci.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa uživatelů zřizování účtu pro podnikové aplikace](tutorial-list.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurovat jednotné přihlašování](google-apps-tutorial.md)



<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png

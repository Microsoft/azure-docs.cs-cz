---
title: 'Kurz: Konfigurace G Suite pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD ke G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 515b21c6854620a6bd1af53af8b1778d70613622
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203942"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Kurz: Konfigurace G Suite pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukazují, jak automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure Active Directory (Azure AD) ke G Suite.

> [!NOTE]
> Tento kurz popisuje konektor postavené na službě zřizování uživatelů služby Azure AD. Důležité podrobnosti o význam této služby, jak to funguje a nejčastější dotazy najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS ve službě Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s G Suite, potřebujete následující položky:

- Předplatné Azure AD
- G Suite jednotného přihlašování povolená předplatného
- Google Apps předplatného nebo předplatného Google Cloud Platform.

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-g-suite"></a>Přiřazení uživatelů ke G Suite

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů jsou synchronizovány pouze uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Než začnete konfigurovat a povolit službu zřizování, musíte se rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebovat přístup k vaší aplikaci. Poté, co jste provedli tato rozhodnutí, můžete přiřadit tito uživatelé do vaší aplikace podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

> [!IMPORTANT]
> Doporučujeme jeden uživatele Azure AD přiřadit ke G Suite k otestování konfigurace zřizování. Další uživatelé a skupiny můžete později přiřadit.

> Když přiřadíte uživatele k G Suite, vyberte **uživatele** nebo **skupiny** v dialogovém okně přiřazení role. **Výchozího přístupu k** role nebude fungovat pro zřizování.

## <a name="enable-automated-user-provisioning"></a>Povolit zřizování automatizované uživatelů

Tato část vás provede procesu připojování k uživatelskému účtu API G Suite zřizování služby Azure AD. Pomáhá také nakonfigurovat službu zřizování vytvářet, aktualizovat a vypnout přiřazené uživatelské účty v G Suite. na základě uživatele a přiřazení skupiny ve službě Azure AD.

>[!TIP]
>Můžete také zvolit povolit založené na SAML jednotného přihlašování pro G Suite, a to podle pokynů v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurovat automatické účet zřizování uživatelů

> [!NOTE]
> Další možné možnost automatizace zřizování uživatelů pro G Suite je použití [Google Apps Directory Sync (GADS)](https://support.google.com/a/answer/106368?hl=en). GADS ustanovení vaší místní služby Active Directory identity ke G Suite. Naproti tomu zřídí řešení v tomto kurzu služby Azure Active Directory (cloud) uživatelů a skupin ke G Suite s povoleným e-mailu. 

1. Přihlaste se k [konzoly pro správu služby Google Apps](https://admin.google.com/) pomocí účtu správce a pak vyberte **zabezpečení**. Pokud nevidíte odkaz, může být skrytá pod **další ovládací prvky** nabídce v dolní části obrazovky.
   
    ![Vyberte možnost zabezpečení.][10]

1. Na **zabezpečení** stránce **Reference k rozhraní API**.
   
    ![Vyberte Reference k rozhraní API.][15]

1. Vyberte **přístup přes rozhraní API povolit**.
   
    ![Vyberte Reference k rozhraní API.][16]

    > [!IMPORTANT]
    > Pro každého uživatele, který máte v úmyslu zřídíte v G Suite, svoje uživatelské jméno ve službě Azure Active Directory *musí* být spojený s vlastní doménou. Například uživatel názvy tento vzhled jako bob@contoso.onmicrosoft.com nejsou přijal G Suite. Na druhé straně bob@contoso.com je přijat. Existujícího uživatele domény lze změnit úpravou jejich vlastností ve službě Azure AD. Přidali jsme pokyny, jak nastavit vlastní doménu pro Azure Active Directory a G Suite v následujících krocích.
      
1. Pokud ještě jste nepřidali vlastního názvu domény do Azure Active Directory, pak proveďte následující kroky:
  
    a. V [webu Azure portal](https://portal.azure.com), v levém navigačním podokně vyberte **služby Active Directory**. V seznamu adresářů vyberte svůj adresář. 

    b. Vyberte **název domény** na levém navigačním podokně a pak vyberte **přidat**.
     
     ![Domain (Doména)](./media/google-apps-provisioning-tutorial/domain_1.png)

     ![Přidat doménu](./media/google-apps-provisioning-tutorial/domain_2.png)

    c. Zadejte název vaší domény do **název domény** pole. Tento název domény by měl být se stejným názvem domény, který máte v úmyslu používat pro G Suite. Vyberte **přidat doménu** tlačítko.
     
     ![Název domény](./media/google-apps-provisioning-tutorial/domain_3.png)

    d. Vyberte **Další** přejděte na stránku ověřování. Pokud chcete ověřit, že vlastníte tuto doménu, upravte záznamy DNS domény podle hodnoty, které jsou k dispozici na této stránce. Můžete ověřit pomocí **záznamů MX** nebo **záznamů TXT**podle toho, co vyberete **typ záznamu** možnost. 
    
    Komplexnější pokyny o tom, jak ověřit názvy domén s Azure AD najdete v tématu [přidání vlastního názvu domény do Azure AD](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409).
     
     ![Domain (Doména)](./media/google-apps-provisioning-tutorial/domain_4.png)

    e. Zopakujte předchozí kroky pro všechny domény, které chcete přidat do vašeho adresáře.

    > [!NOTE]
    Pro zřizování uživatelů, musí odpovídat vlastní doménu, název domény zdroje služby Azure AD. Pokud shodné nejsou, bude pravděpodobně možné vyřešit problém s implementací přizpůsobení mapování atributů.


1. Teď, když po ověření všech domén s Azure AD, musíte ověřit, je znovu s Google Apps. Pro každou doménu, která již není zaregistrovaný u Google proveďte následující kroky:
   
    a. V [konzoly pro správu služby Google Apps](https://admin.google.com/)vyberte **domén**.
     
     ![Select Domains][20]

    b. Vyberte **přidat doménu nebo doména alias**.
     
     ![Přidat novou doménu][21]

    c. Vyberte **přidat jiné domény**a pak zadejte název domény, který chcete přidat.
     
     ![Zadejte název domény][22]

    d. Vyberte **pokračovat a ověří vlastnictví domény**. Postupujte podle pokynů k ověření, že jste vlastníkem názvu domény. Komplexní pokyny o tom, jak ověřit doménu s Google najdete v tématu [ověří vlastnictví vaší lokality s Google Apps](https://support.google.com/webmasters/answer/35179).

    e. Zopakujte předchozí kroky pro všechny další domény, které chcete přidat ke Google Apps.
     
     > [!WARNING]
     > Pokud změníte primární doménou pro vašeho tenanta G Suite, a pokud už máte nakonfigurované jednotné přihlašování s Azure AD, pak budete muset opakovat krok #3 v části [krok 2: Povolit jednotné přihlašování](#step-two-enable-single-sign-on).
       
1. V [konzoly pro správu služby Google Apps](https://admin.google.com/)vyberte **rolí administrátora**.
   
     ![Select Google Apps][26]

1. Určete, které účet správce, kterou chcete použít ke správě zřizování uživatelů. Pro **roli správce** tohoto účtu, upravte **oprávnění** pro danou roli. Ujistěte se, že chcete povolit všechny **oprávnění rozhraní API Správce** tak, aby tento účet slouží pro zřizování.
   
     ![Select Google Apps][27]
   
    > [!NOTE]
    > Pokud konfigurujete produkčním prostředí, osvědčeným postupem je vytvoření účtu správce v G Suite speciálně pro tento krok. Tyto účty musí mít roli správce k nim má přiřazené, který má nezbytná oprávnění rozhraní API.
     
1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory** > **podnikové aplikace** > **všechnyaplikace** oddílu.

1. Pokud jste už nakonfigurovali G Suite pro jednotné přihlašování, vyhledejte pomocí vyhledávacího pole vaší instance G Suite. V opačném případě vyberte **přidat**a poté vyhledejte **G Suite** nebo **Google Apps** v galerii aplikací. Ve výsledcích hledání vyberte vaši aplikaci a pak je přidejte do seznamu aplikací.

1. Vyberte instanci služby G Suite a pak vyberte **zřizování** kartu.

1. Nastavte **režim zřizování** k **automatické**. 

     ![Zřizování](./media/google-apps-provisioning-tutorial/provisioning.png)

1. V části **přihlašovacích údajů správce** vyberte **Authorize**. Otevře se dialogové okno ověřování Google v novém okně prohlížeče.

1. Potvrďte, že chcete udělit oprávnění Azure Active Directory provádět změny do svého tenanta služby G Suite. Vyberte **Přijmout**.
    
     ![Zkontrolujte oprávnění.][28]

1. Na webu Azure Portal, vyberte **Test připojení** zajistit, že Azure AD může připojit k vaší aplikaci. Pokud se nepovede, ujistěte se, že váš účet G Suite má oprávnění správce týmu. Poté **Authorize** krok znovu.

1. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole. Vyberte zaškrtávací políčko.

1. Vyberte **uložit.**

1. V části **mapování** vyberte **synchronizace Azure Active Directory uživatelů ke Google Apps**.

1. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD ke G Suite. Atributy, které jsou **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v G Suite pro operace update. Vyberte **Uložit** potvrďte všechny změny.

1. Chcete-li povolit služba pro G Suite zřizování Azure AD, změňte **stavu zřizování** k **na** v **nastavení**.

1. Vyberte **Uložit**.

Tento proces spustí počáteční synchronizaci všech uživatelů nebo skupin, které jsou přiřazeny ke G Suite v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut, zatímco je služba spuštěna. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokolů aktivit. Tyto protokoly popisují všechny akce, které provádí službu zřizování ve vaší aplikaci.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](google-apps-tutorial.md)



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

---
title: 'Kurz: Konfigurace Salesforce pro Automatické zřizování uživatelů pomocí Azure Active Directory | Microsoft Docs'
description: Seznamte se s kroky potřebnými k provedení v Salesforce a Azure AD pro automatické zřízení a zrušení zřízení uživatelských účtů ze služby Azure AD do Salesforce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: c5df0a5fc054a12e3fa2ef1e352645c57c357b01
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798727"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Kurz: Konfigurace Salesforce pro Automatické zřizování uživatelů

Cílem tohoto kurzu je Ukázat kroky potřebné k provedení v Salesforce a Azure AD pro automatické zřízení a zrušení zřízení uživatelských účtů ze služby Azure AD do Salesforce.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující:

* Tenant Azure Active Directory
* Tenant Salesforce.com

> [!Note]
> Role by se neměly při importu rolí ručně upravovat v Azure Active Directory.

> [!IMPORTANT]
> Pokud používáte zkušební účet Salesforce.com, nebudete moct nakonfigurovat automatizované zřizování uživatelů. Zkušební účty nemají povolený přístup k rozhraní API, dokud se nezakoupí. Toto omezení můžete obejít pomocí bezplatného [vývojářského účtu](https://developer.salesforce.com/signup) k dokončení tohoto kurzu.

Pokud používáte prostředí izolovaného prostoru (sandbox) Salesforce, přečtěte si [kurz integrace služby Salesforce Sandbox](./salesforce-sandbox-tutorial.md).

## <a name="assigning-users-to-salesforce"></a>Přiřazování uživatelů k Salesforce

Azure Active Directory používá koncept nazvaný "přiřazení" k určení uživatelů, kteří mají získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů se synchronizují jenom uživatelé a skupiny přiřazené k aplikaci v Azure AD.

Než nakonfigurujete a povolíte službu zřizování, musíte určit, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k vaší aplikaci Salesforce. Po provedení tohoto rozhodnutí můžete těmto uživatelům přiřadit aplikaci Salesforce podle pokynů v tématu [přiřazení uživatele nebo skupiny k podnikové aplikaci](../manage-apps/assign-user-or-group-access-portal.md) .

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Důležité tipy pro přiřazení uživatelů k Salesforce

* Doporučuje se, aby se ke službě Salesforce přiřadil jeden uživatel Azure AD, aby se mohla testovat konfigurace zřizování. Další uživatele a skupiny můžete přiřadit později.

* Při přiřazování uživatele k Salesforce musíte vybrat platnou roli uživatele. Role výchozí přístup nefunguje pro zřizování.

    > [!NOTE]
    > Tato aplikace importuje profily ze služby Salesforce jako součást procesu zřizování, který může zákazník chtít vybrat při přiřazování uživatelů v Azure AD. Upozorňujeme, že profily, které se importují ze služby Salesforce, se zobrazí jako role v Azure AD.

## <a name="enable-automated-user-provisioning"></a>Povolit automatizované zřizování uživatelů

V této části se seznámíte s připojením k [rozhraní API pro zřizování uživatelského účtu](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)ve službě Salesforce a konfigurací služby zřizování pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v Salesforce na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip]
> Můžete se také rozhodnout povolit pro Salesforce Single Sign-On založený na SAML, a to podle pokynů uvedených v [Azure Portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatickém zřizování, i když se tyto dvě funkce navzájem doplňují.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů

Cílem této části je vysvětlit, jak povolit uživatelům zřizování uživatelských účtů služby Active Directory do Salesforce.

1. V [Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Enterprise Apps > všechny aplikace** .

2. Pokud jste již nakonfigurovali Salesforce pro jednotné přihlašování, vyhledejte vaši instanci Salesforce pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **Salesforce** v galerii aplikací. Ve výsledcích hledání vyberte Salesforce a přidejte ho do seznamu aplikací.

3. Vyberte instanci Salesforce a pak vyberte kartu **zřizování** .

4. Nastavte **Režim zřizování** na hodnotu **Automaticky**.

    ![Snímek obrazovky se stránkou zřizování Salesforce s režimem zřizování nastaveným na automatické a jiné hodnoty, které můžete nastavit.](./media/salesforce-provisioning-tutorial/provisioning.png)

5. V části **přihlašovací údaje správce** zadejte následující nastavení konfigurace:

    a. Do textového pole **správce uživatelské jméno** zadejte název účtu Salesforce, který má přiřazený profil **správce systému** v Salesforce.com.

    b. Do textového pole **heslo správce** zadejte heslo pro tento účet.

6. Pokud chcete získat token zabezpečení Salesforce, otevřete novou kartu a přihlaste se ke stejnému účtu správce Salesforce. V pravém horním rohu stránky klikněte na své jméno a pak klikněte na **Nastavení**.

    ![Snímek obrazovky se zobrazuje vybraný odkaz nastavení.](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Povolit automatické zřizování uživatelů")

7. V levém navigačním podokně klikněte na **osobní informace** a rozbalte související část a potom klikněte na **resetovat můj token zabezpečení**.
  
    ![Snímek obrazovky s vybraným osobním údajem resetování tokenu zabezpečení](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Povolit automatické zřizování uživatelů")

8. Na stránce **resetovat token zabezpečení** klikněte na tlačítko **resetovat token zabezpečení** .

    ![Snímek obrazovky se stránkou s tokenem zabezpečení REST s vysvětlujícím textem a možností resetovat token zabezpečení](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Povolit automatické zřizování uživatelů")

9. Ověřte e-mailové doručené pošty přidružené k tomuto účtu správce. Vyhledejte e-mail z Salesforce.com, který obsahuje nový token zabezpečení.

10. Zkopírujte token, otevřete okno Azure AD a vložte ho do pole **tajný token** .

11. Pokud je instance Salesforce v cloudu pro státní správu Salesforce, měla by se zadat **Adresa URL tenanta** . V opačném případě je volitelná. Zadejte adresu URL tenanta pomocí formátu "https:// \<your-instance\> . my.Salesforce.com" a nahraďte \<your-instance\> názvem vaší instance Salesforce.

12. V Azure Portal klikněte na **Test připojení** a ujistěte se, že se služba Azure AD může připojit k vaší aplikaci Salesforce.

13. V poli **e-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, které by měly dostávat oznámení o chybách zřizování, a zaškrtněte políčko níže.

14. Klikněte na **Uložit.**  

15. V části mapování vyberte **synchronizovat Azure Active Directory uživatelů do Salesforce.**

16. V části **mapování atributů** zkontrolujte atributy uživatelů synchronizované z Azure AD do Salesforce. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování uživatelských účtů v Salesforce pro operace aktualizace. Výběrem tlačítka Uložit potvrďte provedené změny.

17. Pokud chcete povolit službu Azure AD Provisioning pro Salesforce, změňte **stav zřizování** na **zapnuto** v části nastavení.

18. Klikněte na **Uložit.**

> [!NOTE]
> Po zřízení uživatelů v aplikaci Salesforce musí správce nakonfigurovat pro ně specifická nastavení jazyka. Další podrobnosti o konfiguraci jazyka najdete v [tomto](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) článku.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených k Salesforce v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace trvá déle než další synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná. Pomocí části **Podrobnosti o synchronizaci** můžete sledovat průběh a postupovat podle odkazů na zřizování protokolů aktivit, které popisují všechny akce prováděné službou zřizování ve vaší aplikaci Salesforce.

Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Běžné problémy
* Pokud máte problémy s autorizací přístupu k Salesforce, zajistěte následující:
    * Přihlašovací údaje, které se používají, mají přístup správce k Salesforce.
    * Verze Salesforce, kterou používáte, podporuje Web Access (například Developer, Enterprise, Sandbox a neomezenou edici Salesforce).
    * Přístup k webovému rozhraní API je pro uživatele povolený.
* Služba zřizování Azure AD podporuje zřizování jazyka, národního prostředí a časového pásma pro uživatele. Tyto atributy jsou v mapování výchozích atributů, ale nemají výchozí zdrojový atribut. Ujistěte se, že jste vybrali výchozí zdrojový atribut a že zdrojový atribut je ve formátu očekávaném službou SalesForce. Například localeSidKey pro angličtinu (USA) je en_US. Pro určení správného formátu localeSidKey si Projděte [zde](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) uvedené pokyny. Formáty languageLocaleKey najdete [tady](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Kromě toho, že je správný formát, možná budete muset zajistit, aby byl jazyk povolený pro vaše uživatele, jak je popsáno [zde](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceeded:** V cílové aplikaci nelze vytvořit uživatele, protože pro tohoto uživatele nejsou k dispozici žádné licence. Buď si zajistěte další licence pro cílovou aplikaci, nebo zkontrolujte přiřazení uživatelů a konfiguraci mapování atributů, abyste se ujistili, že správným uživatelům jsou přiřazeny správné atributy.
* **SalesforceDuplicateUserName:** Uživatele nelze zřídit, protože má Salesforce.com "username", které je duplikováno v jiném tenantovi Salesforce.com.V Salesforce.com musí být hodnoty pro atribut UserName jedinečné ve všech klientech Salesforce.com.Ve výchozím nastavení se hodnota userPrincipalName uživatele v Azure Active Directory v Salesforce.com stala "username".Máte dvě možnosti.Jednou z možností je vyhledat a přejmenovat uživatele s duplicitním názvem username v jiném tenantovi Salesforce.com, pokud spravujete i tohoto jiného tenanta.Druhou možností je odebrat přístup z Azure Active Directoryho uživatele do tenanta Salesforce.com, se kterým je adresář integrovaný. Tato operace se při dalším pokusu o synchronizaci zopakuje. 
* **SalesforceRequiredFieldMissing:** Salesforce vyžaduje, aby uživatel mohl v případě úspěšného vytvoření nebo aktualizace uživatele zobrazit určité atributy. Tomuto uživateli chybí jeden z požadovaných atributů. Zajistěte, aby se atributy jako e-mail a alias naplnily všemi uživateli, které chcete zřídit v Salesforce. Můžete nastavit obor pro uživatele, kteří nemají tyto atributy, pomocí [filtrů oborů založeného na atributech](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 
* Výchozí mapování atributů pro zřizování na Salesforce zahrnuje výraz SingleAppRoleAssignments pro mapování appRoleAssignments ve službě Azure AD na profilaci v Salesforce. Ujistěte se, že uživatelé nemají ve službě Azure AD přiřazení více rolí aplikace, protože mapování atributů podporuje zřizování pouze jedné role. 
* Salesforce vyžaduje, aby se aktualizace e-mailu před změnou schválily ručně. V důsledku toho se může stát, že v protokolech zřizování dojde k aktualizaci e-mailu uživatele na více položkách (dokud se neschválí změna e-mailu).


## <a name="additional-resources"></a>Další zdroje informací

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](./salesforce-tutorial.md)

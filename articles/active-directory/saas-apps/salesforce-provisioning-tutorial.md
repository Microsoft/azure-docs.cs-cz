---
title: 'Kurz: Konfigurace služby Salesforce pro automatické zřizování uživatelů pomocí služby Azure Active Directory| Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a službou Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060517"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Salesforce pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky potřebné k provedení v Salesforce a Azure AD automaticky zřídit a de-zřídit uživatelské účty z Azure AD salesforce.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

* Klient adresáře Azure Active Directory
* Klient Salesforce.com

> [!IMPORTANT]
> Pokud používáte Salesforce.com zkušební účet, nebude možné nakonfigurovat automatické zřizování uživatelů. Zkušební účty nemají potřebný přístup rozhraní API povolen, dokud nejsou zakoupeny. Toto omezení můžete obejít pomocí bezplatného [vývojářského účtu](https://developer.salesforce.com/signup) k dokončení tohoto kurzu.

Pokud používáte prostředí salesforce sandboxu, podívejte se na [kurz integrace salesforce sandboxu](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Přiřazení uživatelů ke sanu Salesforce

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k vaší aplikaci Salesforce. Po tomto rozhodnutí můžete tyto uživatele přiřadit k aplikaci Salesforce podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Důležité tipy pro přiřazení uživatelů ke sanu Salesforce

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Salesforce k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele ke službě Salesforce je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování

    > [!NOTE]
    > Tato aplikace importuje profily z Salesforce jako součást procesu zřizování, který zákazník může chtít vybrat při přiřazování uživatelů ve službě Azure AD. Upozorňujeme, že profily, které se importují ze služby Salesforce, se ve službě Azure AD zobrazují jako role.

## <a name="enable-automated-user-provisioning"></a>Povolení automatického zřizování uživatelů

Tato část vás provede připojením azure ad k [rozhraní API pro zřizování uživatelských účtů Salesforce – v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v Salesforce na základě přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip]
> Můžete se také rozhodnout povolit jednotné přihlašování pro Salesforce založené na SAML podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů

Cílem této části je nastínit, jak povolit zřizování uživatelských účtů služby Active Directory službě Salesforce.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

2. Pokud jste již salesforce nakonfigurovali pro jednotné přihlašování, vyhledejte svou instanci služby Salesforce pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **salesforce** v galerii aplikací. Ve výsledcích hledání vyberte Salesforce a přidejte ji do seznamu aplikací.

3. Vyberte svou instanci služby Salesforce a pak vyberte kartu **Zřizování.**

4. Nastavte **režim zřizování** na **automatické**.

    ![Zajišťování](./media/salesforce-provisioning-tutorial/provisioning.png)

5. V části **Pověření správce** zadejte následující nastavení konfigurace:

    a. Do textového pole **Uživatelské jméno správce** zadejte název účtu Salesforce, ve Salesforce.com přiřazen profil správce **systému.**

    b. Do textového pole **Heslo správce** zadejte heslo pro tento účet.

6. Chcete-li získat token zabezpečení Salesforce, otevřete novou kartu a přihlaste se ke stejnému účtu správce Salesforce. V pravém horním rohu stránky klikněte na své jméno a potom klikněte na **Nastavení**.

    ![Povolit automatické zřizování uživatelů](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Povolit automatické zřizování uživatelů")

7. V levém navigačním podokně rozbalte související oddíl klepnutím na **položku Osobní údaje** a potom klepněte na tlačítko **Obnovit token zabezpečení**.
  
    ![Povolit automatické zřizování uživatelů](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Povolit automatické zřizování uživatelů")

8. Na stránce **Reset security token** klikněte na tlačítko Obnovit token **zabezpečení.**

    ![Povolit automatické zřizování uživatelů](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Povolit automatické zřizování uživatelů")

9. Zkontrolujte e-mailovou schránku přidruženou k tomuto účtu správce. Vyhledejte e-mail od Salesforce.com, který obsahuje nový token zabezpečení.

10. Zkopírujte token, přejděte do okna Azure AD a vložte ho do pole **Tajný token.**

11. **Adresa URL klienta** by měla být zadána, pokud je instance Salesforce v cloudu služby Salesforce Government Cloud. V opačném případě je volitelné. Zadejte adresu URL klienta\<ve formátu\>"https:// instance \<.my.salesforce.com", která nahradí vaši instanci\> názvem instance Salesforce.

12. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Salesforce.

13. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko níže.

14. Klikněte na **Uložit.**  

15. V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory se službou Salesforce.**

16. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD na Salesforce. Všimněte si, že atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v Salesforce pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

17. Chcete-li povolit službu zřizování Azure AD pro Salesforce, změňte **stav zřizování** **na Zapnuto** v části Nastavení

18. Klikněte na **Uložit.**

> [!NOTE]
> Jakmile jsou uživatelé zřízeni v aplikaci Salesforce, musí správce nakonfigurovat nastavení specifické pro daný jazyk. Další podrobnosti o konfiguraci jazyka naleznete v [tomto](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) článku.

Tím se spustí počáteční synchronizace všech uživatelů nebo skupin přiřazených k salesforce v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci Salesforce.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="common-issues"></a>Běžné problémy
* Pokud máte problémy s autorizací přístupu ke sáňkování Salesforce, zajistěte následující:
    * Použitá pověření mají přístup správce k Salesforce.
    * Verze služby Salesforce, kterou používáte, podporuje webový přístup (např. edice Developer, Enterprise, Sandbox a Unlimited služby Salesforce).)
    * Přístup k webovému rozhraní API je pro uživatele povolen.
* Služba zřizování Azure AD podporuje zřizování jazyka, národního prostředí a timeZone pro uživatele. Tyto atributy jsou ve výchozím mapování atributů, ale nemají výchozí zdrojový atribut. Ujistěte se, že vyberete výchozí zdrojový atribut a že zdrojový atribut je ve formátu očekávaném SalesForce. Například localeSidKey pro angličtinu (Spojené státy) je en_US. Projděte si [pokyny](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) zde uvedené k určení správného formátu localeSidKey. JazykLocaleKey formáty lze nalézt [zde](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Kromě zajištění správná formát, může být nutné zajistit, že jazyk je povolen pro uživatele, jak je popsáno [zde](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitpřekročena:** Uživatele nelze vytvořit v cílové aplikaci, protože pro tohoto uživatele nejsou k dispozici žádné licence. Buď obkupte další licence pro cílovou aplikaci, nebo zkontrolujte přiřazení uživatelů a konfiguraci mapování atributů, abyste zajistili, že budou správným uživatelům přiřazeni správné atributy.
* **SalesforceDuplicateUserName:** Uživatel nemůže být zřízena, protože má Salesforce.com "uživatelské jméno", který je duplikován v jiném Salesforce.com tenanta.V Salesforce.com musí být hodnoty atributu Uživatelské jméno jedinečné ve všech Salesforce.com tenantů.Ve výchozím nastavení se uživatelské principalname uživatele ve službě Azure Active Directory stane jejich "uživatelským jménem" v Salesforce.com.Máte dvě možnosti.Jednou z možností je najít a přejmenovat uživatele s duplicitní 'uživatelské jméno' v druhé Salesforce.com tenanta, pokud spravujete, že jiný klient také.Druhou možností je odebrat přístup z uživatele služby Azure Active Directory do Salesforce.com tenanta, se kterým je váš adresář integrovaný. Tuto operaci zopakujeme při dalším pokusu o synchronizaci. 
* **SalesforceRequiredFieldChybějící:** Salesforce vyžaduje, aby uživatel mohl úspěšně vytvořit nebo aktualizovat určité atributy. Tomuto uživateli chybí jeden z požadovaných atributů. Ujistěte se, že atributy, jako je e-mail a alias jsou naplněny na všechny uživatele, které chcete být zřízena do Salesforce. Uživatele, kteří tyto atributy nemají, můžete určit pomocí [filtrů oborů založených na atributech](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 
* Výchozí mapování atributů pro zřizování do Salesforce zahrnuje výraz SingleAppRoleAssignments pro mapování appRoleAssignments v Azure AD na ProfileName v Salesforce. Ujistěte se, že uživatelé nemají více přiřazení rolí aplikace ve službě Azure AD, protože mapování atributů podporuje jenom zřizování jedné role. 
* Salesforce vyžaduje, aby byly aktualizace e-mailů před změnou schváleny ručně. V důsledku toho se může zobrazit více položek v protokolech zřizování aktualizovat e-mail uživatele (dokud změna e-mailu byla schválena).


## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)

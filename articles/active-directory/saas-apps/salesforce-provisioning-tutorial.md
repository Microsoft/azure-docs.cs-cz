---
title: 'Kurz: Konfigurace služby Salesforce pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Salesforce.
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
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: e2a5020bec94614971b6e9e7f4dcf94a0df96108
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888392"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Kurz: Konfigurace pro automatické zřizování uživatelů Salesforce

Cílem tohoto kurzu je zobrazit kroky nutné k provedení v Salesforce a Azure AD automaticky zřizovat a rušit přístup uživatelských účtů ze služby Azure AD k Salesforce.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

* Tenanta služby Azure Active directory
* Salesforce.com tenanta

> [!IMPORTANT]
> Pokud používáte zkušební verzi účtu Salesforce.com, pak bude schopen konfigurace zřizování uživatelů automatizované. Účty nemají potřebná přístup přes rozhraní API povoleno, dokud se kupují se smlouvou. Toto omezení můžete obejít pomocí bezplatného [vývojářský účet](https://developer.salesforce.com/signup) k dokončení tohoto kurzu.

Pokud používáte prostředí izolovaného prostoru služby Salesforce, najdete [Salesforce Sandboxu Průvodce integrací](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Přiřazování uživatelů k Salesforce

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů je synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k aplikaci Salesforce. Poté, co jste provedli tato rozhodnutí, můžete přiřadit tyto uživatele k aplikaci Salesforce podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Důležité tipy pro přiřazování uživatelů k Salesforce

* Dále je doporučeno jednoho uživatele Azure AD je přiřazená k Salesforce testovací konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazení uživatele k Salesforce, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování

    > [!NOTE]
    > Tato aplikace importuje profily ze Salesforce jako součást procesu zřizování, které zákazník může chtít vybrat při přiřazování uživatelů ve službě Azure AD. Mějte prosím na paměti, že profily, které importovat ze služby Salesforce se zobrazí jako role ve službě Azure AD.

## <a name="enable-automated-user-provisioning"></a>Povolit zřizování automatizované uživatelů

Tato část vás provede připojením služby Azure AD k uživatelskému účtu Salesforce pro rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazené uživatelské účty v Salesforce podle přiřazení uživatelů a skupin ve službě Azure AD.

> [!Tip]
> Můžete také povolena založené na SAML jednotného přihlašování pro Salesforce, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurovat automatické účet zřizování uživatelů

Cílem této části se popisují, jak povolit zřizování uživatelů z Active Directory uživatelské účty do Salesforce.

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

2. Pokud jste už nakonfigurovali Salesforce pro jednotné přihlašování, vyhledejte svoji instanci služby Salesforce pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Salesforce** v galerii aplikací. Ve výsledcích hledání vyberte Salesforce a přidat do seznamu aplikací.

3. Vyberte instanci služby Salesforce a potom **zřizování** kartu.

4. Nastavte **režim zřizování** k **automatické**.

    ![zřizování](./media/salesforce-provisioning-tutorial/provisioning.png)

5. V části **přihlašovacích údajů správce** části, zadejte následující nastavení konfigurace:

    a. V **uživatelské jméno správce** textové pole, typ účtu Salesforce název, který má **správce systému** profilu na Salesforce.com přiřazené.

    b. V **heslo správce** textového pole zadejte heslo pro tento účet.

6. K získání tokenu zabezpečení služby Salesforce, otevřete novou kartu a přihlášení do stejné správce účtu Salesforce. V pravém horním rohu stránky klikněte na své jméno a potom klikněte na tlačítko **nastavení**.

    ![Povolit automatické zřizování uživatelů](./media/salesforce-provisioning-tutorial/sf-my-settings.png "povolit automatické zřizování uživatelů")

7. V levém navigačním podokně klikněte na tlačítko **mé osobní údaje** související rozbalíte, a pak klikněte na **obnovit tento Token zabezpečení**.
  
    ![Povolit automatické zřizování uživatelů](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "povolit automatické zřizování uživatelů")

8. Na **obnovit Token zabezpečení** klikněte na **obnovit Token zabezpečení** tlačítko.

    ![Povolit automatické zřizování uživatelů](./media/salesforce-provisioning-tutorial/sf-reset-token.png "povolit automatické zřizování uživatelů")

9. Zkontrolujte doručenou poštu e-mailu spojený s tímto účtem správce. Vyhledejte e-mailu z Salesforce.com, který obsahuje nový token zabezpečení.

10. Zkopírujte token, přejděte do okna Azure AD a vložte ho do **tajný klíč tokenu** pole.

11. **Adresy URL Tenanta** by měly být zadány, pokud je v Salesforce Government Cloud instance Salesforce. V opačném případě je volitelný. Zadejte adresu URL tenanta pomocí formátu "https://\<vaše instance\>. my.salesforce.com," nahrazení \<vaše instance\> s názvem vaší instance služby Salesforce.

12. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Salesforce.

13. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měl přijímat oznámení zřizování chyby a zaškrtněte políčko níže.

14. Klikněte na tlačítko **uložit.**  

15. V oddílu mapování, vyberte **synchronizace Azure Active Directory Users do Salesforce.**

16. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do služby Salesforce. Všimněte si, že vybrané atributy jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Salesforce. pro operace update. Vyberte tlačítko Uložit potvrďte změny.

17. Chcete-li povolit služba pro Salesforce zřizování Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

18. Klikněte na tlačítko **uložit.**

Tím se spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Salesforce v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci Salesforce zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
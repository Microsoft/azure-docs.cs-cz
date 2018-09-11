---
title: 'Kurz: Konfigurace služby Salesforce Sandboxu pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Sandboxu služby Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1ad53c4ba1b79a1918177ce73862b603d157153
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346497"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Kurz: Konfigurace služby Salesforce Sandboxu pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v Sandboxu služby Salesforce a Azure AD automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do izolovaného prostoru služby Salesforce.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta služby Azure Active directory.
*   Platný tenanta pro Salesforce Sandboxu pro práci nebo Salesforce Sandboxu pro vzdělávání. Bezplatný zkušební účet můžete použít buď služby.
*   Účet uživatele s oprávněními správce týmu v Sandboxu služby Salesforce.

## <a name="assigning-users-to-salesforce-sandbox"></a>Přiřazování uživatelů do izolovaného prostoru Salesforce

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů jsou synchronizovány pouze uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD.

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, které uživatele nebo skupiny ve službě Azure AD potřebují přístup k aplikaci Salesforce izolovaného prostoru. Poté, co jste provedli tato rozhodnutí, můžete přiřadit tyto uživatele k aplikaci Salesforce Sandboxu podle pokynů v [přiřadit uživatele nebo skupiny k podnikové aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Důležité tipy pro přiřazování uživatelů do izolovaného prostoru Salesforce

* Dále je doporučeno jednoho uživatele Azure AD je přiřazen do izolovaného prostoru Salesforce pro testování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

* Při přiřazování uživatele do izolovaného prostoru služby Salesforce, musíte vybrat platné uživatelské role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.

> [!NOTE]
> Tato aplikace naimportuje vlastní role z izolovaného prostoru Salesforce jako součást procesu zřizování, které zákazník může chtít vybrat při přiřazování uživatelů.

## <a name="enable-automated-user-provisioning"></a>Povolit zřizování automatizované uživatelů

Tato část vás provede připojením služby Azure AD k uživatelskému účtu Salesforce Sandboxu rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazených uživatelských účtů v Sandboxu služby Salesforce v závislosti na uživatele a skupiny přiřazení ve službě Azure AD.

>[!Tip]
>Můžete také povolena založené na SAML jednotného přihlašování pro Salesforce izolovaného prostoru, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce návrzích mezi sebou.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurovat automatické účet zřizování uživatelů

Cílem této části se popisují, jak povolit zřizování uživatelů z Active Directory uživatelské účty do izolovaného prostoru služby Salesforce.

1. V [webu Azure portal](https://portal.azure.com), přejděte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

1. Pokud jste už nakonfigurovali Salesforce Sandboxu pro jednotné přihlašování, vyhledejte svoji instanci služby Salesforce izolovaného prostoru pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Salesforce Sandboxu** v galerii aplikací. Ve výsledcích hledání vyberte Sandboxu Salesforce a přidat do seznamu aplikací.

1. Vyberte instanci služby Salesforce izolovaného prostoru a potom **zřizování** kartu.

1. Nastavte **režim zřizování** k **automatické**.

    ![zřizování](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. V části **přihlašovacích údajů správce** části, zadejte následující nastavení konfigurace:
   
    a. V **uživatelské jméno správce** textové pole, typ účtu Salesforce Sandboxu název, který má **správce systému** profilu na Salesforce.com přiřazené.
   
    b. V **heslo správce** textového pole zadejte heslo pro tento účet.

1. K získání tokenu zabezpečení Sandboxu služby Salesforce, otevřete novou kartu a přihlášení do stejný účet správce izolovaného prostoru služby Salesforce. V pravém horním rohu stránky klikněte na své jméno a potom klikněte na tlačítko **nastavení**.

     ![Povolit automatické zřizování uživatelů](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "povolit automatické zřizování uživatelů")

1. V levém navigačním podokně klikněte na tlačítko **mé osobní údaje** související rozbalíte, a pak klikněte na **obnovit tento Token zabezpečení**.
  
    ![Povolit automatické zřizování uživatelů](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "povolit automatické zřizování uživatelů")

1. Na **obnovit Token zabezpečení** stránky, klikněte na tlačítko **obnovit Token zabezpečení** tlačítko.

    ![Povolit automatické zřizování uživatelů](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "povolit automatické zřizování uživatelů")

1. Zkontrolujte doručenou poštu e-mailu spojený s tímto účtem správce. Vyhledejte e-mailu ze Salesforce Sandbox.com, který obsahuje nový token zabezpečení.

1. Zkopírujte token, přejděte do okna Azure AD a vložte ho do **tajný klíč tokenu** pole.

1. Na webu Azure Portal, klikněte na tlačítko **Test připojení** aby Azure AD můžete připojit k aplikaci Salesforce izolovaného prostoru.

1. V **e-mailové oznámení** zadejte e-mailovou adresu osoby nebo skupiny, který by měl přijímat oznámení zřizování chyby a zaškrtněte políčko.

1. Klikněte na tlačítko **uložit.**  
    
1.  V oddílu mapování, vyberte **synchronizace Azure Active Directory Users do izolovaného prostoru služby Salesforce.**

1. V **mapování atributů** , projděte si atributy uživatele, které se synchronizují ze služby Azure AD do izolovaného prostoru služby Salesforce. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty v Sandboxu služby Salesforce pro operace update. Vyberte tlačítko Uložit potvrďte změny.

1. Chcete-li povolit zřizování pro Salesforce Sandboxu služby Azure AD, změňte **stavu zřizování** k **na** v sekci nastavení

1. Klikněte na tlačítko **uložit.**

Spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Salesforce izolovaného prostoru v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v zřizovací služba Salesforce izolovaný prostor aplikace.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)

---
title: 'Kurz: Konfigurace izolovaného prostoru Služby Salesforce pro automatické zřizování uživatelů pomocí služby Azure Active Directory| Dokumenty společnosti Microsoft'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Službou Azure Active Directory a sandboxem Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: bab73fda-6754-411d-9288-f73ecdaa486d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48df954f680c6774b0aa189453615156f4f0b4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063267"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Kurz: Konfigurace sandboxu Salesforce pro automatické zřizování uživatelů

Cílem tohoto kurzu je ukázat kroky, které je potřeba provést v Salesforce Sandbox a Azure AD automaticky zřídit a de-provision uživatelské účty z Azure AD do Salesforce Sandbox.

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu předpokládá, že již máte následující položky:

*   Tenant advitus azure active directory.
*   Platný tenant pro salesforce sandbox pro práci nebo salesforce sandbox pro vzdělávání. Pro kteroukoli službu můžete použít bezplatný zkušební účet.
*   Uživatelský účet v izolovaném prostoru Salesforce s oprávněními správce týmu.

## <a name="assigning-users-to-salesforce-sandbox"></a>Přiřazení uživatelů k izolovanému prostoru Salesforce

Azure Active Directory používá koncept s názvem "přiřazení" k určení, kteří uživatelé by měli získat přístup k vybraným aplikacím. V kontextu automatického zřizování uživatelských účtů jsou synchronizováni pouze uživatelé a skupiny, které byly "přiřazeny" k aplikaci ve službě Azure AD.

Před konfigurací a povolením zřizovací služby se musíte rozhodnout, kteří uživatelé nebo skupiny ve službě Azure AD potřebují přístup k vaší aplikaci Salesforce Sandbox. Po provedení tohoto rozhodnutí můžete tyto uživatele přiřadit k aplikaci Salesforce Sandbox podle pokynů v části [Přiřazení uživatele nebo skupiny k podnikové aplikaci.](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Důležité tipy pro přiřazení uživatelů do sandboxu Salesforce

* Doporučuje se, aby jeden uživatel Azure AD je přiřazen k Salesforce Sandbox k testování konfigurace zřizování. Další uživatelé a/nebo skupiny mohou být přiřazeny později.

* Při přiřazování uživatele k izolovanému prostoru Služby Salesforce je nutné vybrat platnou roli uživatele. Role "Výchozí přístup" nefunguje pro zřizování.

> [!NOTE]
> Tato aplikace importuje vlastní role z Salesforce Sandbox jako součást procesu zřizování, který zákazník může chtít vybrat při přiřazování uživatelů.

## <a name="enable-automated-user-provisioning"></a>Povolení automatického zřizování uživatelů

Tato část vás provede připojením azure ad k rozhraní API pro zřizování uživatelských účtů služby Salesforce Sandbox a konfigurací zřizovací služby pro vytváření, aktualizaci a zakázání přiřazených uživatelských účtů v izolovaném prostoru Salesforce na základě uživatele a skupiny přiřazení ve službě Azure AD.

>[!Tip]
>Můžete se také rozhodnout povolit jednotné přihlašování na saml pro Salesforce Sandbox podle pokynů uvedených na [webu Azure Portal](https://portal.azure.com). Jednotné přihlašování lze nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce kompliment navzájem.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurace automatického zřizování uživatelských účtů

Cílem této části je nastínit, jak povolit zřizování uživatelských účtů služby Active Directory do sandboxu Salesforce.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do části **Azure Active Directory > Podnikové aplikace > všechny aplikace.**

1. Pokud jste již nakonfigurovali sandbox Salesforce pro jednotné přihlašování, vyhledejte instanci sandboxu Salesforce pomocí vyhledávacího pole. V opačném případě vyberte **Přidat** a vyhledejte **sandbox Salesforce** v galerii aplikací. Ve výsledcích hledání vyberte Sandbox Salesforce a přidejte ho do seznamu aplikací.

1. Vyberte svou instanci sandboxu Salesforce a pak vyberte kartu **Zřizování.**

1. Nastavte **režim zřizování** na **automatické**.

    ![Zajišťování](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. V části **Pověření správce** zadejte následující nastavení konfigurace:
   
    a. Do textového pole **Uživatelské jméno správce** zadejte název účtu salesforce sandbox, který má v Salesforce.com přiřazen profil správce **systému.**
   
    b. Do textového pole **Heslo správce** zadejte heslo pro tento účet.

1. Chcete-li získat token zabezpečení služby Salesforce Sandbox, otevřete novou kartu a přihlaste se ke stejnému účtu správce služby Salesforce Sandbox. V pravém horním rohu stránky klikněte na své jméno a potom klikněte na **Nastavení**.

     ![Povolit automatické zřizování uživatelů](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Povolit automatické zřizování uživatelů")

1. V levém navigačním podokně rozbalte související oddíl klepnutím na **položku Osobní údaje** a potom klepněte na tlačítko **Obnovit token zabezpečení**.
  
    ![Povolit automatické zřizování uživatelů](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Povolit automatické zřizování uživatelů")

1. Na stránce **Reset security token** klikněte na tlačítko Obnovit token **zabezpečení.**

    ![Povolit automatické zřizování uživatelů](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Povolit automatické zřizování uživatelů")

1. Zkontrolujte e-mailovou schránku přidruženou k tomuto účtu správce. Vyhledejte e-mail od společnosti Salesforce Sandbox.com, která obsahuje nový token zabezpečení.

1. Zkopírujte token, přejděte do okna Azure AD a vložte ho do pole **Tajný token.**

1. Na webu Azure Portal klikněte na **Test Connection** a ujistěte se, že se Azure AD může připojit k vaší aplikaci Salesforce Sandbox.

1. Do pole **E-mail s oznámením** zadejte e-mailovou adresu osoby nebo skupiny, která by měla dostávat oznámení o chybách zřizování, a zaškrtněte políčko.

1. Klikněte na **Uložit.**  
    
1.  V části Mapování vyberte **Synchronizovat uživatele služby Azure Active Directory s sandboxem Salesforce.**

1. V části **Mapování atributů** zkontrolujte atributy uživatele, které jsou synchronizovány z Azure AD do salesforce sandbox. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelským účtům v sandboxu Salesforce pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte tlačítko Uložit.

1. Chcete-li povolit zřizovací službu Azure AD pro salesforce sandbox, změňte **stav zřizování** **na Zapnuto** v části Nastavení

1. Klikněte na **Uložit.**

Spustí počáteční synchronizaci všech uživatelů nebo skupin přiřazených k sandboxu Salesforce v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna. Část **Podrobnosti synchronizace** můžete použít ke sledování průběhu a sledování odkazů na protokoly zřizování aktivit, které popisují všechny akce prováděné službou zřizování v aplikaci Salesforce Sandbox.

Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)

---
title: 'Kurz: Nakonfigurujte centrální Cerner pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat služby Azure Active Directory tak, aby automaticky zřizovat uživatele pro plán Indie – střed Cerner.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: daveba
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9c6b1e77b6fce8bedb8f035fcb18acb8c56ad5a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200715"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Kurz: Nakonfigurujte centrální Cerner pro automatické zřizování uživatelů

Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v centrální Cerner a Azure AD automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD do seznamu uživatelů v centrální Cerner. 


## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta Azure Active Directory.
*   Centrální Cerner tenanta 

> [!NOTE]
> Azure Active Directory se integruje s centrální Cerner pomocí [SCIM](http://www.simplecloud.info/) protokolu.

## <a name="assigning-users-to-cerner-central"></a>Přiřazování uživatelů k Cerner – střed

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů jsou synchronizovány pouze uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Před konfigurací a povolení služby zřizování, byste měli rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k Cerner střed. Jakmile se rozhodli, můžete přiřadit těmto uživatelům Cerner centrální podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Důležité tipy pro přiřazování uživatelů k Cerner – střed

*   Dále je doporučeno jednoho uživatele Azure AD pro centrální Cerner přidělí k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

* Po dokončení pro jednoho uživatele počátečního testování Cerner centrální doporučuje přiřazení celý seznam uživatelů určená pro přístup k žádným řešením Cerner (nejen Cerner centrální) být zřízená soupisky Cerner od uživatele.  Jiná řešení Cerner využít tento seznam uživatelů v seznamu uživatelů.

*   Při přiřazení uživatele k centrální Cerner, je nutné vybrat **uživatele** v dialogovém okně přiřazení role. Uživatelé s rolí "Výchozí přístup" jsou vyloučeny z zřizování.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurace zřizování uživatelů pro centrální Cerner

Tato část vás provede připojením služby Azure AD pro centrální Cerner soupisky uživatele pomocí jeho Cerner SCIM uživatelského účtu rozhraní API zřizování a konfigurace služby zřizování, pokud chcete vytvořit, aktualizovat a zakázat přiřazených uživatelských účtů, Indie – střed Cerner v závislosti na přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Můžete také pro centrální Cerner povoleno založené na SAML jednotného přihlašování, postupujte podle pokynů uvedených v [portálu Azure portal (https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují. Další informace najdete v tématu [Cerner centrální jednotné přihlašování – kurz](cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Postup konfigurace automatického zřizování uživatelských účtů do centrální Cerner ve službě Azure AD:


Pro zřízení uživatelských účtů do centrální Cerner, budete muset Cerner žádat Cerner centrální systémový účet a vygenerování tokenu nosiče OAuth, Azure AD můžete použít k připojení ke koncovému bodu SCIM Cerner společnosti. Doporučuje se také, že integrace provést v prostředí izolovaného prostoru Cerner, před nasazením do produkčního prostředí.

1.  Prvním krokem je zajistit uživatelům správu Cerner a integrace služby Azure AD s účtem CernerCare, je nutná pro přístup k dokumentaci, které jsou nezbytné pro dokončení pokynů. V případě potřeby použijte k vytvoření účtů CernerCare v každé příslušné prostředí níže uvedených adres URL.

   * Izolovaného prostoru:  https://sandboxcernercare.com/accounts/create

   * Produkční:  https://cernercare.com/accounts/create  

2.  V dalším kroku musí být vytvořený účet systém pro službu Azure AD. Postupujte podle pokynů níže požádat o systémový účet pro izolovaný prostor a produkční prostředí.

   * Pokyny:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Izolovaného prostoru: https://sandboxcernercentral.com/system-accounts/

   * Produkční:  https://cernercentral.com/system-accounts/

3.  Dále vygenerujte tokenu nosiče OAuth pro každý z vašich účtů systému. Chcete-li to provést, postupujte podle pokynů níže.

   * Pokyny:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Izolovaného prostoru: https://sandboxcernercentral.com/system-accounts/

   * Produkční:  https://cernercentral.com/system-accounts/

4. Nakonec budete muset získat ID sféry seznamu uživatelů pro prostředí izolovaného prostoru i produkčním prostředí v Cerner a dokončete tak konfiguraci. Informace o tom, jak získat to najdete v tématu: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Teď můžete konfigurovat Azure AD za účelem zřízení uživatelských účtů do Cerner. Přihlaste se k [webu Azure portal](https://portal.azure.com)a vyhledejte **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

6. Pokud jste už nakonfigurovali Cerner centrální pro jednotné přihlašování, hledání instance Cerner střední pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **Cerner centrální** v galerii aplikací. Ve výsledcích hledání vyberte Cerner centrální a přidat do seznamu aplikací.

7.  Vyberte instanci Cerner centrální a potom **zřizování** kartu.

8.  Nastavte **režim zřizování** k **automatické**.

   ![Centrální Cerner zřizování](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Vyplňte následující pole v rámci **přihlašovacích údajů správce**:

   * V **adresy URL Tenanta** pole, zadejte adresu URL ve formátu níže, nahraďte ID sféry jste získali v kroku #4 "– seznam členů-sféry – ID uživatele".

> Izolovaného prostoru: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

> Produkční: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * V **tajný klíč tokenu** pole, zadejte tokenu nosiče OAuth, který jste vygenerovali v kroku #3 a klikněte na tlačítko **Test připojení**.

   * Zobrazí se oznámení o úspěchu upperright straně na portálu.

10. Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko níže.

11. Klikněte na **Uložit**. 

12. V **mapování atributů** , projděte si atributy uživatelů a skupin ze služby Azure AD synchronizovány se službou Cerner střed. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelské účty a skupiny v centrální Cerner pro operace update. Vyberte tlačítko Uložit potvrďte změny.

13. Služba pro centrální Cerner zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu

14. Klikněte na **Uložit**. 

Tím se spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k centrální Cerner v části Uživatelé a skupiny. Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut tak dlouho, dokud je spuštěna služba zřizování Azure AD. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené v aplikaci Centrální Cerner zřizovací služba.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Další materiály

* [Centrální Cerner: Publikování dat identit pomocí Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Kurz: Konfigurace Cerner centrální pro jednotné přihlašování s Azure Active Directory](cernercentral-tutorial.md)
* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup
* [Zjistěte, jak kontrolovat protokoly a získat sestavy o zřizování aktivity](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

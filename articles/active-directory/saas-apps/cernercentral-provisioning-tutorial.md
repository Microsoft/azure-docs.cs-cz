---
title: 'Kurz: Konfigurace Cerner střed pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Zjistěte, jak nakonfigurovat služby Azure Active Directory tak, aby automaticky zřizovat uživatelům soupisky in – střed Cerner.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser-msft
ms.openlocfilehash: 34e1f122a9dcae274f3b2c5341996dcc8b996322
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "35899422"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Kurz: Konfigurace pro zřizování uživatelů automatické Cerner – střed

Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v Cerner střed a Azure AD a automaticky zřizovat a zrušte zřídit uživatelské účty ze služby Azure AD pro uživatele soupisky in – střed Cerner. 


## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klient služby Azure Active Directory
*   Klient Cerner – střed 

> [!NOTE]
> Azure Active Directory se integruje s centrální Cerner pomocí [SCIM](http://www.simplecloud.info/) protokolu.

## <a name="assigning-users-to-cerner-central"></a>Přiřazení uživatelů k Cerner – střed

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu jsou synchronizovány pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Než nakonfigurujete a povolíte službu zřizování, byste měli rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k Cerner střed. Jakmile se rozhodli, můžete přiřadit těmto uživatelům Cerner střed podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Důležité tipy pro přiřazování uživatelů do Cerner – střed

*   Dále je doporučeno jednoho uživatele Azure AD pro centrální Cerner přidělí otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

* Po dokončení pro jednoho uživatele počáteční testování Cerner střed doporučuje přiřazení celý seznam uživatelů mají přístup k žádným řešením Cerner (ne jenom Cerner střed) být zřízená soupisky Cerner na uživatele.  Jiná řešení Cerner využívají tento seznam uživatelů v soupisky uživatele.

*   Při přiřazení uživatele k Cerner – střed, je nutné vybrat **uživatele** role v dialogovém okně přiřazení. Uživatelé s rolí "Výchozí přístup" jsou vyloučeny z zřizování.


## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfiguraci zřizování uživatelů k Cerner – střed

Tato část vás provede připojení k centrální Cerner soupisky uživatele pomocí na Cerner SCIM uživatelského účtu zřizování rozhraní API služby Azure AD a konfiguraci zřizování službu, kterou chcete vytvořit, aktualizovat a zakázat přiřazené uživatele na základě účtů v Cerner – střed přiřazení uživatelů a skupin ve službě Azure AD.

> [!TIP]
> Také můžete povolit na základě SAML jednotné přihlašování pro centrální Cerner, postupujte podle pokynů uvedených v [portál Azure (https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce vzájemně doplňují. Další informace najdete v tématu [Cerner střed jeden přihlašování kurzu](cernercentral-tutorial.md).


### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurace automatického účet zřizování uživatelů na střed Cerner ve službě Azure AD:


Chcete-li zřídit uživatelských účtů do centrální Cerner, budete muset požádat Cerner Cerner střed systémový účet a vygenerování tokenu nosiče OAuth, Azure AD můžete použít k připojení ke koncovému bodu na Cerner SCIM. Doporučujeme také provést integraci v prostředí izolovaného prostoru Cerner před nasazením do produkčního prostředí.

1.  V prvním kroku je zajistit osoby Správa Cerner a integrace Azure AD mají CernerCare účet, který je nutné pro přístup k potřebné k dokončení podle pokynů v dokumentaci. V případě potřeby použijte níže uvedené adresy URL můžete vytvořit účty CernerCare v každé příslušné prostředí.

   * Izolovaný prostor:  https://sandboxcernercare.com/accounts/create

   * Produkční:  https://cernercare.com/accounts/create  

2.  Účet systému dále musí být vytvořen pro Azure AD. Pomocí níže uvedené pokyny o systémový účet pro vaše prostředí izolovaného prostoru a provozní.

   * Pokyny:  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Izolovaný prostor: https://sandboxcernercentral.com/system-accounts/

   * Produkční:  https://cernercentral.com/system-accounts/

3.  V dalším kroku generovat tokenu nosiče OAuth pro všechny systémové účty. Chcete-li to provést, postupujte podle pokynů níže.

   * Pokyny:  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Izolovaný prostor: https://sandboxcernercentral.com/system-accounts/

   * Produkční:  https://cernercentral.com/system-accounts/

4. Nakonec budete muset získat ID sféry soupisky uživatelů pro izolovaný prostor i produkčním prostředí v Cerner k dokončení konfigurace. Informace o tom, jak získat to najdete v tématu: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM. 

5. Teď můžete konfigurovat Azure AD pro zřízení uživatelských účtů do Cerner. Přihlaste se k [portál Azure](https://portal.azure.com)a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

6. Pokud jste již nakonfigurovali Cerner střed pro jednotné přihlašování, vyhledejte instanci Cerner střed pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **Cerner střed** v galerii aplikací. Vyberte Cerner střed ve výsledcích hledání a přidejte ji do seznamu aplikací.

7.  Vyberte instanci Cerner střed a pak vyberte **zřizování** kartě.

8.  Nastavte **režimu zřizování** k **automatické**.

   ![Střed Cerner zřizování](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9.  Vyplňte následující pole v části **přihlašovací údaje správce**:

   * V **URL klienta** pole, zadejte adresu URL ve formátu níže nahraďte "User-soupisky-sféry-ID" s ID sféry jste získali v kroku #4.

> Izolovaný prostor: https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

> Produkční: https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * V **tajný klíč tokenu** pole, zadejte tokenu nosiče OAuth, který jste vygenerovali v kroku #3 a klikněte na **Test připojení**.

   * Měli byste vidět úspěšné oznámení na straně ÚETUChcete portálu.

10. Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole a zaškrtněte políčko níže.

11. Klikněte na **Uložit**. 

12. V **mapování atributů** , projděte si atributy uživatelů a skupin na střed Cerner synchronizaci z Azure AD. Atributy vybrán jako **párování** vlastnosti jsou slouží k přiřazení uživatelských účtů a skupin v centrální Cerner pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

13. Povolit Azure AD zřizování služby pro Cerner ústředí, změňte **Stav zřizování** k **na** v **nastavení** části

14. Klikněte na **Uložit**. 

Tím se spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k Cerner střed v části Uživatelé a skupiny. Počáteční synchronizace trvá déle provést než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud běží zřizování služby Azure AD. Můžete použít **podrobnosti synchronizace** části monitorovat průběh a odkazech zřízení protokoly aktivity, které popisují všechny akce, které provádí službu zřizování na střed Cerner aplikace.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Další zdroje informací:

* [Střed Cerner: Publikování dat identity pomocí služby Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Kurz: Konfigurace Cerner střed pro jednotné přihlašování s Azure Active Directory](cernercentral-tutorial.md)
* [Správa uživatelů zřizování účtu pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Další postup
* [Zjistěte, jak získat sestavy o zřizování aktivity a zkontrolujte protokoly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).

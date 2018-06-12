---
title: 'Kurz: Konfigurace LinkedIn prodej navigátor pro zřizování automatické uživatelů s Azure Active Directory | Microsoft Docs'
description: Informace o konfiguraci Azure Active Directory a automaticky zřizovat a zrušte zřízení uživatelských účtů do Navigátor prodej LinkedIn.
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
ms.openlocfilehash: a330396c1c7f2033725ff4ef44ed467f5eee34de
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35292782"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Kurz: Konfigurace LinkedIn prodej navigátor pro zřizování automatické uživatelů


Cílem tohoto kurzu je tak, aby zobrazovalo kroky, které je třeba provést v Navigátor prodej LinkedIn a Azure AD a automaticky zřizovat a zrušte zřízení uživatelských účtů ze služby Azure AD do Navigátor prodej LinkedIn. 

## <a name="prerequisites"></a>Požadavky

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

*   Klient služby Azure Active Directory
*   Navigátor prodej LinkedIn klienta 
*   Účet správce v LinkedIn prodej Navigátor přístup k centru účtů LinkedIn

> [!NOTE]
> Azure Active Directory se integruje s LinkedIn prodej Navigátor pomocí [SCIM](http://www.simplecloud.info/) protokolu.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Přiřazení uživatelů k LinkedIn prodej Navigátor

Azure Active Directory používá koncept označované jako "úlohy" k určení uživatelů, kteří obdrželi přístup k vybrané aplikace. V kontextu uživatele automatické zřizování účtu se budou synchronizovat pouze uživatelé a skupiny, které byly "přiřazeny" aplikace ve službě Azure AD. 

Před konfigurací a povolení zřizování služby, musíte se rozhodnout, jaké uživatelů nebo skupin ve službě Azure AD představují uživatele, kteří potřebují přístup k Navigátor prodej LinkedIn. Jakmile se rozhodli, můžete přiřadit těmto uživatelům LinkedIn prodej Navigátor podle pokynů tady:

[Přiřazení uživatele nebo skupiny do aplikace enterprise](manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Důležité tipy pro přiřazování uživatelů do LinkedIn prodej Navigátor

*   Dále je doporučeno jednoho uživatele Azure AD pro LinkedIn prodej Navigátor přidělí otestovat konfiguraci zřizování. Další uživatele nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k Navigátor LinkedIn prodej, je nutné vybrat **uživatele** role v dialogovém okně přiřazení. Roli "Výchozí přístup" nefunguje pro zřizování.


## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfiguraci zřizování uživatelů pro LinkedIn prodej Navigátor

Tato část vás provede připojení k Navigátor prodej LinkedIn SCIM uživatelský účet zřizování rozhraní API služby Azure AD a konfiguraci zřizování služby vytvářet, aktualizovat a zakázat přiřadit uživatelské účty v Navigátor prodej LinkedIn na základě uživatele a přiřazení skupiny ve službě Azure AD.

> [!TIP]
> Můžete také pro LinkedIn prodej Navigátor povoleno na základě SAML jednotné přihlašování, postupujte podle pokynů uvedených v [portál Azure](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce vzájemně doplňují.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Konfigurace automatického účet zřizování uživatelů na LinkedIn prodej v tabulce v Azure AD:


Prvním krokem je načíst LinkedIn přístupový token. Pokud jste správce podnikové sítě, můžete zřídit samoobslužné přístupový token. V centru váš účet, přejděte na **nastavení &gt; globální nastavení** a otevřete **SCIM instalace** panelu.

> [!NOTE]
> Pokud se připojujete centra účtů přímo a nikoli v rámci odkaz, můžete dosáhnout pomocí následujících kroků.

1)  Přihlaste se k účtu Center.

2)  Vyberte **správce &gt; nastavení správce** .

3)  Klikněte na tlačítko **Advanced integrace** na levém bočním panelu. Budete přesměrováni do centra účtů.

4)  Klikněte na tlačítko **+ přidat novou konfiguraci SCIM** a postupujte podle pokynů podle hodnot v každé pole.

> Když autoassign licencí není povolen, znamená to, že je synchronizovat pouze data uživatele.

![Navigátor prodeje LinkedIn zřizování](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

> Pokud je povoleno autolicense přiřazení, budete muset poznamenejte si instanci aplikace a typ licence. Licence jsou přiřazeny na první přijde, nejprve sloužit základ, dokud všechny licence se provádějí.

![Navigátor prodeje LinkedIn zřizování](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5)  Klikněte na tlačítko **vygenerovat token**. Měli byste vidět zobrazení tokenu přístupu v části **přístupový token** pole.

6)  Uložte přístupový token do schránky nebo počítač před opuštěním stránky.

7) V dalším kroku se přihlaste k [portál Azure](https://portal.azure.com)a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace** části.

8) Pokud jste již nakonfigurovali LinkedIn prodej navigátor pro jednotné přihlašování, vyhledejte instanci Navigátor prodej LinkedIn pomocí pole hledání. Jinak vyberte možnost **přidat** a vyhledejte **LinkedIn prodej Navigátor** v galerii aplikací. Vyberte LinkedIn prodej Navigátor ve výsledcích hledání a přidejte ji do seznamu aplikací.

9)  Vyberte instanci Navigátor prodej LinkedIn a pak vyberte **zřizování** kartě.

10) Nastavte **režimu zřizování** k **automatické**.

![Navigátor prodeje LinkedIn zřizování](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11)  Vyplňte následující pole v části **přihlašovací údaje správce** :

* V **URL klienta** zadejte https://api.linkedin.com.

* V **tajný klíč tokenu** pole, zadejte přístupový token, který jste vygenerovali v kroku 1 a klikněte na **Test připojení** .

* Měli byste vidět úspěšné oznámení na straně ÚETUChcete portálu.

12) Zadejte e-mailovou adresu uživatele nebo skupiny, který by měly dostávat oznámení zřizování Chyba v **e-mailové oznámení** pole a zaškrtněte políčko níže.

13) Klikněte na **Uložit**. 

14) V **mapování atributů** , projděte si atributy uživatelů a skupin, které budou synchronizované z Azure AD Navigátor prodej LinkedIn. Všimněte si, že atributy vybrán jako **párování** vlastnosti se použije tak, aby odpovídaly uživatelské účty a skupiny v LinkedIn prodej navigátor pro operace aktualizace. Kliknutím na tlačítko Uložit potvrzení změny.

![Navigátor prodeje LinkedIn zřizování](./media/active-directory-saas-linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15) Povolit zřizování služby pro LinkedIn prodej Navigátor Azure AD, změňte **Stav zřizování** k **na** v **nastavení** části

16) Klikněte na **Uložit**. 

Tato akce spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené na LinkedIn prodej v tabulce v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut, dokud je služba spuštěná. Můžete použít **podrobnosti synchronizace** oddílu monitorovat průběh a odkazech zřízení protokoly aktivity, které popisují všechny akce prováděné při zřizování služby ve vaší aplikaci Navigátor prodej LinkedIn.

Další informace o tom, jak číst zřizování protokoly služby Azure AD najdete v tématu [zprávy o zřizování účtu automatické uživatele](active-directory-saas-provisioning-reporting.md).


## <a name="additional-resources"></a>Další prostředky

* [Správa uživatelů zřizování účtu pro podnikové aplikace](manage-apps/configure-automatic-user-provisioning-portal.md)
* [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

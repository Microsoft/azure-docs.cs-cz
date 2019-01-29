---
title: 'Kurz: Konfigurace zvýšení oprávnění LinkedIn pro automatické zřizování uživatelů pomocí Azure Active Directory | Dokumentace Microsoftu'
description: Zjistěte, jak konfigurovat Azure Active Directory a automaticky zřizovat a rušit zřízení uživatelských účtů ke zvýšení LinkedIn.
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
ms.date: 01/28/2018
ms.author: asmalser-msft
ms.openlocfilehash: bff7b819bfe0d0a2bd62b4b6452fe64b6c12beee
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182621"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Kurz: Konfigurace zvýšení oprávnění LinkedIn pro automatické zřizování uživatelů


Cílem tohoto kurzu je zobrazit kroky, které je třeba provést v rozvoji Linkedinem a Azure AD automaticky zřizovat a rušit zřízení uživatelských účtů ze služby Azure AD k rozvoji Linkedinem. 

## <a name="prerequisites"></a>Požadavky

Scénář popsaný v tomto kurzu se předpokládá, že máte následující položky:

*   Tenanta Azure Active Directory.
*   Tenant rozvoji Linkedinem 
*   Účet správce v rozvoji Linkedinem přístup do centra pro účty LinkedIn

> [!NOTE]
> Integruje do Linkedinu zvýšení oprávnění pomocí Azure Active Directory [SCIM](http://www.simplecloud.info/) protokolu.

## <a name="assigning-users-to-linkedin-elevate"></a>Přiřazování uživatelů k rozvoji Linkedinem

Azure Active Directory používá koncept nazvaný "přiřazení" k určení, kteří uživatelé měli obdržet přístup k vybrané aplikace. V rámci zřizování automatické uživatelských účtů se budou synchronizovat jenom uživatelé a skupiny, které se "přiřadily" aplikace ve službě Azure AD. 

Před konfigurací a povolení služby zřizování, je potřeba rozhodnout, jaké uživatele a/nebo skupiny ve službě Azure AD představují uživatele, kteří potřebují přístup k rozvoji Linkedinem. Jakmile se rozhodli, můžete přiřadit tito uživatelé k rozvoji Linkedinem podle zde uvedených pokynů:

[Přiřadit uživatele nebo skupiny k podnikové aplikace](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Důležité tipy pro přiřazování uživatelů k rozvoji Linkedinem

*   Dále je doporučeno jednoho uživatele Azure AD přiřadit k rozvoji Linkedinem k otestování konfigurace zřizování. Další uživatele a/nebo skupiny může být přiřazen později.

*   Při přiřazení uživatele k rozvoji Linkedinem, je nutné vybrat **uživatele** v dialogovém okně přiřazení role. Tuto roli "Výchozí přístupu" nefunguje pro zřizování.


## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurace zřizování uživatelů k rozvoji Linkedinem

Tato část vás provede připojení služby Azure AD na LinkedIn zvýšení SCIM uživatelský účet rozhraní API zřizování a konfigurace služby zřizování vytvářet, aktualizovat a vypnout přiřazení uživatelských účtů v Linkedinu zvýšit oprávnění na základě uživatele a přiřazení skupiny ve službě Azure AD.

**Tip:** Můžete také povolena založené na SAML jednotného přihlašování pro zvýšení LinkedIn, postupujte podle pokynů uvedených v [webu Azure portal](https://portal.azure.com). Jednotné přihlašování se dá nakonfigurovat nezávisle na automatické zřizování, i když tyto dvě funkce se vzájemně doplňují.


### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Postup konfigurace automatického zřizování uživatelských účtů ke zvýšení LinkedIn ve službě Azure AD:


Prvním krokem je získání přístupového tokenu LinkedIn. Pokud jste správce podnikové sítě, můžete zřídit vlastní přístupový token. V Centru pro váš účet, přejděte na **nastavení &gt; globální nastavení** a otevřete **SCIM nastavení** panelu.

> [!NOTE]
> Při přístupu k centru účtů přímo spíše než pomocí odkazu, můžete dosáhnout pomocí následujících kroků.

1)  Přihlaste se k účtu System Center.

2)  Vyberte **správce &gt; nastavení správy** .

3)  Klikněte na tlačítko **pokročilé integrace** na levém bočním panelu. Budete přesměrováni na centrum účtů.

4)  Klikněte na tlačítko **+ přidat novou konfiguraci SCIM** a postupujte podle pokynů vyplněním jednotlivá pole.

> Pokud autoassign licence není povolená, znamená to, že se synchronizuje pouze uživatelská data.

![LinkedIn zvýšení zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

> Přiřazení autolicense je povoleno, musíte uvést instance aplikace a typ licence. Přiřazení licencí na první přijde, nejprve sloužit základ, dokud se všechny licence pocházejí.

![LinkedIn zvýšení zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5)  Klikněte na tlačítko **vygenerování tokenu**. Měli byste vidět zobrazení token přístupu v rámci **přístupový token** pole.

6)  Před opustit stránku uložte přístupový token do schránky nebo počítače.

7) V dalším kroku se přihlaste k [webu Azure portal](https://portal.azure.com)a přejděte do **Azure Active Directory > podnikové aplikace > všechny aplikace** oddílu.

8) Pokud jste už nakonfigurovali rozvoji Linkedinem pro jednotné přihlašování, vyhledejte svoji instanci služby LinkedIn zvýšení oprávnění pomocí vyhledávacího pole. V opačném případě vyberte **přidat** a vyhledejte **rozvoji Linkedinem** v galerii aplikací. Ve výsledcích hledání vyberte zvýšení oprávnění LinkedIn a přidat do seznamu aplikací.

9)  Vyberte instanci služby LinkedIn zvýšení oprávnění a potom **zřizování** kartu.

10) Nastavte **režim zřizování** k **automatické**.

![LinkedIn zvýšení zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11)  Vyplňte následující pole v rámci **přihlašovacích údajů správce** :

* V **adresy URL Tenanta** zadejte https://api.linkedin.com.

* V **tajný klíč tokenu** pole, zadejte přístupový token, který jste vygenerovali v kroku 1 a klikněte na tlačítko **Test připojení** .

* Zobrazí se oznámení o úspěchu upperright straně na portálu.

12) Zadejte e-mailovou adresu osoby nebo skupiny, která má obdržet oznámení zřizování chyby v **e-mailové oznámení** pole a zaškrtněte políčko níže.

13) Klikněte na **Uložit**. 

14) V **mapování atributů** , projděte si uživatele a skupiny atributů, které se budou synchronizovat ze služby Azure AD na LinkedIn zvýšení oprávnění. Všimněte si, že vybrané atributy jako **odpovídající** použije vlastnosti tak, aby odpovídaly uživatelské účty a skupiny v rozvoji Linkedinem pro operace update. Vyberte tlačítko Uložit potvrďte změny.

![LinkedIn zvýšení zřizování](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15) Služba pro zvýšení LinkedIn zřizování Azure AD povolit, změňte **stavu zřizování** k **na** v **nastavení** oddílu

16) Klikněte na **Uložit**. 

Tím se spustí počáteční synchronizaci všech uživatelů a skupiny přiřazené k rozvoji Linkedinem v části Uživatelé a skupiny. Všimněte si, že počáteční synchronizace bude trvat déle než následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. Můžete použít **podrobnosti synchronizace** části ke sledování průběhu a odkazech na zřizování protokoly aktivit, které popisují všechny akce provedené službě zřizování na vaši aplikaci zvyšovat LinkedIn.

Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](../manage-apps/check-status-user-account-provisioning.md).


## <a name="additional-resources"></a>Další prostředky

* [Správa zřizování uživatelských účtů pro podnikové aplikace](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

---
title: Přiřazení uživatelů a skupin k aplikaci | Dokumentace Microsoftu
description: Přiřazení uživatelů k aplikaci udělit přístup
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8163b2261601b701913e07d703f5db0ac6fa7c0
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688074"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Přiřazení uživatelů a skupin k aplikaci v Azure Active Directory
Tento článek ukazuje, jak přiřadit uživatele nebo skupiny k aplikaci v Azure Active Directory (Azure AD). Uživatelé musí být přiřazen k aplikaci předtím, než správce můžete jim udělit přístup k následujícím:

-   Přístup k aplikaci pomocí **přejdete na adresu URL aplikace přímo** (označované také jako iniciovaného Zprostředkovatelem přihlašování přihlášení).

-   Přístup k aplikaci pomocí **adresa URL portálu User Access** na aplikace **vlastnosti** stránky (označované také jako zahájené pomocí IDP přihlášení).

-   V tématu aplikace se zobrazí na jejich [přístupového panelu aplikací](https://myapps.microsoft.com/) nebo mobilních aplikací.

-   V tématu aplikace se zobrazí na jejich [Spouštěče aplikací Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

Dostupnost přiřazení na základě skupin je určená vaší licenční smlouvou. Přiřazení na základě skupin se podporuje jenom pro skupiny zabezpečení. Vnořené členství ve skupinách a skupiny O365 se momentálně nepodporují.

## <a name="prerequisites"></a>Požadavky
Než budete moci přiřadit uživatele a skupiny k aplikaci, musí vyžadují přiřazení uživatele. Vyžadování přiřazení uživatele:

1. Přihlaste se k webu Azure portal pomocí účtu správce.
2. Klikněte na **všechny služby** položku v hlavní nabídce.
3. Vyberte adresář, který používáte pro aplikaci.
4. Klikněte na **podnikové aplikace** kartu.
5. Vyberte aplikaci ze seznamu aplikací, které jsou přidružené k tomuto adresáři.
6. Klikněte na tlačítko **vlastnosti** kartu.
7. Změnit **přiřazení uživatelů povinné?** přepnout na Ano.
8. Klikněte na tlačítko **Uložit** tlačítko v horní části obrazovky.

## <a name="assign-users"></a>Přiřazení uživatelů

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní navigační nabídce vlevo hlavní.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** z navigační nabídce vlevo Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

    * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** z navigační nabídce vlevo aplikaci.

8.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky uživatele nebo logo, které chcete přidat uživatele **vybrané** seznamu.

12. **Volitelné** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e** -mailová adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatele, které jste vybrali.

## <a name="assign-groups"></a>Přiřazení skupin

Jednu nebo více skupin přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní navigační nabídce vlevo hlavní.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** z navigační nabídce vlevo Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

    * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** z navigační nabídce vlevo aplikaci.

8.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **název celé skupiny** zájem o přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu zobrazíte **zaškrtávací políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo, které chcete přidat uživatele do skupiny **vybrané** seznamu.

12. **Volitelné** Chcete-li **Přidat více než jednu skupinu**, zadejte do pole **Hledat podle jména nebo e-mailové adresy** další **název celé skupiny** a kliknutím na zaškrtávací políčko přidejte tuto skupinu do **vybraného** seznamu.

13. Když jste hotovi s výběrem skupin, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit ke zvoleným skupinám, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko a přiřazení aplikace k vybraným skupinám.

Po krátké době možné ke spouštění těchto aplikací pomocí metod popsaných v části popis řešení uživatelů v rámci jednotlivých skupin, které jste vybrali. Pokud jsou dynamické skupiny, může být některé další zpracování zpoždění v tato přiřazení pro uživatele v rámci těchto přiřazené skupiny.

## <a name="enable-self-service-application-access"></a>Povolení samoobslužného přístupu k aplikacím

Obchodní skupině schvalovat přístup k těmto aplikacím samoobslužného přístupu k aplikacím je skvělý způsob, jak povolit uživatelům samoobslužné zjišťování aplikací, volitelně povolit. Můžete povolit obchodní skupiny pro správu přiřazené těmto uživatelům pro heslo jednotné přihlašování v aplikacích vpravo od svých přístupových panelech přihlašovací údaje.

Povolení samoobslužného přístupu k aplikaci, postupujte podle následujících kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2. Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní navigační nabídce vlevo hlavní.

3. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4. Klikněte na tlačítko **podnikové aplikace** z navigační nabídce vlevo Azure Active Directory.

5. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6. Vyberte aplikaci, kterou chcete povolit samoobslužné služby přístup ze seznamu.

7. Po načtení aplikace, klikněte na tlačítko **samoobslužné** z navigační nabídce vlevo aplikaci.

8. Povolení samoobslužného přístupu k aplikacím pro tuto aplikaci, zapněte **povolit uživatelům žádat o přístup k této aplikaci?** přepnutím **Ano.**

9. V dalším kroku vyberte skupiny, kterým uživatelé, kteří žádají o přístup k této aplikaci by měly být přidány, klepněte na volič vedle popisku **do které skupiny by měl přiřazení uživatelé měli přidat?** a vyberte skupinu.

10. **Volitelné** Pokud chcete před povolením přístupu k této aplikaci vyžadovat obchodní schválení, nastavte **před udělením přístupu k této aplikaci oprávnění vyžadovat schválení?** přepněte na **Ano**.

11. **Volitelné U aplikací, které používají pouze jednotné přihlašování,** Pokud chcete těmto obchodním schvalovatelům dovolit zadat hesla, která se do této aplikace odesílají pro schválené uživatele, nastavte **pro toto heslo uživatele oprávnění je aplikace?** Přepněte na **Ano**.

12. **Volitelné** Pokud chcete určit obchodní schvalovatele, kteří mají povolený přístup k této aplikaci, klikněte na selektor vedle popisku **, který má povolený souhlas s přístupem k této aplikaci?** Pokud chcete vybrat až 10 individuálních obchodních schvalovatelů.

    >[!NOTE]
    >Skupiny se nepodporují.
    >
    >

13. **Volitelné** V případě **aplikací, které zveřejňují role**, pokud chcete roli přiřadit schváleným uživatelům samoobslužné služby, klikněte na selektor vedle role, **které by se měly přiřazovat uživatelům v této aplikaci** . Pokud chcete vybrat roli, ke které se tito uživatelé mají přiřadit.

14. Klikněte na tlačítko **Uložit** tlačítko v horní části podokna na dokončení.

Po dokončení konfigurace samoobslužné služby aplikace, uživatelé mohou přejít na jejich [přístupového panelu aplikací](https://myapps.microsoft.com/) a klikněte na tlačítko **+ přidat** tlačítko a hned uvidíte aplikace, které jste povolili samoobslužné funkce přístup. Firemními schvalovateli oznámení se zobrazí také v jejich [přístupového panelu aplikací](https://myapps.microsoft.com/). Můžete povolit e-mailu oznamující, že uživatel má požádali o přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporují jeden schvalovacích pracovních postupů, což znamená, že pokud zadáte několik schvalovatelů, jeden schvalovatel může schvalovatel přístup k aplikaci.

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

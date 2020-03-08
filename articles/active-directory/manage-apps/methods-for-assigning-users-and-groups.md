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
ms.openlocfilehash: abd6b13dc56f8f948d50e2b3564712ed8f5b1476
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376439"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Přiřazení uživatelů a skupin k aplikaci v Azure Active Directory
Tento článek ukazuje, jak přiřadit uživatele nebo skupiny k aplikaci v Azure Active Directory (Azure AD). Uživatelé musí být přiřazen k aplikaci předtím, než správce můžete jim udělit přístup k následujícím:

-   Přístup k aplikaci získáte tak, že **přejdete přímo na adresu URL aplikace** (označuje se také jako přihlašování inicializované pomocí protokolu SP).

-   Přístup k aplikaci pomocí **adresy URL přístupu uživatele** na stránce **vlastností** aplikace (označuje se také jako přihlášení iniciované IDP).

-   Podívejte se, že se aplikace zobrazuje na svém [přístupovém panelu aplikace](https://myapps.microsoft.com/) nebo v mobilní aplikaci.

-   Podívejte se, že se ve [Spouštěči aplikací Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)zobrazí aplikace.

Dostupnost přiřazení na základě skupin je určená vaší licenční smlouvou. Přiřazení na základě skupin se podporuje jenom pro skupiny zabezpečení. Vnořené členství ve skupinách a skupiny O365 se momentálně nepodporují.

## <a name="configure-the-application-to-require-assignment"></a>Konfigurace aplikace tak, aby vyžadovala přiřazení

Aplikaci lze nakonfigurovat tak, aby vyžadovala přiřazení před tím, než bude k ní možné přistupovat. Vyžadovat přiřazení:

1. Přihlaste se k Azure Portal s účtem správce nebo jako vlastníkem aplikace v části **podnikové aplikace**.
2. V hlavní nabídce klikněte na položku **všechny služby** .
3. Vyberte adresář, který používáte pro aplikaci.
4. Klikněte na kartu **podnikové aplikace** .
5. Vyberte aplikaci ze seznamu aplikací, které jsou přidružené k tomuto adresáři.
6. Klikněte na kartu **vlastnosti** .
7. Změňte **požadované přiřazení uživatele?** přepněte na Ano.
8. V horní části obrazovky klikněte na tlačítko **Uložit** .

## <a name="assign-users"></a>Přiřazení uživatelů

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce nebo jako vlastník aplikace bez oprávnění správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní nabídky navigace na levé straně.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

    * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Jakmile se aplikace načte, klikněte na **Uživatelé a skupiny** v navigační nabídce aplikace na levé straně.

8.  Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9.  v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a **příjmení** nebo **e-mailovou adresu** uživatele, kterého se zajímáte o přiřazení.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **uživatele** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga uživatele přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e-mailová** adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** kliknutím na výběr **role** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte vybraným uživatelům.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace pomocí metod popsaných v části popis řešení.

## <a name="assign-groups"></a>Přiřazení skupin

Jednu nebo více skupin přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo jako vlastník aplikace bez oprávnění správce s přiřazenou licencí Azure AD Premium.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní nabídky navigace na levé straně.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

    * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Jakmile se aplikace načte, klikněte na **Uživatelé a skupiny** v navigační nabídce aplikace na levé straně.

8.  Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9.  v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte **úplný název skupiny** , které vás zajímá.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **skupinu** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga skupiny přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Chcete-li **Přidat více než jednu skupinu**, zadejte do pole **Hledat podle jména nebo e-mailové adresy** další **název celé skupiny** a kliknutím na zaškrtávací políčko přidejte tuto skupinu do **vybraného** seznamu.

13. Po dokončení výběru skupin klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** Pokud chcete vybrat roli, kterou chcete přiřadit vybraným skupinám, klikněte v podokně **Přidat přiřazení** na výběr **role vybrat** .

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte k vybraným skupinám.

Po krátké době budou uživatelé v rámci vybraných skupin moci spouštět tyto aplikace pomocí metod popsaných v části popis řešení. Pokud jsou dynamické skupiny, může být některé další zpracování zpoždění v tato přiřazení pro uživatele v rámci těchto přiřazené skupiny.

## <a name="enable-self-service-application-access"></a>Povolení samoobslužného přístupu k aplikacím

Obchodní skupině schvalovat přístup k těmto aplikacím samoobslužného přístupu k aplikacím je skvělý způsob, jak povolit uživatelům samoobslužné zjišťování aplikací, volitelně povolit. Můžete povolit obchodní skupiny pro správu přiřazené těmto uživatelům pro heslo jednotné přihlašování v aplikacích vpravo od svých přístupových panelech přihlašovací údaje.

Povolení samoobslužného přístupu k aplikaci, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní nabídky navigace na levé straně.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete povolit samoobslužné služby přístup ze seznamu.

7. Po načtení aplikace klikněte v navigační nabídce aplikace na **Samoobslužná služba** .

8. Chcete-li pro tuto aplikaci povolit Samoobslužný přístup k aplikacím, zapněte možnost **Povolit uživatelům žádat o přístup k této aplikaci?** přepněte na **Ano.**

9. Pokud chcete vybrat skupinu, do které chcete přidat uživatele, kteří požadují přístup k této aplikaci, klikněte na selektor vedle popisku, **ke kterému by se měla přiřadit skupina uživatelů?** a vyberte skupinu.

10. **Volitelné:** Pokud chcete před povolením přístupu k této aplikaci vyžadovat obchodní schválení, nastavte **před udělením přístupu k této aplikaci oprávnění vyžadovat schválení?** přepněte na **Ano**.

11. **Volitelné: u aplikací, které používají jenom jednotné přihlašování,** Pokud chcete těmto obchodním schvalovatelům udělit hesla, která se do této aplikace odesílají pro schválené uživatele, nastavte pro **tuto aplikaci** hesla, která chcete u této aplikace odeslat? přepněte na **Ano**.

12. **Volitelné:** Pokud chcete určit obchodní schvalovatele, kteří mají povolený přístup k této aplikaci, klikněte na selektor vedle popisku **, který má povolený souhlas s přístupem k této aplikaci?** Pokud chcete vybrat až 10 individuálních obchodních schvalovatelů.

    >[!NOTE]
    >Skupiny se nepodporují.
    >
    >

13. **Volitelné:** **u aplikací, které zveřejňují role**. Pokud chcete roli přiřadit schválené uživatele samoobslužné služby, klikněte na selektor vedle **role, které by se měly přiřazovat uživatelům v této aplikaci. Pokud** chcete vybrat roli, ke které se tito uživatelé mají přiřadit.

14. Kliknutím na tlačítko **Uložit** v horní části podokna dokončíte.

Jakmile dokončíte konfiguraci samoobslužné aplikace, uživatelé můžou přejít na svůj [přístupový panel aplikace](https://myapps.microsoft.com/) a kliknout na tlačítko **+ Přidat** a najít tak aplikace, na které jste povolili Samoobslužný přístup. Obchodní schvalovatelé také uvidí oznámení na svém [přístupovém panelu aplikací](https://myapps.microsoft.com/). Můžete povolit e-mailu oznamující, že uživatel má požádali o přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporují pouze pracovní postupy s jedním schválením, což znamená, že pokud zadáte více schvalovatelů, může každý jednotlivý schvalovatel schválit přístup k aplikaci.

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)

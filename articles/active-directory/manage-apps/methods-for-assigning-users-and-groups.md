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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443353"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Přiřazení uživatelů a skupin k aplikaci v Azure Active Directory
Tento článek ukazuje, jak přiřadit uživatele nebo skupiny k aplikaci v Azure Active Directory (Azure AD). Uživatelé musí být přiřazen k aplikaci předtím, než správce můžete jim udělit přístup k následujícím:

-   Přístup k aplikaci pomocí **přejdete na adresu URL aplikace přímo** (označované také jako iniciovaného Zprostředkovatelem přihlašování přihlášení).

-   Přístup k aplikaci pomocí **adresa URL portálu User Access** na aplikace **vlastnosti** stránky (označované také jako zahájené pomocí IDP přihlášení).

-   V tématu aplikace se zobrazí na jejich [přístupového panelu aplikací](https://myapps.microsoft.com/) nebo mobilních aplikací.

-   V tématu aplikace se zobrazí na jejich [Spouštěče aplikací Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

Dostupnost přiřazení na základě skupin je určená vaší licenční smlouvou. Přiřazení na základě skupin se podporuje jenom pro skupiny zabezpečení. Vnořené členství ve skupinách a skupiny O365 se momentálně nepodporují.

## <a name="configure-the-application-to-require-assignment"></a>Konfigurace aplikace tak, aby vyžadovala přiřazení

Aplikaci lze nakonfigurovat tak, aby vyžadovala přiřazení před tím, než bude k ní možné přistupovat. Vyžadovat přiřazení:

1. Přihlaste se k Azure Portal s účtem správce nebo jako vlastníkem aplikace v části **podnikové aplikace**.
2. Klikněte na **všechny služby** položku v hlavní nabídce.
3. Vyberte adresář, který používáte pro aplikaci.
4. Klikněte na **podnikové aplikace** kartu.
5. Vyberte aplikaci ze seznamu aplikací, které jsou přidružené k tomuto adresáři.
6. Klikněte na tlačítko **vlastnosti** kartu.
7. Změnit **přiřazení uživatelů povinné?** přepnout na Ano.
8. Klikněte na tlačítko **Uložit** tlačítko v horní části obrazovky.

## <a name="assign-users"></a>Přiřazení uživatelů

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce nebo jako vlastník aplikace bez oprávnění správce.**

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

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle názvu nebo e-mailová adresa** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace pomocí metod popsaných v části popis řešení.

## <a name="assign-groups"></a>Přiřazení skupin

Jednu nebo více skupin přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo jako vlastník aplikace bez oprávnění správce s přiřazenou licencí Azure AD Premium.

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

12. **Volitelné:** Pokud byste chtěli **přidat více než jednu skupinu**, typ v jiném **název celé skupiny** do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole a Klikněte na zaškrtávací políčko k přidání do této skupiny **vybrané** seznamu.

13. Když jste hotovi s výběrem skupin, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit ke zvoleným skupinám, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko a přiřazení aplikace k vybraným skupinám.

Po krátké době budou uživatelé v rámci vybraných skupin moci spouštět tyto aplikace pomocí metod popsaných v části popis řešení. Pokud jsou dynamické skupiny, může být některé další zpracování zpoždění v tato přiřazení pro uživatele v rámci těchto přiřazené skupiny.

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

10. **Volitelné:** Pokud budete chtít vyžadovat schválení firmy před uživatelé mají povolen přístup, nastavte **vyžadovat schválení před udělením přístupu k této aplikaci?** přepnutím **Ano**.

11. **Volitelné: pro aplikace používající jednotné přihlašování heslem, pouze na** Pokud chcete povolit tyto firemními schvalovateli k zadání hesla, které se odesílají na tuto aplikaci pro schválené uživatele, nastavte **povolit schvalovatelům nastavovat uživatele hesla pro tuto aplikaci?**  přepnutím **Ano**.

12. **Volitelné:** k určení firemními schvalovateli, kteří se může schvalovat přístup k této aplikaci, klepněte na volič vedle popisku **kdo může schvalovat přístup k této aplikaci?** vybrat až 10 osoba firemními schvalovateli.

    >[!NOTE]
    >Skupiny se nepodporují.
    >
    >

13. **Volitelné:** **pro aplikace, které zpřístupňují role**, pokud chcete přiřadit k roli schválených uživatelů samoobslužných služeb, klepněte na volič vedle **ke které roli by měla být přiřazena uživatelům v této aplikaci?** Vyberte roli, ke kterému by se měla přiřadit těmto uživatelům.

14. Klikněte na tlačítko **Uložit** tlačítko v horní části podokna na dokončení.

Po dokončení konfigurace samoobslužné služby aplikace, uživatelé mohou přejít na jejich [přístupového panelu aplikací](https://myapps.microsoft.com/) a klikněte na tlačítko **+ přidat** tlačítko a hned uvidíte aplikace, které jste povolili samoobslužné funkce přístup. Firemními schvalovateli oznámení se zobrazí také v jejich [přístupového panelu aplikací](https://myapps.microsoft.com/). Můžete povolit e-mailu oznamující, že uživatel má požádali o přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporují pouze pracovní postupy s jedním schválením, což znamená, že pokud zadáte více schvalovatelů, může každý jednotlivý schvalovatel schválit přístup k aplikaci.

## <a name="next-steps"></a>Další kroky
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

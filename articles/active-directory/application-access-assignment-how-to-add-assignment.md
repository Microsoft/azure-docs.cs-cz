---
title: Přiřazení uživatelů a skupin k aplikaci | Microsoft Docs
description: Přiřazení uživatelů k aplikaci udělit přístup
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 19303237e7e2c3c3a7f373102465875c34a571d1
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="how-to-assign-users-and-groups-to-an-application"></a>Přiřazení uživatelů a skupin k aplikaci

Než mohou uživatelé provádět žádné z následujících pro konkrétní aplikaci, musíte první **je přiřadit k aplikaci** a udělit mu přístup:

-   Přístup k aplikaci pomocí **přejdete na adresu URL aplikace přímo** (také označované jako spouštěná SP přihlášení).

-   Přístup k aplikaci pomocí **adresu URL pro přístup uživatelů** na aplikace **vlastnosti** stránky (také označované jako spouštěná IDP přihlašování).

-   V tématu aplikace se zobrazují v jejich [Panel přístupu aplikace](https://myapps.microsoft.com/) nebo mobilních aplikací.

-   V tématu aplikace se zobrazují v jejich [Spouštěč aplikace Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="methods-to-assign-applications-with-azure-active-directory"></a>Metody přiřadit aplikací s Azure Active Directory 

Aplikace s Azure Active Directory můžete přiřadit 3 způsoby:

-   [Přiřazení uživatele přímo k aplikaci jako správce](#assign-a-user-directly-as-an-administrator)

-   [Přiřazení skupiny přímo na aplikace jako správce](#assign-a-group-directly-to-an-application-as-an-administrator)

-   [Povolit přístup k aplikaci Samoobslužné služby umožnit uživatelům najít vlastní aplikace](#enable-self-service-application-access-to-allow-users-to-find-their-own-applications)

## <a name="assign-a-user-directly-as-an-administrator"></a>Přiřazení uživatele přímo jako správce

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části navigační nabídce vlevo hlavní.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** v navigační nabídce vlevo Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** navigační nabídce vlevo aplikace.

8.  Klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu otevřete **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele vás zajímá přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, zadejte v jiném **celý název** nebo **e-mailová adresa** do **vyhledávání podle názvu nebo e-mailová adresa** pole pro vyhledávání a klikněte na zaškrtávací políčko, chcete-li přidat tento uživatel **vybrané** seznamu.

13. Po dokončení výběru uživatelů klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** selektor v **přidat přiřazení** podokně vyberte roli přiřadit uživatele, který jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřadit aplikace pro vybraného uživatele.

Po krátké době uživatele, které jste vybrali moci spustit tyto aplikace pomocí metody popsané v části popis řešení.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Přiřazení skupiny přímo na aplikace jako správce

Jeden nebo více skupin přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části navigační nabídce vlevo hlavní.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** v navigační nabídce vlevo Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** navigační nabídce vlevo aplikace.

8.  Klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu otevřete **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **název úplné skupiny** vás zajímá přiřazení do skupiny **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **skupiny** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do skupiny **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jednu skupinu**, typ v jiném **název úplné skupiny** do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole a Klikněte na zaškrtávací políčko k přidání do této skupiny **vybrané** seznamu.

13. Po dokončení výběru skupiny klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte **vybrat roli** selektor v **přidat přiřazení** podokně vyberte role pro přiřazení do skupin, které jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřazení aplikace k vybraným skupinám.

Po krátké době uživatelů v rámci skupiny, které jste vybrali moci spustit tyto aplikace pomocí metody popsané v části popis řešení. Pokud jsou dynamické skupiny, může být zpoždění některé další zpracování v těchto přiřazení zobrazování pro uživatele v rámci těchto přiřazeny skupiny.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Povolit přístup k aplikaci Samoobslužné služby umožnit uživatelům najít vlastní aplikace

Přístup k aplikaci Samoobslužné služby je skvělým způsobem, jak povolit uživatelům samoobslužné zjišťování aplikací, můžete povolit obchodní skupiny můžete schválit přístup pro tyto aplikace. Můžete povolit obchodní skupině pro správu přiřazené pro tyto uživatele pro heslo jednotné přihlašování v aplikacích vpravo z jejich přístup panelů přihlašovací údaje.

Pokud chcete povolit samoobslužné služby aplikaci přístup k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části navigační nabídce vlevo hlavní.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** v navigační nabídce vlevo Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

   * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, které chcete povolit samoobslužné přístup ze seznamu.

7.  Po načtení aplikace, klikněte na **samoobslužné služby** navigační nabídce vlevo aplikace.

8.  Pokud chcete povolit přístup k aplikaci Samoobslužné služby pro tuto aplikaci, zapněte **povolit uživatelům žádat o přístup k této aplikaci?** přepnutím **Ano.**

9.  V dalším kroku vyberte skupiny, které uživatelům, kteří požadují by se měl přístup k této aplikaci přidat, klikněte na tlačítko modulu pro výběr vedle popisek **skupinu, pro kterou má přiřazené byli přidáni uživatelé?** a vyberte skupinu.

10. **Volitelné:** nastaví, pokud chcete vyžadovat schválení obchodní před uživatelé mají povolen přístup **vyžadovat schválení před udělením přístupu k této aplikaci?** přepnutím **Ano**.

11. **Volitelné: pro aplikace pomocí hesla jednotné přihlašování na pouze** Pokud chcete povolit tyto firmy schvalovatelů k zadání hesla, které se odesílají na tuto žádost o schválení uživatelé, nastavte **povolit schvalovatelů nastavit uživatele hesla pro tuto aplikaci?**  přepnutím **Ano**.

12. **Volitelné:** pro zadání schvalovatelů firmy, kteří mají povoleno schválit přístup k této aplikaci, klikněte na výběr vedle popisek **kdo může schválit přístup k této aplikaci?** vybrat jednotlivé až 10 obchodní schvalovatelů.

  >[!NOTE]
  >Skupiny nejsou podporovány.
  >
  >

13. **Volitelné:** **pro aplikace, které zveřejňují role**, pokud chcete přiřadit roli schválené uživatelé samoobslužné služby, klikněte na modulu pro výběr vedle **do role, které by měl být přiřazena uživatelům v této aplikaci?** Vyberte roli, ke kterému by se měla přiřadit těmto uživatelům.

14. Klikněte **Uložit** tlačítka v horní části podokna ukončíte.

Po dokončení konfigurace samoobslužné služby aplikace, uživatelé mohou přejít na jejich [Panel přístupu aplikace](https://myapps.microsoft.com/) a klikněte na tlačítko **+ přidat** tlačítko k vyhledání aplikace, na které jste povolili samoobslužné služby přístup. Obchodní schvalovatelů také zobrazit oznámení v jejich [Panel přístupu aplikace](https://myapps.microsoft.com/). Můžete povolit e-mail s upozorněním, když uživatel požaduje přístup k aplikaci, která vyžaduje schválení. 

Tato schválení podporovat pouze jeden schválení pracovních, což znamená, že pokud zadáte několik schvalovatelů, může jeden schvalovatel schvalovatel přístup k aplikaci.

## <a name="next-steps"></a>Další postup
[Zadejte jednotné přihlašování pro vaše aplikace s Proxy aplikace](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)

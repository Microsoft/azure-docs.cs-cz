---
title: Jednotné přihlašování pomocí hesla (SSO) na přístupovém panelu | Dokumenty společnosti Microsoft
description: Popisuje problémové oblasti, které poskytují pokyny k řešení problémů souvisejících s přihlášením k aplikacím Azure AD Gallery nakonfigurovaným pro jednotné přihlašování heslem.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381234"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problémy s přihlášením k aplikaci Azure AD Gallery nakonfigurované pro jednotné přihlašování hesla

Přístupový panel je webový portál, který umožňuje uživateli, který má pracovní nebo školní účet ve službě Azure Active Directory (Azure AD) zobrazit a spustit cloudové aplikace, které jim správce Azure AD udělil přístup. Uživatel, který má edice Azure AD, může také používat samoobslužné možnosti správy skupin a aplikací prostřednictvím přístupového panelu. Přístupový panel je oddělený od portálu Azure a nevyžaduje, aby uživatelé měli předplatné Azure.

Chcete-li na přístupovém panelu používat jednotné přihlašování založené na heslech (SSO), musí být rozšíření přístupového panelu nainstalováno v prohlížeči uživatele. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurována pro zabezpečení založené na heslech.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Splnění požadavků prohlížeče na přístupovém panelu

Přístupový panel vyžaduje prohlížeč, který podporuje JavaScript a má povoleno CSS. Chcete-li na přístupovém panelu používat jednotné přihlašování založené na heslech (SSO), musí být rozšíření přístupového panelu nainstalováno v prohlížeči uživatele. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurována pro zabezpečení založené na heslech.

Pro heslo založené na přisazože, prohlížeče koncového uživatele může být:

-   Internet Explorer 8, 9, 10, 11 -- ve Windows 7 nebo novějším

-   Chrome – ve Windows 7 nebo novějším a v MacOS X nebo novějším

-   Firefox 26.0 nebo novější – v systému Windows XP SP2 nebo novějším a v systému Mac OS X 10.6 nebo novějším

>[!NOTE]
>Rozšíření s přistaň založené na heslech k dispozici pro Microsoft Edge v systému Windows 10, když rozšíření prohlížeče stanou podporovány pro Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Jak nainstalovat rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte podle následujících kroků:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve svém Azure AD.

2.  klikněte na **aplikaci s heslem a spřimaže** na přístupovém panelu.

3.  V řádku s žádostí o instalaci softwaru vyberte **možnost Nainstalovat .**

4.  Na základě vašeho prohlížeče budete přesměrováni na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se váš prohlížeč zeptá, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a zjistěte, jestli můžete **spustit** aplikace s heslem a semaforem

Rozšíření pro Chrome a Firefox si můžete také stáhnout z přímých odkazů níže:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefoxu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro aplikaci Internet Explorer

Můžete nastavit zásady skupiny, které umožňují vzdáleně nainstalovat rozšíření přístupového panelu pro aplikaci Internet Explorer do počítačů uživatelů.

Mezi předpoklady patří:

-   Nastavili jste [službu Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)a připojili jste počítače uživatelů k doméně.

-   Chcete-li upravit objekt zásad skupiny (GPO), musíte mít oprávnění Upravit nastavení. Ve výchozím nastavení mají členové následujících skupin zabezpečení toto oprávnění: Správci domény, Podnikoví správci a Vlastníci tvůrců zásad skupiny. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Postupujte podle kurzu [Jak nasadit rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) postupujte krok za krokem, jak nakonfigurovat zásady skupiny a nasadit je uživatelům.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Poradce při potížích s přístupovým panelem v aplikaci Internet Explorer

Postupujte [podle pokynů pro řešení potíží s rozšířením přístupového panelu pro aplikaci Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) a získejte přístup k diagnostickému nástroji a krok za krokem ke konfiguraci rozšíření pro aplikaci Internet E.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurace jednotného přihlašování hesla pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci z galerie Azure AD, musíte:

-   [Přidání aplikace bez galerie](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování pomocí hesla](#configure-the-application-for-password-single-sign-on)

-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Přidání aplikace bez galerie

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevření [portálu Azure](https://portal.azure.com) a přihlášení jako **globální správce** nebo **spolusprávce**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4.  V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5.  klikněte na tlačítko **Přidat** v pravém horním rohu v podokně **Podnikové aplikace.**

6.  Klikněte na **Aplikace bez galerie.**

7.  Do textového pole **Název** zadejte název aplikace. Vyberte **Přidat.**

Po krátké době budete moci zobrazit konfigurační podokno aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování pomocí hesla

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. Vyberte režim **Přihlašování na základě hesla.**

9. Zadejte **přihlašovací adresu URL**. Toto je adresa URL, na které uživatelé zadávají své uživatelské jméno a heslo pro přihlášení. Ujistěte se, že přihlašovací pole jsou zobrazena na adrese URL.

10. Přiřaďte uživatelé k aplikaci.

11. Kromě toho můžete také zadat pověření jménem uživatele výběrem řádků uživatelů a kliknutím na **aktualizovat pověření** a zadáním uživatelského jména a hesla jménem uživatelů. V opačném případě budou uživatelé při spuštění vyzváni k zadání pověření sami.

### <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

Chcete-li k aplikaci přiřadit jednoho nebo více uživatelů přímo, postupujte podle následujících kroků:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** z levé navigační nabídky aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **Uživatelé a skupiny** otevřete podokno **Přidat přiřazení.**

9. V podokně **Přidat přiřazení** klikněte na volič uživatelů **a skupin.**

10. Zadejte **celé jméno** nebo **e-mailovou adresu** uživatele, kterého chcete přiřadit do vyhledávacího pole **vyhledávání podle jména nebo e-mailové adresy.**

11. Najeďte na **uživatele** v seznamu a zvedejte **zaškrtávací políčko**. Kliknutím na zaškrtávací políčko vedle profilové fotky nebo loga uživatele přidáte uživatele do seznamu **Vybrané.**

12. **Nepovinné:** Pokud chcete **přidat více uživatelů**, zadejte do vyhledávacího pole Hledat podle jména nebo **e-mailové adresy** jiné celé **jméno** nebo **e-mailovou adresu** a kliknutím na toto políčko přidejte tohoto uživatele do seznamu **Vybrané.**

13. Po dokončení výběru uživatelů je klepnutím na tlačítko **Vybrat** přidejte do seznamu uživatelů a skupin, které mají být přiřazeny k aplikaci.

14. **Volitelné:** Kliknutím na volič **role v** podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Klepnutím na tlačítko **Přiřadit** přiřadíte aplikaci vybraným uživatelům.

Po krátké době budou moci vybraní uživatelé tyto aplikace spustit na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky řešení potíží problém nevyřeší

otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (e-mailová adresa uživatele)

-   ID tenanta

-   Typ prohlížeče

-   Časové pásmo a časový rámec během chyby

-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)


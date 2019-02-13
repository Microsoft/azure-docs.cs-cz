---
title: Potíže při přihlašování k aplikaci pomocí odkazu deeplink | Dokumentace Microsoftu
description: Jak řešit problémy s přístupem k aplikaci z adresy URL odkazu deeplink systému pomocí služby Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b9cf271f694c0ea5da8996baac2261684e66d83
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56217883"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Potíže při přihlašování k aplikaci pomocí odkazu deeplink

Přístupový Panel je webový portál, který umožňuje uživateli pomocí pracovního nebo školního účtu ve službě Azure Active Directory (Azure AD) k zobrazení a spuštění aplikace založené na cloudu, že správce Azure AD udělil jim přístup k. 

Tyto aplikace jsou konfigurovány jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurovali a přiřadili pro uživatele nebo skupiny, jejichž je uživatel členem skupiny a prohlédněte si aplikaci na přístupovém panelu.

Přímé odkazy nebo uživatelský přístup adresy URL jsou odkazy, které uživatelé můžou použít pro přístup k jejich hesla jednotného přihlašování aplikace přímo z jejich prohlížeče adresa URL pruhy. Přejděte na tento odkaz, uživatelé nebudou automaticky do aplikace přihlásili bez nutnosti nejprve přejděte na přístupovém panelu. Toto je stejný odkaz, který uživatelé použít k těmto aplikacím přistupují ze Spouštěče aplikací Office 365.

## <a name="general-issues-to-check-first"></a>Obecné problémy a proveďte nejprve kontrolu

-   Ujistěte se, že vaše používání **prohlížeče** , který splňuje minimální požadavky na přístupový Panel.

-   Ujistěte se, že v prohlížeči uživatele bylo přidáno adresu URL aplikace do jeho **Důvěryhodné servery**.

-   Ujistěte se, že chcete zkontrolovat, že aplikace je **nakonfigurované** správně.

-   Ujistěte se, že účet uživatele je **povolené** pro přihlášení.

-   Ujistěte se, že účet uživatele je **není uzamčen.**

-   Ujistěte se, že uživatele **není platnost vypršela nebo zapomněli heslo.**

-   Ujistěte se, že **ověřování službou Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Ujistěte se, že **zásady podmíněného přístupu** nebo **Identity Protection** zásad neblokuje přístup uživatelů.

-   Ujistěte se, že uživatele **kontaktní údaje pro ověření** neustále aktuální, aby ověřování službou Multi-Factor Authentication nebo podmíněného přístupu zásady vynucení.

-   Ujistěte se, že k také zkuste vymazání souborů cookie v prohlížeči a pokoušel se přihlásit znovu.

## <a name="checking-the-deeplink"></a>Kontroluje se odkazu deeplink systému

Pokud chcete zkontrolovat, jestli máte správnou odkazu deeplink systému, postupujte podle těchto kroků:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

7.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

8.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

9.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

10. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

11. Vyberte aplikaci, kterou chcete odkazu deeplink systému pro kontroly.

12. Najít popisek **adresa URL portálu User Access**. Tato adresa URL by měla odpovídat vaší odkazu deeplink systému.

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče Panel přístupu

Pokud chcete nainstalovat rozšíření prohlížeče Panel přístupu, postupujte takto:

1.  Otevřít [přístupového panelu](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatele** ve službě Azure AD.

2.  Klikněte na tlačítko **heslem jednotného přihlašování aplikace** na přístupovém panelu.

3.  V příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat**.

4.  Založené na prohlížeči, které jsou směrované na odkaz ke stažení. **Přidat** rozšíření do prohlížeče.

5.  Pokud vás prohlížeč vyzve, vyberte buď **povolit** nebo **povolit** rozšíření.

6.  Po instalaci **restartovat** relace prohlížeče.

7.  Přihlaste se na přístupovém panelu a zobrazit, pokud můžete **spuštění** vaše heslo jednotného přihlašování aplikace

Rozšíření lze stáhnout z těchto přímé odkazy taky pro Chrome a Firefox:

-   [Rozšíření Chrome přístupového panelu](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření Firefox přístupového panelu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Postup konfigurace hesel jednotného přihlašování pro aplikaci Galerie Azure AD

Pokud chcete nakonfigurovat aplikaci z Galerie Azure AD, musíte mít:

-   [Přidat aplikaci z Galerie Azure AD](#add-an-application-from-the-Azure-AD-gallery)

-   [Konfigurace aplikace pro heslo jednotného přihlašování](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte takto:

1.  Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  V **zadejte název** textové pole z **přidat z Galerie** části, zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace, můžete změnit její název ze **název** textového pole.

9.  Chcete-li tuto aplikaci přidat, klikněte na tlačítko **přidat**.

Po krátké době budete moct zobrazit podokno konfiguračních vaší aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  [Přiřazení uživatelů k aplikaci](#how-to-assign-a-user-to-an-application-directly).

10. Kromě toho můžete také zadat přihlašovací údaje jménem uživatele výběr řádků uživatele a kliknutím na **aktualizaci přihlašovacích údajů** a zadáním uživatelského jména a hesla jménem uživatele. V opačném případě uživatelé vyzváni k zadání samotné přihlašovací údaje při spuštění.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Postup konfigurace hesel jednotného přihlašování pro aplikaci mimo Galerii

Pokud chcete nakonfigurovat aplikaci z Galerie Azure AD, musíte mít:

-   [Přidat aplikaci mimo Galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro heslo jednotného přihlašování](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo Galerii

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte takto:

1.  Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako **globálního správce** nebo **spolusprávce**.

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **přidat** tlačítko v pravém horním rohu **podnikové aplikace** podokně.

6.  Klikněte na tlačítko **aplikace mimo galerii.**

7.  Zadejte název vaší aplikace v **název** textového pole. Vyberte **přidat.**

Po krátké době budete moct zobrazit podokno konfiguračních vaší aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro heslo jednotného přihlašování

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

    1.  Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Vyberte režim **přihlašování na základě heslo.**

9.  Zadejte **přihlašovací adresa URL**, adresu URL, kde uživatelé zadat svoje uživatelské jméno a heslo pro přihlášení. Ujistěte se, že pole přihlášení jsou viditelné na adrese URL.

10. Přiřazení uživatelů k aplikaci.

11. Kromě toho můžete také zadat přihlašovací údaje jménem uživatele výběr řádků uživatele a kliknutím na **aktualizaci přihlašovacích údajů** a zadáním uživatelského jména a hesla jménem uživatele. V opačném případě uživatelé vyzváni k zadání samotné přihlašovací údaje při spuštění.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Jak přiřadit uživatele k aplikaci přímo

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele ze seznamu.

7.  Po načtení aplikace, klikněte na tlačítko **uživatelů a skupin** levé navigační nabídce aplikace.

8.  Klikněte na tlačítko **přidat** tlačítko nahoře **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** selektor z **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele zájem o přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu zobrazíte **zaškrtávací políčko**. Chcete-li přidat uživatele **vybrané** seznamu, klikněte na zaškrtávací políčko vedle loga nebo profilové fotky uživatele.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, typ v jiném **celý název** nebo **e-mailová adresa** do **hledat podle jména nebo e-mailové adresy** vyhledávací pole a klikněte na zaškrtávací políčko a přidáním tohoto uživatele do **vybrané** seznamu.

13. Po dokončení výběru uživatelů, klikněte na tlačítko **vyberte** tlačítko pro přidání do seznamu uživatelů a skupin pro přiřazení k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** oblasti pro výběr **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Klikněte na tlačítko **přiřadit** tlačítko přiřadit aplikaci do vybraného uživatele.

Po krátké době uživatele, které jste vybrali dát spustit tyto aplikace na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží není vyřešit problém. 

vytvořit lístek podpory s použitím následujících informací, pokud je k dispozici:

-   Chyba ID korelace

-   Hlavní název uživatele (uživatel e-mailová adresa)

-   ID Tenanta

-   Typ prohlížeče

-   Vyvolá se časové pásmo a čas nebo časový rámec při chybě

-   Trasování Fiddler

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

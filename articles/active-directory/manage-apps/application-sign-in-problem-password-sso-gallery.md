---
title: Problém s přihlášením k aplikaci Galerie Azure AD nakonfigurované pro služby Přihlašování | Dokumenty společnosti Microsoft
description: Jak řešit problémy s aplikací Azure AD Gallery, která je nakonfigurovaná pro jednotné přihlašování hesla.
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
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381306"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problémy s přihlášením pomocí aplikace Galerie Azure AD nakonfigurované pro služby Přihlašování

Přístupový panel je webový portál. Umožňuje uživatelům, kteří mají pracovní nebo školní účty Azure Active Directory (Azure AD), přístup ke cloudovým aplikacím, ke kterým mají oprávnění. Uživatelé, kteří mají edice Azure AD, můžou taky používat samoobslužné skupiny a možnosti správy aplikací prostřednictvím přístupového panelu.

Přístupový panel je oddělený od portálu Azure. Uživatelé k používání přístupového panelu nepotřebují předplatné Azure.

Chcete-li na přístupovém panelu používat jednotné přihlašování na základě hesla (SSO), musí být v prohlížeči nainstalováno rozšíření přístupového panelu. Rozšíření se automaticky stáhne, když vyberete aplikaci, která je nakonfigurovaná pro zabezpečení založené na heslech.

## <a name="browser-requirements-for-access-panel"></a>Požadavky prohlížeče pro přístupový panel

Přístupový panel vyžaduje prohlížeč, který podporuje JavaScript a má povoleno CSS.

Následující prohlížeče podporují zabezpečení založené na heslech:

- Internet Explorer 8, 9, 10 a 11 ve Windows 7 nebo novějším

- Chrome ve Windows 7 nebo novějším nebo na MacOS X nebo novějším

- Firefox 26.0 nebo novější v systému Windows XP SP2 nebo novějším nebo mac os x 10.6 nebo novějším

>[!NOTE]
>Rozšíření s přistaň založené na heslech k dispozici pro Microsoft Edge v systému Windows 10 při přidání podpory rozšíření prohlížeče do Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče přístupového panelu

Postupujte následovně:

1. Otevřete [přístupový panel](https://myapps.microsoft.com) v podporovaném prohlížeči a přihlaste se jako uživatel ve službě Azure AD.

2. Na přístupovém panelu vyberte aplikaci s podporou hesla SSO.

3. Po zobrazení výzvy vyberte **Instalovat .**

4. Budete přesměrováni na odkaz ke stažení na základě vašeho prohlížeče. Chcete-li nainstalovat rozšíření prohlížeče, vyberte **Přidat.**

5. Pokud se zobrazí výzva, vyberte **Povolit** nebo **Povolit**.

6. Po instalaci restartujte prohlížeč.

7.  Přihlaste se na přístupový panel a zjistěte, jestli můžete spustit aplikace s podporou hesla s heslem.

Můžete také přímo stáhnout rozšíření pro Chrome a Firefox prostřednictvím těchto odkazů:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefoxu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro Internet Explorer

Můžete nastavit zásady skupiny, které umožňují vzdáleně nainstalovat rozšíření přístupového panelu pro aplikaci Internet Explorer na počítačích uživatelů.

Jedná se o předpoklady:

-   [Služba Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) musí být nastavena a počítače uživatelů musí být připojeny k vaší doméně.

-   Máte oprávnění Upravit nastavení k úpravám objektu zásad skupiny (GPO). Ve výchozím nastavení mají členové následujících skupin zabezpečení toto oprávnění: Správci domény, Podnikoví správci a Vlastníci tvůrců zásad skupiny. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Pokud chcete nakonfigurovat zásady skupiny a nasadit je uživatelům, přečtěte si informace [o tom, jak nasadit rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Poradce při potížích s přístupovým panelem v aplikaci Internet Explorer

Informace o přístupu k diagnostickému nástroji a pokynům ke konfiguraci rozšíření naleznete [v tématu Poradce při potížích s rozšířením přístupového panelu pro aplikaci Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurace přizpůsobovaného zabezpečení hesla pro aplikaci galerie Azure AD

Pokud chcete nakonfigurovat aplikaci z galerie Azure AD, musíte udělat tyto věci:

-   Přidání aplikace z galerie Azure AD
-   [Konfigurace aplikace pro jednotné přihlašování pomocí hesla](#configure-the-app-for-password-sso)
-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Přidání aplikace z galerie Azure AD

Postupujte následovně:

1. Otevřete [portál Azure](https://portal.azure.com) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **Všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. V pravém horním rohu podokna **Podnikové aplikace** vyberte **Přidat.**

6. Do **oddílu Přidat z galerie** zadejte název aplikace do pole **Zadat název.**

7. Vyberte aplikaci, kterou chcete nakonfigurovat pro službu SSO.

8. *Nepovinné:* Před přidáním aplikace můžete změnit její název v poli **Název.**

9. Pokud chcete tuto aplikaci přidat, klikněte na **Přidat**.

   Po krátké prodlevě uvidíte konfigurační podokno aplikace.

### <a name="configure-the-app-for-password-sso"></a>Konfigurace aplikace pro heslo s připojetí hesla

Postupujte následovně:

1. Otevřete [portál Azure](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **Všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Výběrem **možnosti Všechny aplikace** zobrazíte seznam svých aplikací.

   > [!NOTE]
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace**. Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro službu SSO.

7. Po načtení aplikace vyberte **jednotné přihlašování** v podokně na levé straně aplikace.

8. Vyberte režim **přihlašování na základě hesla.**

9. Přiřaďte k aplikaci uživatele.

10. Můžete také zadat pověření pro uživatele. (V opačném případě budou uživatelé při spuštění aplikace vyzváni k zadání přihlašovacích údajů.) Chcete-li to provést, vyberte řádky uživatelů. Pak vyberte **Aktualizovat pověření** a zadejte jejich uživatelská jména a hesla.

### <a name="assign-users-to-the-app"></a>Přiřazení uživatelů k aplikaci

Pokud chcete přímo přiřadit uživatele k aplikaci, postupujte takto:

1. Otevřete [portál Azure](https://portal.azure.com/) a přihlaste se jako globální správce.

2. Vyberte **Všechny služby** v navigační bolesti na levé straně otevřít rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Chcete-li zobrazit seznam aplikací, vyberte **možnost Všechny aplikace.**

   > [!NOTE]
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace**. Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. V seznamu vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace vyberte **Uživatelé a skupiny** z navigačního podokna aplikace na levé straně.

8. Chcete-li otevřít podokno **Přidat přiřazení,** vyberte v horní části seznamu **Add** **Uživatelé a skupiny** přidat.

9. V podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny.**

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte celé jméno nebo e-mailovou adresu uživatele, kterého chcete přiřadit.

11. Najeďte na uživatele v seznamu. Zaškrtnutím políčka vedle profilové fotky nebo loga uživatele přidáte tohoto uživatele do seznamu **Vybrané.**

12. *Nepovinné:* Chcete-li přidat jiného uživatele, zadejte do pole **Hledat podle jména nebo e-mailové adresy** jiné jméno nebo e-mailovou adresu a zaškrtněte políčko pro přidání tohoto uživatele do seznamu **Vybrané.**

13. Po dokončení výběru uživatelů klikněte na **Vybrat** a přidejte je do seznamu uživatelů a skupin, kteří jsou k aplikaci přiřazeni.

14. *Nepovinné:* Kliknutím na **Vybrat roli** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Vyberte **Přiřadit,** chcete-li aplikaci přiřadit vybraným uživatelům.

    Po krátké prodlevě budou mít uživatelé přístup k těmto aplikacím z přístupového panelu.

## <a name="request-support"></a>Požádat o podporu 
Pokud se při nastavování přihlašování a přiřazování uživatelů zobrazí chybová zpráva, otevřete lístek podpory. Uveďte co nejvíce z následujících informací:

-   ID chyby korelace
-   UPN (e-mailová adresa uživatele)
-   ID tenanta
-   Typ prohlížeče
-   Časové pásmo a časový rámec, kdy došlo k chybě
-   Fiddler stopy

## <a name="next-steps"></a>Další kroky
[Poskytněte svým aplikacím jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

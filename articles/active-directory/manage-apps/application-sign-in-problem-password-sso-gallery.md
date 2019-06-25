---
title: Potíže s přihlášením k aplikaci Galerie Azure AD nakonfigurované pro heslem jednotného přihlašování | Dokumentace Microsoftu
description: Řešení potíží s aplikace Galerie Azure AD, který je nakonfigurovaný pro heslo jednotného přihlašování.
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
ms.openlocfilehash: 742df882fb64e09ff63ef2eceb5514ca070dc227
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190325"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problémy s přihlášením k aplikaci Galerie Azure AD nakonfigurované pro jednotné přihlašování

Přístupový Panel je webový portál. Umožňuje uživatelům, kteří si Azure Active Directory (Azure AD) pracovní nebo školní účty pro cloudové aplikace, ke kterým mají oprávnění pro přístup. Uživatelé, kteří mají edice Azure AD můžete použít rovněž samoobslužné skupiny a možnosti správy aplikací prostřednictvím přístupového panelu.

Přístupový Panel je nezávisle na webu Azure portal. Uživatelé nemusejí předplatné Azure použít přístupový Panel.

Použití založené na heslech jednotné přihlašování (SSO) v přístupovém panelu, musíte přístupový Panel rozšíření nainstalovat v prohlížeči. Rozšíření stáhne automaticky, když vyberete aplikaci, která je nakonfigurovaná pro jednotné přihlašování pomocí hesla.

## <a name="browser-requirements-for-access-panel"></a>Požadavky na prohlížeč pro přístupový Panel

Přístupový Panel vyžaduje prohlížeč, který podporuje JavaScript a má povolený šablon stylů CSS.

Jednotné přihlašování pomocí hesla podporují následující prohlížeče:

- Aplikace Internet Explorer 8, 9, 10 a 11 ve Windows 7 nebo novější

- Chrome ve Windows 7 nebo novější nebo v systému MacOS X nebo novější

- Firefox 26.0 nebo novější, Windows XP SP2 nebo novější nebo na Mac OS X 10.6 nebo novější

>[!NOTE]
>Rozšíření jednotné přihlašování pomocí hesla budou k dispozici pro Microsoft Edge ve Windows 10 při podpora byla přidána rozšíření prohlížeče Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Instalace rozšíření prohlížeče Panel přístupu

Postupujte následovně:

1. Otevřít [přístupového panelu](https://myapps.microsoft.com) v podporovaného prohlížeče a přihlaste se jako uživatel ve službě Azure AD.

2. Vyberte aplikaci povoleno jednotné přihlašování heslem v přístupovém panelu.

3. Po zobrazení výzvy, vyberte **nainstalovat**.

4. Budete přesměrováni na odkaz ke stažení založené na prohlížeči. Vyberte **přidat** k instalaci rozšíření prohlížeče.

5. Pokud budete vyzváni, vyberte **povolit** nebo **povolit**.

6. Po instalaci restartujte prohlížeč.

7.  Přihlaste se na přístupovém panelu a zobrazit, pokud můžete spustit vaše heslo podporou jednotného přihlašování aplikace.

Rozšíření pro Chrome a Firefox můžete také přímo stáhnout pomocí těchto odkazů:

-   [Rozšíření Chrome přístupového panelu](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření Firefox přístupového panelu](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro aplikaci Internet Explorer

Můžete nastavit zásady skupiny, který umožňuje vzdálenou instalaci rozšíření přístupový Panel pro aplikaci Internet Explorer v počítačích uživatelů.

Tady jsou uvedeny požadavky:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) musí být nastavené, a vaši uživatelé počítačů musí být připojené k vaší doméně.

-   Máte oprávnění "Upravit nastavení" k úpravě objektu zásad skupiny (GPO). Ve výchozím nastavení mají toto oprávnění členy z těchto skupin zabezpečení: Domain Administrators, Enterprise Administrators a Group Policy Creator Owners. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Pokud chcete nakonfigurovat zásady skupiny a nasadit pro uživatele, najdete v článku [nasazení rozšíření přístupový Panel pro aplikaci Internet Explorer pomocí zásad skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Řešení potíží s přístupového panelu v aplikaci Internet Explorer

Pro přístup k nástroji Diagnostika a pokyny ke konfiguraci rozšíření, naleznete v tématu [řešení potíží s rozšířením přístupový Panel pro aplikaci Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurace heslem jednotného přihlašování pro aplikaci Galerie Azure AD

Jak nakonfigurovat aplikaci z Galerie Azure AD, musíte udělat tyto věci:

-   Přidat aplikaci z Galerie Azure AD
-   [Konfigurace aplikace pro heslo jednotného přihlašování](#configure-the-app-for-password-sso)
-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Přidat aplikaci z Galerie Azure AD

Postupujte následovně:

1. Otevřít [webu Azure portal](https://portal.azure.com) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **přidat** v pravém horním rohu **podnikové aplikace** podokně.

6. V **přidat z Galerie** části, zadejte název aplikace **zadejte název** pole.

7. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

8. *Volitelné:* Abyste mohli přidat aplikaci, můžete změnit jeho název v **název** pole.

9. Klikněte na tlačítko **přidat** a přidejte tak aplikaci.

   Po krátké prodlevě budete moci zobrazit podokno konfiguračních aplikace.

### <a name="configure-the-app-for-password-sso"></a>Konfigurace aplikace pro heslem jednotného přihlašování

Postupujte následovně:

1. Otevřít [webu Azure portal](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací**. Nastavte **zobrazit** možnost "Všech aplikací."

6. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7. Po dokončení načítání aplikace, vyberte **jednotného přihlašování** v podokně na levé straně aplikace.

8. Vyberte **přihlašování na základě heslo** režimu.

9. Přiřazení uživatelů k aplikaci.

10. Můžete také zadat pověření pro uživatele. (V opačném případě uživatelé vyzváni k zadání přihlašovacích údajů při spuštění aplikace.) K tomuto účelu výběr řádků uživatele. Potom vyberte **aktualizaci přihlašovacích údajů** a zadejte své uživatelské jméno a heslo.

### <a name="assign-users-to-the-app"></a>Přiřazení uživatelů k aplikaci

Přiřazení uživatelů k aplikaci přímo, postupujte podle těchto kroků:

1. Otevřít [webu Azure portal](https://portal.azure.com/) a přihlaste se jako globální správce.

2. Vyberte **všechny služby** v navigační bolest na levé straně otevřete rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu vašich aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací**. Nastavte **zobrazit** možnost "Všech aplikací."

6. V seznamu vyberte, kterou chcete přiřadit uživatele k aplikaci.

7. Po načtení aplikace, vyberte **uživatelů a skupin** z aplikace navigačního podokna na levé straně.

8. Vyberte **přidat** v horní části **uživatelů a skupin** seznamu a otevře **přidat přiřazení** podokně.

9. Vyberte **uživatelů a skupin** v **přidat přiřazení** podokně.

10. V **hledat podle jména nebo e-mailové adresy** zadejte úplný název nebo e-mailová adresa uživatele, kterého chcete přiřadit.

11. Najeďte myší uživatele v seznamu. Zaškrtněte políčko vedle profilové fotky nebo logo, které chcete přidat tohoto uživatele pro uživatele **vybrané** seznamu.

12. *Volitelné:* Chcete-li přidat jiného uživatele, zadejte jiné jméno nebo e-mailovou adresu v **hledat podle jména nebo e-mailové adresy** pole a potom zaškrtněte políčko pro přidání tohoto uživatele do **vybrané** seznamu.

13. Jakmile budete hotovi, vyberte uživatele, klikněte na tlačítko **vyberte** přidat do seznamu uživatelů a skupin, kteří jsou přiřazeni k aplikaci.

14. *Volitelné:* Klikněte na tlačítko **vybrat roli** v **přidat přiřazení** podokně vyberte roli, kterou chcete přiřadit uživatelům, které jste vybrali.

15. Vyberte **přiřadit** aplikace přiřadit vybraným uživatelům.

    Po krátké prodlevě uživatelé budou mít přístup k těmto aplikacím z přístupového panelu.

## <a name="request-support"></a>Žádosti o podporu 
Pokud se zobrazí chybová zpráva, pokud chcete nastavit jednotné přihlašování a přiřadit uživatelům, otevřete lístek podpory. Zahrnout co nejvíce následujících informací, jako je to možné:

-   Chyba ID korelace
-   Hlavní název uživatele (uživatel e-mailová adresa)
-   ID Tenanta
-   Typ prohlížeče
-   Časové pásmo a čas nebo časový rámec, když došlo k chybě
-   Trasování Fiddler

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

---
title: Jednotné přihlašování (SSO) založené na heslech na přístupovém panelu | Microsoft Docs
description: Popisuje problematické oblasti, které poskytují pokyny k řešení problémů souvisejících s přihlašováním k aplikacím Galerie Azure AD nakonfigurovaným pro jednotné přihlašování k heslům.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381234"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problémy s přihlášením k aplikaci Galerie Azure AD konfigurované pro jednotné přihlašování s heslem

Přístupový panel je webový portál, který umožňuje uživateli, který má pracovní nebo školní účet ve službě Azure Active Directory (Azure AD) zobrazovat a spouštět cloudové aplikace, ke kterým správce Azure AD udělil přístup. Uživatel, který má edice Azure AD, může pomocí přístupového panelu použít taky samoobslužnou skupinu a možnosti správy aplikací. Přístupový panel je oddělený od Azure Portal a nevyžaduje, aby uživatelé měli předplatné Azure.

Pokud chcete na přístupovém panelu použít jednotné přihlašování založené na heslech (SSO), musí se v prohlížeči uživatele nainstalovat rozšíření přístupového panelu. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurovaná pro jednotné přihlašování založené na heslech.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Požadavky prohlížeče schůzky na přístupový panel

Přístupový panel vyžaduje prohlížeč, který podporuje jazyk JavaScript a je povolený pomocí šablon stylů CSS. Pokud chcete na přístupovém panelu použít jednotné přihlašování založené na heslech (SSO), musí se v prohlížeči uživatele nainstalovat rozšíření přístupového panelu. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurovaná pro jednotné přihlašování založené na heslech.

V případě jednotného přihlašování založeného na heslech můžou být prohlížeče koncového uživatele:

-   Internet Explorer 8, 9, 10, 11 – v systému Windows 7 nebo novějším

-   Chrome – v systému Windows 7 nebo novějším a na MacOS X nebo novějším

-   Firefox 26,0 nebo novější – v systému Windows XP SP2 nebo novějším a v Mac OS X 10,6 nebo novějším

>[!NOTE]
>Rozšíření jednotného přihlašování založené na heslech bude k dispozici pro Microsoft Edge ve Windows 10, když se rozšíření prohlížeče budou podporovat pro Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte podle následujících kroků:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve službě Azure AD.

2.  na přístupovém panelu klikněte na **aplikaci pro jednotné přihlašování pomocí hesla** .

3.  Na příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat hned**.

4.  V závislosti na prohlížeči, který budete přesměrováni na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se v prohlížeči zobrazí dotaz, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a podívejte se, jestli můžete **spouštět** aplikace pro jednotné přihlašování k heslům.

Rozšíření pro Chrome a Firefox si můžete stáhnout také z přímých odkazů níže:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro Internet Explorer

Můžete nastavit zásady skupiny, které vám umožní vzdáleně nainstalovat rozšíření přístupového panelu pro Internet Explorer na počítačích uživatelů.

Požadavky zahrnují:

-   Nastavili jste [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)a připojili jste počítače uživatelů k vaší doméně.

-   Chcete-li upravit objekt Zásady skupiny (GPO), je nutné mít oprávnění upravit nastavení. Ve výchozím nastavení mají toto oprávnění členové těchto skupin zabezpečení: Domain Administrators, Enterprise Administrators a Zásady skupiny Creator Owners. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Postupujte podle kurzu [nasazení rozšíření přístupového panelu pro Internet Explorer pomocí Zásady skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) , kde najdete podrobné pokyny ke konfiguraci zásad skupiny a jejich nasazení pro uživatele.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Řešení potíží s přístupovým panelem v aplikaci Internet Explorer

Postupujte podle pokynů v tématu [Poradce při potížích s rozšířením přístupového panelu pro Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) a získejte přístup ke diagnostickému nástroji a krok za krokem pokyny ke konfiguraci rozšíření pro IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci mimo galerii

Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidat aplikaci mimo galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo galerii

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce** .

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  klikněte na **aplikaci mimo galerii.**

7.  Do textového pole **název** zadejte název vaší aplikace. Vyberte **Přidat.**

Po krátké době se bude moci zobrazit podokno konfigurace aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Vyberte možnost režim **přihlašování založené na heslech.**

9. Zadejte **přihlašovací adresu URL**. Toto je adresa URL, kam uživatelé zadají své uživatelské jméno a heslo, aby se mohli přihlásit. Ujistěte se, že přihlašovací pole jsou viditelná na adrese URL.

10. Přiřaďte uživatele k aplikaci.

11. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

### <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ze které chcete uživatele přiřadit.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a **příjmení** nebo **e-mailovou adresu** uživatele, kterého se zajímáte o přiřazení.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **uživatele** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga uživatele přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e-mailová** adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** kliknutím na výběr **role** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte vybraným uživatelům.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace na přístupovém panelu.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží nevyřešily problém

Otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (uživatelská e-mailová adresa)

-   TenantID

-   Typ prohlížeče

-   Časové pásmo a čas/časový rámec při výskytu chyby

-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)


---
title: Potíže s přihlášením k aplikaci Galerie Azure AD nakonfigurované pro jednotné přihlašování | Microsoft Docs
description: Jak řešit problémy s aplikací Galerie Azure AD, která je nakonfigurovaná pro jednotné přihlašování pomocí hesla
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "68381306"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problémy s přihlašováním pomocí aplikace Galerie Azure AD nakonfigurované pro jednotné přihlašování

Přístupový panel je webový portál. Umožňuje uživatelům, kteří mají Azure Active Directory (Azure AD) pracovní nebo školní účty pro přístup k cloudovým aplikacím, ke kterým mají oprávnění. Uživatelé, kteří mají edice Azure AD, můžou prostřednictvím přístupového panelu použít taky samoobslužnou skupinu a možnosti správy aplikací.

Přístupový panel je oddělený od Azure Portal. Uživatelé nepotřebují k používání přístupového panelu předplatné Azure.

Chcete-li použít jednotné přihlašování založené na heslech (SSO) na přístupovém panelu, musí být v prohlížeči nainstalováno rozšíření přístupového panelu. Rozšíření se automaticky stáhne při výběru aplikace, která je nakonfigurovaná pro jednotné přihlašování založené na heslech.

## <a name="browser-requirements-for-access-panel"></a>Požadavky prohlížeče na přístupový panel

Přístupový panel vyžaduje prohlížeč, který podporuje jazyk JavaScript a je povolený pomocí šablon stylů CSS.

Jednotné přihlašování založené na heslech podporují následující prohlížeče:

- Internet Explorer 8, 9, 10 a 11 v systému Windows 7 nebo novějším

- Chrome ve Windows 7 nebo novějším nebo na MacOS X nebo novějším

- Firefox 26,0 nebo novější v systému Windows XP SP2 nebo novějším nebo v Mac OS X 10,6 nebo novějším

>[!NOTE]
>Rozšíření jednotného přihlašování založené na heslech bude k dispozici pro Microsoft Edge ve Windows 10, pokud byla podpora rozšíření prohlížeče přidána do Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>Nainstalovat rozšíření prohlížeče přístupového panelu

Postupujte následovně:

1. Otevřete [panel pro přístup](https://myapps.microsoft.com) v podporovaném prohlížeči a přihlaste se jako uživatel ve službě Azure AD.

2. V přístupovém panelu vyberte aplikaci s podporou jednotného přihlašování pomocí hesla.

3. Po zobrazení výzvy vyberte **nainstalovat hned**.

4. Budete přesměrováni na odkaz ke stažení na základě vašeho prohlížeče. Vyberte **Přidat** a nainstalujte rozšíření prohlížeče.

5. Pokud budete vyzváni, vyberte **Povolit** nebo **Povolit**.

6. Po instalaci restartujte prohlížeč.

7.  Přihlaste se k přístupovému panelu a podívejte se, jestli můžete začít používat aplikace s podporou jednotného přihlašování k heslům.

Rozšíření pro Chrome a Firefox můžete také přímo stáhnout pomocí těchto odkazů:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření přístupového panelu Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Nastavení zásad skupiny pro Internet Explorer

Můžete nastavit zásady skupiny, které vám umožní vzdáleně nainstalovat rozšíření přístupového panelu pro Internet Explorer na počítačích uživatelů.

Požadavky:

-   Musíte nastavit [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) a počítače uživatelů musí být připojené k vaší doméně.

-   Pro úpravu objektu Zásady skupiny (GPO) máte oprávnění upravit nastavení. Ve výchozím nastavení mají toto oprávnění členové těchto skupin zabezpečení: Domain Administrators, Enterprise Administrators a Zásady skupiny Creator Owners. [Další informace](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Pokud chcete nakonfigurovat zásady skupiny a nasadit je pro uživatele, přečtěte si téma [nasazení rozšíření přístupového panelu pro Internet Explorer pomocí zásad skupiny](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy).

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Řešení potíží s přístupovým panelem v aplikaci Internet Explorer

Přístup k nástroji pro diagnostiku a pokyny ke konfiguraci rozšíření najdete v tématu věnovaném [řešení potíží s rozšířením přístupového panelu pro Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting).

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Konfigurace jednotného přihlašování k heslům pro aplikaci Galerie Azure AD

Pokud chcete nakonfigurovat aplikaci z Galerie Azure AD, musíte provést tyto akce:

-   Přidání aplikace z Galerie Azure AD
-   [Konfigurace jednotného přihlašování pro aplikaci pro heslo](#configure-the-app-for-password-sso)
-   [Přiřazení uživatelů k aplikaci](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Postupujte následovně:

1. Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .

5. V pravém horním rohu podokna **podnikové aplikace** vyberte **Přidat** .

6. V části **Přidat z Galerie** zadejte název aplikace do pole **Zadejte název** .

7. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8. *Volitelné:* Před přidáním aplikace můžete změnit její název v poli **název** .

9. Pokud chcete tuto aplikaci přidat, klikněte na **Přidat**.

   Po krátké prodlevě budete moct zobrazit podokno konfigurace aplikace.

### <a name="configure-the-app-for-password-sso"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Postupujte následovně:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace**. Nastavte možnost **Zobrazit** na všechny aplikace.

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v podokně na levé straně aplikace **jednotné přihlašování** .

8. Vyberte režim **přihlašování založený na hesle** .

9. Přiřaďte uživatele k aplikaci.

10. Můžete také zadat přihlašovací údaje pro uživatele. (Jinak se uživatelům zobrazí výzva k zadání přihlašovacích údajů při spuštění aplikace.) To provedete tak, že vyberete řádky uživatelů. Pak vyberte **Aktualizovat přihlašovací údaje** a zadejte jejich uživatelská jména a hesla.

### <a name="assign-users-to-the-app"></a>Přiřazení uživatelů k aplikaci

Pokud chcete přiřadit uživatele k aplikaci přímo, použijte následující postup:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako globální správce.

2. V oblasti navigace na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace**. Nastavte možnost **Zobrazit** na všechny aplikace.

6. V seznamu vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace vyberte **Uživatelé a skupiny** v navigačním podokně aplikace na levé straně.

8. V horní části seznamu **uživatelů a skupin** vyberte **Přidat** a otevřete tak podokno **Přidat přiřazení** .

9. V podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a příjmení nebo e-mailovou adresu uživatele, kterého chcete přiřadit.

11. Najeďte myší na uživatele v seznamu. Zaškrtněte políčko vedle fotografie profilu uživatele nebo logo a přidejte tak tohoto uživatele do **vybraného** seznamu.

12. *Volitelné:* Chcete-li přidat jiného uživatele, zadejte do pole **Hledat podle jména nebo e-mailové adresy** jiný název nebo e-mailovou adresu a potom zaškrtněte políčko pro přidání tohoto uživatele do **vybraného** seznamu.

13. Až budete s výběrem uživatelů hotovi, klikněte na **Vybrat** a přidejte je do seznamu uživatelů a skupin, kteří jsou přiřazeni k aplikaci.

14. *Volitelné:* Klikněte na **Vybrat roli** v podokně **Přidat přiřazení** a vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Vyberte **přiřadit** a aplikaci přiřaďte vybraným uživatelům.

    Po krátké prodlevě budou uživatelé mít přístup k těmto aplikacím z přístupového panelu.

## <a name="request-support"></a>Požádat o podporu 
Pokud se zobrazí chybová zpráva, když nastavíte jednotné přihlašování a přiřadíte uživatele, otevřete lístek podpory. Co je možné zahrnout co nejvíc následujících informací:

-   ID chyby korelace
-   UPN (uživatelská e-mailová adresa)
-   TenantID
-   Typ prohlížeče
-   Časové pásmo a časový/časový rámec, kdy došlo k chybě
-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)

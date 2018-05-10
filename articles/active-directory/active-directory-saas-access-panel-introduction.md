---
title: Co je na přístupovém panelu v Azure Active Directory? | Dokumenty Microsoft
description: Další informace o použití variace přístupový panel (webový prohlížeč, aplikace pro Android, aplikace iPhone a iPad) pro přístup k aplikacím SaaS.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/18
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 442bfa7081865b2549c07a9436296ba2385a0b66
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="what-is-the-access-panel"></a>Co je na přístupovém panelu?

Přístupový panel je webový portál. Pokud máte pracovní nebo školní účet ve službě Azure Active Directory (Azure AD), můžete zobrazit a spustit cloudové aplikace, které správce Azure AD udělil přístup k přístupovému panelu. Můžete vytvořit také skupinu samoobslužné služby a možnosti správy aplikace prostřednictvím na přístupovém panelu.

Přístupový panel je nezávislý na portálu Azure. Nevyžaduje, abyste měli předplatné Azure.

![Přístupový panel][1] pomocí přístupového panelu můžete upravit některé z nastavení profilu a proveďte následující:

- Změňte heslo přidružené k pracovní nebo školní účet.

- Upravte nastavení resetování hesla.

- Upravte nastavení kontaktů a předvoleb týkající se vícefaktorového ověřování (pro účty, které byly vyžaduje použití ho správce).

- Zobrazit účet, který podrobnosti, jako je například ID uživatele, alternativní e-mailu, mobilní a office telefonních čísel a zařízení.

- Zobrazit a spustit cloudové aplikace, které správce Azure AD udělil přístup k. 

- Samoobslužná správa skupin. Správci mohou vytvořit a spravovat skupiny zabezpečení a žádosti o členství ve skupinách zabezpečení ve službě Azure AD. Další informace najdete v tématu [Samoobslužná správa skupin uživatelů ve službě Azure AD](active-directory-accessmanagement-self-service-group-management.md) a [spravovat vaše skupiny](active-directory-manage-groups.md).




## <a name="access-the-access-panel"></a>Přístupový panel

Dostanete tak, že přejdete do přístupového panelu `http://myapps.microsoft.com`.

Pokud máte vlastní branding nakonfigurovaný pro přihlašovací stránku, můžete načíst branding připojením domény vaší organizace na adresu URL (například `http://myapps.microsoft.com/<your domain>.com`).

Můžete použít jakýkoli název aktivní nebo ověřené domény, který byl nakonfigurován na portálu Azure, jak je vidět tady: ![adresář Wingtip Toys název domény][2]  

Distribuujte adresu URL pro všechny uživatele, kteří se přihlašují k aplikacím, které jsou integrované s Azure AD.

## <a name="authentication"></a>Authentication

Pokud chcete dostat na přístupovém panelu, musíte být ověřeni přes pracovní nebo školní účet ve službě Azure AD. Vám mohou být ověřeni pro Azure AD přímo. Případně pokud organizace nakonfiguroval federace pomocí služby Active Directory Federation Services (AD FS) nebo jiných technologií, vám může být ověřen pomocí Windows Server Active Directory.

Pokud máte předplatné Azure nebo Office 365 a používáte portál Azure nebo aplikace Office 365, zobrazí se seznam aplikací bez přihlásit znova. Pokud jste nejsou ověřené, zobrazí se výzva k přihlášení pomocí uživatelského jména a hesla pro váš účet ve službě Azure AD. Pokud vaše organizace má nakonfigurovali federaci, zadejte uživatelské jméno je dostačující.

Pokud jste se ověřili, můžete pracovat s aplikacemi, které správce má integrované s adresářem. Informace o integraci aplikací s Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Požadavky na webový prohlížeč

Minimálně na přístupovém panelu vyžaduje prohlížeč, který podporuje jazyk JavaScript a aktivoval šablon stylů CSS. Být přihlášeni k aplikacím prostřednictvím založené na heslech jednotné přihlašování (SSO), musí mít příponu panel přístupu nainstalovaný v prohlížeči. Rozšíření se stáhne automaticky, když vyberete aplikaci, která je nakonfigurována pro jednotné přihlašování založené na heslech.

Instalační program je specifický pro architekturu. Pokud kliknete odkaz ke stažení, zobrazí pouze instalační program pro architektury operačního systému, které jsou aktuálně spuštěné na. Pokud jste správce nasazení aplikací, ujistěte se, že navštívíte odkaz ke stažení z 64bitové a 32bitové zařízení a získat i instalační programy.


Rozšíření přístup k panelu je aktuálně dostupné pro:
- **Hraniční**: v systému Windows 10 Anniversary Edition nebo novějším. 
- **Chrome**: v systému Windows 7 nebo novější a v systému MacOS X nebo novější.
- **Firefox 26.0 nebo novější**: v systému Windows XP SP2 nebo novější a Mac OS X 10.6 nebo novější.
- **Internet Explorer 11**: na Windows 7 nebo novější (omezená podpora).

## <a name="my-apps-secure-sign-in-extension"></a>Rozšíření Zabezpečené přihlašování pro Moje aplikace
Pro přihlášení k založené na heslech jednotné přihlašování, musíte použít rozšíření. Po instalaci rozšíření se můžete přihlásit se k povolení dalších funkcí výběrem **Přihlaste se k Začínáme**. 

- Přihlaste se do aplikace přímo pomocí aplikace **přihlašovací adresa URL**. Pokud použijete adresu URL aplikace, rozšíření akci rozpozná a vám dává možnost přihlášení z rozšíření.
- Můžete spustit kterýkoli aplikací na přístupovém panelu pomocí *rychlého vyhledávání* funkce rozšíření. 
- Rozšíření se dozvíte, poslední tři aplikace, které můžete spustit v **nedávno použité** části.
- Můžete použít interní společnosti adresy URL při vzdálené prostřednictvím [Proxy aplikace](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> Další funkce jsou dostupné pouze pro okraj a Chrome, Firefox.
>

Pokud používáte Moje adresa URL aplikace jiné než `https://myapps.microsoft.com`, nakonfigurujte výchozí adresu URL pomocí těchto kroků:
1. Když jste *není* přihlášení k rozšíření, klikněte pravým tlačítkem na ikonu rozšíření.
2. V nabídce vyberte **Moje adresa URL aplikace**.
3. Vyberte výchozí adresa URL.
4. Vyberte ikonu pro rozšíření.
5. Vyberte **Přihlaste se k Začínáme**.

## <a name="mobile-app-support"></a>Podpora mobilních aplikací

Tým služby Azure Active Directory publikuje Moje aplikace mobilní aplikace. Při instalaci aplikace se můžete přihlásit pomocí hesla aplikace jednotného přihlašování na zařízení iOS a Android.

> [!NOTE]
> Můžete přihlásit do aplikace, které podporují federace se službou Azure AD (včetně Salesforce, Google Apps, Dropbox, pole, Concur, Workday, Office 365 a více než 70 dalších) prakticky libovolný webový prohlížeč na libovolném zařízení, bez nutnosti modul plug-in nebo mobilní aplikace. K použití v mobilním zařízení, dalších [přístup k panelu prostředí](https://myapps.microsoft.com/) také nevyžadují Moje aplikace mobilní aplikace.
>
>

### <a name="my-apps-for-android"></a>Moje aplikace pro Android

Moje aplikace pro Android je podporována na jakékoli zařízení s Androidem se systémem Android verze 4.1 nebo novější.  

Je k dispozici na [obchodu Google Play](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Moje aplikace pro Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>Moje aplikace pro zařízení iPhone a iPad

Moje aplikace pro iOS je podporována na jakékoli zařízení iPhone nebo iPad, který používá verzi iOS 7 nebo novější.  

Je k dispozici na [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Moje aplikace pro iOS][4]    


## <a name="managed-browser-for-my-apps"></a>Spravovaný prohlížeč pro Moje aplikace

Moje aplikace je také integrovat s Intune Managed Browser. Intune Managed Browser pro iOS a zařízení se systémem Android hraje důležitou roli v pomáhá zajistit, že jejího zabezpečení dat na mobilních zařízeních. Prohlížeč umožňuje bezpečně a přejděte na webové stránky, které může obsahovat informace o společnosti, a pomáhá poskytovat zabezpečené procházení webu.  

Získáte rychlý přístup k Moje aplikace na spravovaný prohlížeč domovské stránky a ve své záložky proto méně kliknutí je potřeba dosáhnout na jakoukoli aplikaci, kterou chcete získat přístup.

Spravovaný prohlížeč Intune je k dispozici na [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) a [Google Play Storu](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Spravovaný prohlížeč pro Moje aplikace][5]    


## <a name="tips-for-testing-the-user-experience"></a>Tipy pro testování činnost koncového uživatele

Pokud jste správcem Azure a jste přihlášení k portálu Azure pomocí účtu v adresáři, budete automaticky přihlášení k přístupovému panelu jako aktuálního účtu. Toto zobrazení ukazuje všechny aplikace, které jsou přiřazené vám.

K testování v *různých* uživatel účet, postupujte takto:

1. V pravém horním rohu portálu Azure nebo na přístupovém panelu vyberte **Odhlásit**. 
2. Přejděte na [přístupový panel](http://myapps.microsoft.com).
3. Na stránce přihlášení zadejte uživatelské jméno a heslo pro účet ve vašem adresáři, který chcete otestovat.


## <a name="starting-applications"></a>Spuštění aplikace

Tato část popisuje několik typů aplikací, které se mohou objevit na panel přístupu.

### <a name="office-365-applications"></a>Aplikace Office 365

Pokud vaše organizace používá Office 365 aplikace a máte licenci pro ně, zobrazí se aplikace Office 365 na přístupový panel.

Když vyberete dlaždici aplikace pro aplikaci Office 365, je přesměrován do aplikace a automaticky přihlášení.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Společnost Microsoft a třetích stran aplikace, které jsou nakonfigurované pomocí jednotného přihlašování na základě federation

Správce můžete přidat aplikace v oddílu služby Active Directory na portálu Azure, s režimem jednotné přihlašování, nastavte na **Azure AD jednotné přihlašování**. Tyto aplikace můžete zobrazit jenom v případě, že vám správce udělil explicitně přístup k nim.

Když vyberete dlaždici pro aplikaci, jsou přesměrování a automaticky přihlášení k němu.

### <a name="password-based-sso-without-identity-provisioning"></a>Založené na heslech jednotné přihlašování bez zřizování identity

Správce můžete přidat aplikace v oddílu služby Active Directory na portálu Azure, s režimem jednotné přihlašování, nastavte na **založené na heslech jednotné přihlašování**. Všechny aplikace, které jsou nakonfigurované v tomto režimu mohou prohlížet všichni uživatelé v adresáři.

Při prvním vyberete dlaždici aplikace, zobrazí se výzva k instalaci modulu plug-in heslo jednotné přihlašování pro aplikace Internet Explorer nebo Chrome. Instalace může vyžadovat restartování webového prohlížeče. Po návratu do přístupového panelu a znovu vyberte dlaždici aplikace, zobrazí se výzva k zadání uživatelského jména a hesla pro aplikaci. Když zadáte uživatelské jméno a heslo, přihlašovací údaje jsou bezpečně uložené a propojený s vaším účtem ve službě Azure AD.

Při příštím vyberte dlaždici aplikace automaticky nejste přihlášení k aplikaci.  

Nemusíte znovu zadejte své přihlašovací údaje a nebo instalace modulu plug-in jednotné přihlašování heslo.

Pokud vaše přihlašovací údaje se změnila v cílové aplikace třetích stran, je nutné také aktualizovat svoje přihlašovací údaje, které jsou uložené ve službě Azure AD. 

Pokud chcete aktualizovat přihlašovací údaje, postupujte takto:

1. Vyberte ikonu na dlaždici aplikace.
2. Vyberte **aktualizovat přihlašovací údaje** opětovné zadání uživatelského jména a hesla pro aplikaci.


### <a name="password-based-sso-with-identity-provisioning"></a>Založené na heslech jednotného přihlašování k při zřizování identity

Správce můžete přidat aplikace v oddílu služby Active Directory na portálu Azure, s režimem jednotné přihlašování, nastavte na **založené na heslech jednotné přihlašování**, společně s zřizování identity.

Při prvním vyberete dlaždici aplikace, zobrazí se výzva k instalaci modulu plug-in heslo jednotné přihlašování pro aplikace Internet Explorer nebo Chrome. Instalace může vyžadovat restartování webového prohlížeče.  

Po návratu do přístupového panelu a znovu vyberte dlaždici aplikace, budete automaticky přihlášení k aplikaci.

Některé aplikace může vyžadovat, abyste změnili heslo při prvním přihlášení. Pokud vaše přihlašovací údaje se změnila v cílové aplikace třetích stran, je nutné také aktualizovat přihlašovací údaje, které jsou uložené ve službě Azure AD. 

Pokud chcete aktualizovat přihlašovací údaje, postupujte takto:

1. Vyberte ikonu na dlaždici aplikace.
2. Vyberte **aktualizovat přihlašovací údaje** opětovné zadání uživatelského jména a hesla pro aplikaci.


### <a name="application-with-existing-sso-solutions"></a>Aplikace s stávajících řešení pro jednotné přihlašování

Konfigurace jednotného přihlašování pro aplikace, portál Azure poskytuje třetí možnost názvem existující jednotné přihlašování. Tato možnost umožňuje správci vytvořit odkaz na aplikaci a umístěte ji na panel přístupu pro vybraného uživatele.

Například pokud aplikace je nakonfigurovaný k ověřování uživatelů pomocí služby AD FS 2.0, správce slouží možnost existující jednotného přihlašování k vytvoření odkazu na na panel přístupu. Při přístupu k propojení, se ověřují pomocí služby AD FS 2.0 nebo jakoukoli existující jednotného přihlašování k řešení, které poskytuje aplikace.


## <a name="next-steps"></a>Další postup

- Chcete-li zobrazit seznam všech témat, které se vztahují ke správě aplikací, najdete v článku [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md).
 
- Další postup pro integraci aplikací SaaS Azure AD najdete v tématu [seznam kurzů k integraci aplikací SaaS](active-directory-saas-tutorial-list.md).
 
- Další informace o správě aplikací s Azure AD, najdete v článku [Úvod k přístupu k jedné přihlašování a správu aplikace pomocí Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Další informace o zřizování uživatelů najdete v tématu [automatizace zřizování uživatelů a rušení zajištění pro aplikace SaaS](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png

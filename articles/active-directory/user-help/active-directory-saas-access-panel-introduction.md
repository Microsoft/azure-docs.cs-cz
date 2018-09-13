---
title: Co je portálu MyApps ve službě Azure Active Directory? | Dokumenty Microsoft
description: Další informace o použití změn na portálu MyApps (webový prohlížeč, aplikace pro Android, aplikace pro iPhone a iPad) pro přístup k aplikacím SaaS.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: asteen
ms.openlocfilehash: 1caf69ee822ec11e8797ca23c9c163d36344edc7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719983"
---
# <a name="what-is-the-myapps-portal"></a>Co je na portálu MyApps?

Pokud máte svůj pracovní nebo školní účet v Azure Active Directory (Azure AD), můžete zobrazit a spustit cloudových aplikací, které správce Azure AD udělil přístup k Moje aplikace webový portál. Můžete také použít samoobslužné skupiny a možnosti správy aplikací prostřednictvím portálu MyApps.

Na portálu MyApps je nezávislá na webu Azure portal. Nevyžaduje, abyste měli předplatné Azure.

![Portálu MyApps][1] s použitím portálu MyApps, můžete upravit některé z nastavení profilu a udělejte toto:

- Změňte heslo přidružené k pracovnímu nebo školnímu účtu.

- Upravte nastavení resetování hesla.

- Upravte kontaktní údaje a předvoleb nastavení související s ověřováním službou Multi-Factor Authentication (pro účty, na kterých je potřeba použít microsoftem nebo správcem).

- Zobrazit účet, podrobnosti, jako je například ID uživatele, alternativní e-mailu, mobilní a office telefonní čísla a zařízení.

- Zobrazení a spuštění cloudových aplikací, které správce Azure AD udělil přístup k. 

- Správa samoobslužných skupin. Správci mohou vytvořit a spravovat skupiny zabezpečení a požádat o členství ve skupinách zabezpečení ve službě Azure AD. Další informace najdete v tématu [Samoobslužná správa skupin uživatelů ve službě Azure AD](../users-groups-roles/groups-self-service-management.md) a [spravovat skupiny](../fundamentals/active-directory-manage-groups.md).

## <a name="access-the-myapps-portal"></a>Přístup k portálu MyApps

Dostanete tak, že přejdete na portálu MyApps `http://myapps.microsoft.com`.

Pokud máte vlastní značka nakonfigurovaná pro přihlašovací stránku, můžete načíst branding přidáním domény vaší organizace na adresu URL (například `http://myapps.microsoft.com/<your domain>.com`).

Můžete použít libovolný název aktivní nebo ověřené domény, který byl nakonfigurovaný do webu Azure portal, jak je znázorněno zde: ![Wingtip Toys název domény][2]  

Distribuujte adresy URL pro všechny uživatele, kteří přihlašovat do aplikací, které jsou integrované s Azure AD.

## <a name="authentication"></a>Authentication

K dosažení portálu MyApps, musíte být ověřeni pomocí pracovního nebo školního účtu ve službě Azure AD. Mohou být ověřeni pro Azure AD přímo. Případně pokud organizace má nakonfigurovanou federace pomocí služby Active Directory Federation Services (AD FS) nebo jiné technologie, vám může být ověřen pomocí systému Windows Server Active Directory.

Pokud máte předplatné Azure nebo Office 365 a používáte na webu Azure portal nebo aplikace Office 365, můžete zobrazit seznam aplikací bez opakovaného přihlášení. Pokud nejste ověřeni, zobrazí se výzva k přihlášení pomocí uživatelského jména a hesla pro váš účet ve službě Azure AD. Pokud vaše organizace má nakonfigurovanou federace, zadáním uživatelského jména je dostačující.

Když jste ověřeni, můžete pracovat s aplikacemi, které správce má integrované s adresářem. Zjistěte, jak integrovat aplikace s Azure AD, najdete v článku [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="web-browser-requirements"></a>Požadavky na webový prohlížeč

Minimálně na portálu MyApps vyžaduje prohlížeč, který podporuje JavaScript a má povolený šablon stylů CSS. Být přihlášení k aplikacím prostřednictvím založené na heslech jednotné přihlašování (SSO), musíte mít nainstalované ve vašem prohlížeči rozšíření portálu MyApps. Rozšíření se automaticky stáhne při výběru, který je nakonfigurovaný pro jednotné přihlašování pomocí hesla aplikace.

Instalační program je specifické pro architekturu. Pokud kliknete odkaz ke stažení, získají instalační program pro architekturu operačního systému, které jsou aktuálně spuštěné na. Pokud jste správce nasazení aplikací, ujistěte se, navštivte odkaz ke stažení z 64bitové a 32bitové zařízení, abyste získali oba instalační programy.


Rozšíření portálu MyApps je aktuálně k dispozici pro:
- **Edge**: Windows 10 Anniversary Edition nebo novější. 
- **Chrome**: ve Windows 7 nebo novější a v systému MacOS X nebo novější.
- **Firefox 26.0 nebo novější**: ve Windows XP SP2 nebo novější a Mac OS X 10.6 nebo novější.
- **Internet Explorer 11**: Windows 7 nebo novější (omezená podpora).

## <a name="my-apps-secure-sign-in-extension"></a>Moje aplikace zabezpečené přihlašování rozšíření
Pro přihlášení k založené na heslech jednotného přihlašování, je nutné použít příponu. Po instalaci rozšíření můžete přihlásit k jeho povolení dalších funkcí tak, že vyberete **Přihlaste se k Začínáme**. 

- Přihlaste se k aplikaci přímo s použitím aplikace **přihlašovací adresa URL**. Pokud používáte adresu URL aplikace, rozšíření akci rozpozná a dá vám možnost přihlašování z rozšíření.
- Některé z vašich aplikací z portálu MyApps můžete spustit pomocí *rychlého vyhledávání* funkce rozšíření. 
- Rozšíření se zobrazí poslední tři aplikace, které jste spustili v **naposledy použité** oddílu.
- Můžete použít interní firemní adresy URL při vzdálené prostřednictvím [Proxy aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> Další funkce jsou dostupné pouze pro Edge, Chrome a Firefox.
>
Rozšíření si můžete stáhnout přímo z následujících webů:
- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Pokud používáte Moje adresy URL aplikací jiných než `https://myapps.microsoft.com`, nakonfigurujte adresu URL svého výchozí následujícím způsobem:
1. Když jste *není* přihlášení k rozšíření, klikněte pravým tlačítkem na ikonu rozšíření.
2. V nabídce vyberte **Moje aplikace URL**.
3. Vyberte výchozí adresu URL.
4. Vyberte ikonu rozšíření.
5. Vyberte **Přihlaste se k Začínáme**.

Použití interní firemní adresy URL při vzdálené pomocí rozšíření, postupujte takto:
1. [Konfigurace Proxy aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) ve svém tenantovi.
2. [Publikování aplikace](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) a adresa URL, přes Proxy aplikací.
3. Instalace rozšíření a přihlaste se k němu tak, že vyberete přihlášení začít.
4. Teď můžete přejít na adresu URL interní společnosti i v průběhu vzdálené.

> [!NOTE]
> Můžete také vypnout automatické přesměrování na adresy URL společnosti výběrem v hlavní nabídce ozubené kolo nastavení a vyberete **vypnout** pro možnost společnosti interní adresa URL přesměrování.


## <a name="mobile-app-support"></a>Podpora mobilních aplikací

Tým služby Azure Active Directory publikuje Moje aplikace z mobilní aplikace. Při instalaci aplikace se můžete přihlásit založené na heslech jednotného přihlašování k aplikacím v iOS a androidem.

> [!NOTE]
> Můžete přihlásit k aplikacím, které podporují federace s Azure AD (včetně Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 a více než 70 ostatní) na prakticky libovolného webového prohlížeče na libovolném zařízení a bez nutnosti modul plug-in nebo mobilní aplikace. K použití v mobilních zařízení, druhý [prostředí portálu MyApps](https://myapps.microsoft.com/) také nevyžadují Moje aplikace z mobilní aplikace.

### <a name="my-apps-for-iphone-and-ipad"></a>Moje aplikace pro iPhone a iPad

Moje aplikace pro iOS se podporuje na libovolném zařízení iPhone nebo iPad, na kterém běží verze iOS 7 nebo novější.  

Je k dispozici na [Apple App Store](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![Moje aplikace pro iOS][4]    


## <a name="intune-managed-browser-for-my-apps"></a>Intune Managed Browser pro mé aplikace

Moje aplikace je integrovaná taky s Intune Managed Browser. Intune Managed Browser pro iOS a androidem vám umožní bezpečněji a přejděte na webové stránky, které může obsahovat informace o společnosti, a usnadnit tak poskytovat bezpečnější prostředí procházení webu.  

Můžete získat na Moje aplikace i na domovské stránce Managed Browser a ze své záložky, což znamená, že existují šestnáct kliknutí potřebné k dosažení vašich aplikací.

Intune Managed Browser je k dispozici na [Apple App Store](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) a [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

![Spravovaný prohlížeč pro Moje aplikace][5]    


## <a name="tips-for-testing-the-user-experience"></a>Tipy pro testování uživatelského rozhraní

Pokud jste správcem Azure a jste přihlášení k webu Azure portal pomocí účtu v adresáři, budete automaticky přihlášení k portálu MyApps jako aktuálního účtu. Tento náhled zobrazuje všechny aplikace, které jsou vám přiřazeny.

Testování v *různých* uživatelského účtu, postupujte takto:

1. V pravém horním rohu webu Azure portal nebo portálu MyApps, vyberte **Odhlásit**. 
2. Přejděte [portálu MyApps](http://myapps.microsoft.com).
3. Na stránce přihlášení zadejte uživatelské jméno a heslo pro účet ve vašem adresáři, který chcete testovat.


## <a name="starting-applications"></a>Spouštění aplikací

Tato část popisuje několik typů aplikací, které se mohou objevit na portálu MyApps.

### <a name="office-365-applications"></a>Aplikace Office 365

Pokud vaše organizace používá Office 365 aplikací a máte licenci, aplikace Office 365 se objeví na portálu MyApps.

Při výběru dlaždice aplikací pro aplikace Office 365, je přesměrován do aplikace a automaticky přihlášeni.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Aplikace Microsoftu a třetích stran nakonfigurovat s jednotným Přihlašováním federační

Správce můžete přidat aplikace v sekci Active Directory na webu Azure portal s režimem jednotné přihlašování nastavit na **Azure AD Single Sign-On**. Tyto aplikace můžete zobrazit pouze v případě, že správce explicitně udělí přístup k nim.

Při výběru dlaždice pro aplikace, jsou přesměrovány a automaticky přihlášeni k němu.

### <a name="password-based-sso-without-identity-provisioning"></a>Založené na heslech jednotné přihlašování bez zřizování identity

Správce můžete přidat aplikace v sekci Active Directory na webu Azure portal s režimem jednotné přihlašování nastavit na **založené na heslech Single Sign-On**. Všichni uživatelé v adresáři vidí všechny aplikace, které jsou nakonfigurované v tomto režimu.

Při prvním vyberte dlaždici aplikace, zobrazí se výzva k instalaci modulu plug-in jednotné přihlašování heslem pro aplikaci Internet Explorer nebo Chrome. Instalace může vyžadovat restartování ve webovém prohlížeči. Vraťte se do portálu MyApps a znovu vyberete dlaždici aplikace, zobrazí se výzva pro uživatelské jméno a heslo pro aplikaci. Po zadání uživatelského jména a hesla, přihlašovací údaje jsou bezpečně uložená a propojený s vaším účtem ve službě Azure AD.

Při příštím vyberete dlaždici aplikace, budete automaticky přihlášení k aplikaci.  

Není nutné znovu zadejte svoje přihlašovací údaje a nainstalovat modul plug-in jednotného přihlašování hesla.

Pokud se vaše přihlašovací údaje změnily v cílové aplikaci třetí strany, je nutné také aktualizovat svoje přihlašovací údaje, které jsou uložené ve službě Azure AD. 

Pokud chcete aktualizovat svoje přihlašovací údaje, postupujte takto:

1. Výběrem ikony v dlaždici aplikace.
2. Vyberte **aktualizovat přihlašovací údaje** znovu zadat uživatelské jméno a heslo pro aplikaci.


### <a name="password-based-sso-with-identity-provisioning"></a>Jednotné přihlašování založené na heslo při zřizování identity

Správce můžete přidat aplikace v sekci Active Directory na webu Azure portal s režimem jednotné přihlašování nastavit na **založené na heslech Single Sign-On**, spolu s zřizování identity.

Při prvním vyberte dlaždici aplikace, zobrazí se výzva k instalaci modulu plug-in jednotné přihlašování heslem pro aplikaci Internet Explorer nebo Chrome. Instalace může vyžadovat restartování ve webovém prohlížeči.  

Vraťte se do portálu MyApps a znovu vyberete dlaždici aplikace, budete automaticky přihlášení k aplikaci.

Některé aplikace mohou vyžadovat změnu hesla při prvním přihlášení. Pokud se vaše přihlašovací údaje změnily v cílové aplikaci třetí strany, je nutné také aktualizovat přihlašovací údaje, které jsou uložené ve službě Azure AD. 

Pokud chcete aktualizovat svoje přihlašovací údaje, postupujte takto:

1. Výběrem ikony v dlaždici aplikace.
2. Vyberte **aktualizovat přihlašovací údaje** znovu zadat uživatelské jméno a heslo pro aplikaci.


### <a name="application-with-existing-sso-solutions"></a>Aplikace se stávajícími řešeními jednotného přihlašování

Konfigurace jednotného přihlašování pro aplikaci, na webu Azure portal nabízí třetí možnost volat existující jednotné přihlašování. Tato možnost umožňuje správcům vytvořit odkaz na aplikaci a umístěte ji na portálu MyApps pro vybraného uživatele.

Například pokud aplikace je nakonfigurovaná k ověřování uživatelů pomocí služby AD FS 2.0, správce použít možnost existující Single Sign-On pro vytvoření odkazu na ni na portálu MyApps. Když se dostanete na odkaz, ověření prostřednictvím služby AD FS 2.0 nebo jakýkoli existující jednotného přihlašování k řešení, které poskytuje aplikace.


## <a name="next-steps"></a>Další postup

- Další informace o správě aplikací, najdete v článku [Správa aplikací v Azure Active Directory](../manage-apps/what-is-application-management.md).
 
- Zjistěte, jak integrovat aplikace SaaS s Azure AD, najdete v článku [seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md).
 
- Další informace o správě aplikací s Azure AD, najdete v článku [Úvod jednotné přihlašování a správu přístupu k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
 
- Další informace o zřizování uživatelů najdete v tématu [automatizace zřizování uživatelů a jeho rušení pro aplikace SaaS](../manage-apps/user-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png

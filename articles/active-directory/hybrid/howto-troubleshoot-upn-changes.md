---
title: Plánování a řešení potíží se změnami názvu uživatelského principu Azure (UPN)
description: Principy známých problémů a skutečnosti snižující závažnost rizika pro změny upn
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695773da624bc8d4ccff09119d64fc43319ff488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246428"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Plánování a odstraňování potíží se změnami hlavního jména uživatele ve službě Azure Active Directory

Hlavní jméno uživatele (UPN) je atribut, který je standardem internetové komunikace pro uživatelské účty. Hlavní název uživatele se skládá z předpony hlavního názvu uživatele (název uživatelského účtu) a přípony hlavního názvu uživatele (název domény DNS). Předpona spojuje příponu pomocí symbolu "@". Například, someone@example.com. Hlavní název domény musí být jedinečný mezi všemi objekty zabezpečení v doménové struktuře adresáře. 

> [!NOTE]
> Pro vývojáře doporučujeme použít objekt uživateleID jako neměnný identifikátor, nikoli hlavní název uživatele. Pokud vaše aplikace aktuálně používají hlavní hlupávku, doporučujeme nastavit hlavní hlupávku tak, aby odpovídala primární e-mailové adrese uživatele, aby se zlepšilo jejich prostředí.<br> **V hybridním prostředí je důležité, aby hlavní název uživatele byl identický v místním adresáři a ve službě Azure Active Directory**.

**Tento článek předpokládá, že používáte hlavní číslo uživatele jako identifikátor uživatele. Řeší plánování změn UPN a zotavuje se z problémů, které mohou být důsledkem změn upn.**

## <a name="learn-about-upns-and-upn-changes"></a>Informace o změnách upn a UPN
Přihlašovací stránky často vyzývají uživatele, aby zadali svou e-mailovou adresu, když je požadovaná hodnota ve skutečnosti jejich hlavní značky. Proto byste měli mít jistotu, že změníte hlavní uživatele, kdykoli se změní jejich primární e-mailová adresa.

Primární e-mailové adresy uživatelů se mohou změnit z mnoha důvodů:

* rebranding společnosti

* zaměstnanci, kteří se stěhují do různých divizí společnosti 

* fúze a akvizice

* změny názvu zaměstnance

### <a name="types-of-upn-changes"></a>Typy změn UPN

Hlavní upn můžete změnit změnou předpony, přípony nebo obojího.

* **Změna předpony**.

   *  Pokud se například jméno osoby změnilo, můžete změnit jeho název účtu:  
BSimon@contoso.com ažBJohnson@contoso.com

   * Můžete také změnit firemní standard pro předpony:  
Bsimon@contoso.com ažBritta.Simon@contoso.com

* **Změna přípony**. <br>

    Pokud například někdo změnil rozdělení, můžete změnit jeho doménu: 

   * Britta.Simon@contoso.comažBritta.Simon@contosolabs.com <br>
     Nebo<br>
    *   Britta.Simon@corp.contoso.comažBritta.Simon@labs.contoso.com 

Při každé aktualizaci primární e-mailové adresy uživatele změňte hlavní uživatelský hlavní uživatel. Bez ohledu na důvod změny e-mailu musí být hlavní odkaz vždy aktualizován tak, aby odpovídal.

Během počáteční synchronizace ze služby Active Directory do služby Azure AD zajistěte, aby e-maily uživatelů byly identické s jejich názvy UPN

### <a name="upns-in-active-directory"></a>Názvy upn ve službě Active Directory

Ve službě Active Directory je výchozí příponou hlavního názvu uživatele název DNS domény, ve které jste uživatelský účet vytvořili. Ve většině případů se jedná o název domény, který registrujete jako podnikovou doménu v Síti Internet. Pokud vytvoříte uživatelský účet v contoso.com doméně, bude výchozí hlavní název uživatele

username@contoso.com

 Pomocí domén a vztahů důvěryhodnosti služby Active Directory však můžete [přidat další přípony hlavního názvu](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) uživatele. 

Můžete například chtít přidat labs.contoso.com a nechat hlavní uživatele a e-maily, které to odrážejí. Poté by se staly

username@labs.contoso.com.

>[!IMPORTANT]
> Pokud se názvy UPN ve službě Active Directory a službě Azure Active Directory neshodují, vzniknou problémy. Pokud [měníte příponu ve službě Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain), musíte zajistit, aby byl ve službě Azure [AD přidán a ověřen](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)odpovídající název vlastní domény . 

![Snímek obrazovky s ověřenými doménami](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UpN ve službě Azure Active Directory

Uživatelé se přihlašují ke službě Azure AD s hodnotou v atributu userPrincipalName. 

Při použití Služby Azure AD ve spojení s místní službou Active Directory se uživatelské účty synchronizují pomocí služby Azure AD Connect. Ve výchozím nastavení průvodce Azure AD Connect používá atribut userPrincipalName z místní služby Active Directory jako hlavní název uživatele ve službě Azure AD. Ve vlastní instalaci jej můžete změnit na jiný atribut.

Je důležité, že máte definovaný proces při aktualizaci hlavního uživatelského jména (UPN) jednoho uživatele nebo pro celou organizaci. 

Podívejte se na známé problémy a řešení v tomto dokumentu.

Při synchronizaci uživatelských účtů ze služby Active Directory do služby Azure AD se ujistěte, že hlavní názvy uživatelů ve službě Active Directory mapují na ověřené domény ve službě Azure AD.

![Snímek obrazovky s ověřenými doménami](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Pokud hodnota atributu userPrincipalName neodpovídá ověřené doméně ve službě Azure AD, proces synchronizace nahradí příponu výchozí hodnotou .onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Hromadné změny upn zaváděcí podstaty

Postupujte podle[osvědčených postupů pro pilota](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) pro hromadné změny hlavního upn. Máte také testovaný plán vrácení zpět pro vrácení upnů, pokud zjistíte problémy, které nelze rychle vyřešit. Jakmile je pilotní provoz spuštěný, můžete začít cílit na malé skupiny uživatelů s různými organizačními rolemi a jejich specifickými sadami aplikací nebo zařízení.

Prochází touto první podmnožinou uživatelů vám dá dobrou představu o tom, co uživatelé by měli očekávat jako součást změny. Uveďte tyto informace do komunikace s uživateli.

Vytvořte definovaný postup pro změnu upn s jednotlivými uživateli jako součást běžného provozu. Doporučujeme mít testovaný postup, který obsahuje dokumentaci o známých problémech a řešení.

V následujících částech jsou podrobně popsány potenciální známé problémy a řešení při změně hlavní ho nesete.

## <a name="user-provisioning-known-issues-and-workarounds"></a>zřizování známých problémů a řešení uživatelů

[Aplikace Software jako služba (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) a line of business (LoB) často spoléhají na upny při hledání uživatelů a ukládání informací o profilu uživatele, včetně rolí. Aplikace, které používají [zřizování just in Time](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) k vytvoření uživatelského profilu, když se uživatelé poprvé přihlásí k aplikaci, mohou být ovlivněny změnami UPN.

**Známý problém**<br>
Změna hlavního uživatele hlavního uživatele by mohla přerušit vztah mezi uživatelem Azure AD a profilem uživatele vytvořeným v aplikaci. Pokud aplikace používá [just in Time zřizování](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning), může vytvořit zcela nový profil uživatele. To bude vyžadovat, aby správce aplikace provést ruční změny opravit tento vztah.

**Řešení**<br>
[Azure AD Automatizované zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) umožňuje automaticky vytvářet, udržovat a odebírat identity uživatelů v podporovaných cloudových aplikacích. Konfigurace automatického zřizování uživatelů v aplikacích automaticky aktualizuje hlavní uživatele v aplikacích. Otestujte aplikace jako součást postupného zavádění, abyste ověřili, že nejsou ovlivněny změnami hlavního upn.

## <a name="managed-devices-known-issues-and-workarounds"></a>Známé problémy a řešení spravovaných zařízení

[Tím, že vaše zařízení do Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview), můžete maximalizovat produktivitu uživatelů prostřednictvím jednotného přihlašování (SSO) napříč cloudovými a místními prostředky.

### <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

[Zařízení připojená k Azure AD se](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) připojou přímo ke službě Azure AD a umožňují uživatelům přihlásit se k zařízení pomocí identity jejich organizace.

**Známé problémy** <br>
Uživatelé mohou zaznamenat problémy s jedním přihlášením s aplikacemi, které závisí na Azure AD pro ověřování.

**Řešení** <br>
Ponechte dostatek času pro změnu hlavního upn pro synchronizaci s Azure AD. Jakmile ověříte, že se nový hlavní název uživatele projeví na portálu Azure AD Portal, požádejte uživatele, aby vybral dlaždici "Jiný uživatel", abyste se přihlásili pomocí svého nového hlavního název uživatele. můžete také ověřit prostřednictvím [prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0). Po přihlášení pomocí nového hlavního panelu ONN se odkazy na starý hlavní hlnu stále mohou zobrazovat v nastavení Windows "Přístup k práci nebo do školy".

![Snímek obrazovky s ověřenými doménami](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Hybridní zařízení [připojená k Azure AD jsou](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) připojená ke službě Active Directory a azure ad. Hybridní připojení Azure AD můžete implementovat, pokud vaše prostředí má místní stopu služby Active Directory a chcete také těžit z možností poskytovaných službou Azure AD.

**Známé problémy** 

Zařízení připojená k hybridnímu azure ad windows 10 pravděpodobně dojde k neočekávaným restartováním a problémům s přístupem.

Pokud se uživatelé přihlásí k Systému Windows před synchronizací nového hlavního směrovače ve službě Azure AD nebo budou nadále používat existující relaci Windows, mohou se vyskytnou problémy s jedním přihlášením s aplikacemi, které používají Azure AD pro ověřování, pokud byl nakonfigurovaný podmíněný přístup vynucení použití hybridních spojených zařízení pro přístup k prostředkům. 

Navíc se zobrazí následující zpráva vynucení restartování po jedné minutě. 

"Počítač se automaticky restartuje za minutu. Systém Windows narazil na problém a je třeba restartovat. Tuto zprávu byste měli zavřít a uložit svou práci".

**Řešení** 

Zařízení musí být odpojeno od Azure AD a restartováno. Po restartování se zařízení znovu automaticky připojí k Azure AD a uživatel se musí přihlásit pomocí nového hlavního název uživatele výběrem dlaždice "Jiný uživatel". Chcete-li se odpojit od zařízení z Azure AD, spusťte na příkazovém řádku následující příkaz:

**dsregcmd /dovolená**

Uživatel bude muset [znovu zaregistrovat](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) Windows Hello pro firmy, pokud se používá. Windows 7 a 8.1 zařízení nejsou ovlivněny tímto problémem po změně upn.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Známé problémy a řešení programu Microsoft Authenticator

Vaše organizace může vyžadovat použití [aplikace Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) pro přihlášení a přístup k organizačním aplikacím a datům. Přestože se uživatelské jméno může v aplikaci zobrazit, účet není nastaven tak, aby fungoval jako metoda ověření, dokud uživatel nedokončí proces registrace.

[Aplikace Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) má čtyři hlavní funkce:

* Vícefaktorové ověřování pomocí nabízeného oznámení nebo ověřovacího kódu

* Fungovat jako zprostředkovatel ověřování na zařízeních se systémem iOS a Android poskytovat jednotné přihlášení pro aplikace, které používají [zprostředkované ověřování](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth)

* Registrace zařízení (označovaná také jako Připojení k workplace) do Azure AD, což je požadavek na další funkce, jako je Ochrana aplikací Intune a Registrace/správa zařízení,

* Přihlášení k telefonu, které vyžaduje vícefaktorové zabezpečení a registraci zařízení.

### <a name="multi-factor-authentication-with-android-devices"></a>Vícefaktorové ověřování se zařízeními android

Aplikace Microsoft Authenticator nabízí možnost mimopásmového ověření. Namísto automatického telefonního hovoru nebo SMS uživateli během přihlášení, [vícefaktorové ověřování (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) odešle oznámení do aplikace Microsoft Authenticator na smartphone uživatele nebo tabletu. Uživatel jednoduše klepne na tlačítko Schválit (nebo zadá PIN nebo biometrický kód a klepne na "Ověření") v aplikaci k dokončení přihlášení.

Když změníte hlavní číslo uživatele, mobilní zařízení mohou zaznamenat následující problémy:

**Známé problémy** 

Starý hlavní uživatel hlavního účtu se stále zobrazuje v uživatelském účtu a nemusí být přijato oznámení. [Ověřovací kódy](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) nadále fungují.

**Řešení**

Pokud obdržíte oznámení, dejte pokyn uživateli, aby oznámení odmítl, otevřel aplikaci Authenticator, klepnul na možnost Vyhledat oznámení a schválil výzvu mfa. Poté se hlavní upn zobrazený na účtu aktualizuje. Všimněte si, že aktualizovaný hlavní účel společnosti MŮŽE být zobrazen jako nový účet, což je způsobeno dalšími používanými funkcemi ověřování. Další informace další známé problémy v tomto článku.

### <a name="brokered-authentication"></a>Zprostředkované ověřování

Na Android a iOS makléři jako Microsoft Authenticator povolit:

* Jednotné přihlašování (SSO) – vaši uživatelé se nebudou muset přihlašovat ke každé aplikaci.

* Identifikace zařízení – zprostředkovatel přistupuje k certifikátu zařízení vytvořenému v zařízení, když bylo připojeno k pracovišti.

* Ověření identifikace aplikace - Když aplikace volá zprostředkovatele, předá jeho adresu URL přesměrování a zprostředkovatel ji ověří.

Kromě toho umožňuje aplikacím účastnit se pokročilejších funkcí, jako je [podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/), a podporuje [scénáře Microsoft Intune](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps).

**Známé problémy**<br>
Uživateli se zobrazí více interaktivní výzvy k ověření na nové aplikace, které používají přihlášení s pomocí zprostředkovatele z důvodu neshody mezi login_hint předaným aplikací a hlavní název uživatele uložený v zprostředkovateli.

**Řešení** <br> Uživatel musí ručně odebrat účet z Microsoft Authenticator a spustit nové přihlášení z aplikace s pomocí zprostředkovatele. Účet bude automaticky přidán po počátečním ověření.

### <a name="device-registration"></a>Registrace zařízení

Aplikace Microsoft Authenticator je zodpovědná za registraci zařízení do Azure AD. Registrace zařízení umožňuje zařízení k ověření azure ad a je požadavek pro následující scénáře:

* Intune App Protection

* Registrace zařízení Intune

* Přihlášení po telefonu

**Známé problémy**<br>
Když změníte hlavní číslo, nový účet s novým hlavní ho nese novým upn zobrazí v aplikaci Microsoft Authenticator, zatímco účet se starým UPN je stále uveden. Kromě toho se starý hlavní název sítě zobrazí v části Registrace zařízení v nastavení aplikace. Neexistuje žádná změna v normální funkčnost registrace zařízení nebo závislé scénáře.

**Řešení** <br> Chcete-li odebrat všechny odkazy na starý hlavní název uživatele v aplikaci Microsoft Authenticator, dejte uživateli pokyn, aby ručně odebral staré i nové účty z microsoft authenticatoru, znovu se zaregistroval pro mfa a znovu se připojil k zařízení.

### <a name="phone-sign-in"></a>Přihlášení po telefonu

Přihlášení po telefonu umožňuje uživatelům přihlásit se k Azure AD bez hesla. Chcete-li povolit přihlášení k telefonu, uživatel se musí zaregistrovat pro vícefaktorové ověřování pomocí aplikace Authenticator a pak povolit přihlášení telefonu přímo na Authenticator. Jako součást konfigurace zařízení registruje s Azure AD.

**Známé problémy** <br>
Uživatelé nemohou používat přihlášení k telefonu, protože neobdrží žádné oznámení. Pokud uživatel klepne na zkontrolovat oznámení, zobrazí se chyba.

**Řešení**<br>
Uživatel musí vybrat rozevírací nabídku v účtu povoleném pro přihlášení telefonem a vybrat Zakázat přihlášení k telefonu. V případě potřeby lze znovu povolit přihlášení k telefonu.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Bezpečnostní klíč (FIDO2) známé problémy a řešení

**Známé problémy** <br>
Pokud je na stejném klíči registrováno více uživatelů, na přihlašovací obrazovce se zobrazí stránka pro výběr účtu, kde se zobrazí starý hlavní upn. Přihlášení pomocí klíčů zabezpečení nejsou změnami upn ovlivněna.  

**Řešení**<br>
Chcete-li odebrat odkazy na staré hlavní národní seznamy, musí uživatelé [resetovat klíč zabezpečení a znovu se zaregistrovat](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Známé problémy a řešení OneDrivu

Uživatelé OneDrivu mají po změnách upn problémy. Další informace najdete v [tématu Jak změny hlavního upn ovlivní adresu URL OneDrivu a funkce OneDrivu](https://docs.microsoft.com/onedrive/upn-changes).

## <a name="next-steps"></a>Další kroky

Podívejte se na tyto zdroje:
* [Azure AD Connect: Koncepty návrhu](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Naplnění Azure AD UserPrincipalName](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Tokeny ID platformy identit y Microsoft](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)

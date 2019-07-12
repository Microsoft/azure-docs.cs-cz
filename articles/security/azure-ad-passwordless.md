---
title: Principy world bez hesla pomocí služby Azure Active Directory | Dokumentace Microsoftu
description: Tento průvodce pomůže CEOs, vedoucí, CISOs, Chief Identity architekty, podnikové architekty a zabezpečení a IT pracovníky s rozhodovací pravomocí odpovědnost za výběr passwordless ověřování pro jejich implementaci služby Azure Active Directory.
services: active-directory
keywords: passwordless, Azure AD
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.openlocfilehash: 00f66b6010bead3de131095a47ba1e419d2511c0
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723438"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>World bez hesla pomocí služby Azure Active Directory

Je čas k rozdělení váš vztah s hesly. Hesla se pro nás dobrým v minulosti, ale v dnešních digitálních síti na pracovišti se stala vektor útoku relativně jednoduché je pro hackery. Hesla je skvělá hackery a není obtížné zjistit, proč při zvažování, které nejčastěji zavádí odmítnuté hesla ve službě Azure Active Directory (Azure AD) zahrnují podmínky jako rok, měsíc, období, nebo místní sportovní tým. Kromě toho [výzkum ukázal](https://aka.ms/passwordguidance) správnost kontraproduktivní pro celou řadu důvodů tradiční doporučení pro správu hesel, jako jsou požadavky na délku, složitost a frekvencí změn související s lidskou přirozeností.

Tři typy útoků běžně používají k ohrožení zabezpečení uživatelské účty se heslo zařízení, útoky phishing a breach přehrát. Funkce Azure AD, jako [inteligentním uzamčením](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout), [zakázaná hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), a [ochrana heslem](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) můžou ochránit proti těmto typům útoků. Obdobně implementace [ověřování službou Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) (MFA), nebo dvoustupňové ověřování, poskytuje dodatečné zabezpečení vyžadováním druhou podobu ověřování. Ale v dlouhodobém horizontu passwordless řešení je nejlepší řešení pro zajištění nejbezpečnější metodu ověřování.

Tento článek je začátek na vaší cestě k vám pomůžou pochopit a implementovat passwordless řešení od Microsoftu a pomáhají zajistit si vybrat mezi jeden nebo více z následujících možností:

* **Windows Hello pro firmy**. Ve Windows 10 Windows Hello pro firmy nahradí hesla silným dvojúrovňovým ověřováním na počítačích a mobilních zařízeních. Toto ověření se skládá z nový typ přihlašovacích údajů uživatele, který se váže na zařízení a používá biometrika nebo PIN kód.

* **Přihlašování s aplikací Microsoft Authenticator**. Aplikace Microsoft Authenticator je možné se přihlásit k účtu služby Azure AD bez použití hesla. Podobně jako na technologii Windows Hello pro firmy, Microsoft Authenticator používá ověřování založené na klíč k povolení pověření uživatele, který se váže na zařízení a používá biometrika nebo PIN kód.

* **Klíče zabezpečení FIDO2**. FIDO2 zajišťuje šifrování přihlašovacích údajů, které jsou jedinečné v rámci každému webu a jsou uložené na místním zařízení, jako je Windows Hello nebo externí zabezpečení klíčů. Tyto klíče zabezpečení jsou odolné vůči rizik útoků phishing, krádeží hesla a útoky opakováním. V kombinaci s ověřováním uživatele prostřednictvím biometrii nebo PIN kódu, řešením je dva požadavky na moderní zabezpečení faktor ověřování schůzky.

## <a name="the-future-of-passwordless-authentication"></a>Budoucnost passwordless ověřování

Tyto dny, bank, společnosti platební kartu a jiných organizací a online služby často chránit svůj účet tak, že vyžaduje, můžete si ověřit vaši identitu dvakrát: jednou pomocí hesla, pak znovu pomocí telefonu, text nebo aplikace. Při ověřování službou Multi-Factor Authentication řeší problém zabezpečení hesel sdílí, odcizení nebo uhodnout, nezabývá faktor potíže při pokusu o jejich mějte na paměti. Co uživatelé a organizace chtějí v dnešních cloudovou éru je metody passwordless ověřování, které jsou vysoce zabezpečené *a* vhodné.

![Zabezpečení pohodlí vs](./media/azure-ad/azure-ad-pwdless-image1.png)

Windows Hello pro obchodní, passwordless Přihlaste se pomocí aplikace Microsoft Authenticator a FIDO2 zabezpečení klíčů sdílejí jednoduché, běžné architektury, které uživatelům poskytnout metodu ověřování, která je vysoce zabezpečené a pohodlné používat ho. Všechny tři jsou založeny na technologii veřejného/soukromého klíče, třeba místní gesta, jako je biometrika nebo PIN kód a soukromé klíče vázán na jediné zařízení a bezpečně uložená a nikdy sdílet.

## <a name="windows-hello-for-business"></a>Windows Hello pro firmy

Ve Windows 10 Windows Hello pro firmy nahradí hesla silným dvojúrovňovým ověřováním na počítačích a zařízeních. Ověřování se skládá z nový typ přihlašovacích údajů uživatele, který se váže na zařízení a používá biometrická gesta nebo PIN kód, který umožňuje uživatelům ověření do služby Azure AD a místní Active Directory. Jednoduše podepisování do zařízení pomocí Windows Hello pro firmy je snadné. Můžete buď použít PIN kód nebo biometrické gesta jako je například rozpoznávání otisk prstu nebo facial.

### <a name="windows-hello-for-business-scenarios"></a>Windows Hello pro firmy scénáře

Windows Hello pro firmy je ideální pro informační pracovníky, kteří mají své vlastní určené Windows PC. Biometrické a přihlašovací údaje jsou přímo vázané na počítač uživatele, který brání v přístupu z všem uživatelům kromě vlastníka. Díky integraci infrastruktury veřejných KLÍČŮ a integrovanou podporu pro jednotné přihlašování (SSO) Windows Hello pro firmy poskytuje jednoduché a pohodlnou metodu pro bezproblémově přístup k firemním prostředkům v místním prostředí i v cloudu.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello pro firmy důležité informace o nasazení

Windows Hello pro firmy je distribuovaný systém, který používá několik komponent k provedení registrace zařízení, zřizování a ověřování. Proto nasazení vyžaduje důkladné plánování prostřednictvím několika týmů v rámci organizace. Ve Windows Hello pro firmy [Příručka pro plánování](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) lze použít při rozhodování o typu Windows Hello pro firmy nasazení a možnosti je potřeba vzít v úvahu.

Pro hybridní nebo místní nasazení očekává se, že máte:

* Dobře propojené sítě pracovní

* Přístup k internetu

* Multi-Factor Authentication Server pro podporu vícefaktorového ověřování během Windows Hello pro firmy zřizování

* Správný překlad, interní a externí názvy

* Active Directory a odpovídající počet řadičů domény na lokalitu pro podporu ověřování

* Active Directory Certificate Services 2012 nebo novější

* Jeden nebo více pracovních stanic s Windows 10, verze 1903

Protože všechny typy Windows Hello pro firmy nasazení používat certifikáty vydané enterprise pro řadiče domény jako kořenové důvěryhodnosti, budete chtít automaticky obnovovat certifikáty s vypršenou platností pomocí [konfigurace automatické registrace pro server a uživatele certifikáty](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/configure-server-certificate-autoenrollment). Zprostředkovatel identity pro místní nasazení, je na místním serveru s rolí Windows Server Active Directory Federation Services (AD FS). Federované systémy obvykle vyžadují pole s vyrovnáváním zatížení serverů, známé jako farmy. Tato farma je nakonfigurovaný v interní síti a topologie hraniční sítě k zajištění vysoké dostupnosti pro žádosti o ověření.

Při nastavení zásad skupiny tak, aby vyžadovala Windows Hello pro firmy na pracovišti, můžete chtít připravit uživatelé ve vaší organizaci tím, že vám vysvětlíme, jak použít Windows Hello. Například když někdo nastaví nové zařízení, zobrazí se výzva k výběru mezi **toto zařízení patří mojí organizace** nebo **Toto je vlastní osobní zařízení**. Pro zařízení by měl vyberou bývalé. Uživatelé budou také muset říct, kterou možnost připojení by měl vybrat: **Připojení ke službě Azure AD** nebo **nastavit místní účet (připojení k doméně později)** .

Je běžné, že uživatelé, kteří jsou zvyklí na použití biometrická gesta k ověření den po dni nakonec zapomněli svůj PIN kód. Aby se zabránilo volání na HelpDesk, doporučujeme poskytnout uživatelům resetovat zapomenuté [hesla](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) a [PIN kódy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset).

Existuje mnoho možností, ze kterých se dá vybírat při nasazování Windows Hello pro firmy. Poskytuje několik možností zajišťuje že téměř každá organizace může nasadit Windows Hello pro firmy. Vezměte v úvahu následující typy nasazení, které jsou podporovány:

* [Hybridní služby Azure AD připojená k nasazení klíče důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hybridní služby Azure AD připojená k nasazení certifikátu vztahu důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Postupujte podle průvodců nasazením jednotné přihlašování – připojení k Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Na místního nasazení klíče důvěryhodnosti](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Místního certifikát důvěryhodnosti nasazení](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Poskytuje mnoho možností díky nasazení se zobrazí komplexní. Ale většina organizací bude pravděpodobně určit, že se už implementovali většina infrastruktury, na kterém ve Windows Hello pro firmy nasazení závisí. Bez ohledu na to je důležité pochopit, že Windows Hello pro firmy je distribuovaný systém a důkladném plánování se doporučuje.

Doporučujeme, abyste si přečetli [plánování z Windows Hello pro firmy nasazení](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) vám pomohou rozhodnout nejlepší model nasazení vhodných pro konkrétní organizace. Potom podle plánování, které provedete, najdete [Windows Hello pro firmy Deployment Guide](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) k zajištění úspěšné nasazení Windows Hello pro firmy v prostředí vašeho stávajícího.

### <a name="how-windows-hello-for-business-works"></a>Windows Hello pro firmy fungování

#### <a name="user-sets-up-windows-hello-for-business"></a>Uživatel nastaví Windows Hello pro firmy

Po počátečním dvoustupňovém ověření uživatele při registraci Windows Hello je nastavený na zařízení uživatele a Windows žádá uživatele, chcete-li nastavit gesto, což může být biometrika, třeba otisk prstu nebo facial rozpoznávání nebo PIN kód. Po nastavení, uživatel zadá gesta ověřit svoji identitu. Windows pak používá Windows Hello k ověřování uživatelů.

Podle možnosti zařízení s Windows 10, musíte tedy integrované zabezpečené enklávy, označované jako čip hardwaru platform module (TPM) nebo softwaru čipu TPM. Čip TPM ukládá privátní klíč, který vyžaduje vaši pro rozpoznávání tváře, otisk prstu nebo PIN kód, abyste ho mohli odemknout. Biometrickými daty nebude přesouvat a nikdy neodesílá do externích zařízení nebo serverů. Neexistuje žádný bod jedné kolekce, které útočník může ohrozit ke krádeži biometrické údaje, protože Windows Hello se uchovávají pouze biometrické identifikační údaje na zařízení.

> [!TIP]
> Na ploše kód PIN, jako třeba hesla, ale je ve skutečnosti bezpečnější. Důležitý rozdíl mezi heslem a PIN kód je, že PIN kód se váže na konkrétní zařízení, na kterém byl nastaven. Někdo ukradne heslo můžete přihlásit ke svému účtu z libovolného místa. Ale pokud zcizení svůj PIN kód, bylo by nutné příliš ukrást vaše fyzické zařízení. Navíc vzhledem k tomu, že kód PIN je lokální vzhledem k zařízení, to se nebudou přenášet kdekoli tak nemůže být zachycen v přenosu nebo vám ho někdo ukradne ze serveru.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Použití Windows Hello pro firmy pro přihlášení uživatele

Windows Hello pro firmy biometrické údaje a PIN kódů pomocí asymetrického (veřejného a privátního klíče) šifrování pro ověřování. Během ověřování šifrování se váže na klíč relace TLS, který zajišťuje zabezpečení procesu ověřování tak, aby útok man-in-the-middle (typu MiTM) nelze ukrást výsledný token zabezpečení nebo artefaktů a přehrát ho z jiných míst.

Windows Hello pro firmy poskytuje pohodlné přihlášení prostředí, která ověřuje uživatele do služby Azure AD a prostředků služby Active Directory. Azure zařízení připojených k doméně AD ověření při přihlašování do Azure a může volitelně ověřování do služby Active Directory. Zařízení Azure Active Directory připojená k hybridní ověřování při přihlašování do služby Active Directory a ověřování do Azure Active Directory na pozadí.

![Zobrazit zdroj obrázku](./media/azure-ad/azure-ad-pwdless-image2.jpeg)

Následující postup ukazuje ověřování přihlášení k Azure Active Directory.

![Zamykací obrazovky Windows 10](./media/azure-ad/azure-ad-pwdless-image3.png)

1. Uživatel se přihlásí pomocí Windows biometrika nebo PIN kód gesta. Gesto odemkne ve Windows Hello pro firmy privátní klíč a jsou odeslána do zprostředkovatele podpory zabezpečení cloudové ověřování označovány jako poskytovatele cloudu přístupový bod.

2. Poskytovatel cloudu Asie a Tichomoří vyžaduje hodnotu nonce z Azure Active Directory.

3. Azure AD vrací hodnotu nonce, který je platný po dobu 5 minut.

4. Poskytovateli cloudu Asie a Tichomoří podepíše nonce pomocí soukromého klíče uživatele a vrátí podepsané nonce ke službě Azure Active Directory.

5. Azure Active Directory ověří podepsaný nonce pomocí bezpečně registrované veřejného klíče uživatele s nonce podpisem. Po ověření podpisu, Azure AD pak ověří vrácené hodnoty nonce podepsaný držitelem. Po ověření hodnotu nonce, vytvoří Azure AD PRT s klíčem relace, který se zašifruje do zařízení přenosu klíče a vrátí k poskytovateli cloudu Asie a Tichomoří.

6. Poskytovatel cloudu Asie a Tichomoří obdrží šifrované PRT s klíčem relace. Pomocí přenosu privátní klíč zařízení, poskytovatele cloudu Asie a Tichomoří dešifruje klíč relace a chrání klíč relace pomocí zařízení TPM.

7. Poskytovatel cloudu Asie a Tichomoří vrátí odpověď úspěšné ověřování Windows, po jejímž uplynutí je uživatel přístup k Windows, stejně jako cloudové a místní aplikace bez nutnosti znovu ověřovat (SSO).

Podrobnější přehled celého procesu ověřování v jiných scénářích zahrnující Windows Hello pro firmy, naleznete v tématu [Windows Hello pro firmy a ověřování](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#Azure-AD-join-authentication-to-Active-Directory-using-a-Key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Spravuje jejich Windows Hello pro firmy pověření

[Microsoft PIN reset služby](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) je funkce ve službě Azure AD, který umožňuje uživatelům resetovat kód PIN, v případě potřeby. Pomocí zásad skupiny, Microsoft Intune nebo kompatibilní MDM, Správce může nakonfigurovat zařízení s Windows 10 bezpečně používat Microsoft PIN reset service, která umožňuje uživatelům resetovat zapomenuté kód PIN prostřednictvím nastavení nebo nad zamykací obrazovkou bez nutnosti opětovné registrace.

Někdy uživatelé muset vrátit zpět k použití hesla. [Samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment) (SSPR) je další funkce služby Azure AD, který umožňuje uživatelům resetovat svá hesla bez nutnosti kontaktovat pracovníky. Uživatelé musí registrovat pro nebo zaregistrovat pro resetování před použitím služby hesla pomocí samoobslužné služby. Během registrace uživatel zvolí jednu nebo více metod ověřování ve své organizaci povolené. Samoobslužné resetování HESLA umožňuje rychle získat odblokuje a pokračovat v práci bez ohledu na to, kde jsou, nebo čas. Tím, že uživatele odblokujete sami, můžete snížit vaše organizace neproduktivní čas a náklady na vysokou podporu pro nejběžnější problémy související s hesly.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Přihlašování s aplikací Microsoft Authenticator

Přihlašování s aplikací Microsoft Authenticator je jiný passwordless řešení, které lze použít k přihlášení k účtům Azure AD pomocí přihlášení telefonem. Stále musíte ověřit svou identitu zadáním něco, co víte a něco mají, ale phone přihlašování umožňuje přeskočit zadání vašeho hesla a provede všechny ověření identity na mobilním zařízení pomocí otisků prstů, rozpoznávání tváře nebo PIN kód.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator passwordless scénáře

Aplikace Microsoft Authenticator umožňuje uživatelům ověřit svoji identitu a ověřit svůj pracovní nebo osobní účet. To také umožňuje rozšířit heslo s jednorázovým heslem nebo nabízené oznámení nebo zcela eliminovat potřebu k zadání hesla. Místo použití hesla, uživatelé potvrdit svou identitu pomocí svého mobilního telefonu prostřednictvím skenování otisků prstů, rozpoznávání facial nebo iris nebo PIN kód. Využívá zabezpečené podobný co Windows Hello používá technologii, tento nástroj je zabalená do jednoduchou aplikaci na mobilním zařízení díky tomu je vhodné možnosti pro uživatele. Aplikace Microsoft Authenticator je dostupná pro zařízení s Androidem a iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Důležité informace o nasazení aplikace Microsoft Authenticator

Požadavky pomocí aplikace Microsoft Authenticator passwordless přihlašování Azure AD zahrnují následující:

* Koncoví uživatelé jsou povoleny pro ověřování Azure Multi-Factor Authentication

* Možnost uživatelé registrovat svá zařízení pomocí Microsoft Intune nebo řešení pro správu (MDM) třetí strany mobilních zařízení

Za předpokladu, že jsou splněny tyto požadavky, správci povolit přihlašování telefonem passwordless v tenantovi pomocí [prostředí Windows PowerShell.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) Po přihlášení telefonem je povolené v tenantovi, koncovým uživatelům možnost přihlásit pomocí svého telefonu tak, že vyberete svůj pracovní nebo školní účet na **účty** obrazovky aplikaci a pak vyberte **povolit přihlašování telefonem** .

Za předpokladu, že přihlašování je povolený správcem, koncoví uživatelé budou muset splnit následující požadavky:

* Registrované v Azure Multi-Factor Authentication

* Nejnovější verzi aplikace Microsoft Authenticator nainstalované v zařízení se systémem iOS 8.0 nebo novější nebo Android 6.0 nebo vyšší

* Pracovní nebo školní účet pomocí nabízených oznámení do aplikace

Chcete-li se vyhnout potenciálnímu získávání uzamčeno mimo váš účet nebo nutnosti opětného vytváření účtů na novém zařízení, se doporučuje použít aplikaci Microsoft Authenticator a [zálohovat přihlašovací údaje účtu](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-backup-recovery) do cloudu. Po dokončení zálohování, můžete také použít aplikace obnovte informace na novém zařízení, potenciálně zabránit získávání uzamčen na více instancí nebo nutnosti opětného vytváření účtů.

Protože většina uživatelů jsou zvyklí používat jenom hesla pro ověření, je důležité, že vaše organizace poučná uživatelé týkající se tohoto procesu. Sledování může snížit pravděpodobnost, že uživatelé volat vaše oddělení technické podpory pro všechny [problémy](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#known-issues) související s přihlašováním pomocí aplikace Microsoft Authenticator.

> [!NOTE]
> Potenciálních bodů selhání pro toto řešení je roamingu uživatel nachází v umístění, kde není k dispozici internetové připojení. FIDO2 zabezpečení klíčů a Windows Hello pro firmy se nevztahují stejné omezení.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Způsob přihlašování pomocí aplikace Microsoft Authenticator funguje

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Uživatel nastaví přihlašování s aplikací Microsoft Authenticator

Před použitím aplikace Microsoft Authenticator jako passwordless řešení pro přihlášení k účtu Azure AD, musí správce a koncoví uživatelé provést kroky.

Nejprve bude muset správce [povolit použití aplikace jako přihlašovací údaje](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-phone-sign-in#enable-my-users) v tenantovi pomocí Windows Powershellu. Správce budete muset povolit koncovým uživatelům pro ověřování Azure Multi-Factor Authentication (Azure MFA) a nakonfiguruje jako jednu z aplikace Microsoft Authenticator [metod ověření](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings#verification-methods).

Koncoví uživatelé budou muset [stáhněte a nainstalujte](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install) aplikaci Microsoft Authenticator a [nastavení svého účtu](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-auth-app) k používání aplikace Microsoft Authenticator jako jednu z metod ověřování.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Uživatele s využitím Microsoft Authenticator pro přihlašování

Aplikace Microsoft Authenticator je možné se přihlásit k libovolnému účtu služby Azure AD bez použití hesla. Zatímco zamykací obrazovky Windows 10 neobsahuje aplikaci Microsoft Authenticator jako možnost přihlášení, uživatelů lze také zadat svoje uživatelské jméno a pak dostanete nabízené oznámení na mobilním zařízení k ověření přítomnosti. Uživatelé potvrzení jejich přítomnost odpovídající číslo na přihlašovací obrazovce a pak poskytuje kontroly pro rozpoznávání tváře, otisk prstu nebo PIN kód a odemknout privátní klíč a bylo možné ověření dokončit. Tato metoda ověřování službou Multi-Factor Authentication je bezpečnější než hesla a pohodlnější než zadání hesla a kódu.

![Ověřovací data přihlášení](./media/azure-ad/azure-ad-pwdless-image4.png)

Passwordless ověřování pomocí aplikace Microsoft Authenticator používá stejný základní vzor jako Windows Hello pro firmy ale je o něco složitější vzhledem k tomu, že uživatel musí být určena v této službě Azure AD si najdou se verze aplikace Microsoft Authenticator použít.

![Ověřovací proces](./media/azure-ad/azure-ad-pwdless-image5.png)

1. Uživatel zadá své uživatelské jméno.

2. Azure AD zjistí, že uživatel má silné přihlašovací údaje a spustí tok silné přihlašovací údaje.

3. Odešle se oznámení do aplikace přes Apple Push Notification Service (APNS) na zařízeních s iOS nebo pomocí služby Firebase Cloud Messaging (FCM) na zařízeních s Androidem.

4. Uživatel obdrží nabízená oznámení a otevře aplikaci.

5. Volání služby Azure AD a aplikace obdrží výzvu testování of-presence a hodnota nonce.

6. Uživatel je výzva dokončena zadáním jejich biometriku nebo PIN kód pro odemknutí privátní klíč.

7. Hodnota nonce je podepsána s privátním klíčem a odesílaných zpět do služby Azure AD.

8. Azure AD provádí ověřování veřejného/soukromého klíče a vrátí token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Spravuje jejich přihlašování pomocí přihlašovacích údajů Microsoft Authenticator

S [kombinovat registrace](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), uživatelé můžou zaregistrovat a získat výhody Azure Multi-Factor Authentication a samoobslužné resetování hesla. Uživatelé registrovat a spravovat tato nastavení tak, že přejdete na jejich [Stránka Můj profil](https://aka.ms/mysecurityinfo). Kromě povolení samoobslužného resetování HESLA, kombinované registrace podporuje více metod ověřování a akce.

## <a name="fido2-security-keys"></a>FIDO2 zabezpečení klíčů

FIDO2 má nejnovější verzi standard FIDO Alliance a má dvě součásti – standard W3C pro webové ověřování (WebAuthN) a odpovídající protokol klienta Authenticator FIDO Alliance (CTAP2). FIDO2 standardy, které umožňují využívat hardware - mobile- a na základě biometrika ověřovací data snadno ověřit pomocí mnoha aplikace a weby do mobilních a desktopových prostředí.

Partneři Microsoftu a oboru spolupracují na FIDO2 zabezpečení zařízení pro Windows Hello umožňuje snadné a zabezpečené ověřování na sdílených zařízeních. FIDO2 zabezpečení klíče umožňují provádět svoje přihlašovací údaje s vámi a bezpečně ověřit [Azure AD](https://aka.ms/azuread418)– připojené k doméně, která je součástí vaší organizaci zařízení s Windows 10.

WebAuthN definuje rozhraní API, které umožňuje vývoj a implementace silných, passwordless ověřování ve webových aplikacích a službách. Protokol CTAP umožňuje externích zařízení, jako jsou kompatibilní s FIDO klíčů zabezpečení pracovat WebAuthN a slouží jako ověřovací data. S webovým ověřováním uživatelů můžete přihlásit k online službám s jejich pro rozpoznávání tváře, otisk prstu, PIN kód nebo přenosné FIDO2 zabezpečení klíčů, využívá silné přihlašovací údaje veřejného klíče místo hesla. Aktuálně WebAuthN se podporuje v Microsoft Edge a podpoře pro Chrome a Firefox je ve vývoji.

Zařízení a tokeny, které se řídí FIDO2 WebAuthN a CTAP protokolů přinášejí informace o řešení pro různé platformy ze silného ověřování bez použití hesla. Partneři Microsoftu pracují na různých faktorech klíče formuláře zabezpečení, jako jsou USB zabezpečení klíčů a podporou NFC čipové karty.

### <a name="fido2-security-keys-scenarios"></a>Scénáře FIDO2 zabezpečení klíčů

Klíče FIDO2 zabezpečení lze použít pro přihlášení k Azure AD pomocí klávesy zabezpečení jako poskytovatele přihlašovacích údajů na zamykací obrazovce Windows 10. Uživatelské jméno nebo heslo není povinné, takže je ideálním řešením pro první řádek pracovníky, kteří sdílet mezi více uživateli počítače. Jsou také vynikající ověřování možnost v případě podnikové zásady určují, přihlašovacích údajů uživatele musí být fyzicky oddělená od jejich zařízení. Uživatelé také mohou přihlásit k webovým stránkám s použitím jejich FIDO2 zabezpečení klíče uvnitř prohlížeče Microsoft Edge ve Windows 10 verze 1809 nebo vyšší.

### <a name="fido2-security-keys-deployment-considerations"></a>Informace o nasazení FIDO2 zabezpečení klíčů

Správci mohou povolit podporu FIDO2 ve službě Azure AD a možnost přiřadit uživatelům nebo skupinám. Zásady můžete vytvořit také pro jak klíče jsou zřízené a konfigurovat omezení, jako například povolení nebo blokování konkrétní sadu klíčů zabezpečení hardwaru. Klíče musí být fyzicky distribuovány koncovým uživatelům.

**Požadavky nutné k povolení passwordless přihlášení k Azure AD a weby pomocí klíčů zabezpečení FIDO2 zahrnují následující:**

* Azure AD

* Azure Multi-Factor Authentication

* Kombinované registraci ve verzi preview

* Vyžaduje kompatibilní klíč zabezpečení FIDO2 FIDO2 zabezpečení klíčů ve verzi preview

* Webové ověřování (WebAuthN) vyžaduje Microsoft Edge ve Windows 10 verze 1809 nebo vyšší

* Přihlášení založené na Windows vyžaduje Azure AD FIDO2 připojený k Windows 10 verze 1809 nebo vyšší (dosažení co nejlepších výsledků je ve Windows 10 verze 1903 nebo vyšší)

FIDO2 kompatibilní typy zařízení patří USB, NFC a Bluetooth zařízení. Doporučujeme, abyste zvolte faktor formuláře, který se bude splňovat vaše konkrétní požadavky od některé platformy a prohlížeče ještě nejsou FIDO2 nedodržující předpisy.

Doporučujeme také, že každá organizace vytvořit protokol pro uživatele a Správce by měl bezpečnostní klíč ztráta nebo odcizení. Uživatelé hlásit ztraceného nebo odcizeného klíč tak, aby správce nebo uživatel může odstranit jejich klíče zabezpečení z profilu uživatele a zřízení nového.

### <a name="how-fido2-security-keys-works"></a>Jak funguje FIDO2 zabezpečení klíčů

#### <a name="user-sets-up-fido2-security-key"></a>Uživatel nastaví FIDO2 klíč zabezpečení

Když správci můžou [ručně zřízení klíče](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) a jejich distribuci na koncové uživatele, budou podporovat zřizování a povolení FIDO2 poskytovatele přihlašovacích údajů na zamykací obrazovce Windows 10 prostřednictvím [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Správci budou také muset použít [webu Azure portal](https://portal.azure.com/) umožňující hardwarových tokenů zařízení jako metoda passwordless ověření.

Nasazení FIDO2 zabezpečení klíčů taky vyžaduje, aby uživatelé registrovat svoje klíče pomocí [kombinovat registrace](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined). Kombinovaná registrace uživatelé po registraci a získat výhody Azure Multi-Factor Authentication a resetování hesla jednotné přihlašování (SSPR).

Kromě výběru hardwarový token jako výchozí metodu ověřování službou Multi-Factor Authentication, doporučujeme také vybrat možnost dalšího ověření.

* Microsoft Authenticator – oznámení

* Aplikace Authenticator nebo hardware token – kód

* Telefonní hovor

* Textová zpráva

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Uživatele s využitím FIDO2 bezpečnostní klíč k přihlášení

FIDO2 poskytuje abstraktní vrstvu mezi provedení používá jako ověřovací data a veřejného/soukromého klíče k umožnění integrované platformy ověřovací data jako jsou Windows Hello a zabezpečení klíče přeložit na privátní klíč a dodávat veřejný klíč který může sloužit jako identifikátor pro přístup k externím prostředkům. FIDO2 zabezpečení klíče jsou vybaveny vlastní integrované zabezpečené enklávy, který uloží privátní klíč a vyžaduje biometriku nebo PIN kód k jeho odemčení. Přihlašovací údaje nejde použít znovu, přehrály, nebo sdílet napříč službami a nejsou v souladu s útoky phishing a útoky MiTM nebo server porušení.

![Přihlášení FIDO2](./media/azure-ad/azure-ad-pwdless-image6.png)

Klíče zabezpečení FIDO2 poskytování zabezpečeného ověřování, nezávisle na provedení. Klíč zabezpečení obsahuje přihlašovací údaje a s další druhý faktor, třeba otisk prstu (integrováno klíč zabezpečení) nebo PIN kód je třeba zadat při přihlášení Windows by měly být chráněné. Partneři Microsoftu pracují na různých faktorech zabezpečení klíčů formuláře. Mezi příklady patří USB zabezpečení klíčů a NFC povolené čipové karty.

> [!NOTE]
> Klíč zabezpečení musí implementovat určité funkce a rozšíření z protokolu FIDO2 CTAP bude [kompatibilní se službou Microsoft](https://aka.ms/fido2securitykeys). Společnost Microsoft testovala těchto řešení pro kompatibilitu s Windows 10 a Azure Active Directory.

![Proces přihlašování FIDO2](./media/azure-ad/azure-ad-pwdless-image9.png)

1. Uživatel připojí zařízení FIDO2 do počítače.

2. Windows zjistí FIDO2 klíč zabezpečení.

3. Windows odešle požadavek ověřování.

4. Azure AD odešle zpět hodnotu nonce.

5. Uživatel vykoná jejich gesta odemknout privátní klíč uložený ve klíč zabezpečení FIDO2 zabezpečené enklávy.

6. Klíč zabezpečení FIDO2 podepíše hodnotu nonce s privátním klíčem.

7. PRT žádosti o token s podpisem hodnota nonce je odeslána do služby Azure AD.

8. Azure AD ověřuje podepsané nonce pomocí FIDO2 veřejného klíče.

9. Azure AD vrací PRT umožňuje přístup k místním prostředkům.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Spravuje jejich FIDO2 pověření klíče zabezpečení

Podobně jako aplikaci Microsoft Authenticator, Správa přihlašovacích údajů pro zabezpečení klíčů FIDO2 spoléhá na kombinované registraci prostředí pro koncové uživatele.

## <a name="deciding-a-passwordless-method"></a>Rozhodování o tom, passwordless – metoda

Volba mezi těmito třemi passwordless možnostmi závisí na zabezpečení vaší společnosti, platformy a požadavky na aplikace.

Zde jsou některé faktory, které je třeba zvážit při výběru technologie společnosti Microsoft bez hesla:

||**Windows Hello pro firmy**|**Přihlašování v aplikaci Microsoft Authenticator**|**FIDO2 zabezpečení klíčů**|
|:-|:-|:-|:-|
|**Předpoklad**| Windows 10, verze 1809 nebo novější<br>Azure Active Directory| Aplikace Microsoft Authenticator<br>Telefon (iOS a Android zařízení se systémem Android 6.0 nebo vyšší.)|Windows 10, verze 1809 nebo novější<br>Azure Active Directory|
|**Mode**|Platforma|Software|Hardware|
|**Systémů a zařízení**|Počítače s integrované čipu Trusted Platform Module (TPM)<br>PIN kód a biometrika rozpoznávání |PIN kód a biometrika rozpoznávání na telefonu|FIDO2 zabezpečení zařízení, která jsou kompatibilní Microsoft|
|**Činnost koncového uživatele**|Přihlaste se pomocí PIN kód nebo biometrické rozpoznávání (facial, iris nebo otisk prstu) se zařízeními s Windows.<br>Ověřování Windows Hello se váže na zařízení. Uživatel musí zařízení a komponenty přihlášení jako je například PIN kód nebo biometrické faktor přístup k firemním prostředkům.|Přihlaste se pomocí rozpoznávání kontroly, facial nebo iris otisk prstu mobilní telefon nebo PŘIPNOUT.<br>Uživatelé přihlásit na pracovní nebo osobní účet ze svého počítače nebo mobilního telefonu.|Přihlaste se pomocí zabezpečovacího zařízení FIDO2 (biometrika, PIN kód a NFC)<br>Uživatel může získat přístup k zařízení podle organizace ovládací prvky a ověřování na základě PIN kód, biometrické údaje, pomocí zařízení, jako jsou USB zabezpečení klíčů a čipové karty s povolenou technologií NFC, klíčů nebo přenosné.|
|**Scénáře povolené**| Prostředí bez hesla pro zařízení Windows.<br>Vztahuje se vyhrazené počítači v práci s možnosti jednotného přihlašování k zařízení a aplikací.|Bez hesla kdekoli řešení pomocí mobilního telefonu.<br>Použít pro přístup k pracovní nebo osobní aplikace na webu z libovolného zařízení.|Prostředí bez hesla pro pracovní procesy podle biometrika, PIN kód a NFC.<br>Platí pro sdílené počítače a kde mobilního telefonu není vhodným řešením (například pracovníci helpdesku, veřejného terminálu nebo tým nemocnice)|

V následující tabulce použijte zvolit, která metoda bude podporovat požadavky a uživatelů.

|Modelové osoby|Scénář|Prostředí|Passwordless technologie|
|:-|:-|:-|:-|
|**Správce**|Zabezpečený přístup k zařízení pro úlohy správy|Přiřazená zařízení s Windows 10|Windows Hello pro firmy a/nebo FIDO2 klíč zabezpečení|
|**Správce**|Úlohy správy na zařízení bez Windows| Mobilní telefon nebo jiný systém než windows zařízení|Přihlašování v aplikaci Microsoft Authenticator|
|**Informační pracovník**|Pracovní produktivity|Přiřazená zařízení s Windows 10|Windows Hello pro firmy a/nebo FIDO2 klíč zabezpečení|
|**Informační pracovník**|Pracovní produktivity| Mobilní telefon nebo jiný systém než windows zařízení|Přihlašování v aplikaci Microsoft Authenticator|
|**Prvotní pracovního procesu**|Veřejné terminály ve vstupní objekt pro vytváření, zařízení, maloobchodního prodeje nebo dat.|Sdílená zařízení s Windows 10|Zabezpečení FIDO2 klíče|

## <a name="getting-started"></a>Začínáme

Passwordless ověřování je vlnou budoucnost a cesta k lepšímu zabezpečení prostředí. Doporučuje se, že organizace začít plánovat pro tuto změnu, jednak snižuje závislé na hesla. Abyste mohli začít, vezměte v úvahu následující požadavky:

* Povolte uživatele pro MFA, aplikaci Microsoft Authenticator + podmíněného přístupu.

* Ochrana hesel Azure AD zavedení + zásady aktualizace.

* Povolte uživatelům s kombinovaná registrace pro samoobslužné resetování HESLA.

* Nasaďte aplikaci Microsoft Authenticator pro mobilitu.

* Nasazení Windows Hello pro firmy (1903: aktuální).

* Nasazení FIDO2 zařízení pro uživatele, kteří nelze použít telefony.

* Pokud je to možné, zakažte ověřování pomocí hesla.

K dosažení těchto cílů, doporučujeme následující postup:

1. Povolte Azure Active Directory a využívat všech výhod funkce, jako je Azure MFA a podmíněného přístupu.

2. Povolení služby Multi-Factor authentication k zajištění další ochrany.

3. Povolení samoobslužného resetování hesla v případě, že uživatelé musí vrátit zpět k použití hesla.

4. Nasaďte aplikaci Microsoft Authenticator přihlašování telefonem pro přidání mobility.

5. Nasazení Windows Hello pro firmy do všech zařízení s Windows 10.

6. Příprava pro FIDO2 zabezpečení klíče.

> [!NOTE]
> Odkazovat na Azure Active Directory [licenční stránka](https://azure.microsoft.com/pricing/details/active-directory/) podrobné informace o licenčním požadavkům pro passwordless metody.

## <a name="conclusion"></a>Závěr

V posledních několika let společnost Microsoft pokračuje jejich počáteční závazek k povolení world bez hesla. S Windows 10 Microsoft zavedl službu Windows Hello pro firmy, silné, chráněný hardware dvojúrovňového pověření, která umožňuje jednotné přihlašování k Azure Active Directory a Active Directory. Podobně jako na technologii Windows Hello pro firmy, aplikaci Microsoft Authenticator používá ověřování založené na klíč k povolení přihlašovací údaje uživatele, který se váže na mobilním zařízení a používá biometrika nebo PIN kód. FIDO2 zabezpečení klíčů nyní umožňují provádět svoje přihlašovací údaje s vámi a přihlaste se k Azure AD pomocí klávesy zabezpečení jako poskytovatele přihlašovacích údajů na zamykací obrazovce Windows 10. Všechny tři z těchto řešení passwordless snížilo riziko útoků phishing, hesla zařízení a útoky opakováním a poskytují uživatelům vysoce zabezpečené a pohodlný způsob, jak přihlásit a získat přístup k datům z libovolného místa.

Přijetí moderního ověřování službou Multi-Factor Authentication technologie, jako je biometrika a kryptografii využívající veřejného klíče v široce dostupný zařízení je jeden z jejich činnost nejvíc ohrožují kroků, které může smysluplně snížit rizika identity vaší společnosti. Přechod passwordless je dlouhodobý přístup pro zabezpečené ověřování a se dál vyvíjejí. Zadaný nově vznikající požadavky, organizace můžete připravit tak, že chcete začít s přesunem passwordless technologie.

## <a name="next-steps"></a>Další postup

* Přehled [novinky passwordless?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)
* [Jak povolit passwordless ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable)

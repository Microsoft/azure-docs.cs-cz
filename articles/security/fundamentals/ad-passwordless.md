---
title: Porozumění celému světě bez hesel s Azure Active Directory | Microsoft Docs
description: Tato příručka pomáhá CEOs, ředitelé informačních technologií, CISO, vedoucím architektům identity, podnikovým architektům a organizacím pro rozhodování o tom, kdo zodpovídá za výběr metody ověřování bez hesla pro svou Azure Active Directory implementaci.
keywords: bez hesla, azuread
author: martincoetzer
ms.author: martinco
ms.date: 07/09/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: e702a5921e785100c5971057d1652db9aab26acf
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899959"
---
# <a name="a-world-without-passwords-with-azure-active-directory"></a>World bez hesel s Azure Active Directory

Je čas k přerušení relace s hesly. Hesla jsou pro nás v minulosti dobrá, ale v dnešním digitálním pracovišti se stanou poměrně jednoduchým způsobem útoku hackerů. Hackeři mají hesla a není obtížné zjistit, proč se vám v Azure Active Directory (Azure AD) nejvíce odmítají hesla zahrnou například rok, měsíc, období nebo místní sportovní tým. Kromě toho [výzkum ukázal](https://aka.ms/passwordguidance) , že tradiční doporučení pro správu hesel, jako jsou požadavky na délku, požadavky na složitost a frekvence změny, jsou counterproductive z nejrůznějších důvodů, které se týkají lidského charakteru.

Tři typy útoků, které se běžně používají k ohrožení uživatelských účtů, jsou postřikem hesla, útoky phishing a nedodržení předpisů. Funkce Azure AD, jako je [inteligentní uzamčení](../../active-directory/authentication/howto-password-smart-lockout.md), [zakázaná hesla](../../active-directory/authentication/concept-password-ban-bad-on-premises.md)a [ochrana heslem](../../active-directory/authentication/concept-password-ban-bad-on-premises.md) , můžou přispět k ochraně před těmito typy útoků. Podobně implementace [vícefaktorového ověřování](../../active-directory/authentication/concept-mfa-howitworks.md) (MFA) nebo dvoustupňové ověřování poskytuje dodatečné zabezpečení tím, že vyžaduje druhou formu ověřování. Ale v dlouhodobém běhu je řešení bez hesla nejlepší řešení pro zajištění nejbezpečnější metody ověřování.

Tento článek představuje začátek vaší cesty, který vám pomůže pochopit a implementovat řešení nejenom v heslech od Microsoftu a pomůže vám vybrat jednu nebo více z těchto možností:

* **Windows Hello pro firmy**. Ve Windows 10 nahrazuje Windows Hello pro firmy hesla se silným dvojúrovňovém ověřováním na počítačích a mobilních zařízeních. Toto ověřování se skládá z nového typu uživatelských přihlašovacích údajů, které jsou vázané na zařízení, a používá biometriku nebo PIN.

* **Přihlášení bez hesla pomocí Microsoft Authenticator**. Aplikaci Microsoft Authenticator můžete použít k přihlášení k účtu Azure AD bez použití hesla. Podobně jako technologie Windows Hello pro firmy používá Microsoft Authenticator k povolení přihlašovacích údajů uživatele, které jsou vázané na zařízení, a používá biometriku nebo PIN kód.

* **FIDO2 klíče zabezpečení**. FIDO2 poskytuje přihlašovací údaje pro kryptografické přihlášení, které jsou jedinečné napříč všemi weby a jsou uložené na místním zařízení, jako je Windows Hello nebo externí bezpečnostní klíče. Tyto bezpečnostní klíče jsou odolné vůči rizikům útoků phishing, krádeží hesla a přehrání. V kombinaci s ověřením uživatele prostřednictvím biometrika nebo PIN je řešení dvou ověření faktorů, které splňuje požadavky moderních zabezpečení.

## <a name="the-future-of-passwordless-authentication"></a>Budoucí ověřování neheslem

Tyto dny, banky, společnosti s kreditními kartami a dalšími organizacemi a online služby často chrání váš účet tím, že vyžadují, abyste ověřili identitu dvakrát: jednou pomocí svého hesla a potom znovu telefonem, textem nebo aplikací. I když Multi-Factor Authentication řeší problémy se zabezpečením sdílených, odcizených nebo vypočítaných hesel, neřeší nepříjemnosti při jejich zapamatování. Kteří uživatelé a organizace chtějí v dnešním cloudovém období používat metody ověřování bez hesla, které jsou vysoce zabezpečené *a* pohodlné.

![Pohodlí a zabezpečení](./media/ad-passwordless/azure-ad-pwdless-image1.png)

Windows Hello pro firmy, přihlášení bez hesla pomocí Microsoft Authenticator a FIDO2 klíče zabezpečení sdílí jednoduchou, společnou architekturu, která uživatelům poskytuje metodu ověřování, která je vysoce zabezpečená a pohodlná k použití. Všechny tři jsou založené na technologii veřejného a privátního klíče, potřebují místní gesto, jako je biometrika nebo PIN, a privátní klíče vázané na jedno zařízení a bezpečně uložené a nikdy sdílené.

## <a name="windows-hello-for-business"></a>Windows Hello pro firmy

Ve Windows 10 nahrazuje Windows Hello pro firmy hesla se silným dvojúrovňovém ověřováním na počítačích a zařízeních. Ověřování se skládá z nového typu přihlašovacích údajů uživatele, které jsou svázané se zařízením a používá biometrické gesto nebo PIN kód, který umožňuje uživatelům ověřování ve službě Azure AD i v místní službě Active Directory. Jednoduše se přihlašujete k zařízení pomocí Windows Hello pro firmy, které je snadné. Používáte buď PIN nebo biometrické gesto, jako je otisk prstu nebo rozpoznávání obličeje.

### <a name="windows-hello-for-business-scenarios"></a>Scénáře Windows Hello pro firmy

Windows Hello pro firmy je ideální pro informační pracovníky, kteří mají vlastní určený počítač s Windows. Biometrika a přihlašovací údaje jsou přímo vázané na počítač uživatele, který brání přístupu od jiných osob než vlastník. S integrací infrastruktury veřejných klíčů a integrovanou podporou jednotného přihlašování (SSO) nabízí Windows Hello pro firmy jednoduchou a pohodlnou metodu pro bezproblémové přístup k podnikovým prostředkům místně a v cloudu.

### <a name="windows-hello-for-business-deployment-considerations"></a>Windows Hello pro firmy – požadavky na nasazení

Windows Hello pro firmy je distribuovaný systém, který pomocí několika komponent provádí registraci, zřizování a ověřování zařízení. Proto nasazení vyžaduje správné plánování napříč několika týmy v rámci organizace. [Průvodce plánováním](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello pro firmy se dá využít k rozhodování o typu nasazení Windows Hello pro firmy a možnostech, které budete muset vzít v úvahu.

Existuje mnoho možností, ze kterých si můžete vybrat při nasazování Windows Hello pro firmy. Poskytování více možností zajišťuje, aby skoro každá organizace mohla nasadit Windows Hello pro firmy. Vezměte v úvahu následující typy nasazení, které jsou podporovány:

* [Nasazení vztahu důvěryhodnosti pro hybridní klíč připojený k Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-key-trust)

* [Hybridní nasazení důvěryhodného certifikátu připojené k Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)

* [Příručky k nasazení jednotného přihlašování v Azure AD](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso)

* [Nasazení důvěryhodnosti místních klíčů](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-key-trust)

* [Nasazení místních důvěryhodných certifikátů](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-cert-trust)

Poskytování mnoha možností způsobí, že se nasazení bude složitých. Většina organizací ale pravděpodobně určí, že už implementují většinu infrastruktury, na které závisí nasazení Windows Hello pro firmy. Bez ohledu na to je důležité pochopit, že Windows Hello pro firmy je distribuovaný systém a doporučuje se správné plánování.

Doporučujeme, abyste si přečetli téma [Plánování nasazení Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) , které vám pomůžou rozhodnout, jak model nasazení nejlépe vyhovuje vašemu konkrétní organizaci. Pak v závislosti na plánování, které uděláte, najdete v [Průvodci nasazením Windows Hello pro firmy](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide) , které vám pomůže zajistit úspěšné nasazení Windows Hello pro firmy ve vašem stávajícím prostředí.

### <a name="how-windows-hello-for-business-works"></a>Jak funguje Windows Hello pro firmy

#### <a name="user-sets-up-windows-hello-for-business"></a>Uživatel nastaví Windows Hello pro firmy.

Po prvním dvoustupňovém ověření uživatele během registrace se na zařízení uživatele nastaví Windows Hello a systém Windows vyzve uživatele k nastavení gesta, což může být biometrika, jako je otisk prstu nebo rozpoznávání obličeje nebo PIN kód. Po nastavení uživatel poskytne gesto pro ověření identity. Systém Windows pak pomocí Windows Hello ověří uživatele.

Na základě schopností vašeho zařízení s Windows 10 budete mít integrovaný zabezpečený enklávy, který je známý jako hardware Trusted Platform Module (TPM) nebo softwarový čip TPM. ČIP TPM ukládá privátní klíč, který vyžaduje, abyste ho odemkli od svého obličeje, otisku prstu nebo PIN kódu. Biometriková data neroamingá a nikdy se neodesílají externím zařízením nebo serverům. Neexistuje žádný jednotlivý bod shromažďování. útočník může ohrozit krádež dat, protože Windows Hello ukládá do zařízení pouze data biometrická identifikace.

> [!TIP]
> Na povrchu se PIN kód zdá jako heslo, ale ve skutečnosti je bezpečnější. Důležitým rozdílem mezi heslem a PIN kódem je to, že PIN kód je vázaný na konkrétní zařízení, na kterém se nastavilo. Někdo, kdo heslo ukrást, se může přihlásit k účtu odkudkoli. Pokud ale ukrást váš PIN kód, museli by to ukrást i vaše fyzické zařízení! Vzhledem k tomu, že je PIN kód pro zařízení místní, není přenos odkudkoli, takže se nedá zachytit v přenosu nebo odcizení ze serveru.

#### <a name="user-using-windows-hello-for-business-for-sign-in"></a>Uživatel, který používá Windows Hello pro firmy pro přihlášení

Biometrika a PIN Windows Hello pro firmy používají pro ověřování asymetrické šifrování (veřejný a privátní klíč). Při ověřování je šifrování vázané na klíč relace protokolu TLS, který zabezpečuje proces ověřování, aby útok MiTM (man-in-the-middle-the-middle) nemohl ukrást výsledný token zabezpečení nebo artefakt a přehrát ho z jiných míst.

Windows Hello pro firmy nabízí pohodlný způsob přihlašování, který ověřuje uživatele v prostředcích Azure AD a Active Directory. Zařízení připojená k Azure AD se během přihlašování ověřují do Azure a můžou se volitelně ověřit ve službě Active Directory. Zařízení připojená k hybridním Azure Active Directory se během přihlašování ověřují do služby Active Directory a ověřují se Azure Active Directory na pozadí.

![Zobrazit zdrojový obrázek](./media/ad-passwordless/azure-ad-pwdless-image2.jpeg)

Následující kroky ilustrují ověřování přihlašovacích Azure Active Directory.

![Zamykací obrazovka Windows 10](./media/ad-passwordless/azure-ad-pwdless-image3.png)

1. Uživatel se do systému Windows přihlásí pomocí gesta biometriky nebo PIN kódu. Gesto odemkne privátní klíč Windows Hello pro firmy a pošle se do zprostředkovatele podpory zabezpečení Cloud Authentication, který se označuje jako poskytovatel cloudového přístupového bodu.

2. Zprostředkovatel cloudového AP požaduje hodnotu NONCE z Azure Active Directory.

3. Služba Azure AD vrátí hodnotu NONCE platnou po dobu 5 minut.

4. Poskytovatel cloudového AP podepíše hodnotu NONCE pomocí privátního klíče uživatele a vrátí znaménku příznaku Azure Active Directory.

5. Azure Active Directory ověří znaménko hodnoty nonce pomocí bezpečného registrovaného veřejného klíče uživatele s podpisem hodnoty nonce. Po ověření signatury ověří služba Azure AD vrácenou znaménku nonce. Po ověření hodnoty nonce vytvoří Azure AD PRT s klíčem relace, který se zašifruje do přenosového klíče zařízení a vrátí ho do poskytovatele cloudového AP.

6. Zprostředkovatel cloudového AP obdrží šifrované PRT s klíčem relace. Pomocí privátního transportního klíče zařízení poskytovatel cloudového AP dešifruje klíč relace a chrání klíč relace pomocí čipu TPM zařízení.

7. Zprostředkovatel cloudového AP vrátí úspěšnou odpověď ověření systému Windows, po které bude mít uživatel přístup k Windows i k cloudovým a místním aplikacím bez nutnosti ověřování (SSO).

Podrobnější přehled procesu ověřování v jiných scénářích týkajících se Windows Hello pro firmy najdete v tématu [Windows Hello pro firmy a ověřování](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-authentication#azure-ad-join-authentication-to-active-directory-using-a-key).

#### <a name="user-manages-their-windows-hello-for-business-credentials"></a>Uživatel spravuje svoje přihlašovací údaje ve Windows Hello pro firmy.

[Služba Microsoft PIN Reset Services](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-features#pin-reset) je funkce služby Azure AD, která uživatelům umožňuje v případě potřeby resetovat PIN kód. Pomocí zásad skupiny, Microsoft Intune nebo kompatibilní správy mobilních zařízení (MDM) může správce nakonfigurovat zařízení s Windows 10 tak, aby bezpečně používala službu Microsoft PIN Reset Service, která uživatelům umožňuje resetovat své zapomenuté kódy PIN pomocí nastavení nebo nad zamykací obrazovkou bez nutnosti opětovné registrace.

Někdy se uživatelé musí vrátit k používání hesel. [Samoobslužné resetování hesla](../../active-directory/authentication/howto-sspr-deployment.md) (SSPR) je další funkce služby Azure AD, která uživatelům umožňuje resetovat hesla bez nutnosti kontaktovat pracovníky oddělení IT. Před použitím této služby je třeba, aby se uživatelé zaregistrovali pro Samoobslužné resetování hesla. Během registrace uživatel zvolí jednu nebo více metod ověřování povolených organizací. SSPR umožňuje uživatelům rychle získat odblokování a pokračovat v práci bez ohledu na to, kde jsou, nebo v denní době. Díky tomu, že uživatelé můžou odblokovat samy sebe, může vaše organizace zkrátit neproduktivní dobu a náklady na vysokou podporu pro nejběžnější problémy související s heslem.

## <a name="passwordless-sign-in-with-microsoft-authenticator"></a>Přihlášení bez hesla pomocí Microsoft Authenticator

Přihlášení bez hesla pomocí Microsoft Authenticator je jiné řešení bez hesla, které se dá použít k přihlášení k účtům Azure AD pomocí přihlášení telefonem. Pořád musíte ověřit vaši identitu tím, že zadáte něco, co znáte, a vy, ale telefonní přihlášení vám umožní přeskočit zadání hesla a na vašem mobilním zařízení provádět veškerá ověření identity pomocí otisku prstu, obličeje nebo PIN kódu.

### <a name="microsoft-authenticator-passwordless-scenarios"></a>Microsoft Authenticator scénáře s nestejnými hesly

Aplikace Microsoft Authenticator umožňuje uživatelům ověřit svoji identitu a ověřit ji pro svůj pracovní nebo osobní účet. Dá se použít taky k rozšíření hesla s jednorázovým heslem nebo nabízeným oznámením nebo k tomu, aby se heslo zcela nahradilo. Místo použití hesla uživatelé potvrdí svoji identitu pomocí svého mobilního telefonu prostřednictvím kontroly otisků prstů, obličeje nebo Iris nebo PIN kódu. Tento nástroj je integrovaný do jednoduché aplikace na mobilním zařízení, která je postavená na zabezpečené technologii podobně jako to, co používá Windows Hello. pro uživatele to umožní pohodlnou možnost. Aplikace Microsoft Authenticator je k dispozici pro Android a iOS.

### <a name="microsoft-authenticator-deployment-considerations"></a>Microsoft Authenticator – požadavky na nasazení

Požadavky na použití aplikace Microsoft Authenticator k přihlášení neheslem do služby Azure AD zahrnuje následující:

* Koncoví uživatelé mají povolený Azure Multi-Factor Authentication.

* Doporučuje se, aby si uživatelé zaregistrovali svoje zařízení pomocí Microsoft Intune nebo řešení správy mobilních zařízení (MDM) od jiného výrobce, aby se usnadnilo nasazení aplikace.

Za předpokladu, že jsou splněny tyto požadavky, můžou správci povolit přihlášení bez hesla v tenantovi pomocí [Windows PowerShellu.](../../active-directory/authentication/howto-authentication-passwordless-phone.md) Po povolení telefonického přihlášení v tenantovi se koncoví uživatelé můžou přihlásit pomocí svého telefonu tak, že na obrazovce **účty** aplikace zvolí svůj pracovní nebo školní účet a pak se vybere **možnost Povolit přihlášení k telefonu**.

Pokud správce povolí přihlášení bez hesla, budou muset koncoví uživatelé splňovat následující požadavky:

* Zaregistrováno v Azure Multi-Factor Authentication

* Nejnovější verze Microsoft Authenticator nainstalovaná na zařízeních se systémem iOS 8,0 nebo novějším nebo Androidem 6,0 nebo novějším

* Pracovní nebo školní účet s nabízenými oznámeními přidanými do aplikace

Aby nedocházelo k tomu, že by se zabránilo zablokování účtu nebo opětovnému vytvoření účtů na novém zařízení, doporučujeme použít Microsoft Authenticator k [zálohování přihlašovacích údajů k účtu](../../active-directory/user-help/user-help-auth-app-backup-recovery.md) do cloudu. Po zálohování můžete také použít aplikaci k obnovení svých informací na novém zařízení, což může zabránit uzamknutí nebo opětovnému vytvoření účtů.

Vzhledem k tomu, že většina uživatelů je zvykla používat k ověřování pouze hesla, je důležité, aby vaše organizace pomohly uživatele týkající se tohoto procesu. Povědomí může snížit pravděpodobnost, že uživatelé volají vaši technickou podporu ohledně všech [problémů](../../active-directory/authentication/howto-authentication-passwordless-phone.md) souvisejících s přihlášením pomocí aplikace Microsoft Authenticator.

> [!NOTE]
> Potenciálním bodem selhání tohoto řešení je, když je uživatel typu roaming v umístění, kde není k dispozici připojení k Internetu. Bezpečnostní klíče FIDO2 a Windows Hello pro firmy nepodléhají stejnému omezení.

### <a name="how-passwordless-sign-in-with-microsoft-authenticator-works"></a>Jak funguje přihlašování bez hesla pomocí Microsoft Authenticator

#### <a name="user-sets-up-passwordless-sign-in-with-microsoft-authenticator"></a>Uživatel nastaví přihlášení bez hesla pomocí Microsoft Authenticator

Aby bylo možné aplikaci Microsoft Authenticator použít jako řešení bez hesla pro přihlášení k účtu služby Azure AD, musí být kroky provedeny správcem i koncovými uživateli.

Nejdřív bude správce muset [Povolit používání aplikace jako přihlašovací údaje](../../active-directory/authentication/howto-authentication-passwordless-phone.md) v Tenantovi pomocí Windows PowerShellu. Správce bude taky muset povolit koncové uživatele pro Azure Multi-Factor Authentication (Azure MFA) a nakonfigurovat Microsoft Authenticator aplikaci jako jednu z [metod ověřování](../../active-directory/authentication/howto-mfa-mfasettings.md#verification-methods).

Koncoví uživatelé budou muset [Stáhnout a nainstalovat](../../active-directory/user-help/user-help-auth-app-download-install.md) aplikaci Microsoft Authenticator a [nastavit účet](../../active-directory/user-help/security-info-setup-auth-app.md) tak, aby používal aplikaci Microsoft Authenticator jako jednu z metod ověřování.

> [!VIDEO https://www.youtube.com/embed/uWbkLuI4g30]

#### <a name="user-using-microsoft-authenticator-for-passwordless-sign-in"></a>Uživatel, který používá Microsoft Authenticator pro přihlášení se neheslem

Aplikaci Microsoft Authenticator můžete použít k přihlášení k jakémukoli účtu Azure AD bez použití hesla. I když zamykací obrazovka Windows 10 nezahrnuje aplikaci Microsoft Authenticator jako možnost přihlášení, uživatelé můžou pořád zadat své uživatelské jméno a pak na svém mobilním zařízení dostávat nabízená oznámení, aby ověřili přítomnost. Uživatelé potvrdí jejich přítomnost tak, že odpovídají číslům na přihlašovací obrazovce a pak poskytují kontrolu obličeje, otisk prstu nebo PIN pro odemknutí privátního klíče a dokončí ověřování. Tato metoda vícefaktorového ověřování je bezpečnější než heslo a pohodlnější než zadání hesla a kódu.

![Přihlášení ověřovatele](./media/ad-passwordless/azure-ad-pwdless-image4.png)

Ověřování bez hesla pomocí Microsoft Authenticator odpovídá stejnému základnímu vzoru jako Windows Hello pro firmy, ale je trochu složitější, protože je potřeba identifikovat uživatele, aby služba Azure AD mohla najít Microsoft Authenticator verzi aplikace. pro.

![Proces ověřovatele](./media/ad-passwordless/azure-ad-pwdless-image5.png)

1. Uživatel zadá své uživatelské jméno.

2. Azure AD zjistí, že uživatel má silné přihlašovací údaje a spustí tok silných přihlašovacích údajů.

3. Oznámení se pošle do aplikace přes Apple Push Notification Service (APNS) na zařízeních s iOS nebo přes Firebase Cloud Messaging (FCM) na zařízeních s Androidem.

4. Uživatel obdrží nabízené oznámení a otevře aplikaci.

5. Aplikace volá Azure AD a obdrží výzvu a hodnotu NONCE pro kontrolu přítomnosti.

6. Uživatel dokončí výzvu zadáním jejich biometriky nebo PIN kódu k odemknutí privátního klíče.

7. Hodnota nonce je podepsaná privátním klíčem a pošle zpátky do služby Azure AD.

8. Azure AD provede ověření veřejného a privátního klíče a vrátí token.

#### <a name="user-manages-their-passwordless-sign-in-with-microsoft-authenticator-credentials"></a>Uživatel spravuje přihlášení bez hesla pomocí Microsoft Authenticator přihlašovacích údajů.

Díky [kombinované registraci](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md)se uživatelé můžou zaregistrovat a získat výhody ověřování Azure Multi-Factor Authentication a samoobslužného resetování hesla. Uživatelé registrují a spravují tato nastavení tak, že přejde na [stránku Můj profil](https://aka.ms/mysecurityinfo). Kromě povolení SSPR podporuje kombinovaná registrace více metod ověřování a akcí.

## <a name="fido2-security-keys"></a>FIDO2 klíče zabezpečení

FIDO2 je nejnovější verze FIDO Alliance Standard a má dvě úrovně Standard-W3C's web Authentication (WebAuthN) a odpovídající protokol FIDO Alliance Client-to-Authenticator (CTAP2). Standardy FIDO2 umožňují uživatelům využívat ověřování na základě hardwaru, mobilních zařízení a biometrika, aby se mohli snadno ověřit s mnoha aplikacemi a weby v mobilních a desktopových prostředích.

Společnost Microsoft a její partneři spolupracovali na zařízeních zabezpečení FIDO2 pro Windows Hello, aby umožnili snadné a zabezpečené ověřování na sdílených zařízeních. Klíče zabezpečení FIDO2 vám umožňují přenášet vaše přihlašovací údaje s vámi a bezpečně ověřit na zařízení s Windows 10 připojené k [Azure AD](https://aka.ms/azuread418), které je součástí vaší organizace.

Operace WebAuthN definuje rozhraní API, které umožňuje vývoj a implementaci silného ověřování bez hesla pomocí webových aplikací a služeb. Protokol CTAP umožňuje externím zařízením, jako jsou bezpečnostní klíče kompatibilní s FIDO, pracovat s operacemi WebAuthN a sloužit jako ověřovatelé. S webovým ověřováním se uživatelé můžou přihlásit k online služby se svými klíčovými a otisky prstů, otisků nebo přenosných klíčů zabezpečení FIDO2 a využívají silné přihlašovací údaje veřejného klíče místo hesel. V současné době je operace WebAuthN podporována v Microsoft Edge a podpora pro Chrome a Firefox je ve vývoji.

Zařízení a tokeny, které jsou součástí FIDO2, WebAuthN a CTAP, přinášejí informace o řešeních silného ověřování pro různé platformy bez použití hesel. Partneři Microsoftu pracují na nejrůznějších faktorech ve formě bezpečnostních klíčů, jako jsou klíče zabezpečení USB a čipové karty s podporou NFC.

### <a name="fido2-security-keys-scenarios"></a>Scénáře bezpečnostních klíčů FIDO2

Klíče zabezpečení FIDO2 se dají použít k přihlášení ke službě Azure AD tak, že na zamykací obrazovce Windows 10 vyberete klíč zabezpečení jako poskytovatele přihlašovacích údajů. Uživatelské jméno nebo heslo není vyžadováno, což umožňuje ideální řešení pro první linii pracovníků, kteří sdílejí počítače mezi více uživateli. Představují taky vynikající možnost ověřování, když firemní zásady určují, že přihlašovací údaje uživatele musí být fyzicky oddělené od jejich zařízení. Uživatelé se také můžou rozhodnout přihlašovat se k webům pomocí svého bezpečnostního klíče FIDO2 v prohlížeči Microsoft Edge ve Windows 10 verze 1809 nebo vyšší.

### <a name="fido2-security-keys-deployment-considerations"></a>FIDO2 klíče zabezpečení – požadavky na nasazení

Správci můžou povolit podporu FIDO2 ve službě Azure AD a přiřazovat funkce uživatelům nebo skupinám. Je také možné vytvořit zásady pro způsob zřízení klíčů a konfiguraci omezení, jako je povolení nebo blokování konkrétní sady klíčů zabezpečení hardwaru. Klíče je nutné fyzicky distribuovat koncovým uživatelům.

**Požadavky na povolení přihlášení pomocí hesla k Azure AD a webům pomocí klíčů zabezpečení FIDO2 zahrnují následující:**

* Azure AD

* Azure Multi-Factor Authentication

* Kombinovaná registrace – náhled

* FIDO2 Security Key Preview vyžaduje kompatibilní klíč zabezpečení FIDO2.

* Webové ověřování (WebAuthN) vyžaduje Microsoft Edge ve Windows 10 verze 1809 nebo vyšší.

* Přihlášení systému Windows založené na FIDO2 vyžaduje Windows 10 připojená k Azure AD verze 1809 nebo vyšší (nejlepší prostředí je ve Windows 10 verze 1903 nebo novější).

Mezi faktory vyhovující FIDO2 patří zařízení USB, NFC a Bluetooth. Doporučujeme, abyste vybrali faktor formuláře, který vyhovuje vašim konkrétním potřebám, protože některé platformy a prohlížeče ještě nejsou kompatibilní s FIDO2.

Doporučujeme také, aby každá organizace vytvořila protokol pro uživatele a správce, aby se mohlo postupovat při ztrátě nebo odcizení bezpečnostního klíče. Uživatelé by měli hlásit ztracený nebo odcizený klíč, aby správci nebo uživatelé mohli odstranit klíče zabezpečení z profilu uživatele a zřídit nový.

### <a name="how-fido2-security-keys-works"></a>Jak fungují klíče zabezpečení FIDO2

#### <a name="user-sets-up-fido2-security-key"></a>Uživatel nastaví klíč zabezpečení FIDO2.

Správci sice můžou [ručně zřizovat klíče](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) a distribuovat je koncovým uživatelům, takže zřizování a povolování poskytovatele přihlašovacích údajů FIDO2 na zamykací obrazovce Windows 10 budou podporované prostřednictvím [Intune](https://docs.microsoft.com/intune/windows-enrollment-methods). Správci budou taky muset použít [Azure Portal](https://portal.azure.com/) k povolení zařízení hardwarových tokenů jako metody ověřování pomocí hesla.

Nasazování klíčů zabezpečení FIDO2 také vyžaduje, aby uživatelé zaregistrovali své klíče pomocí [kombinované registrace](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md). Při kombinované registraci se uživatelé registrují jednou a získají výhody pro Azure Multi-Factor Authentication i resetování hesla jednotného přihlašování (SSPR).

Kromě výběru hardwarového tokenu jako výchozí metody Multi-Factor Authentication doporučujeme také vybrat další možnost ověření.

* Microsoft Authenticator – oznámení

* Ověřovací aplikace nebo hardwarový token – kód

* Telefonní hovor

* Zpráva SMS

#### <a name="user-using-fido2-security-key-for-sign-in"></a>Uživatel používající FIDO2 bezpečnostní klíč pro přihlášení

FIDO2 poskytuje abstrakci mezi systémem, který se používá jako ověřovatel a kryptografie veřejného a privátního klíče k tomu, aby byly integrované ověřovatele platforem, například Windows Hello a klíče zabezpečení, předávány na privátní klíč a doručují veřejný klíč. který lze použít jako identifikátor pro přístup k externím prostředkům. Bezpečnostní klíče FIDO2 jsou vybavené vlastním integrovaným zabezpečeným enklávy, který ukládá soukromý klíč a vyžaduje, aby se biometrika nebo kód PIN odemkl. Přihlašovací údaje se nedají znovu použít, přehrát ani sdílet mezi službami a nevztahují se k útokům phishing a MiTM nebo k narušením serveru.

![Přihlášení k FIDO2](./media/ad-passwordless/azure-ad-pwdless-image6.png)

Klíče zabezpečení FIDO2 poskytují zabezpečené ověřování, nezávisle na tom, jaký je faktor formuláře. Bezpečnostní klíč držitele uchovává přihlašovací údaje a měl by být chráněn pomocí dalšího druhého faktoru, jako je otisk prstu (integrovaný do klíče zabezpečení), nebo PIN kódu, který se má zadat v přihlašování Windows. Partneři Microsoftu pracují na nejrůznějších faktorech formy bezpečnostního klíče. Mezi příklady patří klíče zabezpečení USB a čipové karty s povoleným NFC.

> [!NOTE]
> Bezpečnostní klíč musí implementovat určité funkce a rozšíření od protokolu FIDO2 CTAP, aby byl [kompatibilní](https://aka.ms/fido2securitykeys)s Microsoftem. Společnost Microsoft testovala Tato řešení kvůli kompatibilitě s Windows 10 a Azure Active Directory.

![FIDO2 proces přihlášení](./media/ad-passwordless/azure-ad-pwdless-image9.png)

1. Uživatel připojí zařízení FIDO2 k počítači.

2. Systém Windows detekuje bezpečnostní klíč FIDO2.

3. Systém Windows odešle požadavek na ověření.

4. Azure AD pošle zpátky hodnotu nonce.

5. Uživatel dokončí svůj gesto, aby odemkl privátní klíč uložený v zabezpečeném enklávy bezpečnostního klíče FIDO2.

6. Klíč zabezpečení FIDO2 podepíše hodnoty nonce pomocí privátního klíče.

7. Požadavek na token PRT s podepsanou hodnotu NONCE se pošle do Azure AD.

8. Azure AD ověří podepsaná hodnota nonce pomocí veřejného klíče FIDO2.

9. Azure AD vrátí PRT a povolí přístup k místním prostředkům.

#### <a name="user-manages-their-fido2-security-key-credentials"></a>Uživatel spravuje přihlašovací údaje klíče zabezpečení FIDO2.

Podobně jako u Microsoft Authenticator aplikace se Správa přihlašovacích údajů pro klíče zabezpečení FIDO2 spoléhá na kombinované prostředí registrace pro koncové uživatele.

## <a name="deciding-a-passwordless-method"></a>Rozhodnutí o metodě neplatných hesla

Výběr mezi těmito třemi možnostmi bez hesla závisí na požadavcích na zabezpečení, platformu a aplikace vaší společnosti.

Tady je několik faktorů, které byste měli vzít v úvahu při výběru technologie bez hesla Microsoftu:

||**Windows Hello pro firmy**|**Přihlášení bez hesla pomocí aplikace Microsoft Authenticator**|**FIDO2 klíče zabezpečení**|
|:-|:-|:-|:-|
|**Před požadavky**| Windows 10 verze 1809 nebo novější<br>Azure Active Directory| Aplikace Microsoft Authenticator<br>Telefon (zařízení s iOS a Androidem se systémem Android 6,0 nebo vyšším)|Windows 10 verze 1809 nebo novější<br>Azure Active Directory|
|**Mode**|Platforma|Software|Hardware|
|**Systémy a zařízení**|POČÍTAČ s integrovaným čipem TPM (Trusted Platform Module)<br>Připnutí a biometrika rozpoznávání |Připnutí a biometrika rozpoznávání na telefonu|FIDO2 zabezpečení zařízení, která jsou kompatibilní s Microsoftem|
|**Činnost koncového uživatele**|Přihlaste se pomocí PIN nebo biometrického rozpoznávání (obličeje, Iris nebo otisk prstu) pomocí zařízení s Windows.<br>Ověřování Windows Hello je vázané na zařízení; pro přístup k firemním prostředkům potřebuje uživatel zařízení i přihlašovací komponentu, jako je PIN nebo biometrické faktor.|Přihlaste se pomocí mobilního telefonu pomocí skenování otisků prstů, obličeje nebo Iris nebo PIN kódu.<br>Uživatelé se přihlásí k pracovnímu nebo osobnímu účtu na svém počítači nebo mobilním telefonu.|Přihlášení pomocí zařízení zabezpečení FIDO2 (biometrika, PIN a NFC)<br>Uživatel může získat přístup k zařízení na základě organizačních ovládacích prvků a ověřování na základě kódu PIN, biometrika pomocí zařízení, jako jsou klíče zabezpečení USB a čipové karty, klíče nebo wearables s podporou NFC.|
|**Povolené scénáře**| Heslo – bez možnosti použití zařízení s Windows<br>Platí pro vyhrazený pracovní počítač s možností jednotného přihlašování k zařízením a aplikacím.|Řešení bez hesla odkudkoli pomocí mobilního telefonu.<br>Platí pro přístup k pracovním nebo osobním aplikacím na webu z libovolného zařízení.|Prostředí bez hesla pro pracovní procesy s využitím biometrika, kódu PIN a NFC.<br>Platí pro sdílené počítače a v případě, že mobilní telefon není možnost životaschopnosti (například pro pracovníky helpdesku, veřejný terminál nebo tým v nemocnicích).|

Pomocí následující tabulky vyberte, kterou metodu budou podporovat vaše požadavky a uživatele.

|Nežádoucí|Scénář|Prostředí|Technologie nepracující s heslem|
|:-|:-|:-|:-|
|**Správ**|Zabezpečený přístup k zařízení pro úlohy správy|Přiřazené zařízení s Windows 10|Bezpečnostní klíč Windows Hello pro firmy a/nebo FIDO2|
|**Správ**|Úlohy správy na zařízeních s jiným systémem než Windows| Mobilní zařízení nebo zařízení s jiným systémem než Windows|Přihlášení bez hesla pomocí aplikace Microsoft Authenticator|
|**Informační pracovník**|Produktivita práce|Přiřazené zařízení s Windows 10|Bezpečnostní klíč Windows Hello pro firmy a/nebo FIDO2|
|**Informační pracovník**|Produktivita práce| Mobilní zařízení nebo zařízení s jiným systémem než Windows|Přihlášení bez hesla pomocí aplikace Microsoft Authenticator|
|**Pracovní proces prvotní**|Veřejné terminály v továrně, závodě, maloobchodním prodeji nebo zadávání dat|Sdílená zařízení s Windows 10|FIDO2 klíče zabezpečení|

## <a name="getting-started"></a>Začínáme

Ověřování bez hesla je vlnovkou budoucnosti a cesta k bezpečnějšímu prostředí. Doporučuje organizacím začít plánovat tuto změnu a snížit jejich závislosti na heslech. Začněte tím, že zaberete následující cíle:

* Povolte uživatele pro MFA + Microsoft Authenticator App + podmíněný přístup.

* Zavedení zásad služby Azure AD Password Protection + Update.

* Povolit uživatelům pro SSPR s kombinovanou registrací.

* Nasaďte aplikaci Microsoft Authenticator pro mobilitu.

* Nasaďte Windows Hello pro firmy (1903: Zůstaňte aktuální).

* Nasaďte zařízení FIDO2 pro uživatele, kteří nemůžou používat telefony.

* Pokud je to možné, zakažte ověřování založené na heslech.

Pro dosažení těchto cílů doporučujeme následující postup:

1. Umožněte Azure Active Directory plně využívat funkce, jako je Azure MFA a podmíněný přístup.

2. Povolte službu Multi-Factor Authentication pro zajištění další ochrany.

3. Povolit Samoobslužné resetování hesla v případě, že se uživatelé musí vrátit k používání hesla

4. Nasazení Microsoft Authenticatorho telefonu přihlaste se k přidané mobilitě.

5. Nasaďte Windows Hello pro firmy na všechna zařízení s Windows 10.

6. Příprava na FIDO2 klíče zabezpečení

> [!NOTE]
> Podrobnosti o požadavcích na licencování pro metody neumožňující přihlášení najdete na [stránce Azure Active Directory Licensing](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="conclusion"></a>Závěr

V posledních několika letech pokračovala společnost Microsoft na jejich závazek při povolování celého světa bez hesel. V systému Windows 10 Společnost Microsoft představila Windows Hello pro firmy, což je silné, chráněné hardwarové přihlašovací údaje, které umožňují jednotné přihlašování k Azure Active Directory a službě Active Directory. Podobně jako technologie Windows Hello pro firmy používá aplikace Microsoft Authenticator k povolení přihlašovacích údajů uživatele, které jsou svázané s mobilním zařízením, a používá biometriku nebo PIN kód. FIDO2 klíče zabezpečení teď umožňují přenášet vaše přihlašovací údaje s vámi a přihlásit se k Azure AD výběrem klíče zabezpečení jako poskytovatele přihlašovacích údajů na zamykací obrazovce Windows 10. Všechna tři z těchto řešení bez hesla omezují riziko útoků phishing, postřik hesla a opakovaného přehrávání a poskytují uživatelům vysoce zabezpečený a pohodlný způsob, jak se přihlašovat a přistupovat k datům odkudkoli.

Zavedení moderních technologií Multi-Factor Authentication, jako je biometrika a kryptografie veřejného klíče v široce přístupných zařízeních, je jedním z nejdůležitějších kroků, které mohou smysluplně snížit riziko totožnosti společnosti. Bez hesla je dlouhodobý přístup k zabezpečenému ověřování a stále se vyvíjí. S ohledem na nově vznikající požadavky si organizace můžou připravit plán na zahájení přechodu na technologie bez hesla.

## <a name="next-steps"></a>Další postup

* Přehled toho, [co je](../../active-directory/authentication/concept-authentication-passwordless.md) nejenom pro hesla?
* [Jak ve službě Azure AD povolit nepoužívatelné hesla](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless)

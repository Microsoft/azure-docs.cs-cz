---
title: Nejčastější dotazy k vícefaktorovému ověřování Azure – Azure Active Directory
description: Nejčastější dotazy a odpovědi související s azure multifaktorovým ověřováním.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d28f93f316ac2a63be6b3a8eb0b80678bd7607f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271399"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Nejčastější dotazy ohledně služby Azure Multi-Factor Authentication

Tyto nejčastější dotazy odpovídají na běžné otázky týkající se vícefaktorového ověřování Azure a používání služby vícefaktorového ověřování. Je rozdělena do otázek týkajících se služby obecně, fakturačních modelů, uživatelských prostředí a řešení potíží.

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.
>
> Informace sdílené níže týkající se serveru Azure Multi-Factor Authentication Server se vztahují pouze na uživatele, kteří už mají spuštěný server MFA.
>
> Září 2018 již nejsou pro nové zákazníky k dispozici licencování na základě spotřeby.
> září 2018 již nemusí být vytvářeny nové poskytovatele auth. Stávající zprostředkovatelé auth mohou být nadále používány a aktualizovány. Vícefaktorové ověřování bude i nadále dostupnou funkcí v licencích Azure AD Premium.

## <a name="general"></a>Obecné

* [Jak server Azure s vícefaktorovým ověřováním zpracovává uživatelská data?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Jaké SMS krátké kódy se používají pro odesílání SMS zpráv mým uživatelům?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Jak server Azure s vícefaktorovým ověřováním zpracovává uživatelská data?

U serveru s vícefaktorovým ověřováním jsou uživatelská data uložena pouze na místních serverech. V cloudu se neukládají žádná trvalá data. Když uživatel provede dvoustupňové ověření, vícefaktorový ověřovací server odešle data do cloudové služby Azure Multi-Factor Authentication pro ověřování. Komunikace mezi vícefaktorovým ověřovacím serverem a cloudovou službou vícefaktorového ověřování používá zabezpečenou vrstvu soketů (SSL) nebo zabezpečení transportní vrstvy (TLS) přes odchozí port 443.

Při odeslání požadavků na ověření do cloudové služby jsou shromažďována data pro zprávy o ověřování a využití. Následující datová pole jsou zahrnuta do dvoustupňových ověřovacích protokolů:

* **Jedinečné ID** (uživatelské jméno nebo místní ID vícefaktorového ověřovacího serveru)
* **Jméno a příjmení** (volitelné)
* **E-mailová adresa** (nepovinné)
* **Telefonní číslo** (při použití hlasového hovoru nebo ověřování POMOCÍ SMS)
* **Token zařízení** (při použití ověřování v mobilní aplikaci)
* **Režim ověřování**
* **Výsledek ověřování**
* **Název vícefaktorového ověřovacího serveru**
* **Vícefaktorový ověřovací server IP**
* **IP klienta** (je-li k dispozici)

Volitelná pole lze konfigurovat na vícefaktorovém ověřovacím serveru.

Výsledek ověření (úspěch nebo odmítnutí) a důvod, pokud byl odepřen, je uložen spolu s ověřovacími daty. Tato data jsou k dispozici v sestavách ověřování a používání.

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Jaké SMS krátké kódy se používají pro odesílání SMS zpráv mým uživatelům?

Ve Spojených státech používáme následující SMS krátké kódy:

* *97671*
* *69829*
* *51789*
* *99399*

V Kanadě používáme následující SMS krátké kódy:

* *759731*
* *673801*

Neexistuje žádná záruka konzistentní SMS nebo hlasové multi-factor authentication rychlé doručení stejným číslem. V zájmu našich uživatelů můžeme kdykoli přidat nebo odebrat krátké kódy, protože provedeme úpravy trasy, abychom zlepšili dodávku SMS. Nepodporujeme krátké kódy pro země nebo oblasti kromě Spojených států a Kanady.

## <a name="billing"></a>Fakturace

Na většinu otázek s fakturací lze odpovědět odkazem na [stránku S vícefaktorovým ověřováním](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) nebo na dokumentaci k [verzím a plánům spotřeby azure multifaktorového ověřování](concept-mfa-licensing.md).

* [Je mé organizaci účtováno odesílání telefonních hovorů a textových zpráv, které se používají k ověřování?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Účtuje mi model fakturace pro jednotlivé uživatele za všechny povolené uživatele nebo jen za ty, kteří provedli dvoustupňové ověření?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Jak funguje fakturace s vícefaktorovým ověřováním?](#how-does-multi-factor-authentication-billing-work)
* [Existuje bezplatná verze Azure Multi-Factor Authentication?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [Může moje organizace kdykoli přepínat mezi modely fakturace spotřeby pro jednotlivé uživatele a ověřování?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Může moje organizace kdykoli přepínat mezi fakturací založenou na spotřebě a předplatnými (model založený na licencích)?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Musí moje organizace používat a synchronizovat identity, aby mohla používat azure multifaktorové ověřování?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Je mé organizaci účtováno odesílání telefonních hovorů a textových zpráv, které se používají k ověřování?

Ne, neúčtují se vám poplatky za jednotlivé uskutečněné telefonní hovory nebo textové zprávy odeslané uživatelům prostřednictvím azure multifaktorového ověřování. Pokud používáte zprostředkovatele ověřování Vícefaktorové ověřování, bude se vám účtovat za každé ověřování, ale ne za použitou metodu.

Vašim uživatelům mohou být účtovány telefonní hovory nebo textové zprávy, které obdrží, podle jejich osobní telefonní služby.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Účtuje mi model fakturace pro jednotlivé uživatele za všechny povolené uživatele nebo jen za ty, kteří provedli dvoustupňové ověření?

Fakturace je založena na počtu uživatelů nakonfigurovaných pro použití vícefaktorového ověřování bez ohledu na to, zda provedli dvoustupňové ověření v tomto měsíci.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Jak funguje fakturace s vícefaktorovým ověřováním?

Když vytvoříte poskytovatele vícefaktorové mfa pro jednotlivé uživatele nebo ověřování, bude se předplatné Azure vaší organizace účtovat měsíčně na základě využití. Tento model fakturace je podobný tomu, jak Azure účtuje za využití virtuálních počítačů a webových aplikací.

Když si zakoupíte předplatné azure multi-factor authentication, vaše organizace platí pouze roční licenční poplatek pro každého uživatele. Tímto způsobem se účtují licence MFA a balíčky Office 365, Azure AD Premium nebo Enterprise Mobility + Security.

Další informace najdete v tématu [Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Existuje bezplatná verze Azure Multi-Factor Authentication?

Výchozí nastavení zabezpečení můžete povolit na úrovni Azure AD Free. S výchozími hodnotami zabezpečení jsou všichni uživatelé povoleni pro vícefaktorové ověřování pomocí aplikace Microsoft Authenticator. Neexistuje možnost používat textové zprávy nebo ověření telefonu s výchozími nastaveními zabezpečení, pouze aplikace Microsoft Authenticator.

Další informace naleznete v tématu [Co jsou výchozí nastavení zabezpečení?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Může moje organizace kdykoli přepínat mezi modely fakturace spotřeby pro jednotlivé uživatele a ověřování?

Pokud vaše organizace nakupuje vícefaktorové informace jako samostatnou službu s fakturací založenou na spotřebě, zvolíte při vytváření zprostředkovatele mfa model fakturace. Po vytvoření zprostředkovatele mfa nelze změnit fakturační model. 

Pokud váš poskytovatel MFA *není* propojen s tenantem Azure AD nebo propojíte nového zprostředkovatele MFA s jiným klientem Azure AD, nepřenesou se uživatelská nastavení a možnosti konfigurace. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA Serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv na ověřování pomocí telefonního hovoru a textové zprávy, ale do opětovné aktivace mobilní aplikace přestanou pro všechny uživatele fungovat oznámení mobilní aplikace.

Další informace o zprostředkovatelích MFA najdete v [aplikaci Začínáme s poskytovatelem Azure Multi-Factor Auth .](concept-mfa-authprovider.md)

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Může moje organizace kdykoli přepínat mezi fakturací založenou na spotřebě a předplatnými (model založený na licencích)?

V některých případech ano.

Pokud má váš adresář poskytovatele azure multi-factor authentication *pro jednotlivé uživatele,* můžete přidat licence MFA. Uživatelé s licencemi se do fakturace založené na spotřebě na jednotlivé uživatele nezapočítávají. Uživatelé bez licencí lze stále povolit pro vícefaktorové povolení prostřednictvím zprostředkovatele mfa. Pokud zakoupíte a přiřadíte licence všem uživatelům nakonfigurovaným pro použití vícefaktorového ověřování, můžete odstranit zprostředkovatele azure multi-factor authentication. Pokud máte v budoucnu více uživatelů než licencí, můžete vždy vytvořit jiného poskytovatele vícefaktorové ověřování pro jednotlivé uživatele.

Pokud má váš adresář poskytovatele azure multi-factor authentication pro *ověřování podle ověřování,* bude se vám vždy účtovat za každé ověřování, pokud je poskytovatel MFA propojený s vaším předplatným. Licence MFA můžete přiřadit uživatelům, ale bude se vám stále účtovat každý dvoufázový požadavek na ověření, ať už pochází od někoho, kdo má přiřazenou licenci MFA.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>Musí moje organizace používat a synchronizovat identity, aby mohla používat azure multifaktorové ověřování?

Pokud vaše organizace používá fakturační model založený na spotřebě, služba Azure Active Directory je volitelná, ale není povinná. Pokud váš poskytovatel MFA není propojený s klientem Azure AD, můžete nasadit pouze server Azure s vícefaktorovým ověřováním místně.

Azure Active Directory je vyžadována pro licenční model, protože licence se přidávají do klienta Azure AD při nákupu a přiřadíje je uživatelům v adresáři.

## <a name="manage-and-support-user-accounts"></a>Správa a podpora uživatelských účtů

* [Co mám uživatelům říct, aby udělali, když v telefonu neobdrží odpověď?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Co mám dělat, když se jeden z mých uživatelů nemůže dostat ke svému účtu?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Co mám dělat, když jeden z mých uživatelů ztratí telefon, který používá hesla aplikací?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Co když se uživatel nemůže přihlásit k aplikacím, které nejsou v prohlížeči?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Moji uživatelé říkají, že někdy neobdrží textovou zprávu nebo časový čas ověření.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Mohu změnit dobu, po kterou mají uživatelé zadat ověřovací kód z textové zprávy před časovým výpadkem systému?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Můžu použít hardwarové tokeny s Vícefaktorovým ověřovacím serverem Azure?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Můžu k zabezpečení Terminálové služby použít server Azure S vícefaktorovým ověřováním?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Na serveru MFA byla nakonfigurována ID volajícího, ale uživatelé stále přijímají vícefaktorová volání ověřování od anonymního volajícího.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Proč jsou moji uživatelé vyzváni k registraci svých bezpečnostních údajů?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Co mám uživatelům říct, aby udělali, když v telefonu neobdrží odpověď?

Nechte své uživatele, aby se pokusili až pětkrát za 5 minut získat telefonní hovor nebo SMS pro ověření. Společnost Microsoft používá pro doručování volání a ZPRÁV SMS více poskytovatelů. Pokud tento přístup nefunguje, otevřete případ podpory k dalšímu řešení potíží.

Aplikace zabezpečení třetích stran mohou také blokovat textovou zprávu ověřovacího kódu nebo telefonní hovor. Pokud používáte aplikaci zabezpečení jiného výrobce, zkuste zakázat ochranu a potom požádejte o odeslání jiného ověřovacího kódu vícefaktorové ověřování.

Pokud výše uvedený postup nefunguje, zkontrolujte, zda jsou uživatelé nakonfigurováni pro více než jednu metodu ověření. Zkuste se přihlásit znovu, ale na přihlašovací stránce vyberte jinou metodu ověření.

Další informace naleznete v [příručce pro řešení potíží s koncovým uživatelem](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Co mám dělat, když se jeden z mých uživatelů nemůže dostat ke svému účtu?

Účet uživatele můžete obnovit tak, že jej znovu provedete procesem registrace. Přečtěte si další informace o [správě nastavení uživatelů a zařízení pomocí Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Co mám dělat, když jeden z mých uživatelů ztratí telefon, který používá hesla aplikací?

Chcete-li zabránit neoprávněnému přístupu, odstraňte všechna hesla aplikací uživatele. Poté, co má uživatel náhradní zařízení, může znovu vytvořit hesla. Přečtěte si další informace o [správě nastavení uživatelů a zařízení pomocí Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Co když se uživatel nemůže přihlásit k aplikacím, které nejsou v prohlížeči?

Pokud vaše organizace stále používá starší klienty a povolili jste [používání hesel aplikací](howto-mfa-mfasettings.md#app-passwords), uživatelé se k těmto starším klientům nemohou přihlásit pomocí jejich uživatelského jména a hesla. Místo toho je třeba [nastavit hesla aplikací](../user-help/multi-factor-authentication-end-user-app-passwords.md). Uživatelé musí vymazat (odstranit) přihlašovací údaje, restartovat aplikaci a pak se přihlásit pomocí svého uživatelského jména a *hesla aplikace* namísto svého běžného hesla.

Pokud vaše organizace nemá starší klienty, neměli byste uživatelům povolit vytváření hesel aplikací.

> [!NOTE]
> **Moderní ověřování pro klienty Office 2013**
>
> Hesla aplikací jsou nezbytná jenom pro aplikace, které nepodporují moderní ověřování. Klienti Office 2013 podporují moderní ověřovací protokoly, ale je potřeba je nakonfigurovat. Moderní ověřování je k dispozici všem zákazníkům, kteří scházejí aktualizaci pro Office 2013 z března 2015 nebo novější. Další informace najdete v příspěvku blogu [Aktualizované moderní ověřování Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Moji uživatelé říkají, že někdy neobdrží textovou zprávu nebo časový čas ověření.

Doručování SMS zpráv nejsou zaručeny, protože existují nekontrolovatelné faktory, které mohou ovlivnit spolehlivost služby. Mezi tyto faktory patří cílová země nebo oblast, operátor mobilního telefonu a síla signálu.

Aplikace zabezpečení třetích stran mohou také blokovat textovou zprávu ověřovacího kódu nebo telefonní hovor. Pokud používáte aplikaci zabezpečení jiného výrobce, zkuste zakázat ochranu a potom požádejte o odeslání jiného ověřovacího kódu vícefaktorové ověřování.

Pokud mají uživatelé často problémy se spolehlivým přijímáním textových zpráv, řekněte jim, aby místo toho použili aplikaci Microsoft Authenticator nebo metodu telefonního hovoru. Microsoft Authenticator může přijímat oznámení přes mobilní připojení i Wi-Fi připojení. Kromě toho může mobilní aplikace generovat ověřovací kódy, i když zařízení nemá vůbec žádný signál. Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) a [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Mohu změnit dobu, po kterou mají uživatelé zadat ověřovací kód z textové zprávy před časovým výpadkem systému?

V některých případech ano.

Pro jednosměrnou SMS s Azure MFA Server v7.0 nebo vyšší, můžete nakonfigurovat nastavení časového limitu nastavením klíče registru. Po mrakové službě MFA odešle textovou zprávu, ověřovací kód (nebo jednorázový přístupový kód) je vrácena na server MFA. Server MFA ukládá kód do paměti po dobu 300 sekund ve výchozím nastavení. Pokud uživatel nezadá kód před uplynutím 300 sekund, je jeho ověřování odmítnuto. Pomocí těchto kroků můžete změnit výchozí nastavení časového limitu:

1. Přejděte do části `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor` (Soubor > Nový > Jiné).
2. Vytvořte klíč registru **DWORD** s názvem *pfsvc_pendingSmsTimeoutSeconds* a nastavte čas v sekundách, který chcete, aby server Azure MFA ukládal jednorázové přístupové kódy.

>[!TIP]
>
> Pokud máte více serverů MFA, pouze ten, který zpracoval původní požadavek na ověření, zná ověřovací kód, který byl odeslán uživateli. Když uživatel zadá kód, požadavek na ověření ověření musí být odeslán na stejný server. Pokud je ověření kódu odesláno na jiný server, je ověření odmítnuto.

Pokud uživatelé nereagují na SMS v rámci definovaného časového času, jejich ověřování je odepřeno.

U jednosměrné SMS s Azure MFA v cloudu (včetně adaptéru služby AD FS nebo rozšíření serveru síťových zásad) nelze nakonfigurovat nastavení časového času. Azure AD ukládá ověřovací kód po dobu 180 sekund.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Můžu použít hardwarové tokeny s Vícefaktorovým ověřovacím serverem Azure?

Pokud používáte Azure Multi-Factor Authentication Server, můžete importovat tokeny open authentication (OATH) třetích stran založené na čase, jednorázové heslo (TOTP) a pak je použít pro dvoustupňové ověření.

Tokeny ActiveIdentity, které jsou tokeny OATH TOTP, můžete použít, pokud vložíte tajný klíč do souboru CSV a importujete na server Azure Multi-Factor Authentication Server. Tokeny OATH můžete používat s ověřováním pomocí formulářů ADFS (ADFS) služby ADFS, internetového informačního serveru (IIS) a služby RADIUS (Remote Authentication Dial-In User Service), pokud klientský systém může přijmout vstup uživatele.

Tokeny OATH TOTP třetích stran můžete importovat v následujících formátech:  

- Přenosný kontejner symetrického klíče (PSKC)
- CSV, pokud soubor obsahuje sériové číslo, tajný klíč ve formátu Base 32 a časový interval

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Můžu k zabezpečení Terminálové služby použít server Azure S vícefaktorovým ověřováním?

Ano, ale pokud používáte Windows Server 2012 R2 nebo novější, můžete terminálovou službu zabezpečit jenom pomocí služby Brána vzdálené plochy (Brána VP).

Změny zabezpečení v systému Windows Server 2012 R2 změnily způsob připojení serveru Azure Multi-Factor Authentication Server k balíčku zabezpečení místního úřadu zabezpečení (LSA) v systému Windows Server 2012 a starších verzích. U verzí Terminálové služby v systému Windows Server 2012 nebo starších můžete [zabezpečit aplikaci pomocí ověřování systému Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Pokud používáte Windows Server 2012 R2, potřebujete bránu VP.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Na serveru MFA byla nakonfigurována ID volajícího, ale uživatelé stále přijímají vícefaktorová volání ověřování od anonymního volajícího.

Když jsou vícefaktorová volání ověřování umístěna prostřednictvím veřejné telefonní sítě, někdy jsou směrována prostřednictvím operátora, který nepodporuje ID volajícího. Z důvodu tohoto chování operátora není id volajícího zaručeno, i když jej systém vícefaktorového ověřování vždy odesílá.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Proč jsou moji uživatelé vyzváni k registraci svých bezpečnostních údajů?

Existuje několik důvodů, proč by uživatelé mohli být vyzváni k registraci svých bezpečnostních údajů:

- Uživatel byl povolen pro Vícefaktorové ověřování jejich správce ve službě Azure AD, ale ještě nemá informace o zabezpečení registrované pro svůj účet.
- Uživatel byl povolen pro samoobslužné resetování hesla ve službě Azure AD. Bezpečnostní informace jim pomohou obnovit heslo v budoucnu, pokud na něj někdy zapomenou.
- Uživatel přistupoval k aplikaci, která má zásady podmíněného přístupu vyžadovat vícefaktorové ověřování a nebyl a dosud registrovánpro vícefaktorové ověřování.
- Uživatel registruje zařízení pomocí Azure AD (včetně připojení Azure AD) a vaše organizace vyžaduje vícefaktorové ověřování pro registraci zařízení, ale uživatel se dříve nezaregistroval pro vícefaktorové ověřování.
- Uživatel generuje Windows Hello pro firmy v systému Windows 10 (který vyžaduje vícefaktorové ověřování) a ještě nebyl registrován pro vícefaktorové ověřování.
- Organizace vytvořila a povolila zásadu registrace vícefaktorové ověřování, která byla použita pro uživatele.
- Uživatel dříve zaregistroval vícefaktorové ověřování, ale zvolil metodu ověření, kterou od té doby zakázal správce. Uživatel proto musí znovu projít registraci vícefaktorové ověřování a vybrat novou výchozí metodu ověření.

## <a name="errors"></a>chyby

* [Co by měli uživatelé dělat, když se jim při používání oznámení v mobilní aplikaci zobrazí chybová zpráva Žádost o ověření není pro aktivovaný účet?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Co by měli uživatelé dělat, když se jim při přihlašování k aplikaci, která není v prohlížeči, zobrazí chybová zpráva 0x800434D4L?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Co by měli uživatelé dělat, když se jim při používání oznámení v mobilní aplikaci zobrazí chybová zpráva Žádost o ověření není pro aktivovaný účet?

Požádejte uživatele, aby dokončil následující postup odebrání účtu z programu Microsoft Authenticator a znovu jej přidal:

1. Přejděte na [jejich profil na webu Azure a](https://account.activedirectory.windowsazure.com/profile/) přihlaste se pomocí účtu organizace.
2. Vyberte **další ověření zabezpečení**.
3. Odeberte existující účet z aplikace Microsoft Authenticator.
4. Klepněte na tlačítko **Konfigurovat**a podle pokynů překonfigurujte microsoft authenticator.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Co by měli uživatelé dělat, když se jim při přihlašování k aplikaci, která není v prohlížeči, zobrazí chybová zpráva 0x800434D4L?

Chyba *0x800434D4L* nastane při pokusu o přihlášení k aplikaci, která není v prohlížeči, nainstalované v místním počítači, která nefunguje s účty, které vyžadují dvoustupňové ověření.

Řešení této chyby je mít samostatné uživatelské účty pro operace související s správcem a bez správce. Později můžete propojit poštovní schránky mezi účtem správce a účtem, který není správcem, abyste se mohli přihlásit k Outlooku pomocí účtu, který není správcem. Další podrobnosti o tomto řešení naleznete v příručce správce [k otevření a zobrazení obsahu poštovní schránky uživatele](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Další kroky

Pokud zde vaše otázka není zodpovězena, jsou k dispozici následující možnosti podpory:

* Vyhledejte řešení běžných technických problémů ve [znalostní bázi Microsoft Support Knowledge Base.](https://support.microsoft.com)
* Vyhledejte a procházejte technické otázky a odpovědi z komunity nebo si v [Azure Active Directory Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html)položte vlastní otázku.
* Obraťte se na Microsoft Professional prostřednictvím [podpory azure multi-factor authentication server](https://support.microsoft.com/oas/default.aspx?prid=14947). Když nás kontaktujete, je užitečné, pokud můžete uvést co nejvíce informací o vašem problému. Informace, které můžete zadat, zahrnují stránku, na které jste viděli chybu, konkrétní kód chyby, konkrétní ID relace a ID uživatele, který chybu viděl.
* Pokud jste starší zákazník PhoneFactor a máte dotazy nebo potřebujete pomoc s [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) resetováním hesla, otevřete případ podpory pomocí e-mailové adresy.

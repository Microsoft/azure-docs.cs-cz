---
title: Nejčastější dotazy k Azure Multi-Factor Authentication – Azure Active Directory
description: Nejčastější dotazy a odpovědi související s Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: de86bc7d083fbf1890ed90c588b5bdb2e8479dfb
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963971"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Nejčastější dotazy ohledně služby Azure Multi-Factor Authentication

Tato Nejčastější dotazy vám poodpoví na běžné otázky týkající se Azure Multi-Factor Authentication a používání služby Multi-Factor Authentication. Je rozdělené na dotazy týkající se služby obecně, fakturačních modelů, uživatelských zkušeností a řešení potíží.

> [!IMPORTANT]
> Od 1. července 2019 už Microsoft nenabídne MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat službu Multi-Factor Authentication od uživatelů, by měli používat cloudové Multi-Factor Authentication Azure. Stávající zákazníci, kteří mají aktivovaný MFA Server před 1. července, budou moci stáhnout nejnovější verzi, budoucí aktualizace a generovat přihlašovací údaje pro aktivaci obvyklým způsobem.
>
> Níže uvedené informace týkající se služby Azure Multi-Factor Authentication Server se vztahují jenom na uživatele, kteří už mají Server MFA spuštěný.
>
> Licencování na základě spotřeby už od 1. září 2018 není k dispozici pro nové zákazníky.
> Od 1. září 2018 již možná nebudou vytvořeny Noví zprostředkovatelé ověřování. Stávající zprostředkovatelé ověřování se můžou dál používat a aktualizovat. Multi-Factor Authentication bude nadále dostupná funkce v Azure AD Premium licencích.

## <a name="general"></a>Obecné

* [Jak Azure Multi-Factor Authentication Server zpracovává data uživatelů?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Které krátké kódy SMS slouží k posílání zpráv SMS uživatelům?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Jak Azure Multi-Factor Authentication Server zpracovává data uživatelů?

V Multi-Factor Authentication Server jsou uživatelská data uložená jenom na místních serverech. V cloudu se neukládají žádná trvalá data. Když uživatel provede dvoustupňové ověřování, Multi-Factor Authentication Server odesílá data do cloudové služby Azure Multi-Factor Authentication pro ověřování. Komunikace mezi Multi-Factor Authentication Server a cloudovou službou Multi-Factor Authentication používá SSL (Secure Sockets Layer) (SSL) nebo TLS (Transport Layer Security) prostřednictvím odchozího portu 443.

Když se do cloudové služby odesílají žádosti o ověření, shromažďují se data pro ověřování a sestavy o využití. Následující datová pole jsou součástí protokolů dvou kroků ověřování:

* **Jedinečné ID** (uživatelské jméno nebo místní Multi-Factor Authentication Server ID)
* **Křestní jméno a příjmení** (volitelné)
* **E-mailová adresa** (nepovinné)
* **Telefonní číslo** (při použití hlasového hovoru nebo ověřování SMS)
* **Token zařízení** (při použití ověřování mobilní aplikace)
* **Režim ověřování**
* **Výsledek ověřování**
* **Název Multi-Factor Authentication Server**
* **Multi-Factor Authentication Server IP**
* **IP adresa klienta** (je-li k dispozici)

Volitelná pole lze nakonfigurovat v Multi-Factor Authentication Server.

Výsledek ověření (úspěch nebo odmítnutí) a důvod zamítnutí je uložen s ověřovacími daty. Tato data jsou k dispozici v sestavách ověřování a využití.

Další informace najdete v tématu data [o sídle a zákaznických datech pro Azure Multi-Factor Authentication](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Které krátké kódy SMS slouží k posílání zpráv SMS uživatelům?

V USA používáme následující krátké kódy SMS:

* *97671*
* *69829*
* *51789*
* *99399*

V Kanadě používáme následující krátké kódy SMS:

* *759731*
* *673801*

Neexistuje žádná záruka na konzistentní doručování zpráv SMS nebo hlasové Multi-Factor Authentication s využitím stejného čísla. V zájmu našich uživatelů můžeme kdykoli přidat nebo odebrat krátké kódy, protože provádíme úpravy postupů pro zlepšení poskytování služby SMS.

Pro země nebo oblasti kromě USA a Kanady nepodporujeme krátké kódy.

## <a name="billing"></a>Fakturace

Na většinu otázek fakturace se dá odpovědět tak, že odkazujete na [stránku s cenami Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) nebo na dokumentaci pro [Azure Multi-Factor Authentication verze a plány spotřeby](concept-mfa-licensing.md).

* [Účtuje se mi moje organizace za odesílání telefonních hovorů a textových zpráv používaných k ověřování?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Účtuje se model fakturace podle uživatele pro všechny povolené uživatele nebo jenom ty, které provedly dvoustupňové ověřování?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Jak funguje Multi-Factor Authentication fakturace?](#how-does-multi-factor-authentication-billing-work)
* [Je k dispozici bezplatná verze služby Azure Multi-Factor Authentication?](#is-there-a-free-version-of-azure-multi-factor-authentication)
* [Může moje organizace přepínat mezi jednotlivými modely fakturace podle počtu uživatelů a ověřování?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Může mi kdykoli přepínat mezi fakturací a předplatnými na základě spotřeby (modelem založeným na licencích)?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Musí moje organizace používat a synchronizovat identity pro použití Azure Multi-Factor Authentication?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Účtuje se mi moje organizace za odesílání telefonních hovorů a textových zpráv používaných k ověřování?

Ne, neúčtují se za jednotlivé telefonní hovory nebo textové zprávy odesílané uživatelům prostřednictvím Azure Multi-Factor Authentication. Pokud použijete poskytovatele MFA pro ověřování, bude se vám účtovat každé ověřování, ale ne pro použitou metodu.

Vašim uživatelům se můžou účtovat telefonní hovory nebo textové zprávy, které obdrží, podle svých osobních telefonních služeb.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Účtuje se model fakturace podle uživatele pro všechny povolené uživatele nebo jenom ty, které provedly dvoustupňové ověřování?

Vyúčtování vychází z počtu uživatelů nakonfigurovaných pro použití Multi-Factor Authentication, bez ohledu na to, jestli provedli dvoustupňové ověřování v měsíci.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Jak funguje Multi-Factor Authentication fakturace?

Když vytvoříte poskytovatele MFA podle uživatele nebo ověřování, předplatné Azure vaší organizace se fakturuje měsíčně na základě využití. Tento model fakturace se podobá tomu, jak Azure účtuje využití virtuálních počítačů a Web Apps.

Když si koupíte předplatné pro Azure Multi-Factor Authentication, vaše organizace platí jenom pro každého uživatele roční licenční poplatek. Licence MFA a sady Microsoft 365, Azure AD Premium nebo Enterprise Mobility + Security se účtují tímto způsobem.

Další informace najdete v tématu [Jak získat Azure Multi-Factor Authentication](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-multi-factor-authentication"></a>Je k dispozici bezplatná verze služby Azure Multi-Factor Authentication?

Výchozí nastavení zabezpečení je možné povolit na úrovni Azure AD Free. V případě výchozích hodnot zabezpečení je pro všechny uživatele povolená aplikace Multi-Factor Authentication pomocí aplikace Microsoft Authenticator. Nemůžete použít textovou zprávu nebo ověřování pomocí telefonu s výchozími nastaveními zabezpečení, jenom aplikace Microsoft Authenticator.

Další informace najdete v tématu [co jsou výchozí nastavení zabezpečení?](../fundamentals/concept-fundamentals-security-defaults.md)

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Může moje organizace přepínat mezi jednotlivými modely fakturace podle počtu uživatelů a ověřování?

Pokud vaše organizace koupí MFA jako samostatnou službu s fakturací na základě spotřeby, zvolíte model fakturace při vytváření poskytovatele MFA. Po vytvoření poskytovatele MFA nelze model fakturace změnit. 

Pokud váš poskytovatel MFA není *propojený s* klientem služby Azure AD nebo když propojíte nového poskytovatele MFA s jiným klientem služby Azure AD, uživatelská nastavení a možnosti konfigurace se nepřenášejí. Je také potřeba znovu aktivovat stávající Azure MFA Servery pomocí přihlašovacích údajů pro aktivaci vygenerovaných prostřednictvím nového poskytovatele MFA. Opětovná aktivace MFA Serverů za účelem jejich propojení s novým poskytovatelem MFA nemá vliv na ověřování pomocí telefonního hovoru a textové zprávy, ale do opětovné aktivace mobilní aplikace přestanou pro všechny uživatele fungovat oznámení mobilní aplikace.

Další informace o poskytovatelích VÍCEFAKTOROVÉHO ověřování najdete v článku [Začínáme s poskytovatelem ověřování Azure Multi-Factor auth](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Může mi kdykoli přepínat mezi fakturací a předplatnými na základě spotřeby (modelem založeným na licencích)?

V některých případech Ano.

Pokud má váš adresář poskytovatele Azure Multi-Factor Authentication *pro každého uživatele* , můžete přidat licence MFA. Uživatelům s licencemi se nepočítají při fakturaci na základě spotřeby podle uživatele. Uživatelům bez licencí je stále možné povolit vícefaktorové ověřování prostřednictvím poskytovatele MFA. Pokud si koupíte a přiřadíte licence pro všechny uživatele, kteří jsou nakonfigurováni pro použití Multi-Factor Authentication, můžete poskytovatele služby Azure Multi-Factor Authentication odstranit. Pokud v budoucnu máte více uživatelů, než kolik jich máte, můžete vždy vytvořit dalšího zprostředkovatele vícefaktorového ověřování pro každého uživatele.

Pokud má váš adresář poskytovatele služby Azure Multi-Factor Authentication *pro ověřování* , bude se vám vždycky účtovat každé ověřování, pokud je poskytovatel MFA propojený s vaším předplatným. Uživatelům můžete přiřadit licence MFA, ale pořád se vám bude účtovat každá žádost o dvoustupňové ověření, ať už pochází od někoho, kdo má přiřazenou licenci MFA.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-multi-factor-authentication"></a>Musí moje organizace používat a synchronizovat identity pro použití Azure Multi-Factor Authentication?

Pokud vaše organizace používá model fakturace založený na spotřebě, Azure Active Directory je volitelné, ale nevyžaduje se. Pokud není zprostředkovatel MFA propojený s klientem služby Azure AD, můžete nasadit Azure Multi-Factor Authentication Server jenom místně.

Pro licenční model se vyžaduje Azure Active Directory, protože licence se přidávají do tenanta služby Azure AD, když je koupíte a přiřadíte uživatelům v adresáři.

## <a name="manage-and-support-user-accounts"></a>Správa a podpora uživatelských účtů

* [Co mám sdělit svým uživatelům, pokud neobdrží na svém telefonu odpověď?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Co mám dělat, když některý z mých uživatelů nemůže získat přístup ke svému účtu?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Co mám dělat, když některý z mých uživatelů ztratí telefon, který používá hesla aplikací?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Co když se uživatel nemůže přihlásit k neprohlížečovým aplikacím?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Moji uživatelé říkají, že někdy neobdrží textovou zprávu, nebo vyprší časový limit ověření.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Můžu změnit dobu, po kterou musí uživatelé zadat ověřovací kód z textové zprávy, než vyprší časový limit pro systém?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Můžu používat hardwarové tokeny s Azure Multi-Factor Authentication Server?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Můžu použít Azure Multi-Factor Authentication Server k zabezpečení Terminálové služby?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Nakonfiguroval (a) jsem ID volajícího na serveru MFA, ale moji uživatelé pořád obdrží Multi-Factor Authentication volání od anonymního volajícího.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Proč jsou moji uživatelé vyzváni k registraci svých informací o zabezpečení?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Co mám sdělit svým uživatelům, pokud neobdrží na svém telefonu odpověď?

Požádejte uživatele, aby během 5 minut pomohly získat telefonní hovor nebo SMS pro ověřování. Microsoft používá více poskytovatelů pro doručování hovorů a SMS zpráv. Pokud tento přístup nefunguje, otevřete případ podpory pro další řešení.

Aplikace zabezpečení třetích stran můžou také blokovat textovou zprávu s ověřovacím kódem nebo telefonní hovor. Pokud používáte aplikaci zabezpečení třetí strany, zkuste zakázat ochranu a pak požádejte o odeslání dalšího ověřovacího kódu MFA.

Pokud výše uvedené kroky nefungují, ověřte, jestli jsou uživatelé nakonfigurované pro více než jednu metodu ověřování. Zkuste se znovu přihlásit, ale na přihlašovací stránce vyberte jinou metodu ověřování.

Další informace najdete v [Průvodci odstraňováním potíží koncových uživatelů](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Co mám dělat, když některý z mých uživatelů nemůže získat přístup ke svému účtu?

Můžete resetovat uživatelský účet tím, že ho provedete znovu pomocí procesu registrace. Přečtěte si další informace o [správě nastavení zařízení a uživatelů pomocí Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Co mám dělat, když některý z mých uživatelů ztratí telefon, který používá hesla aplikací?

Pokud chcete zabránit neoprávněnému přístupu, odstraňte všechna hesla aplikací uživatele. Jakmile má uživatel náhradní zařízení, může hesla znovu vytvořit. Přečtěte si další informace o [správě nastavení zařízení a uživatelů pomocí Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Co když se uživatel nemůže přihlásit k neprohlížečovým aplikacím?

Pokud vaše organizace stále používá starší klienty a Vy jste [povolili používání hesel aplikací](howto-mfa-app-passwords.md), nebudou se uživatelé moct k těmto starším klientům přihlašovat pomocí svého uživatelského jména a hesla. Místo toho musí [nastavovat hesla aplikací](../user-help/multi-factor-authentication-end-user-app-passwords.md). Uživatelé musí zrušit (odstranit) jejich přihlašovací údaje, restartovat aplikaci a pak se přihlašovat pomocí uživatelského jména a *hesla aplikace* místo jejich běžného hesla.

Pokud vaše organizace nemá starší verze klientů, neměli byste uživatelům dovolit vytvářet hesla aplikací.

> [!NOTE]
> **Moderní ověřování pro klienty Office 2013**
>
> Hesla aplikací jsou nutná jenom pro aplikace, které nepodporují moderní ověřování. Klienti Office 2013 podporují moderní ověřovací protokoly, ale je potřeba je nakonfigurovat. Moderní ověřování je dostupné pro všechny zákazníky, kteří používají aktualizaci pro Office 2013 z března 2015 nebo novější. Další informace najdete v blogovém příspěvku [aktualizovaného moderního ověřování Office 365](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Moji uživatelé říkají, že někdy neobdrží textovou zprávu, nebo vyprší časový limit ověření.

Doručování zpráv SMS není zaručeno, protože existují neovladatelné faktory, které by mohly ovlivnit spolehlivost služby. Mezi tyto faktory patří cílová země nebo oblast, mobilní telefonní hovor a síla signálu.

Aplikace zabezpečení třetích stran můžou také blokovat textovou zprávu s ověřovacím kódem nebo telefonní hovor. Pokud používáte aplikaci zabezpečení třetí strany, zkuste zakázat ochranu a pak požádejte o odeslání dalšího ověřovacího kódu MFA.

Pokud mají vaši uživatelé často problémy s spolehlivým příjmem textových zpráv, řekněte jim, aby místo toho používali Microsoft Authenticator aplikace nebo telefonní hovor. Microsoft Authenticator může dostávat oznámení přes mobilní připojení i Wi-Fi. Kromě toho může mobilní aplikace generovat ověřovací kódy i v případě, že zařízení nemá žádný signál. Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) a [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Můžu změnit dobu, po kterou musí uživatelé zadat ověřovací kód z textové zprávy, než vyprší časový limit pro systém?

V některých případech Ano.

Pro jednosměrné služby SMS s Azure MFA serverem v 7.0 nebo vyšší můžete nakonfigurovat nastavení časového limitu nastavením klíče registru. Jakmile cloudová služba MFA pošle textovou zprávu, ověřovací kód (nebo jednorázové heslo) se vrátí na server MFA. Server MFA ukládá kód do paměti po dobu 300 sekund ve výchozím nastavení. Pokud uživatel nezadá kód před uplynutím 300 sekund, je jeho ověřování zamítnuto. Pomocí těchto kroků můžete změnit výchozí nastavení časového limitu:

1. Přejděte na `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Vytvořte klíč registru **typu DWORD** s názvem *pfsvc_pendingSmsTimeoutSeconds* a nastavte dobu v sekundách, po kterou má Azure MFA Server ukládat jednorázová hesla.

>[!TIP]
>
> Pokud máte více serverů MFA, pouze ta, která zpracovala původní požadavek na ověření, zná ověřovací kód, který byl odeslán uživateli. Když uživatel zadá kód, žádost o ověření, která se má ověřit, musí být odeslána na stejný server. Pokud je ověřování kódu odesláno na jiný server, ověřování je odepřeno.

Pokud uživatelé nereagují na zprávu SMS v rámci definovaného časového limitu, je jejich ověření zamítnuto.

V případě jednosměrného SMS s Azure MFA v cloudu (včetně adaptéru AD FS nebo rozšíření serveru síťových zásad) nemůžete konfigurovat nastavení časového limitu. Azure AD ukládá ověřovací kód po dobu 180 sekund.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Můžu používat hardwarové tokeny s Azure Multi-Factor Authentication Server?

Pokud používáte Azure Multi-Factor Authentication Server, můžete importovat tokeny Open Authentication (TOTP) založené na době od třetích stran a pak je použít pro dvoustupňové ověřování.

Tokeny ActiveIdentity, které jsou tokeny OATH TOTP, můžete použít, pokud vložíte tajný klíč do souboru CSV a naimportujete do Azure Multi-Factor Authentication Server. Tokeny OATH lze použít s Active Directory Federation Services (AD FS) (ADFS), ověřováním pomocí formulářů služby Internet Information Server (IIS) a protokol RADIUS (Remote Authentication Dial-In User Service) (RADIUS), pokud klientský systém může přijmout vstup uživatele.

Tokeny OATH TOTP třetích stran můžete importovat v následujících formátech:  

- Přenosný kontejner symetrického klíče (PSKC)
- Sdílený svazek clusteru (CSV), pokud soubor obsahuje sériové číslo, tajný klíč ve formátu Base 32 a časový interval

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Můžu použít Azure Multi-Factor Authentication Server k zabezpečení Terminálové služby?

Ano, ale pokud používáte Windows Server 2012 R2 nebo novější, můžete zabezpečit Terminálové služby jenom pomocí Brána vzdálené plochy (Brána VP).

Změny zabezpečení ve Windows Serveru 2012 R2 mění způsob, jakým se Azure Multi-Factor Authentication Server připojuje k balíčku zabezpečení místního úřadu zabezpečení (LSA) v systému Windows Server 2012 a starších verzích. Pro verze Terminálové služby ve Windows Serveru 2012 nebo starší můžete [zabezpečit aplikaci pomocí ověřování systému Windows](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Pokud používáte Windows Server 2012 R2, potřebujete Brána VP.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Nakonfiguroval (a) jsem ID volajícího na serveru MFA, ale moji uživatelé pořád obdrží Multi-Factor Authentication volání od anonymního volajícího.

Když Multi-Factor Authentication volání jsou umístěna prostřednictvím veřejné telefonní sítě, někdy jsou směrována prostřednictvím dopravce, který nepodporuje ID volajícího. Vzhledem k tomuto chování nosné frekvence není ID volajícího zaručeno, i když je systém Multi-Factor Authentication vždy odesílá.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Proč jsou moji uživatelé vyzváni k registraci svých informací o zabezpečení?

Existuje několik důvodů, proč se uživatelům zobrazí výzva k registraci informací o zabezpečení:

- Uživatel povolil ověřování MFA správcem ve službě Azure AD, ale ještě nemá pro svůj účet zaregistrované žádné informace o zabezpečení.
- Uživatel povolil samoobslužné resetování hesla ve službě Azure AD. Informace o zabezpečení jim pomůžou resetovat heslo v budoucnu, pokud ho někdy zapomenou.
- Uživatel, který získal přístup k aplikaci, která má zásady podmíněného přístupu, aby vyžadoval MFA a nebyl dřív zaregistrován pro MFA.
- Uživatel registruje zařízení ve službě Azure AD (včetně připojení k Azure AD) a vaše organizace vyžaduje MFA pro registraci zařízení, ale uživatel už není zaregistrovaný pro MFA.
- Uživatel generuje Windows Hello pro firmy ve Windows 10 (který vyžaduje MFA) a ještě není zaregistrovaný pro MFA.
- Organizace vytvořila a povolila zásady registrace MFA, které se pro uživatele nastavily.
- Uživatel, který byl dříve zaregistrován pro MFA, ale zvolil metodu ověřování, kterou správce od zakázání zakázal. Uživatel musí proto znovu projít registrací MFA a vybrat novou výchozí metodu ověřování.

## <a name="errors"></a>chyby

* [Co by uživatelé měli dělat, pokud se při použití oznámení mobilní aplikace zobrazí chybová zpráva "žádost o ověření není pro aktivovaný účet"?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Co by uživatelé měli dělat, pokud se při přihlašování k aplikaci bez prohlížeče uvidí chybová zpráva 0x800434D4L?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Co by uživatelé měli dělat, pokud se při použití oznámení mobilní aplikace zobrazí chybová zpráva "žádost o ověření není pro aktivovaný účet"?

Požádejte uživatele, aby dokončil následující postup pro odebrání svého účtu z Microsoft Authenticator, a pak ho znovu přidejte:

1. Přejít na [svůj profil Azure Portal](https://account.activedirectory.windowsazure.com/profile/) a přihlaste se pomocí účtu organizace.
2. Vyberte **Další ověření zabezpečení**.
3. Odeberte existující účet z aplikace Microsoft Authenticator.
4. Klikněte na **Konfigurovat**a pak postupujte podle pokynů a překonfigurujte Microsoft Authenticator.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Co by uživatelé měli dělat, pokud se při přihlašování k aplikaci bez prohlížeče uvidí chybová zpráva 0x800434D4L?

K chybě *0x800434D4L* dojde při pokusu o přihlášení do neprohlížečové aplikace nainstalované v místním počítači, která nefunguje s účty, které vyžadují dvoustupňové ověřování.

Alternativním řešením této chyby je, aby byly samostatné uživatelské účty pro operace týkající se správců a jiných správců. Později můžete propojit poštovní schránky mezi účtem správce a účtem bez správce, abyste se mohli přihlásit k Outlooku pomocí účtu bez správce. Další informace o tomto řešení najdete v tématu Jak [dát správci možnost otevřít a zobrazit obsah poštovní schránky uživatele](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Další kroky

Pokud tady na vaši otázku neodpovíte, jsou k dispozici následující možnosti podpory:

* Vyhledejte v [Podpora Microsoftu znalostní bázi](https://support.microsoft.com) řešení běžných technických problémů.
* Vyhledejte a procházejte technické dotazy a odpovědi z komunity nebo si položte svoji vlastní otázku do [Azure Active Directory Q&A](/answers/topics/azure-active-directory.html).
* Kontaktujte Microsoft Professional prostřednictvím [podpory Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Při kontaktování nás je užitečné, pokud můžete zahrnout co nejvíce informací o vašem problému. Informace, které můžete dodat, zahrnují stránku, kde jste viděli chybu, konkrétní kód chyby, konkrétní ID relace a ID uživatele, který chybu viděl.
* Pokud jste starší verze PhoneFactor zákazníka a máte nějaké dotazy nebo potřebujete pomoc s resetem hesla, použijte [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) e-mailovou adresu a otevřete případ podpory.
---
title: Řešení potíží s kódy chyb pro rozšíření Azure MFA serveru NPS | Microsoft Docs
description: Získat pomoc při řešení problémů s příponou NPS pro Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 07/14/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: aa140bceb5f7ad5e638f747fa8d88803c27f02a3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33867661"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Vyřešte chybové zprávy z NPS rozšíření pro Azure Multi-Factor Authentication

Pokud narazíte na chyby s příponou NPS pro Azure Multi-Factor Authentication, použijte tento článek k dosažení řešení rychlejší. 

## <a name="troubleshooting-steps-for-common-errors"></a>Řešení potíží pro běžné chyby

| Kód chyby | Postup při řešení potíží |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Obraťte se na podporu](#contact-microsoft-support)a zmínili seznam kroků pro shromažďování protokolů. Zadejte co nejvíce informací můžete o co se stalo před chybou, včetně id klienta a hlavní název uživatele (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Pravděpodobně problém s jak byl nainstalován klientský certifikát nebo ve spojení s vašeho klienta. Postupujte podle pokynů v [řešení potíží s příponou MFA NPS](howto-mfa-nps-extension.md#troubleshooting) k prozkoumání problémů certifikátu klienta. |
| **ESTS_TOKEN_ERROR** | Postupujte podle pokynů v [řešení potíží s příponou MFA NPS](howto-mfa-nps-extension.md#troubleshooting) prozkoumat klientského certifikátu a ADAL token problémy. |
| **HTTPS_COMMUNICATION_ERROR** | NPS server nemůže přijímat odpovědi z Azure MFA. Ověřte, zda jsou vaší brány firewall otevřít obousměrně pro provoz do a z https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serveru, který spouští rozšíření serveru NPS, ověřte, že může kontaktovat https://adnotifications.windowsazure.com a https://login.microsoftonline.com/. Pokud nemáte zatížení u těchto lokalit, řešení potíží s připojením na tomto serveru. |
| **REGISTRY_CONFIG_ERROR** | Klíč nebyl nalezen v registru pro aplikaci, které může být, že [skript prostředí PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) nebyl po instalaci spustit. Chybová zpráva by měla obsahovat chybí klíč. Ujistěte se, že máte klíč v části HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Chybí povinný atribut protokolu Radius userName\Identifier požadavku protokolu RADIUS. Ověřte, zda je server NPS přijímají požadavky protokolu RADIUS | Tato chyba obvykle odráží potíže s instalací. Rozšíření serveru NPS musí být nainstalován na serverech NPS, které můžou přijímat požadavky protokolu RADIUS. Servery NPS, které se instalují jako závislosti pro služby, jako je RDG a RRAS nemáte přijímal požadavky protokolu radius. Rozšíření serveru NPS nefunguje, pokud je nainstalován v takové instalací a chyb se vzhledem k tomu, že nelze přečíst podrobnosti ze žádosti o ověření. |
| **REQUEST_MISSING_CODE** | Ujistěte se, že protokol šifrování hesla mezi servery NPS a NAS podporuje metodu sekundárního ověřování, který používáte. **PAP** podporuje všechny metody ověřování Azure mfa v cloudu: telefonní hovor, jednosměrné textová zpráva, oznámení mobilní aplikace a kód ověření mobilní aplikace. **CHAPV2** a **EAP** podporují telefonní hovory a oznámení mobilní aplikace. |
| **USERNAME_CANONICALIZATION_ERROR** | Ověřte, že uživatel je přítomen v instanci služby Active Directory v místě, a zda služba NPS má oprávnění pro přístup k adresáři. Pokud používáte vztahy důvěryhodnosti mezi doménovými strukturami, [obraťte se na podporu](#contact-microsoft-support) pro další pomoc. |


   

### <a name="alternate-login-id-errors"></a>Alternativního přihlašovacího ID chyby

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: userObjectSid vyhledávání se nezdařilo | Ověřte, zda uživatel existuje v místní instanci služby Active Directory. Pokud používáte vztahy důvěryhodnosti mezi doménovými strukturami, [obraťte se na podporu](#contact-microsoft-support) pro další pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: Alternativní LoginId vyhledávání se nezdařilo | Ověřte, zda LDAP_ALTERNATE_LOGINID_ATTRIBUTE nastaveno [atribut platný active directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Pokud LDAP_FORCE_GLOBAL_CATALOG nastaven na hodnotu True, nebo LDAP_LOOKUP_FORESTS je konfigurována s hodnotou není prázdný, ověřte, že jste nakonfigurovali na globální katalog a zda je atribut AlternateLoginId přidány k němu. <br><br> Pokud je nakonfigurované LDAP_LOOKUP_FORESTS neprázdnou hodnotu, ověřte, že hodnota správná. Pokud existuje více než jeden název doménové struktury, musí být odděleny názvy oddělte středníkem, ne mezery. <br><br> Pokud tyto kroky nejsou opravit problém, [obraťte se na podporu](#contact-microsoft-support) pro další pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: Alternativní LoginId hodnota je prázdná | Ověřte, že je atribut AlternateLoginId nakonfigurovaný pro tohoto uživatele. |


## <a name="errors-your-users-may-encounter"></a>Chyby vaši uživatelé setkat.

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Volající klient nemá oprávnění k přístupu k provést ověřování pro uživatele | Zkontrolujte, zda doména klienta a doménu hlavní název uživatele (UPN) jsou stejné. Například, ujistěte se, že user@contoso.com se pokusil o ověření klienta Contoso. Hlavní název uživatele představuje platného uživatele pro klienta v Azure. |
| **AuthenticationMethodNotConfigured** | Použití zadané metody ověřování nebyl nakonfigurovaný pro tohoto uživatele | Mít uživatele, přidání nebo ověřte své metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověření](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Zadané metody ověřování není podporována. | Shromažďovat všechny protokoly, které obsahují tato chyba, a [obraťte se na podporu](#contact-microsoft-support). Když se obrátíte na podporu, zadejte uživatelské jméno a metodu sekundární ověření, který aktivoval chyba. |
| **BecAccessDenied** | Volání MSODS Bec vrátil odepření přístupu, pravděpodobně není definováno uživatelské jméno v klientovi | Uživatel je součástí služby Active Directory v místě, ale není synchronizovat do Azure AD pomocí AD Connect. Nebo uživatel chybí u klienta. Přidejte uživatele do služby Azure AD a požádejte o přidání své metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověření](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** nebo **StrongAuthenticationServiceInvalidParameter** | Telefonní číslo je ve formátu nerozpoznatelný | Máte opravte jejich ověření telefonních čísel uživatele. |
| **InvalidSession** | Zadaná relace je neplatný nebo platnost vypršela | Relace trvá víc než tři minut. Ověřte, zda je uživatel zadáte ověřovací kód, nebo odpověď na oznámení aplikace, do tří minut po inicializaci žádosti o ověření. Jestliže problém problém, zkontrolujte, že neexistují žádné síťovou latenci mezi klientem, na Server, NPS Server a koncový bod Azure MFA.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Pro uživatele byla nakonfigurována žádná výchozí metoda ověření | Mít uživatele, přidání nebo ověřte své metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověření](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). Ověřte, zda má uživatel vybrali metodu ověřování výchozí a nakonfigurovat tuto metodu pro svůj účet. |
| **OathCodePinIncorrect** | Nesprávný kód a zadán kód pin. | Tato chyba není očekáván v rozšíření serveru NPS. Pokud uživatel zaznamená, [obraťte se na podporu](#contact-microsoft-support) Poradce při potížích. |
| **ProofDataNotFound** | Doklad data nebyla nakonfigurována pro použití zadané metody ověřování. | Uživatel, zkuste metodu různých ověřování, nebo přidejte nové metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověření](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-manage-settings.md). Pokud uživatel pokračuje se tato chyba po potvrzení správně, nastavit jejich metoda ověření [obraťte se na podporu](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Nesprávný kód a zadán kód pin. (OneWaySMS) | Tato chyba není očekáván v rozšíření serveru NPS. Pokud uživatel zaznamená, [obraťte se na podporu](#contact-microsoft-support) Poradce při potížích. |
| **TenantIsBlocked** | Klient je blokovaný. | [Obraťte se na podporu](#contact-microsoft-support) s ID adresáře na stránce Vlastnosti Azure AD na portálu Azure. |
| **UserNotFound** | Zadaný uživatel nebyl nalezen. | Klient není již zobrazen jako aktivní v Azure AD. Zkontrolujte, zda je vaše předplatné aktivní a zda máte požadovaná první strany aplikace. Také zkontrolujte, že klient v předmětu certifikátu podle očekávání a certifikát je stále platný a registrované v části objektu služby. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Zprávy, že uživatelé setkat, která nejsou chyby

Uživatelé mohou někdy zobrazit zprávy z aplikace Multi-Factor Authentication vzhledem k tomu, že jejich žádosti o ověření se nezdařilo. Tyto nejsou chyby v rámci produktu konfigurace, ale jsou úmyslně upozornění vysvětlením, proč byl odepřen požadavek ověřování.

| Kód chyby | Chybová zpráva | Doporučené kroky | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Nesprávný kód entered\OATH nesprávný kód | Není k chybě, uživatel zadal nesprávný kód. | Uživatel zadal nesprávný kód. Kliknul opakujte žádají o nový kód nebo znovu se přihlaste. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximální povolené kód opakování dosaženo | Uživatele se nezdařilo ověření výzvy příliš mnohokrát. V závislosti na nastavení, budou muset odblokováno správce nyní.  |
| **SMSAuthFailedWrongCodeEntered** | Pokazilo zadali nebo textu zprávy jednorázového HESLA nesprávný kód | Uživatel zadal nesprávný kód. Kliknul opakujte žádají o nový kód nebo znovu se přihlaste. |

## <a name="errors-that-require-support"></a>Chyby, které vyžadují podporu

Pokud některé z těchto chyb narazíte, doporučujeme vám [obraťte se na podporu](#contact-microsoft-support) diagnostiky nápovědu. Neexistuje žádné standardní sadu kroky, které můžete vyřešit tyto chyby. Při kontaktování podpory, nezapomeňte zahrnout jako velkého množství informací nejblíže o krocích, které vedly k chybě a informace o klientovi.

| Kód chyby | Chybová zpráva |
| ---------- | ------------- |
| **InvalidParameter** | Požadavek nesmí mít hodnotu null |
| **InvalidParameter** | ObjectId nesmí být null nebo prázdná pro ReplicationScope:{0} |
| **InvalidParameter** | Délka NázevSpolečnosti \{0} \ je delší než maximální povolená délka {1} |
| **InvalidParameter** | UserPrincipalName nesmí být null nebo prázdný |
| **InvalidParameter** | Zadané hodnoty TenantId není ve správném formátu |
| **InvalidParameter** | ID relace nesmí být null nebo prázdný |
| **InvalidParameter** | Nepodařilo se přeložit všechny ProofData z požadavku nebo Msods. ProofData neznámý. |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Další postup

### <a name="troubleshoot-user-accounts"></a>Řešení potíží s uživatelské účty

Pokud jsou vaši uživatelé [došlo k potížím s dvoustupnovym overovanim](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user-troubleshoot.md), pomoci jim samoobslužné diagnostikovat problémy. 

### <a name="contact-microsoft-support"></a>Obraťte se na podporu společnosti Microsoft

Pokud potřebujete další pomoc, obraťte se na pracovníky technické podpory prostřednictvím [podpory Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Při kontaktování nám, je užitečné, pokud zahrnete tolik informací o problému nejdříve. Obsahuje informace, které můžete zadat stránky, kde jste viděli v chybě, kód chyby ID konkrétní relace, ID uživatele, který viděli k chybě a protokoly pro ladění.

Pokud chcete shromažďovat protokoly ladění pro podporu diagnostiky, použijte následující kroky na serveru NPS:

1. Otevřete Editor registru a přejděte do sady HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** k **TRUE**
2. Otevřete příkazový řádek správce a spusťte tyto příkazy:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reprodukujte problém

4. Zastavte trasování s těmito příkazy:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Otevřete Editor registru a přejděte do sady HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** k **FALSE**
6. ZIP obsah složky C:\NPS a připojte soubor s příponou ZIP k případu podpory.



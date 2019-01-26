---
title: Poradce při potížích s kódy chyb pro rozšíření Azure MFA NPS | Dokumentace Microsoftu
description: Získejte pomoc při řešení problémů pomocí rozšíření NPS pro Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: eb8bce76321bf596f7c51c3437dd2497c827e83f
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55076906"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication

Pokud narazíte na chyby pomocí rozšíření NPS pro Azure Multi-Factor Authentication, použijte tento článek k dosažení řešení rychleji. Protokoly rozšíření NPS se nacházejí v prohlížeči událostí pod **vlastní zobrazení** > **role serveru** > **síťové zásady a přístup ke službám** na instalaci rozšíření NPS server.

## <a name="troubleshooting-steps-for-common-errors"></a>Řešení potíží pro běžné chyby

| Kód chyby | Postup při řešení potíží |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Obraťte se na podporu](#contact-microsoft-support)a uvádí seznam kroků pro shromažďování protokolů. Poskytněte co nejvíce informací, jak jde o co se stalo před chybou, včetně id tenanta a hlavní název uživatele (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Možná existuje problém s jak byl klientský certifikát nainstalovat nebo přidružené k tenantovi. Postupujte podle pokynů v [řešení potíží s rozšíření MFA NPS](howto-mfa-nps-extension.md#troubleshooting) k prozkoumání problémů certifikát klienta. |
| **ESTS_TOKEN_ERROR** | Postupujte podle pokynů v [řešení potíží s rozšíření MFA NPS](howto-mfa-nps-extension.md#troubleshooting) k prozkoumání klientský certifikát a ADAL token problémy. |
| **HTTPS_COMMUNICATION_ERROR** | NPS server je schopna přijímat odpovědi z Azure MFA. Ověřte, zda jsou brány firewall otevřít obousměrně pro provoz do a z https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serveru, na kterém běží server NPS rozšíření, ověřte, že můžete oslovit https://adnotifications.windowsazure.com a https://login.microsoftonline.com/. Pokud se nenačtou těchto webů, řešení potíží s připojením na tomto serveru. |
| **Rozšíření NPS pro Azure MFA:** <br> Rozšíření NPS pro Azure MFA pro požadavky protokolu Radius ve stavu AccessAccept provádí pouze sekundární ověřování. Žádost o zadání uživatelského jména uživatele se stavem odpovědi AccessReject ignoruje požadavek. | Tato chyba obvykle odráží selhání ověřování ve službě AD nebo NPS server je schopna přijímat odpovědi ze služby Azure AD. Ověřte, zda jsou brány firewall otevřít obousměrně pro provoz do a z https://adnotifications.windowsazure.com a https://login.microsoftonline.com pomocí porty 80 a 443. Je také důležité zkontrolovat, že na kartě DIAL-IN oprávnění přístupu k síti je nastavená na "řízení přístupu pomocí serveru NPS síťové zásady". |
| **REGISTRY_CONFIG_ERROR** | Nenašel se klíč v registru pro aplikaci, která může být, že [skript prostředí PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) po instalaci nespustila. Chybová zpráva by měla obsahovat chybí klíč. Ujistěte se, že máte klíč HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Chybí povinný atribut protokolu Radius userName\Identifier požadavku protokolu RADIUS. Ověřte, že server NPS přijímá požadavky protokolu RADIUS | Tato chyba obvykle odráží potíže s instalací. Rozšíření NPS musí být nainstalován na serverech NPS, které můžou přijímat požadavky protokolu RADIUS. Servery NPS, které se instalují jako závislosti pro služby, jako je RDG a RRAS není přijímal požadavky protokolu radius. Rozšíření serveru NPS nefunguje při instalaci těchto zařízení a chyby, protože jej nelze přečíst podrobnosti ze žádosti o ověření. |
| **REQUEST_MISSING_CODE** | Ujistěte se, že protokol šifrování hesla mezi servery NPS a server NAS podporuje sekundární ověřování, který používáte. **PAP** podporuje všechny metody ověřování Azure mfa v cloudu: telefonní hovor, jednosměrná textová zpráva, oznámení mobilní aplikace a ověřovací kód z mobilní aplikace. **CHAPV2** a **EAP** podporují telefonních hovorů a oznámení mobilní aplikace. |
| **USERNAME_CANONICALIZATION_ERROR** | Ověřte, že uživatel je k dispozici v místní instanci Active Directory a že službu NPS má oprávnění pro přístup k adresáři. Pokud používáte vztah důvěryhodnosti mezi doménovými strukturami, [obraťte se na podporu](#contact-microsoft-support) o další pomoc. |

### <a name="alternate-login-id-errors"></a>Alternativní přihlašovací ID chyby

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: userObjectSid vyhledávání se nezdařilo | Ověřte, zda uživatel existuje v instanci aplikace místní služby Active Directory. Pokud používáte vztah důvěryhodnosti mezi doménovými strukturami, [obraťte se na podporu](#contact-microsoft-support) o další pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: Alternativní LoginId vyhledávání se nezdařilo | Ověřte, zda LDAP_ALTERNATE_LOGINID_ATTRIBUTE nastaveno [platné služby active directory atribut](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Pokud LDAP_FORCE_GLOBAL_CATALOG nastavena na hodnotu True nebo LDAP_LOOKUP_FORESTS je nakonfigurována s neprázdnou hodnotu, ověřte, že jste nakonfigurovali globálního katalogu a že je do ní přidat atribut AlternateLoginId. <br><br> Pokud je nakonfigurované LDAP_LOOKUP_FORESTS neprázdná hodnota, ověřte správnost hodnotu. Pokud existuje více než jeden název doménové struktury, musí být názvy oddělené středníkem, nikoli mezery. <br><br> Pokud tyto kroky není problém vyřešit, [obraťte se na podporu](#contact-microsoft-support) o další pomoc. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: Alternativní LoginId hodnota je prázdná | Ověřte, že atribut AlternateLoginId je nakonfigurovaný pro uživatele. |

## <a name="errors-your-users-may-encounter"></a>Chyby uživatele mohou nastat.

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Tenant volající nemá oprávnění k přístupu k provádět ověřování pro uživatele | Zkontrolujte, jestli doména tenanta a domény hlavního názvu uživatele (UPN) jsou stejné. Například, ujistěte se, že user@contoso.com se pokusil o ověření k tenantovi Contoso. Hlavní název uživatele představuje platného uživatele pro příslušného tenanta v Azure. |
| **AuthenticationMethodNotConfigured** | Metoda ověřování zadané nebylo nakonfigurováno pro uživatele | Požádejte uživatele, přidat nebo ověřit své metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Zadaný ověřovací metoda není podporována. | Shromažďovat všechny protokoly, které zahrnují tuto chybu a [obraťte se na podporu](#contact-microsoft-support). Až budete kontaktovat podporu, zadejte uživatelské jméno a sekundární ověřovací metoda, která aktivuje chyby. |
| **BecAccessDenied** | Volání MSODS Bec vrátil odepření přístupu, pravděpodobně username není definované v tenantovi | Uživatel je k dispozici v místní služby Active Directory, ale není synchronizovaný do služby Azure AD pomocí AD Connect. Nebo uživatel nebyl nalezen pro příslušného tenanta. Přidejte uživatele do služby Azure AD a požádejte o přidání své metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** nebo **StrongAuthenticationServiceInvalidParameter** | Telefonní číslo je má nerozpoznatelný formát | Požádejte uživatele, opravte jejich ověřování telefonních čísel. |
| **InvalidSession** | Zadaná relace je neplatný nebo jeho platnost vypršela | Relace obsazené více než tří minut. Ověřte, zda je uživatel zadáte ověřovací kód, nebo reakce na oznámení aplikace do tří minut od zahájení žádosti o ověření. Pokud není, který problém vyřešit, zkontrolujte, že neexistují žádné latenci sítě mezi klientem, NAS Server, NPS Server a koncový bod Azure MFA.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Byl nakonfigurován žádný výchozí metodu ověřování pro uživatele | Požádejte uživatele, přidat nebo ověřit své metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). Ověřte, zda má uživatel zvolit výchozí metodu ověřování a nakonfigurovanou tuto metodu ke svému účtu. |
| **OathCodePinIncorrect** | Chybný kód a zadán kód pin. | Tato chyba se neočekává v rozšíření serveru NPS. Pokud uživatel zaznamená, [obraťte se na podporu](#contact-microsoft-support) pomoc při řešení potíží. |
| **ProofDataNotFound** | Testování dat nebylo nakonfigurováno pro zadaný ověřovací metoda. | Požádejte uživatele, zkuste jinou metodu ověření, nebo přidejte nové metody ověřování podle pokynů v [spravovat nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). Pokud tato chyba zobrazí po potvrzení, že je správně, nastavit metodu ověření uživatele [obraťte se na podporu](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Chybný kód a zadán kód pin. (OneWaySMS) | Tato chyba se neočekává v rozšíření serveru NPS. Pokud uživatel zaznamená, [obraťte se na podporu](#contact-microsoft-support) pomoc při řešení potíží. |
| **TenantIsBlocked** | Tenant je blokovaný. | [Obraťte se na podporu](#contact-microsoft-support) s ID adresáře na stránce Vlastnosti služby Azure AD na webu Azure Portal. |
| **UserNotFound** | Zadaný uživatel se nenašel | Tenant už není viditelná jako aktivní ve službě Azure AD. Zkontrolujte, zda je vaše předplatné aktivní, a máte potřebná první strany aplikace. Také se ujistěte, že je tenant v předmětu certifikátu podle očekávání a certifikát je stále platné a registrované v části instanční objekt služby. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Zprávy, že uživatelé mohou nastat, která nejsou chyby

Uživatelé v některých případech může načíst zprávy z ověřování službou Multi-Factor Authentication vzhledem k tomu, že jejich požadavek na ověření se nezdařilo. Ty nejsou chyby v produktu configuration, ale jsou záměr upozornění s vysvětlením, proč ověření bylo odepřeno.

| Kód chyby | Chybová zpráva | Doporučené kroky | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Chybný kód entered\OATH nesprávný kód | Nejde o chybu, uživatel zadal nesprávný kód. | Uživatel zadal nesprávný kód. Požádejte zkuste znovu požádat o nový kód nebo znovu přihlásit. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximální povolené kód opakování dosažen | Uživatel se nezdařilo ověření před obrovskou výzvou příliš mnohokrát. V závislosti na nastavení, může musí být nyní odblokováno správcem.  |
| **SMSAuthFailedWrongCodeEntered** | Nesprávné kód zadali/Text zprávy nesprávné jednorázové heslo | Uživatel zadal nesprávný kód. Požádejte zkuste znovu požádat o nový kód nebo znovu přihlásit. |

## <a name="errors-that-require-support"></a>Chyby, které vyžadují podporu

Pokud dojde k některé z těchto chyb, doporučujeme vám [obraťte se na podporu](#contact-microsoft-support) diagnostických nápovědu. Neexistuje žádný standardní sadu kroků, které může vyřešit tyto chyby. Až budete kontaktovat podporu, nezapomeňte zahrnout mnoho informace o krocích, které vedly k chybě a informace o tenantovi.

| Kód chyby | Chybová zpráva |
| ---------- | ------------- |
| **InvalidParameter** | Požadavek nesmí mít hodnotu null |
| **InvalidParameter** | ID objektu nesmí být null ani prázdná pro ReplicationScope:{0} |
| **InvalidParameter** | Délka CompanyName \{0} \ je delší než maximální povolená délka {1} |
| **InvalidParameter** | UserPrincipalName nesmí být null ani prázdná |
| **InvalidParameter** | Zadané TenantId není ve správném formátu |
| **InvalidParameter** | ID relace nesmí být null ani prázdná |
| **InvalidParameter** | Nelze přeložit všechny ProofData z požadavku nebo Msods s. ProofData neznámý |
| **Vnitřní chyba** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Další postup

### <a name="troubleshoot-user-accounts"></a>Řešení potíží s uživatelské účty

Pokud jsou vaši uživatelé [potíže s dvoustupňovým ověřováním](../user-help/multi-factor-authentication-end-user-troubleshoot.md), pomoci jim svým diagnostikovat problémy.

### <a name="contact-microsoft-support"></a>Kontaktovat podporu Microsoftu

Pokud potřebujete další pomoc, obraťte se na pracovníka podpory prostřednictvím [podporu ověřování Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Pokud nás kontaktujete, je užitečné, pokud zahrnete co nejvíce informací o svém problému nejvíce. Můžete zadat informace obsahuje stránka, kde jste viděli chyby, kód chyby ID konkrétní relace, ID uživatele, který viděli chyby a ladit protokoly.

Shromažďování protokolů ladění pro podporu diagnostiky, použijte následující kroky na serveru NPS:

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

4. Zastavte trasování pomocí následujících příkazů:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Otevřete Editor registru a přejděte do sady HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** k **FALSE**
6. Obsah složky C:\NPS zkomprimovat a připojte soubor ZIP do případu podpory.

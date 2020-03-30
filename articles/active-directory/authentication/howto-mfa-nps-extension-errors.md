---
title: Poradce při potížích s rozšířením Azure MFA NPS – Azure Active Directory
description: Získání nápovědy k řešení problémů s rozšířením NPS pro azure multifaktorové ověřování
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3462ada0864d2d8321b1936e94f947c55c754879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294504"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication

Pokud narazíte na chyby s rozšířením NPS pro Azure Multi-Factor Authentication, použijte tento článek k dosažení řešení rychleji. Protokoly rozšíření serveru NPS se nacházejí v prohlížeči událostí v části **Vlastní zobrazení** > **zásad sítě serverových rolí** > **a služby Access na** serveru, na kterém je nainstalováno rozšíření NPS.

## <a name="troubleshooting-steps-for-common-errors"></a>Řešení potíží s běžnými chybami

| Kód chyby | Postup při řešení potíží |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Obraťte se na podporu](#contact-microsoft-support)a uveďte seznam kroků pro shromažďování protokolů. Zadejte co nejvíce informací o tom, co se stalo před chybou, včetně id klienta a hlavního jména uživatele (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Pravděpodobně došlo k problému s tím, jak byl klientský certifikát nainstalován nebo přidružen k vašemu tenantovi. Postupujte podle pokynů v [řešení potíží s rozšířením mfa nps](howto-mfa-nps-extension.md#troubleshooting) prozkoumat problémy s certifikátem klienta. |
| **ESTS_TOKEN_ERROR** | Postupujte podle pokynů v [řešení potíží s rozšířením MFA NPS](howto-mfa-nps-extension.md#troubleshooting) a prozkoumejte problémy s certifikátem klienta a tokenem ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | Server NPS nemůže přijímat odpovědi z Azure MFA. Ověřte, zda jsou brány firewall otevřeny obousměrně pro přenos do a zhttps://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serveru, na který je spuštěno rozšíření `https://adnotifications.windowsazure.com` `https://login.microsoftonline.com/`NPS, ověřte, zda lze dosáhnout a . Pokud se tyto weby nenačítají, odstraňte potíže s připojením na tomto serveru. |
| **Rozšíření serveru NPS pro Azure MFA:** <br> Rozšíření nps pro Azure MFA provádí pouze sekundární ověření pro požadavky radius ve stavu AccessAccept. Požadavek přijatý pro uživatelské jméno uživatele se stavem odpovědi AccessReject, ignorování požadavku. | Tato chyba obvykle odráží selhání ověřování ve službě AD nebo že server NPS nemůže přijímat odpovědi z Azure AD. Ověřte, zda jsou brány firewall otevřeny `https://adnotifications.windowsazure.com` `https://login.microsoftonline.com` obousměrně pro přenos y porty 80 a 443 a jejich používání. Je také důležité zkontrolovat, zda je na kartě DIAL-IN v programu Oprávnění k přístupu k síti nastaveno na "řídit přístup prostřednictvím zásad sítě NPS". Tato chyba může také aktivovat, pokud uživatel není přiřazena licence. |
| **REGISTRY_CONFIG_ERROR** | Klíč chybí v registru pro aplikaci, což může být proto, že [skript prostředí PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) nebyl spuštěn po instalaci. Chybová zpráva by měla obsahovat chybějící klíč. Ujistěte se, že máte klíč pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Radius Request chybí povinný atribut Radius userName\Identifier. Ověřte, zda server NPS přijímá požadavky RADIUS | Tato chyba obvykle odráží problém s instalací. Rozšíření serveru NPS musí být nainstalováno na serverech NPS, které mohou přijímat požadavky RADIUS. Servery NPS, které jsou nainstalovány jako závislosti pro služby, jako je RDG a RRAS, nepřijímají požadavky na poloměr. Rozšíření NPS nefunguje při instalaci přes tyto instalace a chyby, protože nemůže číst podrobnosti z požadavku na ověření. |
| **REQUEST_MISSING_CODE** | Ujistěte se, že protokol šifrování hesel mezi servery NPS a SERVERY NAS podporuje metodu sekundárního ověřování, kterou používáte. **Protokol PAP** podporuje všechny metody ověřování Azure MFA v cloudu: telefonní hovor, jednosměrné textové zprávy, oznámení o mobilní aplikaci a ověřovací kód mobilní aplikace. **ChapV2** a **EAP** podporují telefonní hovor a oznámení o mobilních aplikacích. |
| **USERNAME_CANONICALIZATION_ERROR** | Ověřte, zda je uživatel přítomen v místní instanci služby Active Directory a zda má služba NPS oprávnění k přístupu k adresáři. Pokud používáte vztahy důvěryhodnosti mezi doménami, [požádejte o](#contact-microsoft-support) další pomoc podporu. |

### <a name="alternate-login-id-errors"></a>Chyby alternativního přihlášení id

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: Vyhledávání userObjectSid se nezdařilo. | Ověřte, zda uživatel existuje v místní instanci služby Active Directory. Pokud používáte vztahy důvěryhodnosti mezi doménami, [požádejte o](#contact-microsoft-support) další pomoc podporu. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: Vyhledávání Alternativní ho LoginId se nezdařilo. | Ověřte, zda je LDAP_ALTERNATE_LOGINID_ATTRIBUTE nastaven na [platný atribut služby Active Directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Pokud je LDAP_FORCE_GLOBAL_CATALOG nastavena na hodnotu True nebo LDAP_LOOKUP_FORESTS je nakonfigurován s neprázdnou hodnotou, ověřte, zda jste nakonfigurovali globální katalog a zda je do něj přidán atribut AlternateLoginId. <br><br> Pokud je LDAP_LOOKUP_FORESTS nakonfigurován s neprázdnou hodnotou, ověřte, zda je hodnota správná. Pokud existuje více než jeden název doménové struktury, musí být názvy odděleny středníky, nikoli mezerami. <br><br> Pokud tyto kroky problém nevyřeší, [požádejte o další](#contact-microsoft-support) pomoc podporu. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: Alternativní hodnota LoginId je prázdná. | Ověřte, zda je pro uživatele nakonfigurován atribut AlternateLoginId. |

## <a name="errors-your-users-may-encounter"></a>Chyby, se kterými se uživatelé mohou setkat

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **Přístup byl odepřen.** | Klient volajícího nemá přístupová oprávnění k ověřování pro uživatele | Zkontrolujte, zda doména klienta a doména hlavního názvu uživatele (UPN) jsou stejné. Například ujistěte user@contoso.com se, že se pokouší ověřit klienta Contoso. Hlavní upn představuje platného uživatele pro klienta v Azure. |
| **Metodu authenticationNotnakované** | Zadaná metoda ověřování nebyla pro uživatele nakonfigurována. | Nech uživatele přidat nebo ověřit jejich metody ověření podle pokynů v části [Správa nastavení pro dvoustupňové ověření](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Zadaná metoda ověřování není podporována. | Shromažďovat všechny protokoly, které obsahují tuto chybu a [obraťte se na podporu](#contact-microsoft-support). Když kontaktujete podporu, zadejte uživatelské jméno a sekundární metodu ověření, která spustila chybu. |
| **BecAccessDenied** | MSODS Bec volání vrácený přístup odepřen, pravděpodobně uživatelské jméno není definovánv tenantovi | Uživatel je k dispozici ve službě Active Directory místně, ale není synchronizován do služby Azure AD pomocí služby AD Connect. Nebo uživatel chybí pro klienta. Přidejte uživatele do Azure AD a nechte ho přidat své metody ověření podle pokynů v části [Správa nastavení pro dvoustupňové ověření](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **Parametr InvalidFormat** nebo **StrongAuthenticationServiceInvalidParametr** | Telefonní číslo je v nerozpoznatelném formátu | Nech uživatele opravit jejich ověřovací telefonní čísla. |
| **Neplatná relace** | Zadaná relace je neplatná nebo její platnost vypršela. | Dokončení relace trvalo více než tři minuty. Ověřte, zda uživatel zadává ověřovací kód nebo reaguje na oznámení aplikace do tří minut od zahájení žádosti o ověření. Pokud se tím problém nevyřeší, zkontrolujte, zda mezi klientem, SERVEREM NAS, serverem NPS a koncovým bodem Azure MFA nejsou žádné latence sítě.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Pro uživatele nebyla nakonfigurována žádná výchozí metoda ověřování. | Nech uživatele přidat nebo ověřit jejich metody ověření podle pokynů v části [Správa nastavení pro dvoustupňové ověření](../user-help/multi-factor-authentication-end-user-manage-settings.md). Ověřte, zda uživatel zvolil výchozí metodu ověřování, a nakonfigurovali tuto metodu pro svůj účet. |
| **OathCodePinIncorrect** | Byl zadán chybný kód a pin. | Tato chyba není očekávána v rozšíření NPS. Pokud s tím uživatel narazí, [obraťte se](#contact-microsoft-support) na podporu pro řešení potíží s nápovědou. |
| **Nenalezeno u proofData** | Ověřovací údaje nebyly nakonfigurovány pro zadanou metodu ověřování. | Ať uživatel zkusí jinou metodu ověření nebo přidá nové metody ověření podle pokynů v části [Správa nastavení pro dvoustupňové ověření](../user-help/multi-factor-authentication-end-user-manage-settings.md). Pokud uživatel i nadále vidět tuto chybu poté, co jste potvrdili, že jeho metoda ověření je nastavena správně, [obraťte se na podporu](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Byl zadán chybný kód a pin. (OneWaySMS) | Tato chyba není očekávána v rozšíření NPS. Pokud s tím uživatel narazí, [obraťte se](#contact-microsoft-support) na podporu pro řešení potíží s nápovědou. |
| **TenantisBlocked** | Tenant je blokovaný. | [Obraťte se na podporu](#contact-microsoft-support) s ID adresáře ze stránky vlastností Azure AD na webu Azure Portal. |
| **UserNotFound** | Zadaný uživatel nebyl nalezen. | Klient už není viditelný jako aktivní ve službě Azure AD. Zkontrolujte, zda je vaše předplatné aktivní a zda máte požadované aplikace první strany. Také se ujistěte, že klient v předmětu certifikátu je podle očekávání a certifikát je stále platný a registrovaný pod instančníobjekt. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Zprávy, se kterými se mohou uživatelé setkat a které nejsou chyby

V některých případě mohou uživatelé získat zprávy z vícefaktorového ověřování, protože jejich požadavek na ověření se nezdařil. Nejedná se o chyby v produktu konfigurace, ale jsou úmyslné upozornění vysvětlující, proč byl odepřen požadavek na ověření.

| Kód chyby | Chybová zpráva | Doporučené kroky | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Byl zadán chybný kód\Nesprávný kód OATH | Uživatel zadal nesprávný kód. Požádejte je, aby to zkusili znovu vyžádáním nového kódu nebo znovu přihlášením. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Bylo dosaženo maximálnípovolené opakování kódu. | Uživatel nepodařilo ověření výzvu příliš mnohokrát. V závislosti na vašem nastavení může být nutné je nyní odblokovat správcem.  |
| **SMSAuthFailedWrongCodeEntered** | Nesprávný kód zadán/Text Message OTP Nesprávné | Uživatel zadal nesprávný kód. Požádejte je, aby to zkusili znovu vyžádáním nového kódu nebo znovu přihlášením. |

## <a name="errors-that-require-support"></a>Chyby, které vyžadují podporu

Pokud narazíte na jednu z těchto chyb, doporučujeme [kontaktovat podporu](#contact-microsoft-support) pro diagnostickou pomoc. Neexistuje žádná standardní sada kroků, které by mohly tyto chyby vyřešit. Když kontaktujete podporu, nezapomeňte uvést co nejvíce informací o krocích, které vedly k chybě, a informace o vašem klientovi.

| Kód chyby | Chybová zpráva |
| ---------- | ------------- |
| **Parametr InvalidParameter** | Požadavek nesmí být null. |
| **Parametr InvalidParameter** | ObjectId nesmí být null nebo prázdné pro ReplicationScope:{0} |
| **Parametr InvalidParameter** | Délka názvu_společnosti \{0}\ je delší než maximální povolená délka.{1} |
| **Parametr InvalidParameter** | UserPrincipalName nesmí být null nebo prázdný. |
| **Parametr InvalidParameter** | Zadaný TenantId není ve správném formátu |
| **Parametr InvalidParameter** | SessionId nesmí být null nebo prázdný |
| **Parametr InvalidParameter** | Nelze vyřešit žádné ProofData z požadavku nebo Msods. ProofData není znám |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **Verzenení podporována** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Další kroky

### <a name="troubleshoot-user-accounts"></a>Poradce při potížích s uživatelskými účty

Pokud mají uživatelé [potíže s dvoustupňovým ověřením](../user-help/multi-factor-authentication-end-user-troubleshoot.md), pomozte jim problémy s vlastní diagnostikou.

### <a name="health-check-script"></a>Skript kontroly stavu

[Skript kontroly stavu rozšíření Azure MFA NPS](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) provádí základní kontrolu stavu při řešení potíží s rozšířením NPS. Spusťte skript a zvolte možnost 3.

### <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Pokud potřebujete další pomoc, obraťte se na pracovníka podpory prostřednictvím [podpory serveru Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Když nás kontaktujete, je užitečné, pokud můžete uvést co nejvíce informací o vašem problému. Informace, které můžete zadat, zahrnují stránku, na které jste viděli chybu, konkrétní kód chyby, konkrétní ID relace, ID uživatele, který chybu viděl, a protokoly ladění.

Chcete-li shromažďovat protokoly ladění pro diagnostiku podpory, použijte následující kroky na serveru NPS:

1. Otevřete Editor registru a přejděte na HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **nastavenou VERBOSE_LOG** na **hodnotu TRUE**
2. Otevřete příkazový řádek Správce a spusťte tyto příkazy:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reprodukujte problém.

4. Zastavte trasování pomocí těchto příkazů:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Otevřete Editor registru a přejděte na HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **nastavenou VERBOSE_LOG** na **HODNOTU FALSE**
6. Zapněte obsah složky C:\NPS a připojte soubor zip k případu podpory.

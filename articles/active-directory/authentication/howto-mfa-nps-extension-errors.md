---
title: Řešení potíží s rozšířením NPS pro Azure AD MFA – Azure Active Directory
description: Získejte pomoc s řešením problémů s rozšířením NPS pro Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: fa3a2366f007ff1481e7c84f049e606586392037
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742948"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-ad-multi-factor-authentication"></a>Řešení chybových zpráv z rozšíření serveru NPS pro Azure AD Multi-Factor Authentication

Pokud dojde k chybám s rozšířením NPS pro Azure AD Multi-Factor Authentication, použijte tento článek k rychlejšímu dosažení řešení. Protokoly rozšíření NPS se nacházejí v Prohlížeč událostí v části **vlastní zobrazení**  >  **role serveru**  >  **zásady sítě a přístup** na serveru, na kterém je nainstalovaná přípona NPS.

## <a name="troubleshooting-steps-for-common-errors"></a>Postup řešení běžných chyb

| Kód chyby | Postup při řešení potíží |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Obraťte](#contact-microsoft-support)se na podporu a uveďte seznam kroků pro shromažďování protokolů. Poskytněte co nejvíce informací o tom, co se stalo před chybou, včetně ID tenanta a hlavního názvu uživatele (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Může se jednat o problém s tím, jak byl klientský certifikát nainstalován nebo přidružen k vašemu tenantovi. Postupujte podle pokynů v tématu [řešení potíží s rozšířením MFA NPS](howto-mfa-nps-extension.md#troubleshooting) a prozkoumejte problémy s certifikátem klienta. |
| **ESTS_TOKEN_ERROR** | Postupujte podle pokynů v tématu [řešení potíží s rozšířením MFA NPS](howto-mfa-nps-extension.md#troubleshooting) a prozkoumejte problémy klienta a tokenu ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | Server NPS nemůže přijmout odpovědi z Azure AD MFA. Ověřte, že brány firewall jsou otevřené obousměrně pro provoz do a z. https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | Na serveru, na kterém je spuštěno rozšíření serveru NPS, ověřte, že máte přístup k  `https://adnotifications.windowsazure.com` a `https://login.microsoftonline.com/` . Pokud se tyto lokality nenačte, vyřešte potíže s připojením na tomto serveru. |
| **Rozšíření serveru NPS pro Azure AD MFA:** <br> Rozšíření serveru NPS pro Azure AD MFA provádí sekundární ověřování pouze pro žádosti RADIUS ve stavu AccessAccept. Byl přijat požadavek na uživatelské jméno uživatele se stavem odpovědi AccessReject, ignorování požadavku. | Tato chyba obvykle odráží selhání ověřování ve službě AD nebo to, že server NPS nemůže přijímat odpovědi z Azure AD. Ověřte, že brány firewall jsou pro přenos do a z `https://adnotifications.windowsazure.com` a `https://login.microsoftonline.com` pomocí portů 80 a 443 obousměrné. Je také důležité ověřit, že na kartě Telefonické připojení v části oprávnění k přístupu k síti je nastavení nastaveno na možnost řídit přístup prostřednictvím zásad sítě NPS. Tato chyba se může také aktivovat, pokud uživatel nemá přiřazenou licenci. |
| **REGISTRY_CONFIG_ERROR** | V registru aplikace chybí klíč, což může být způsobeno tím, že se [powershellový skript](howto-mfa-nps-extension.md#install-the-nps-extension) po instalaci nespustil. Chybová zpráva by měla obsahovat chybějící klíč. Ujistěte se, že máte klíč pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Požadavek protokolu RADIUS neobsahuje povinný atribut userName\Identifier protokolu RADIUS. Ověření, že server NPS přijímá žádosti RADIUS | Tato chyba obvykle odráží problém instalace. Rozšíření serveru NPS musí být nainstalováno na servery NPS, které mohou přijímat žádosti RADIUS. Servery NPS, které se instalují jako závislosti pro služby, jako je RDG a RRAS, neobdrží žádosti RADIUS. Rozšíření serveru NPS nefunguje při instalaci těchto instalací a chybách, protože nemůže přečíst podrobnosti žádosti o ověření. |
| **REQUEST_MISSING_CODE** | Ujistěte se, že šifrovací protokol hesel mezi servery NPS a servery NAS podporuje sekundární metodu ověřování, kterou používáte. Protokol **PAP** podporuje všechny metody ověřování Azure AD MFA v cloudu: telefonní hovor, jednosměrná textová zpráva, oznámení mobilní aplikace a ověřovací kód mobilní aplikace. Protokol **CHAPv2** a **EAP** podporují telefonní hovor a oznámení mobilní aplikace. |
| **USERNAME_CANONICALIZATION_ERROR** | Ověřte, zda se uživatel nachází v místní instanci služby Active Directory a zda má služba NPS oprávnění pro přístup k tomuto adresáři. Pokud používáte vztahy důvěryhodnosti mezi doménovými strukturami, [požádejte](#contact-microsoft-support) o další pomoc podporu. |

### <a name="alternate-login-id-errors"></a>Chyby alternativního přihlašovacího ID

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: nepovedlo se userObjectSidovat vyhledávání | Ověřte, zda uživatel existuje v místní instanci služby Active Directory. Pokud používáte vztahy důvěryhodnosti mezi doménovými strukturami, [požádejte](#contact-microsoft-support) o další pomoc podporu. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: nepovedlo se provést alternativní LoginId vyhledávání | Ověřte, zda je LDAP_ALTERNATE_LOGINID_ATTRIBUTE nastaveno na [platný atribut služby Active Directory](/windows/win32/adschema/attributes-all). <br><br> Je-li LDAP_FORCE_GLOBAL_CATALOG nastavena na hodnotu true nebo je LDAP_LOOKUP_FORESTS nakonfigurována s neprázdnou hodnotou, ověřte, zda jste nakonfigurovali globální katalog a zda je do něj přidán atribut AlternateLoginId. <br><br> Pokud je LDAP_LOOKUP_FORESTS nakonfigurována s neprázdnou hodnotou, ověřte, zda je hodnota správná. Pokud existuje více než jeden název doménové struktury, názvy musí být odděleny středníkem, ne mezerami. <br><br> Pokud tyto kroky problém nevyřeší, [požádejte](#contact-microsoft-support) o další pomoc podporu. |
| **ALTERNATE_LOGIN_ID_ERROR** | Chyba: hodnota alternativní LoginId je prázdná. | Ověřte, jestli je pro uživatele nakonfigurovaný atribut AlternateLoginId. |

## <a name="errors-your-users-may-encounter"></a>Chyby, se kterými se uživatelé mohou setkat

| Kód chyby | Chybová zpráva | Postup při řešení potíží |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Tenant volající nemá oprávnění k ověření pro uživatele. | Ověřte, zda jsou doména tenanta a doména hlavního názvu uživatele (UPN) stejné. Ujistěte se například, že user@contoso.com se pokoušíte ověřit klienta společnosti Contoso. Hlavní název uživatele představuje platného uživatele pro tenanta v Azure. |
| **AuthenticationMethodNotConfigured** | Zadaná metoda ověřování není pro uživatele nakonfigurovaná. | Přidejte nebo ověřte své metody ověřování podle pokynů v tématu [Správa nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Zadaná metoda ověřování není podporována. | Shromážděte všechny protokoly, které obsahují tuto chybu, a [obraťte](#contact-microsoft-support)se na podporu. Když se obrátíte na podporu, zadejte uživatelské jméno a sekundární metodu ověřování, která aktivovala chybu. |
| **BecAccessDenied** | Volání MSODS BEC vrátilo přístup odepřeno, pravděpodobně uživatelské jméno není v tenantovi definované. | Uživatel se nachází ve službě Active Directory místně, ale není synchronizovaný do Azure AD pomocí služby AD Connect. Nebo, pro tenanta chybí uživatel. Přidejte uživatele do Azure AD a požádejte ho o přidání metod ověřování podle pokynů v tématu [Správa nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** nebo **StrongAuthenticationServiceInvalidParameter** | Telefonní číslo je v nerozpoznatelném formátu. | Uživatel musí opravit telefonní čísla pro ověření. |
| **InvalidSession** | Zadaná relace je neplatná nebo vypršela její platnost. | Dokončení relace trvalo déle než tři minuty. Ověřte, že uživatel zadává ověřovací kód, nebo odpoví na oznámení aplikace do tří minut od zahájení žádosti o ověření. Pokud se tím problém nevyřeší, ověřte, že mezi klientem, serverem NAS, serverem NPS a koncovým bodem Azure AD MFA nedochází k žádné latenci sítě.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Pro uživatele se nenakonfigurovala žádná výchozí metoda ověřování. | Přidejte nebo ověřte své metody ověřování podle pokynů v tématu [Správa nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). Ověřte, že uživatel zvolil výchozí metodu ověřování a pro svůj účet nakonfigurovali tuto metodu. |
| **OathCodePinIncorrect** | Byl zadán nesprávný kód a kód PIN. | Tato chyba se v rozšíření serveru NPS neočekává. Pokud k tomu dojde, obraťte se na [podporu](#contact-microsoft-support) pro pomoc s řešením potíží. |
| **ProofDataNotFound** | Pro zadanou metodu ověřování nebyla konfigurována data kontroly. | Požádejte uživatele, aby si vyzkoušeli jinou metodu ověřování, nebo přidejte nové metody ověřování podle pokynů v tématu [Správa nastavení pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user-manage-settings.md). Pokud uživatel tuto chybu stále uvidí po potvrzení, že je správně nastavená metoda ověřování, obraťte se na [podporu](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Byl zadán nesprávný kód a kód PIN. (OneWaySMS) | Tato chyba se v rozšíření serveru NPS neočekává. Pokud k tomu dojde, obraťte se na [podporu](#contact-microsoft-support) pro pomoc s řešením potíží. |
| **TenantIsBlocked** | Tenant je zablokovaný. | [Obraťte](#contact-microsoft-support) se na podporu s *ID tenanta* ze stránky vlastností Azure AD v Azure Portal. |
| **UserNotFound** | Zadaný uživatel nebyl nalezen. | Tenant už není ve službě Azure AD viditelný jako aktivní. Ověřte, že je vaše předplatné aktivní a že máte požadované aplikace první strany. Také se ujistěte, že je tenant v předmětu certifikátu podle očekávání a že certifikát je stále platný a registrovaný v instančním objektu. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Zprávy, se kterými se uživatelé můžou setkat, že nejsou chyby

V některých případech mohou uživatelé obdržet zprávu od Multi-Factor Authentication, protože žádost o ověření se nezdařila. Tyto chyby nejsou v produktu konfigurace, ale jedná se o úmyslné výstrahy s vysvětlením, proč byla žádost o ověření zamítnuta.

| Kód chyby | Chybová zpráva | Doporučené kroky |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Nesprávný kód entered\OATH kódu | Uživatel zadal nesprávný kód. Zkuste to znovu tak, že si vyžádáte nový kód nebo se znovu přihlásíte. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Bylo dosaženo maximálního povoleného opakování kódu. | Uživatel nedokázal ověřit výzvu příliš mnohokrát. V závislosti na nastavení může být potřeba odblokovat ho správce hned teď.  |
| **SMSAuthFailedWrongCodeEntered** | Chybné zadání kódu/textová zpráva hesla není správná. | Uživatel zadal nesprávný kód. Zkuste to znovu tak, že si vyžádáte nový kód nebo se znovu přihlásíte. |

## <a name="errors-that-require-support"></a>Chyby, které vyžadují podporu

Pokud narazíte na jednu z těchto chyb, doporučujeme, abyste [kontaktovali podporu](#contact-microsoft-support) pro diagnostickou nápovědu. Neexistuje žádná standardní sada kroků, které by mohly tyto chyby vyřešit. Když se obrátíte na podporu, nezapomeňte zahrnout co nejvíc informací o krocích, které vedly k chybě, a informace o vašem tenantovi.

| Kód chyby | Chybová zpráva |
| ---------- | ------------- |
| **InvalidParameter** | Požadavek nesmí mít hodnotu null. |
| **InvalidParameter** | ObjectId nesmí mít hodnotu null ani být prázdné pro ReplicationScope:{0} |
| **InvalidParameter** | Délka CompanyName \{ 0} je delší, než je maximální povolená délka. {1} |
| **InvalidParameter** | Atribut UserPrincipalName nesmí mít hodnotu null ani být prázdný. |
| **InvalidParameter** | Zadaný TenantId nemá správný formát. |
| **InvalidParameter** | Identifikátor SessionId nesmí mít hodnotu null ani být prázdný. |
| **InvalidParameter** | Nepovedlo se vyřešit žádné ProofData z požadavku nebo MSODS. ProofData je neznámý. |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Další kroky

### <a name="troubleshoot-user-accounts"></a>Řešení potíží s uživatelskými účty

Pokud uživatelé mají [problémy se dvoustupňové ověřováním](../user-help/multi-factor-authentication-end-user-troubleshoot.md), pomohou jim při vlastním diagnostikování problémů.

### <a name="health-check-script"></a>Skript kontroly stavu

[Skript kontroly stavu rozšíření Azure AD MFA NPS](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) provede základní kontrolu stavu při řešení potíží s rozšířením NPS. Spusťte skript a vyberte možnost 3.

### <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Pokud potřebujete další pomoc, kontaktujte odborníka na podporu prostřednictvím [podpory Azure Multi-Factor Authentication Server](https://support.microsoft.com/oas/default.aspx?prid=14947). Při kontaktování nás je užitečné, pokud můžete zahrnout co nejvíce informací o vašem problému. Informace, které můžete dodat, zahrnují stránku, kde jste viděli chybu, konkrétní kód chyby, konkrétní ID relace, ID uživatele, který zobrazil chybu, a protokoly ladění.

Chcete-li shromáždit protokoly ladění pro podporu diagnostiky, použijte následující postup na serveru NPS:

1. Otevřete Editor registru a přejděte na HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa nastavit **VERBOSE_LOG** na **hodnotu true** .
2. Otevřete příkazový řádek správce a spusťte tyto příkazy:

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

5. Otevřete Editor registru a přejděte na HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa nastavit **VERBOSE_LOG** na **hodnotu false** .
6. Zip obsah složky C:\NPS a připojte soubor zip k případu podpory.
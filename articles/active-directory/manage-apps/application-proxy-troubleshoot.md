---
title: Poradce při potížích s Proxy aplikací | Dokumentace Microsoftu
description: Obsahuje postup pro řešení potíží s chybami v Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 2904de3243e37d7ee575a504934d5975789c00ef
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135062"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Poradce při potížích s Proxy aplikací problémy a chybové zprávy
Pokud dojde k chybám při přístupu k publikované aplikaci nebo publikování aplikace, zkontrolujte následující možnosti a zda správně funguje Proxy aplikací služby Microsoft Azure AD:

* Otevřete konzolu služby Windows a ověřte, zda **Microsoft AAD Application Proxy Connector** service je povolený a spuštěný. Můžete také chtít podívat na stránku vlastností služby Proxy aplikace, jak je znázorněno na následujícím obrázku:  
  ![Snímek obrazovky okna vlastností konektoru Microsoft AAD Application Proxy](./media/application-proxy-troubleshoot/connectorproperties.png)
* Otevřete Prohlížeč událostí a vyhledejte události konektoru Proxy aplikace v **protokoly aplikací a služeb** > **Microsoft** > **AadApplicationProxy**  >  **Konektor** > **správce**.
* V případě potřeby podrobné protokoly jsou k dispozici ve [protokoly relace konektor Proxy aplikací zapnout](application-proxy-connectors.md#under-the-hood).

Další informace o nástroji pro poradce při potížích s Azure AD najdete v tématu [nástroj řešení potíží se ověřit síťové požadavky na konektor](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>Na stránce není správně vykreslila
Můžete mít problémy s vaší aplikací vykreslování nebo fungovat správně, aniž by mu musela specifické chybové zprávy. Tato situace může nastat, pokud jste publikovali cesta článku, ale aplikace vyžaduje obsah, který existuje mimo tuto cestu.

Například, pokud publikujete cestu https://yourapp/app ale aplikace volání Image v https://yourapp/media, se nevykreslí. Ujistěte se, že publikujete aplikaci pomocí nejvyšší úrovni cesta je nutné zahrnout všechny relevantní obsah. V tomto příkladu, bylo by http://yourapp/.

Pokud změníte vaše cesta k zahrnují odkazovaného obsahu, ale potřebují uživatelé přejít na podrobnější odkaz v cestě, najdete v blogovém příspěvku [nastavení správné odkaz pro aplikace Proxy aplikací ve službě Azure AD k panelu a Spouštěči aplikací Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Chyby konektorů

Pokud se registrace nezdařila během instalace konektoru průvodce, existují dva způsoby, chcete-li zobrazit důvod selhání. Buď vyhledejte v protokolu událostí v rámci **aplikací a služeb Logs\Microsoft\AadApplicationProxy\Connector\Admin**, nebo spuštěním následujícího příkazu Windows Powershellu:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Po nalezení konektor chyby z protokolu událostí použijte tuto tabulku běžné chyby pro vyřešení problému:

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Konektor se nepovedlo zaregistrovat: Ujistěte se, že jste povolili službu Proxy aplikací na portálu pro správu Azure a správně zadané služby Active Directory uživatelské jméno a heslo. : "Jeden nebo více chybám došlo k chybě." | Pokud se zavře okno registrace bez přihlášení ke službě Azure AD, znovu spusťte Průvodce pro konektor nástroje a registrace konektoru. <br><br> Pokud se otevře okno registrace a pak hned zavře bez povolení k přihlášení, zobrazí se pravděpodobně k této chybě. K této chybě dochází, když dojde k chybě sítě ve vašem systému. Ujistěte se, že je možné se připojit z prohlížeče na veřejném webu a porty jsou otevřené, jak je uvedeno v [požadavky na Proxy aplikace](application-proxy-add-on-premises-application.md). |
| Vymazat chyby se zobrazí v okně registrace. Nelze pokračovat | Pokud se zobrazí tato chyba a pak okno zavřete, zadali jste nesprávné uživatelské jméno nebo heslo. Zkuste to znova. |
| Konektor se nepovedlo zaregistrovat: Ujistěte se, že jste povolili službu Proxy aplikací na portálu pro správu Azure a správně zadané služby Active Directory uživatelské jméno a heslo. Chyba: "AADSTS50059: žádné informace o identifikaci tenanta najít v jednom požadavku nebo implikována v žádném zadané přihlašovací údaje a vyhledávání podle služby se nezdařilo instančního objektu URI. | Pokoušíte se přihlásit pomocí Account Microsoft a nikoli domény, který je součástí organizace ID adresáře, který se pokoušíte získat přístup. Ujistěte se, že správce součástí se stejným názvem domény jako doména tenanta, například pokud Azure AD doména je contoso.com, Správce by měl být admin@contoso.com. |
| Nepovedlo se načíst aktuální zásady spouštění pro spuštěné skripty prostředí PowerShell. | Pokud instalace konektoru selže, zkontrolujte, že nejsou zakázané zásady spouštění prostředí PowerShell. <br><br>1. Otevřete Editor zásad skupiny.<br>2. Přejděte na **konfigurace počítače** > **šablony pro správu** > **součásti Windows**  >   **Prostředí Windows PowerShell** a dvakrát klikněte na panel **zapnutí provádění skriptů**.<br>3. Zásady spouštění může být nastaven na hodnotu **Nenakonfigurováno** nebo **povoleno**. Pokud hodnotu **povoleno**, ujistěte se, že v možnostech, zásady spouštění je nastaven na hodnotu **Povolit místní a vzdálené podepsaných skriptů** nebo **povolit všechny skripty**. |
| Konektor se nepovedlo stáhnout konfiguraci. | Tento konektor klientský certifikát, který se používá pro ověřování, vypršela platnost. To může také dojít, pokud máte nainstalovaný za proxy serverem konektor. V tomto případě konektor nemá přístup k Internetu a nebudete moct aplikacím vzdáleným uživatelům poskytnout. Obnovit ručně pomocí vztahu důvěryhodnosti `Register-AppProxyConnector` rutiny v prostředí Windows PowerShell. Pokud váš konektor je za proxy serverem, je potřeba udělit přístup k Internetu k účtům konektor "síťové služby" a "místní systém." Můžete to provést tak, že udělíte přístup k proxy serveru nebo tak, že nastavíte, aby obcházení proxy serveru. |
| Konektor se nepovedlo zaregistrovat: Ujistěte se, že jste globální správce služby Active Directory k registraci konektoru. Chyba: "registrace žádost byla zamítnuta." | Alias, který se snažíte přihlásit se se správcem v této doméně. Váš konektor je vždy nainstalován pro adresář, který vlastní domény uživatele. Ujistěte se, zda má účet správce, který se pokoušíte přihlásit pomocí globální oprávnění k tenantovi Azure AD. |

## <a name="kerberos-errors"></a>Chyby protokolu Kerberos

Tato tabulka obsahuje běžné chyby, které pocházejí z nastavení protokolu Kerberos a konfigurace a zahrnuje návrhy řešení.

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Nepovedlo se načíst aktuální zásady spouštění pro spuštěné skripty prostředí PowerShell. | Pokud instalace konektoru selže, zkontrolujte, že nejsou zakázané zásady spouštění prostředí PowerShell.<br><br>1. Otevřete Editor zásad skupiny.<br>2. Přejděte na **konfigurace počítače** > **šablony pro správu** > **součásti Windows**  >   **Prostředí Windows PowerShell** a dvakrát klikněte na panel **zapnutí provádění skriptů**.<br>3. Zásady spouštění může být nastaven na hodnotu **Nenakonfigurováno** nebo **povoleno**. Pokud hodnotu **povoleno**, ujistěte se, že v možnostech, zásady spouštění je nastaven na hodnotu **Povolit místní a vzdálené podepsaných skriptů** nebo **povolit všechny skripty**. |
| 12008 – azure AD překročil maximální počet povolených pokusů o Kerberos ověřování back-end server. | Tato chyba může znamenat nesprávné konfigurace mezi službami Azure AD a back-endový aplikační server, nebo problém v konfiguraci data a času v obou počítačích. Back-end server odmítl lístek protokolu Kerberos vytvořené službou Azure AD. Ověřte, že Azure AD a správně nakonfigurované back-endový aplikační server. Ujistěte se, že konfigurační data a času v Azure AD a jsou synchronizovány back-endový aplikační server. |
| 13016 – azure AD nejde načíst lístek protokolu Kerberos jménem uživatele, protože neexistuje žádný hlavní název uživatele v hraničním tokenu nebo v přístupu k souboru cookie. | Je nějaký problém s konfigurací služby tokenů zabezpečení. Opravte konfiguraci deklarace identity UPN v Služba tokenů zabezpečení. |
| 13019 – azure AD nelze získat lístek protokolu Kerberos jménem uživatele z důvodu následující obecná chyba rozhraní API. | Tato událost může znamenat nesprávné konfigurace mezi službami Azure AD a server řadiče domény nebo problém v konfiguraci data a času v obou počítačích. Řadič domény odmítl lístek protokolu Kerberos vytvořené službou Azure AD. Ověření Azure AD a back-endový aplikační server jsou nakonfigurované správně, zejména v konfiguraci hlavního názvu služby. Zajistěte, aby že Azure AD je připojený ke stejné doméně jako řadič domény a ověřit, že řadič domény vytvoří vztah důvěryhodnosti s Azure AD. Ujistěte se, že konfigurační data a času v Azure AD a řadiči domény synchronizované. |
| 13020 – azure AD nelze načíst lístek protokolu Kerberos jménem uživatele, protože není definován back-end server hlavní název služby. | Tato událost může znamenat nesprávné konfigurace mezi službami Azure AD a server řadiče domény nebo problém v konfiguraci data a času v obou počítačích. Řadič domény odmítl lístek protokolu Kerberos vytvořené službou Azure AD. Ověření Azure AD a back-endový aplikační server jsou nakonfigurované správně, zejména v konfiguraci hlavního názvu služby. Zajistěte, aby že Azure AD je připojený ke stejné doméně jako řadič domény a ověřit, že řadič domény vytvoří vztah důvěryhodnosti s Azure AD. Ujistěte se, že konfigurační data a času v Azure AD a řadiči domény synchronizované. |
| 13022 – azure AD nemůže ověřit uživatele, protože back-end server odpoví na pokusy o ověření protokolu Kerberos s chybou HTTP 401. | Tato událost může znamenat nesprávné konfigurace mezi službami Azure AD a back-endový aplikační server, nebo problém v konfiguraci data a času v obou počítačích. Back-end server odmítl lístek protokolu Kerberos vytvořené službou Azure AD. Ověřte, že Azure AD a správně nakonfigurované back-endový aplikační server. Ujistěte se, že konfigurační data a času v Azure AD a jsou synchronizovány back-endový aplikační server. Další informace najdete v tématu [řešení potíží s protokolem Kerberos omezeného delegování konfigurace pro Proxy aplikací](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Chyby koncového uživatele

Tento seznam obsahuje chyby, které vaši koncoví uživatelé setkat při pokusu o přístup k aplikaci a selhání. 

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Web nelze zobrazit stránku. | Uživatel může zobrazit tato chyba při pokusu o přístup k aplikaci, kterou jste publikovali, pokud je aplikace IWA. Definované hlavního názvu služby pro tuto aplikaci může být nesprávná. Pro aplikace IWA Ujistěte se, že správnost SPN konfigurované pro tuto aplikaci. |
| Web nelze zobrazit stránku. | Uživatel může zobrazit tato chyba při pokusu o přístup k aplikaci, kterou jste publikovali, pokud je aplikace OWA. To může být způsobeno jedním z následujících:<br><li>Definované hlavního názvu služby pro tuto aplikaci je nesprávný. Ujistěte se, že je správný název SPN konfigurované pro tuto aplikaci.</li><li>Uživatel, který pokusu o přístup k aplikaci je pomocí účtu Microsoft, nikoli na řádné podnikový účet pro přihlášení, nebo je uživatel uživatele typu Host. Ujistěte se, že uživatel přihlásí pomocí firemního účtu, který odpovídá doméně publikovanou aplikaci. Account Microsoft uživatelů a hostů nemá přístup k aplikacím IWA.</li><li>Uživatel, který pokusu o přístup k aplikaci není správně definováno pro tuto aplikaci na straně v místním prostředí. Ujistěte se, že tento uživatel má příslušná oprávnění definované pro tuto aplikaci back-endu na místním počítači. |
| Nelze získat přístup k této podnikové aplikace. Nemáte oprávnění přistupovat k této aplikaci. Ověření se nepovedlo. Ujistěte se, že chcete přiřadit uživatele s přístupem k této aplikaci. | Uživatelé mohou získat tuto chybu při pokusu o přístup k aplikaci, kterou jste publikovali, pokud používají účty Microsoft namísto jejich podnikový účet pro přihlášení. Tato chyba může zobrazit také uživatele typu Host. Uživatelé Microsoft Account a hosté nelze přístup k aplikacím IWA. Ujistěte se, že uživatel přihlásí pomocí firemního účtu, který odpovídá doméně publikovanou aplikaci.<br><br>Nemusí mít přiřazené uživatele pro tuto aplikaci. Přejděte na **aplikace** kartu a v části **uživatelů a skupin**, přiřaďte tento uživatel nebo skupina uživatelů k této aplikaci. |
| Teď je nepřístupný této podnikové aplikace. Zkuste to prosím znovu později... Konektor vypršel časový limit. | Uživatelé mohou získat tuto chybu při pokusu o přístup k aplikaci, kterou jste publikovali, pokud nejsou správně definované pro tuto aplikaci na straně v místním prostředí. Ujistěte se, aby uživatelé měli správná oprávnění, jak jsou definovány pro tuto aplikaci back-endu na místním počítači. |
| Nelze získat přístup k této podnikové aplikace. Nemáte oprávnění přistupovat k této aplikaci. Ověření se nepovedlo. Ujistěte se, že uživatel má licenci pro Azure Active Directory Premium nebo Basic. | Uživatelům může zobrazit tato chyba při pokusu o přístup k aplikaci, kterou jste publikovali, pokud jejich nebyly explicitně přiřazovat licence Premium nebo Basic správcem účastníka. Přejděte do služby Active Directory účastníka **licence** kartu a ujistěte se, že tento uživatel nebo skupina uživatelů je přiřazená licence Premium nebo Basic. |

## <a name="my-error-wasnt-listed-here"></a>Moje chyby se zde uvedené.

Pokud narazíte na chybu nebo problém s Proxy aplikací Azure AD, která není uvedená v této příručce pro řešení potíží, chtěli bychom slyšet o něm. Odešlete e-mail na naše [zpětnou vazbu týmu](mailto:aadapfeedback@microsoft.com) s podrobnostmi vás došlo k chybě.

## <a name="see-also"></a>Další informace najdete v tématech
* [Povolení Proxy aplikace služby Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publikování aplikací pomocí Proxy aplikace](application-proxy-add-on-premises-application.md)
* [Povolit jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md)
* [Povolení podmíněného přístupu](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

---
title: Řešení potíží s Proxy aplikace | Microsoft Docs
description: Vysvětluje postup řešení potíží s chybami v Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 2584af94be5f36d07278d1e3d70d7d3f1403203c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Řešení potíží s Proxy aplikace problémy a chybové zprávy
Pokud dojde k chybám při přístupu k publikované aplikaci nebo v publikování aplikací, zkontrolujte následující možnosti, zda je správně funguje proxy aplikace služby Microsoft Azure AD:

* Otevřete konzolu služby systému Windows a ověřte, zda **Microsoft AAD Application Proxy Connector** je povolená a spuštěná služba. Můžete také podívat se na stránku vlastností Proxy aplikace služby, jak je znázorněno na následujícím obrázku:  
  ![Snímek obrazovky okna vlastností konektoru Microsoft AAD Application Proxy](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)
* Otevřete Prohlížeč událostí a vyhledejte události konektoru Proxy aplikace v **protokoly aplikací a služeb** > **Microsoft** > **AadApplicationProxy** > **konektor** > **správce**.
* V případě potřeby podrobnější protokoly jsou dostupné ve [zapnete protokoly relace konektoru Proxy aplikace](application-proxy-understand-connectors.md#under-the-hood).

Další informace o nástroji Poradce při potížích s Azure AD najdete v tématu [nástroj řešení potíží pro ověření konektoru síťové požadavky](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/03/troubleshooting-tool-to-validate-connector-networking-prerequisites).

## <a name="the-page-is-not-rendered-correctly"></a>Stránky není správně vykreslen.
Můžete mít problémy s aplikací vykreslování nebo funguje správně, aniž by mu musela specifické chybové zprávy. Tato situace může nastat, pokud jste publikovali cesta článek, ale aplikace vyžaduje obsah, který existuje mimo této cestě.

Například, pokud publikujete cesta https://yourapp/app , ale aplikace volá bitové kopie https://yourapp/media, nebude vykreslen. Ujistěte se, že publikování aplikace pomocí nejvyšší úrovně cesty, je nutné zahrnout všechny relevantní obsah. V tomto příkladu je http://yourapp/.

Pokud změníte cestu ke zahrnovat odkazovaného obsahu, ale stále potřebují uživatelům zobrazovat na hlubší propojení v cestě, naleznete v příspěvku blogu [nastavení správné odkaz pro aplikaci Proxy aplikace ve službě Azure AD přístup k panelu a Spouštěč aplikace Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Konektor chyby

Použití [Azure AD Application proxy serveru konektoru porty nástroj pro testování](https://aadap-portcheck.connectorporttest.msappproxy.net/) k ověření, že vaše konektoru může kontaktovat službu Proxy aplikace. Minimálně Ujistěte se, že oblasti střed USA a oblasti nejblíže k vám mají všechny zelené značky zaškrtnutí. Kromě toho další zelené značky zaškrtnutí znamená větší odolnost proti chybám. 

Pokud se registrace nezdařila během instalace konektoru průvodce, existují dva způsoby, chcete-li zobrazit příčinu selhání. Buď vyhledejte v protokolu událostí v části **aplikace a služby Logs\Microsoft\AadApplicationProxy\Connector\Admin**, nebo spuštěním následujícího příkazu prostředí Windows PowerShell:

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

Jakmile zjistíte, konektor chyby z protokolu událostí, použijte tuto tabulku běžné chyby pro vyřešení problému:

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Registrace konektoru se nezdařilo: Zajistěte, aby povolené Proxy aplikace v portálu pro správu Azure a správně zadali služby Active Directory uživatelské jméno a heslo. : 'Jedné nebo více chybám došlo k chybě." | Pokud jste zavřeli okno registrace bez přihlášení do služby Azure AD, znovu spusťte Průvodce pro konektor nástroje a registrace konektoru. <br><br> Pokud se otevře okno registrace a potom se okamžitě ukončí bez umožňuje přihlásit, zobrazí se pravděpodobně této chybě. K této chybě dojde, když dojde k chybě sítě v systému. Ujistěte se, že je možné se připojit k na veřejně přístupný web z prohlížeče a že porty jsou otevřené zadané v [požadavky na Proxy aplikace](manage-apps/application-proxy-enable.md). |
| Vymazat chyba se zobrazí v okně registrace. Nelze pokračovat. | Pokud se zobrazí tato chyba, a pak okno zavřete, zadali jste nesprávné uživatelské jméno nebo heslo. Zkuste to znova. |
| Registrace konektoru se nezdařilo: Zajistěte, aby povolené Proxy aplikace v portálu pro správu Azure a správně zadali služby Active Directory uživatelské jméno a heslo. Chyba: ' AADSTS50059: žádné informace o identifikaci klienta najít buď žádosti nebo implicitní podle zadané přihlašovací údaje a hledání službou se nezdařilo hlavní identifikátor URI. | Pokoušíte se přihlásit pomocí Account Microsoft a ne doméně, která je součástí ID organizace adresáře, který se pokoušíte získat přístup. Ujistěte se, že správce součástí se stejným názvem domény jako doména klienta, například, pokud doména služby Azure AD je contoso.com, Správce by měl být admin@contoso.com. |
| Nepodařilo se načíst aktuální zásady spouštění pro spuštěné skripty prostředí PowerShell. | Pokud instalace konektoru selže, zkontrolujte, abyste měli jistotu, že není zakázána zásady spouštění prostředí PowerShell. <br><br>1. Otevřete Editor zásad skupiny.<br>2. Přejděte na **konfigurace počítače** > **šablony pro správu** > **součásti systému Windows** > **prostředí Windows PowerShell** a dvakrát klikněte na **zapnout provádění skriptu**.<br>3. Zásady spouštění může být nastaven na hodnotu **není nakonfigurováno** nebo **povoleno**. Pokud nastavena na **povoleno**, ujistěte se, že v nabídce Možnosti, zásady spouštění je nastaven na hodnotu **povolit skripty místní a vzdálené podepsaných skriptů** nebo **povolit všechny skripty**. |
| Stažení konfigurace konektoru se nezdařilo. | Konektor nástroje klientský certifikát, který se používá pro ověřování, vypršela platnost. To může taky dojít, pokud máte nainstalovaný za proxy konektor. V takovém případě konektor nemá přístup k Internetu a nebude možné aplikacím vzdáleným uživatelům poskytnout. Obnovit ručně pomocí vztahu důvěryhodnosti `Register-AppProxyConnector` rutiny v prostředí Windows PowerShell. Pokud vaše konektor je za proxy server, je potřeba udělit přístup k Internetu k účtům konektor "síťové služby" a "místní systém." Můžete to provést tak, že udělíte přístup k proxy serveru nebo nastavením je používat proxy server. |
| Registrace konektoru se nezdařilo: Zkontrolujte, zda jsou globální správce služby Active Directory k registraci konektoru. Chyba: 'žádost o registraci byl odepřen." | Alias, který se pokoušíte přihlásit, není správce v této doméně. Vaše konektor je vždy nainstalován pro adresář, který vlastní domény uživatele. Ujistěte se, že má účet správce, který se pokoušíte přihlásit globální oprávnění ke klientovi Azure AD. |

## <a name="kerberos-errors"></a>Chyby protokolu Kerberos

Tato tabulka obsahuje více běžných chyb, které pocházejí z nastavení protokolu Kerberos a konfigurace i návrhy na řešení.

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Nepodařilo se načíst aktuální zásady spouštění pro spuštěné skripty prostředí PowerShell. | Pokud instalace konektoru selže, zkontrolujte, abyste měli jistotu, že není zakázána zásady spouštění prostředí PowerShell.<br><br>1. Otevřete Editor zásad skupiny.<br>2. Přejděte na **konfigurace počítače** > **šablony pro správu** > **součásti systému Windows** > **prostředí Windows PowerShell** a dvakrát klikněte na **zapnout provádění skriptu**.<br>3. Zásady spouštění může být nastaven na hodnotu **není nakonfigurováno** nebo **povoleno**. Pokud nastavena na **povoleno**, ujistěte se, že v nabídce Možnosti, zásady spouštění je nastaven na hodnotu **povolit skripty místní a vzdálené podepsaných skriptů** nebo **povolit všechny skripty**. |
| 12008 – azure AD překročen maximální počet povolených pokusů ověřování protokolu Kerberos back-end server. | Tato chyba může znamenat nesprávné konfiguraci mezi službou Azure AD a serveru back-end aplikace nebo o problém v konfiguraci data a času na obou počítačích. Back-end server odmítl lístek protokolu Kerberos vytvořené Azure AD. Ověřte, že Azure AD a back-end aplikačním serveru jsou správně nakonfigurován. Ujistěte se, že nastavení data a času na Azure AD a jsou synchronizovány back-end aplikačním serveru. |
| 13016 – azure AD nelze načíst lístek protokolu Kerberos jménem uživatele, protože neexistuje žádný UPN v hraničním tokenu nebo v přístupu k souboru cookie. | Došlo k potížím s konfigurací služby tokenů zabezpečení. Konfigurace nároku UPN, odstraňte v Služba tokenů zabezpečení. |
| 13019 – azure AD nelze načíst lístek protokolu Kerberos jménem uživatele, protože došlo k obecné chybě rozhraní API. | Tato událost může znamenat nesprávné konfiguraci mezi službou Azure AD a server řadiče domény, nebo problém v konfiguraci data a času na obou počítačích. Řadič domény odmítl lístek protokolu Kerberos vytvořené Azure AD. Ověřte, že Azure AD a back-end aplikačním serveru jsou správně nakonfigurována, zejména konfigurace hlavního názvu služby. Ujistěte se, že Azure AD je připojený ke stejné doméně jako řadič domény a ověřit, že řadič domény vytváří vztah důvěryhodnosti s Azure AD. Ujistěte se, že nastavení data a času na Azure AD a jsou synchronizovány řadičem domény. |
| 13020 – azure AD nelze načíst lístek protokolu Kerberos jménem uživatele, protože není definováno back-end serveru SPN. | Tato událost může znamenat nesprávné konfiguraci mezi službou Azure AD a server řadiče domény, nebo problém v konfiguraci data a času na obou počítačích. Řadič domény odmítl lístek protokolu Kerberos vytvořené Azure AD. Ověřte, že Azure AD a back-end aplikačním serveru jsou správně nakonfigurována, zejména konfigurace hlavního názvu služby. Ujistěte se, že Azure AD je připojený ke stejné doméně jako řadič domény a ověřit, že řadič domény vytváří vztah důvěryhodnosti s Azure AD. Ujistěte se, že nastavení data a času na Azure AD a jsou synchronizovány řadičem domény. |
| 13022 – azure AD nemůže ověřit uživatele, protože back-end server odpoví na pokusy o ověření pomocí protokolu Kerberos s chybu HTTP 401. | Tato událost může znamenat nesprávné konfiguraci mezi službou Azure AD a serveru back-end aplikace nebo o problém v konfiguraci data a času na obou počítačích. Back-end server odmítl lístek protokolu Kerberos vytvořené Azure AD. Ověřte, že Azure AD a back-end aplikačním serveru jsou správně nakonfigurován. Ujistěte se, že nastavení data a času na Azure AD a jsou synchronizovány back-end aplikačním serveru. Další informace najdete v tématu [řešení potíží s protokolem Kerberos omezeného delegování konfigurace pro Proxy aplikace](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Chyby koncového uživatele

Tento seznam obsahuje chyby, které vaši koncoví uživatelé setkat při pokusu o přístup k aplikaci a nezdaří. 

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Stránku nelze zobrazit stránku. | Uživatel může dojít k této chybě při pokusu o přístup k aplikaci, kterou jste publikovali, pokud aplikace je aplikace, integrované ověřování systému Windows. Definovaný název SPN pro tuto aplikaci může být nesprávný. Pro aplikace, integrované ověřování systému Windows Ujistěte se, zda je správný název SPN nakonfigurován pro tuto aplikaci. |
| Stránku nelze zobrazit stránku. | Uživatel může dojít k této chybě při pokusu o přístup k aplikaci, kterou jste publikovali, pokud aplikace je aplikace OWA. To může být způsobeno jedním z těchto:<br><li>Definovaný název SPN pro tuto aplikaci je nesprávný. Ujistěte se, zda je správný název SPN nakonfigurován pro tuto aplikaci.</li><li>Uživatele, kteří se pokusili získat přístup k aplikaci je k přihlášení pomocí účtu Microsoft, nikoli na správné podnikový účet, nebo uživatel je uživatel typu Host. Zajistěte, aby se uživatel přihlásí pomocí svého účtu podnikové, odpovídající doméně publikované aplikace. Uživatelé s Account Microsoft a hostů nelze přístup k aplikacím integrované ověřování systému Windows.</li><li>Pro tuto aplikaci na straně místní není správně definováno uživatele, kteří se pokusili získat přístup k aplikaci. Ujistěte se, jestli tento uživatel má příslušná oprávnění, jak jsou definovány pro tuto aplikaci back-end na místní počítač. |
| Nelze získat přístup k této podnikové aplikace. Nemáte oprávnění k této aplikaci přístup. Ověření se nepovedlo. Ujistěte se, že přiřaďte uživatele s přístupem k této aplikaci. | Vaši uživatelé může dojít k této chybě při pokusu o přístup k aplikaci, kterou jste publikovali, pokud používají účty Microsoft místo podnikovém účtu pro přihlášení. Uživatele typu Host může také dojít k této chybě. Uživatelé s Account Microsoft a hosté nelze přístup k aplikacím integrované ověřování systému Windows. Zajistěte, aby se uživatel přihlásí pomocí svého účtu podnikové, odpovídající doméně publikované aplikace.<br><br>Nemůže mít přiřazeno uživatele pro tuto aplikaci. Přejděte na **aplikace** kartě a v části **uživatelů a skupin**, přiřadit tohoto uživatele nebo skupiny uživatelů do této aplikace. |
| Nyní je nepřístupný této podnikové aplikace. Zkuste to prosím znovu později... Konektor vypršel časový limit. | Vaši uživatelé může dojít k této chybě při pokusu o přístup k aplikaci, kterou jste publikovali, pokud nejsou správně definované pro tuto aplikaci na straně místní. Ujistěte se, že uživatelé mají oprávnění, jak jsou definovány pro tuto aplikaci back-end na místní počítač. |
| Nelze získat přístup k této podnikové aplikace. Nemáte oprávnění k této aplikaci přístup. Ověření se nepovedlo. Ujistěte se, že uživatel má licenci pro Azure Active Directory Premium nebo Basic. | Vaši uživatelé může dojít k této chybě při pokusu o přístup k aplikaci, kterou jste publikovali, pokud nebyla přiřazena explicitně s licencí Premium nebo Basic správcem odběratele. Přejděte do služby Active Directory odběratele **licence** kartě a ujistěte se, že tohoto uživatele nebo skupiny uživatelů je přiřazena licence Premium nebo Basic. |

## <a name="my-error-wasnt-listed-here"></a>Moje chyba nebyl zde uvedené.

Pokud dojde k chybě nebo problém s Azure AD Application Proxy, který není uveden v této příručce pro řešení potíží, bychom rádi uslyšíme ho. E-mailovou zprávu na našem [názor týmu](mailto:aadapfeedback@microsoft.com) s podrobnostmi můžete došlo k chybě.

## <a name="see-also"></a>Další informace najdete v tématech
* [Povolení Proxy aplikace služby Azure Active Directory](manage-apps/application-proxy-enable.md)
* [Publikování aplikací pomocí Proxy aplikace](manage-apps/application-proxy-publish-azure-portal.md)
* [Povolit jednotné přihlašování](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Povolení podmíněného přístupu](application-proxy-enable-remote-access-sharepoint.md)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

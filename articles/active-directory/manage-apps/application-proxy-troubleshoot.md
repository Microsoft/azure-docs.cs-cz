---
title: Poradce při potížích s proxy aplikacemi | Dokumenty společnosti Microsoft
description: Popisuje, jak řešit chyby v proxy aplikace Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7be9a17bed2a39d16f813332c2d6effc03393264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244222"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Poradce při potížích s proxy aplikacemi a chybové zprávy

Při řešení problémů s proxy aplikací doporučujeme začít s kontrolou toku řešení potíží, [ladění problémů s konektorem proxy aplikace](application-proxy-debug-connectors.md), abyste zjistili, zda jsou konektory proxy aplikace správně nakonfigurovány. Pokud máte stále potíže s připojením k aplikaci, postupujte podle toku řešení potíží s aplikací [Ladicí aplikace proxy](application-proxy-debug-apps.md).

Pokud dojde k chybám při přístupu k publikované aplikaci nebo v aplikacích publikování, zkontrolujte následující možnosti, zda proxy aplikace Microsoft Azure AD funguje správně:

* Otevřete konzolu služeb systému Windows. Ověřte, zda je povolena a spuštěna služba **Microsoft AAD Application Proxy Connector.** Můžete se také podívat na stránku vlastností služby Proxy aplikace, jak je znázorněno na následujícím obrázku:  
  ![Snímek obrazovky s oknem Vlastnosti konektoru proxy aplikace Microsoft AAD](./media/application-proxy-troubleshoot/connectorproperties.png)
* Otevřete Prohlížeč událostí a vyhledejte události konektoru proxy aplikace v **protokolech** > aplikací a služeb**Microsoft** > **AadApplicationProxy** > **Connector** > **Admin**.
* V případě potřeby jsou k dispozici podrobnější protokoly [zapnutím protokolů relací konektoru proxy aplikace](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>Stránka není vykreslena správně.
Můžete mít problémy s vykreslováním nebo nesprávným fungováním aplikace bez přijetí konkrétních chybových zpráv. Tato situace může nastat, pokud jste publikovali cestu k článku, ale aplikace vyžaduje obsah, který existuje mimo tuto cestu.

Pokud například publikujete `https://yourapp/app` cestu, ale `https://yourapp/media`aplikace volá obrázky v aplikaci , nebudou vykresleny. Ujistěte se, že publikujete aplikaci pomocí cesty nejvyšší úrovně, kterou potřebujete zahrnout veškerý relevantní obsah. V tomto příkladu `http://yourapp/`by bylo .

Pokud změníte cestu tak, aby zahrnovala odkazovaný obsah, ale stále potřebujete, aby uživatelé získali hlubší odkaz v cestě, přečtěte si příspěvek blogu [Nastavení správného odkazu pro aplikace Proxy aplikací na přístupovém panelu Azure AD a spouštěči aplikací Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Chyby konektoru

Pokud se registrace nezdaří během instalace průvodce konektorem, existují dva způsoby, jak zobrazit důvod selhání. V protokolu událostí v části **Protokoly aplikací a služeb\Microsoft\AadApplicationProxy\Connector\Admin**nebo spusťte následující příkaz prostředí Windows PowerShell:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Jakmile najdete chybu konektoru z protokolu událostí, použijte tuto tabulku běžných chyb k vyřešení problému:

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Registrace konektoru se nezdařila: Ujistěte se, že jste na portálu pro správu Azure povolili proxy aplikace a že jste správně zadali uživatelské jméno a heslo služby Active Directory. Chyba: Došlo k jedné nebo více chybám. | Pokud jste zavřeli registrační okno bez přihlášení k Azure AD, spusťte průvodce konektorznovu a zaregistrujte konektor. <br><br> Pokud se otevře registrační okno a okamžitě se zavře, aniž byste se mohli přihlásit, pravděpodobně se zobrazí tato chyba. K této chybě dochází, pokud je chyba sítě v systému. Ujistěte se, že je možné se připojit z prohlížeče k veřejnému webu a že porty jsou otevřené tak, jak je uvedeno v [požadavcích proxy aplikace](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Jasná chyba je uvedena v registračním okně. Nelze pokračovat. | Pokud se zobrazí tato chyba a okno se zavře, zadali jste nesprávné uživatelské jméno nebo heslo. Zkuste to znovu. |
| Registrace konektoru se nezdařila: Ujistěte se, že jste na portálu pro správu Azure povolili proxy aplikace a že jste správně zadali uživatelské jméno a heslo služby Active Directory. Chyba: AADSTS50059: V požadavku nebyly nalezeny žádné informace o identifikaci klienta nebo které vyplývají z poskytnutých pověření a vyhledávání podle identifikátoru URI hlavního povinného zabezpečení služby se nezdařilo. | Pokoušíte se přihlásit pomocí účtu Microsoft a nikoli pomocí domény, která je součástí ID organizace adresáře, ke kterému se pokoušíte získat přístup. Ujistěte se, že správce je součástí stejného názvu domény jako doména klienta, například admin@contoso.compokud je doména Azure AD contoso.com, měl by být správce . |
| Načtení aktuální zásady spuštění pro spouštění skriptů prostředí PowerShell se nezdařilo. | Pokud se instalace konektoru nezdaří, zkontrolujte, zda nejsou zakázány zásady spuštění prostředí PowerShell. <br><br>1. Otevřete Editor zásad skupiny.<br>2. Přejděte na**šablonu** >  **pro správu konfigurace** > počítače**Windows Components** > **Windows PowerShell** a poklepejte **na tlačítko Zapnout spouštění skriptů**.<br>3. Zásady provádění lze nastavit buď **není nakonfigurováno** nebo **povoleno**. Pokud je **nastavena**možnost Povoleno , ujistěte se, že v části Možnosti jsou zásady spuštění nastaveny na **povolit místní skripty a vzdálené podepsané skripty** nebo **povolit všechny skripty**. |
| Konektoru se nepodařilo stáhnout konfiguraci. | Platnost klientského certifikátu konektoru, který se používá k ověřování, vypršela. K tomu může dojít také v případě, že máte konektor nainstalovaný za proxy serverem. V takovém případě konektor nemá přístup k Internetu a nebude moci poskytovat aplikace vzdáleným uživatelům. Obnovte vztah důvěryhodnosti `Register-AppProxyConnector` ručně pomocí rutiny v prostředí Windows PowerShell. Pokud je konektor za proxy serverem, je nutné udělit přístup k Internetu k účtům konektoru "síťové služby" a "místní systém". Toho lze dosáhnout buď udělením přístupu k proxy serveru nebo jejich nastavením tak, aby obejít proxy server. |
| Registrace konektoru se nezdařila: Ujistěte se, že jste správcem aplikace služby Active Directory, který konektor zaregistruje. Chyba: Požadavek na registraci byl odepřen. | Alias, ke kterým se pokoušíte přihlásit, není správcem této domény. Konektor je vždy nainstalován pro adresář, který vlastní doménu uživatele. Ujistěte se, že účet správce, pomocí kterého se pokoušíte přihlásit, má alespoň oprávnění správce aplikací k tenantovi Azure AD. |
| Konektor se nemohl připojit ke službě z důvodu problémů se sítí. Konektor se pokusil o přístup k následující adrese URL. | Konektor se nemůže připojit ke cloudové službě Proxy aplikace. K tomu může dojít, pokud máte pravidlo brány firewall, které blokuje připojení. Ujistěte se, že jste povolili přístup ke správným portům a adresám URL uvedeným v [požadavcích proxy aplikace](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Chyby protokolu Kerberos

Tato tabulka popisuje běžnější chyby, které pocházejí z nastavení a konfigurace protokolu Kerberos, a obsahuje návrhy řešení.

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Načtení aktuální zásady spuštění pro spouštění skriptů prostředí PowerShell se nezdařilo. | Pokud se instalace konektoru nezdaří, zkontrolujte, zda nejsou zakázány zásady spuštění prostředí PowerShell.<br><br>1. Otevřete Editor zásad skupiny.<br>2. Přejděte na**šablonu** >  **pro správu konfigurace** > počítače**Windows Components** > **Windows PowerShell** a poklepejte **na tlačítko Zapnout spouštění skriptů**.<br>3. Zásady provádění lze nastavit buď **není nakonfigurováno** nebo **povoleno**. Pokud je **nastavena**možnost Povoleno , ujistěte se, že v části Možnosti jsou zásady spuštění nastaveny na **povolit místní skripty a vzdálené podepsané skripty** nebo **povolit všechny skripty**. |
| 12008 – Azure AD překročil maximální počet povolených pokusů kerberos ověřování na back-endový server. | Tato chyba může znamenat nesprávnou konfiguraci mezi Azure AD a serverem back-endové aplikace nebo problém v konfiguraci času a data v obou počítačích. Back-endový server odmítl lístek Kerberos vytvořený službou Azure AD. Ověřte, že Azure AD a server back-endové aplikace jsou správně nakonfigurované. Ujistěte se, že konfigurace času a data na Azure AD a back-endaplikační server jsou synchronizovány. |
| 13016 – Azure AD nelze načíst lístek protokolu Kerberos jménem uživatele, protože není žádný hlavní upn v tokenu okraje nebo v přístupu cookie. | Došlo k potížím s konfigurací služby STS. Opravte konfiguraci deklarací hlavního uživatele v STS. |
| 13019 – Azure AD nelze načíst lístek protokolu Kerberos jménem uživatele z důvodu následující obecné chyby rozhraní API. | Tato událost může znamenat nesprávnou konfiguraci mezi Azure AD a serverem řadiče domény nebo problém v konfiguraci času a data v obou počítačích. Řadič domény odmítl lístek Kerberos vytvořený službou Azure AD. Ověřte, že Azure AD a server back-endu jsou správně nakonfigurované, zejména konfigurace hlavního směrovače. Ujistěte se, že Azure AD je doména připojená ke stejné doméně jako řadič domény, abyste zajistili, že řadič domény naváže vztah důvěryhodnosti s Azure AD. Ujistěte se, že konfigurace času a data ve službě Azure AD a řadiči domény jsou synchronizovány. |
| 13020 – Azure AD nelze načíst lístek protokolu Kerberos jménem uživatele, protože není definován název služby PRO Zálohování a klienta serveru. | Tato událost může znamenat nesprávnou konfiguraci mezi Azure AD a serverem řadiče domény nebo problém v konfiguraci času a data v obou počítačích. Řadič domény odmítl lístek Kerberos vytvořený službou Azure AD. Ověřte, že Azure AD a server back-endu jsou správně nakonfigurované, zejména konfigurace hlavního směrovače. Ujistěte se, že Azure AD je doména připojená ke stejné doméně jako řadič domény, abyste zajistili, že řadič domény naváže vztah důvěryhodnosti s Azure AD. Ujistěte se, že konfigurace času a data ve službě Azure AD a řadiči domény jsou synchronizovány. |
| 13022 – Azure AD nemůže ověřit uživatele, protože back-endový server reaguje na pokusy o ověření protokolem Kerberos s chybou HTTP 401. | Tato událost může znamenat nesprávnou konfiguraci mezi Azure AD a back-endový aplikační server nebo problém v konfiguraci času a data na obou počítačích. Back-endový server odmítl lístek Kerberos vytvořený službou Azure AD. Ověřte, že Azure AD a server back-endové aplikace jsou správně nakonfigurované. Ujistěte se, že konfigurace času a data na Azure AD a back-endaplikační server jsou synchronizovány. Další informace naleznete [v tématu Poradce při potížích s konfiguracemi delegování s omezeným i protokolem Kerberos pro proxy aplikace](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Chyby koncových uživatelů

Tento seznam obsahuje chyby, se kterými se mohou koncoví uživatelé setkat při pokusu o přístup k aplikaci a selhání. 

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Web nemůže zobrazit stránku. | Uživatel může získat tuto chybu při pokusu o přístup k aplikaci, kterou jste publikovali, pokud je aplikace aplikace IWA aplikace. Definovaný název SPN pro tuto aplikaci může být nesprávný. U aplikací IWA zkontrolujte, zda je hlavní název služby nakonfigurovaný pro tuto aplikaci správný. |
| Web nemůže zobrazit stránku. | Uživatel může získat tuto chybu při pokusu o přístup k aplikaci, kterou jste publikovali, pokud je aplikace aplikace OWA aplikace. Příčinou může být jedna z následujících příčin:<br><li>Definovaný název SPN pro tuto aplikaci je nesprávný. Ujistěte se, že hlavní název služby nakonfigurovaný pro tuto aplikaci je správný.</li><li>Uživatel, který se pokusil o přístup k aplikaci, používá účet Microsoft, nikoli správný firemní účet pro přihlášení, nebo je uživatel uživatelem typu Host. Ujistěte se, že se uživatel přihlásí pomocí svého firemního účtu, který odpovídá doméně publikované aplikace. Uživatelé účtu Microsoft a host nemají přístup k aplikacím IWA.</li><li>Uživatel, který se pokusil o přístup k aplikaci, není správně definován pro tuto aplikaci na místní straně. Ujistěte se, že tento uživatel má správná oprávnění, jak je definováno pro tuto back-endovou aplikaci v místním počítači. |
| Tato podniková aplikace není přístupná. Nemáte oprávnění k přístupu k této aplikaci. Autorizace se nezdařila. Ujistěte se, že přiřadit uživatele s přístupem k této aplikaci. | K této chybě může dojít při pokusu o přístup k aplikaci, kterou jste publikovali, pokud k přihlášení používá účty Microsoft namísto svého firemního účtu. Uživatelé typu Host mohou také získat tuto chybu. Uživatelé a hosté účtu Microsoft nemají přístup k aplikacím IWA. Ujistěte se, že se uživatel přihlásí pomocí svého firemního účtu, který odpovídá doméně publikované aplikace.<br><br>Je možné, že jste uživateli nepřiřadili tuto aplikaci. Přejděte na kartu **Aplikace** a v části **Uživatelé a skupiny**přiřaďte této aplikaci tohoto uživatele nebo skupinu uživatelů. |
| Tato podniková aplikace není přístupná právě teď. Zkuste to prosím znovu později... Časový čas konektoru byl vypován. | Uživatel může získat tuto chybu při pokusu o přístup k aplikaci, kterou jste publikovali, pokud nejsou správně definovány pro tuto aplikaci na místní straně. Ujistěte se, že vaši uživatelé mají správná oprávnění, jak je definováno pro tuto back-endovou aplikaci v místním počítači. |
| Tato podniková aplikace není přístupná. Nemáte oprávnění k přístupu k této aplikaci. Autorizace se nezdařila. Ujistěte se, že uživatel má licenci pro Azure Active Directory Premium. | K této chybě může dojít při pokusu o přístup k aplikaci, kterou jste publikovali, pokud mu správce účastníka explicitně nepřiřadil licenci Premium. Přejděte na kartu **Licence služby** Active Directory odběratele a ujistěte se, že tomuto uživateli nebo skupině uživatelů je přiřazena licence Premium. |
| Server se zadaným názvem hostitele nebyl nalezen. | Uživatel může získat tuto chybu při pokusu o přístup k aplikaci, kterou jste publikovali, pokud vlastní doména aplikace není správně nakonfigurována. Ujistěte se, že jste nahráli certifikát pro doménu a správně nakonfigurovali záznam DNS podle kroků v [části Práce s vlastními doménami v proxy aplikaci Azure AD.](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Moje chyba zde nebyla uvedena

Pokud narazíte na chybu nebo problém s proxy aplikací Azure AD, který není uveden v této příručce pro řešení potíží, rádi bychom o tom slyšeli. Pošlete e-mail našemu [týmu pro zpětnou vazbu](mailto:aadapfeedback@microsoft.com) s podrobnostmi o chybě, se kterou jste se setkali.

## <a name="see-also"></a>Viz také
* [Povolení proxy aplikace pro službu Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publikování aplikací pomocí proxy aplikace](application-proxy-add-on-premises-application.md)
* [Povolení jednotného přihlašování](application-proxy-configure-single-sign-on-with-kcd.md)
* [Povolení podmíněného přístupu](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

---
title: Řešení potíží s proxy aplikací | Microsoft Docs
description: Popisuje, jak řešit chyby v Azure Proxy aplikací služby AD.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244222"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Řešení problémů s proxy aplikací a chybové zprávy

Při řešení potíží s proxy aplikací doporučujeme začít s kontrolou toku řešení potíží, [laděním problémů konektoru proxy aplikací](application-proxy-debug-connectors.md)a zjištěním, zda jsou konektory proxy aplikací správně nakonfigurovány. Pokud stále dochází k potížím s připojením k aplikaci, postupujte podle pokynů v části řešení potíží s [aplikací proxy při ladění aplikace](application-proxy-debug-apps.md).

Pokud dojde k chybám při přístupu k publikované aplikaci nebo při publikování aplikací, zkontrolujte následující možnosti, zda Microsoft Azure AD proxy aplikací správně funguje:

* Otevřete konzolu služby systému Windows. Ověřte, že je služba **Microsoft AAD Application proxy Connector** povolená a spuštěná. Můžete se také podívat na stránku vlastnosti služby proxy aplikace, jak je znázorněno na následujícím obrázku:  
  ![Microsoft AAD Application proxy Connector okno Vlastnosti snímku obrazovky](./media/application-proxy-troubleshoot/connectorproperties.png)
* Otevřete Prohlížeč událostí a vyhledejte události konektoru proxy aplikací v **aplikacích a službách protokoluje** > **správce****konektoru** > **Microsoft** > **AadApplicationProxy** > .
* V případě potřeby jsou k dispozici podrobnější protokoly [zapnutím protokolů relace konektoru proxy aplikace](application-proxy-connectors.md#under-the-hood).

## <a name="the-page-is-not-rendered-correctly"></a>Stránka není vykreslena správně
Můžete mít problémy s vykreslováním aplikace nebo bez nutnosti přijímat konkrétní chybové zprávy. Tato situace může nastat, pokud jste publikovali cestu k článku, ale aplikace vyžaduje obsah, který existuje mimo danou cestu.

Například pokud cestu `https://yourapp/app` publikujete, ale aplikace volá obrázky v `https://yourapp/media`, nebudou vykresleny. Ujistěte se, že jste aplikaci publikovali pomocí cesty nejvyšší úrovně, kterou potřebujete pro zahrnutí veškerého relevantního obsahu. V tomto příkladu by to bylo `http://yourapp/`.

Pokud změníte cestu tak, aby zahrnovala odkazovaný obsah, ale přesto potřebují uživatelé, aby si na cestách pochopili hlubší odkaz, přečtěte si téma nastavení blogového příspěvku [s odkazem na aplikace proxy aplikací na přístupovém panelu Azure AD a ve Spouštěči aplikací Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Chyby konektoru

Pokud se registrace nezdaří během instalace Průvodce konektorem, existují dva způsoby, jak zobrazit důvod selhání. Podívejte se do protokolu událostí v části **aplikace a služby Logs\Microsoft\AadApplicationProxy\Connector\Admin**nebo spusťte následující příkaz Windows PowerShellu:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

Po nalezení chyby konektoru z protokolu událostí použijte tuto tabulku běžných chyb k vyřešení problému:

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Nepovedlo se zaregistrovat konektor: Ujistěte se, že jste povolili proxy aplikace v Azure Portál pro správu a jestli jste zadali správné uživatelské jméno a heslo ke službě Active Directory. Chyba: došlo k jedné nebo více chybám. | Pokud jste okno registrace zavřeli bez přihlášení ke službě Azure AD, spusťte znovu Průvodce konektorem a zaregistrujte konektor. <br><br> Pokud se otevře okno registrace a okamžitě se zavře, aniž byste se museli přihlašovat, pravděpodobně se zobrazí tato chyba. K této chybě dojde, pokud je v systému chyba sítě. Ujistěte se, že je možné se připojit z prohlížeče k veřejnému webu a že porty jsou otevřené, jak je uvedeno v části [požadavky proxy aplikací](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| V okně registrace se zobrazí chyba vymazat. Nelze pokračovat | Pokud se zobrazí tato chyba a pak se okno zavře, zadali jste nesprávné uživatelské jméno nebo heslo. Zkuste to znovu. |
| Nepovedlo se zaregistrovat konektor: Ujistěte se, že jste povolili proxy aplikace v Azure Portál pro správu a jestli jste zadali správné uživatelské jméno a heslo ke službě Active Directory. Chyba: ' AADSTS50059: nebyly nalezeny žádné informace identifikující tenanta ani žádné z uvedených přihlašovacích údajů a hledání podle identifikátoru URI instančního objektu. | Snažíte se přihlásit pomocí účtu Microsoft, nikoli domény, která je součástí ID organizace adresáře, ke kterému se pokoušíte získat přístup. Ujistěte se, že je správce součástí stejného názvu domény jako doména klienta, například pokud je doména Azure AD contoso.com, správce by měl být admin@contoso.com. |
| Nepovedlo se načíst aktuální zásady spouštění pro spouštění skriptů PowerShellu. | Pokud se instalace konektoru nezdařila, zkontrolujte, zda nejsou zakázány zásady spouštění prostředí PowerShell. <br><br>1. Otevřete Editor Zásady skupiny.<br>2. přejděte na **Konfigurace** > **počítače šablony pro správu** > **součásti** > systému Windows**PowerShell** a dvakrát klikněte na **zapnout spuštění skriptu**.<br>3. zásady spouštění lze nastavit na hodnotu **Nenakonfigurováno** nebo **povoleno**. Pokud je nastaveno na **povoleno**, ujistěte se, že je v části Možnosti zásada spouštění nastavena možnost **Povolit místní skripty a vzdálené podepsané skripty** nebo **Povolit všechny skripty**. |
| Konektoru se nepodařilo stáhnout konfiguraci. | Certifikát klienta konektoru, který se používá k ověření, vypršel. Tato situace může nastat i v případě, že máte nainstalovaný konektor za proxy serverem. V takovém případě konektor nemá přístup k Internetu a nebude schopen poskytovat aplikace vzdáleným uživatelům. Obnovte důvěryhodnost ručně pomocí `Register-AppProxyConnector` rutiny ve Windows PowerShellu. Pokud je váš konektor za proxy, je nutné udělit přístup k Internetu účtům konektoru "Network Services" a "Local System". To lze provést tak, že jim udělíte přístup k proxy serveru nebo nastavením pro obejít proxy server. |
| Nepovedlo se zaregistrovat konektor: Ujistěte se, že jste správcem aplikace služby Active Directory, abyste mohli konektor zaregistrovat. Chyba: byla zamítnuta žádost o registraci. | Alias, se kterým se snažíte přihlásit, není správce v této doméně. Konektor je vždy nainstalován pro adresář, který je vlastníkem domény uživatele. Ujistěte se, že účet správce, ke kterému se pokoušíte přihlásit, má minimálně oprávnění správce aplikace pro tenanta Azure AD. |
| Konektor se nemohl připojit ke službě kvůli problémům se sítí. Konektor se pokusil o přístup k následující adrese URL. | Konektor se nemůže připojit ke cloudové službě proxy aplikací. K tomu může dojít, pokud máte pravidlo brány firewall blokující připojení. Ujistěte se, že máte povolený přístup ke správným portům a ADRESám URL uvedeným v části [požadavky proxy aplikace](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Chyby protokolu Kerberos

Tato tabulka popisuje nejběžnější chyby, které pocházejí z nastavení a konfigurace protokolu Kerberos, včetně návrhů na řešení.

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Nepovedlo se načíst aktuální zásady spouštění pro spouštění skriptů PowerShellu. | Pokud se instalace konektoru nezdařila, zkontrolujte, zda není zakázána zásada spouštění prostředí PowerShell.<br><br>1. Otevřete Editor Zásady skupiny.<br>2. přejděte na **Konfigurace** > **počítače šablony pro správu** > **součásti** > systému Windows**PowerShell** a dvakrát klikněte na **zapnout spuštění skriptu**.<br>3. zásady spouštění lze nastavit na hodnotu **Nenakonfigurováno** nebo **povoleno**. Pokud je nastaveno na **povoleno**, ujistěte se, že je v části Možnosti zásada spouštění nastavena možnost **Povolit místní skripty a vzdálené podepsané skripty** nebo **Povolit všechny skripty**. |
| 12008 – služba Azure AD překročila maximální počet povolených pokusů o ověření protokolu Kerberos na back-end Server. | Tato chyba může ukazovat na nesprávnou konfiguraci mezi Azure AD a aplikačním serverem back-end nebo na obou počítačích na základě problému s konfigurací data a času. Back-end server odmítl lístek protokolu Kerberos vytvořený službou Azure AD. Ověřte, zda jsou správně nakonfigurovány služby Azure AD a aplikační server back-end. Ujistěte se, že je synchronizovaná konfigurace data a času ve službě Azure AD a na back-end aplikačním serveru. |
| 13016 – Azure AD nemůže za uživatele načíst lístek protokolu Kerberos, protože v tokenu Edge nebo v přístupovém souboru cookie není k dispozici žádný hlavní název uživatele (UPN). | Došlo k potížím s konfigurací služby STS. Opravte konfiguraci deklarací hlavního názvu uživatele (UPN) v STS. |
| 13019 – Azure AD nemůže za uživatele načíst lístek protokolu Kerberos z důvodu následující obecné chyby rozhraní API. | Tato událost může ukazovat na nesprávnou konfiguraci mezi službou Azure AD a serverem řadiče domény nebo na obou počítačích na základě problému s konfigurací data a času. Řadič domény odmítl lístek protokolu Kerberos vytvořený službou Azure AD. Ověřte, jestli jsou správně nakonfigurované služby Azure AD a aplikační server back-end, zejména konfigurace hlavního názvu služby (SPN). Ujistěte se, že je služba Azure AD připojená k doméně se stejnou doménou jako řadič domény, aby se zajistilo, že řadič domény vytvoří vztah důvěryhodnosti se službou Azure AD. Ujistěte se, že je synchronizována konfigurace data a času v Azure AD a řadiči domény. |
| 13020 – služba Azure AD nemůže za uživatele načíst lístek protokolu Kerberos, protože není definován hlavní název služby (SPN) back-end serveru. | Tato událost může ukazovat na nesprávnou konfiguraci mezi službou Azure AD a serverem řadiče domény nebo na obou počítačích na základě problému s konfigurací data a času. Řadič domény odmítl lístek protokolu Kerberos vytvořený službou Azure AD. Ověřte, jestli jsou správně nakonfigurované služby Azure AD a aplikační server back-end, zejména konfigurace hlavního názvu služby (SPN). Ujistěte se, že je služba Azure AD připojená k doméně se stejnou doménou jako řadič domény, aby se zajistilo, že řadič domény vytvoří vztah důvěryhodnosti se službou Azure AD. Ujistěte se, že je synchronizována konfigurace data a času v Azure AD a řadiči domény. |
| 13022 – Azure AD nemůže ověřit uživatele, protože back-end Server reaguje na pokusy o ověření pomocí protokolu Kerberos s chybou HTTP 401. | Tato událost může ukazovat na nesprávnou konfiguraci mezi Azure AD a aplikačním serverem back-end nebo na obou počítačích na základě problému s konfigurací data a času. Back-end server odmítl lístek protokolu Kerberos vytvořený službou Azure AD. Ověřte, zda jsou správně nakonfigurovány služby Azure AD a aplikační server back-end. Ujistěte se, že je synchronizovaná konfigurace data a času ve službě Azure AD a na back-end aplikačním serveru. Další informace najdete v tématu [řešení potíží s konfigurací vynuceného delegování protokolu Kerberos pro proxy aplikace](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Chyby koncového uživatele

Tento seznam obsahuje chyby, se kterými se můžou koncoví uživatelé setkat při pokusu o přístup k aplikaci a selhání. 

| Chyba | Doporučené kroky |
| ----- | ----------------- |
| Web nemůže zobrazit stránku. | K této chybě může uživatel dostat při pokusu o přístup k aplikaci, kterou jste publikovali, pokud je aplikace IWA aplikací. Definovaný hlavní název služby (SPN) pro tuto aplikaci může být nesprávný. U aplikací IWA se ujistěte, že hlavní název služby (SPN) nakonfigurovaný pro tuto aplikaci je správný. |
| Web nemůže zobrazit stránku. | Uživatel se může při pokusu o přístup k aplikaci, kterou jste publikovali, zobrazit tuto chybu, pokud je aplikace aplikace OWA. To může být způsobeno jedním z následujících způsobů:<br><li>Definovaný hlavní název služby (SPN) pro tuto aplikaci není správný. Ujistěte se, že hlavní název služby (SPN) nakonfigurovaný pro tuto aplikaci je správný.</li><li>Uživatel, který se pokusil o přístup k aplikaci, používá účet Microsoft namísto správného podnikového účtu pro přihlášení nebo uživatel je uživatel typu Host. Ujistěte se, že se uživatel přihlásí pomocí podnikového účtu, který odpovídá doméně publikované aplikace. Uživatelé účtu Microsoft a hosta nemají přístup k IWA aplikacím.</li><li>Uživatel, který se pokusil o přístup k aplikaci, není pro tuto aplikaci na místní straně správně definován. Ujistěte se, že tento uživatel má správná oprávnění definovaná pro tuto back-end aplikaci na místním počítači. |
| Tato podniková aplikace není dostupná. Nemáte oprávnění pro přístup k této aplikaci. Autorizace se nezdařila. Ujistěte se, že uživateli přiřadíte přístup k této aplikaci. | Při pokusu o přístup k aplikaci, kterou jste publikovali při použití účtů Microsoft namísto jejich podnikového účtu, se může zobrazit tato chyba. Tato chyba se může zobrazit také uživatelům typu Host. Uživatelé účtu Microsoft a hosté nemají přístup k aplikacím IWA. Ujistěte se, že se uživatel přihlásí pomocí podnikového účtu, který odpovídá doméně publikované aplikace.<br><br>Pravděpodobně jste nepřiřadili uživatele pro tuto aplikaci. Použijte kartu **aplikace** a v části **Uživatelé a skupiny**přiřaďte tohoto uživatele nebo skupinu uživatelů k této aplikaci. |
| Tato podniková aplikace se teď nedá použít hned. Zkuste to prosím znovu později... Vypršel časový limit konektoru. | Při pokusu o přístup k aplikaci, kterou jste publikovali, se tato chyba může zobrazit, pokud nejsou správně definované pro tuto aplikaci na místní straně. Ujistěte se, že uživatelé mají správná oprávnění definovaná pro tuto back-end aplikaci na místním počítači. |
| Tato podniková aplikace není dostupná. Nemáte oprávnění pro přístup k této aplikaci. Autorizace se nezdařila. Ujistěte se, že uživatel má licenci pro Azure Active Directory Premium. | K této chybě může uživatel získat přístup při pokusu o přístup k aplikaci, kterou jste publikovali, pokud správce předplatitele nepřiřadil explicitně licenci na prémii. Klikněte na kartu **licence** pro předplatitele služby Active Directory a ujistěte se, že je tomuto uživateli nebo skupině uživatelů přiřazena licence Premium. |
| Server se zadaným názvem hostitele se nepovedlo najít. | Při pokusu o přístup k aplikaci, kterou jste publikovali v případě, že vlastní doména aplikace není správně nakonfigurovaná, může se uživateli zobrazit tato chyba. Postupujte podle kroků v části [práce s vlastními doménami v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md) Ujistěte se, že jste nahráli certifikát pro doménu a správně nakonfigurovali záznam DNS. |

## <a name="my-error-wasnt-listed-here"></a>Zde uvedená chyba není.

Pokud narazíte na chybu nebo problém s Proxy aplikací služby AD Azure, která není uvedená v tomto průvodci odstraňováním potíží, rádi bychom o tom dozvěděli. Pošlete nám e-mail do našeho [týmu Feedback](mailto:aadapfeedback@microsoft.com) s podrobnostmi o zjištěné chybě.

## <a name="see-also"></a>Viz také
* [Povolit proxy aplikací pro Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publikování aplikací pomocí proxy aplikace](application-proxy-add-on-premises-application.md)
* [Povolit jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md)
* [Povolit podmíněný přístup](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png

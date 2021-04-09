---
title: Samoobslužné resetování hesla s hloubkou podrobně-Azure Active Directory
description: Jak funguje Samoobslužné resetování hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 496a8dba9b9ea7fb82ad9016479154d6a61cb767
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98703271"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to funguje: Samoobslužné resetování hesla v Azure AD

Samoobslužné resetování hesla (SSPR) Azure Active Directory (Azure AD) umožňuje uživatelům změnit nebo resetovat svoje heslo bez zásahu správce ani helpdesku. Pokud je účet uživatele uzamčený nebo zapomněl heslo, může postupovat podle výzev ke zrušení odblokování a opětovnému získání práce. Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci.

> [!IMPORTANT]
> Tento koncepční článek vysvětluje správce, jak funguje Samoobslužné resetování hesla. Pokud už jste koncoví uživatelé zaregistrovali pro Samoobslužné resetování hesla a potřebujete se zpátky do svého účtu, pokračujte na [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Pokud váš IT tým nepovolil možnost resetovat si vlastní heslo, obraťte se na helpdesk a získáte další pomoc.

## <a name="how-does-the-password-reset-process-work"></a>Jak proces resetování hesla funguje?

Uživatel může resetovat nebo změnit heslo pomocí [portálu SSPR](https://aka.ms/sspr). Musí nejdřív zaregistrovat požadované metody ověřování. Když uživatel přistupuje k portálu SSPR, platforma Azure bere v úvahu následující faktory:

* Jak má být stránka lokalizována?
* Je uživatelský účet platný?
* Jakou organizaci uživatel patří?
* Kde je spravované heslo uživatele?
* Má uživatel licenci používat tuto funkci?

Když uživatel vybere odkaz bez **přístupu k účtu** z aplikace nebo stránky nebo přejde přímo na [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) , jazyk použitý na portálu SSPR je založený na následujících možnostech:

* Ve výchozím nastavení se k zobrazení SSPR v příslušném jazyku používá národní prostředí prohlížeče. Prostředí pro resetování hesla je lokalizované do stejného jazyka, který [Microsoft 365 podporuje](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Pokud chcete odkazovat na SSPR v konkrétním lokalizovaném jazyce, připojte se `?mkt=` ke konci adresy URL pro resetování hesla spolu s požadovaným národním prostředím.
    * Pokud například chcete zadat španělský národní prostředí *ES-US* , použijte `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Po zobrazení portálu SSPR v požadovaném jazyce se uživateli zobrazí výzva k zadání ID uživatele a předání CAPTCHA. Azure AD teď ověřuje, že uživatel může používat SSPR, a to provedením následujících kontrol:

* Kontroluje, jestli má uživatel povolený SSPR a má přiřazenou licenci Azure AD.
  * Pokud uživatel není povolený pro SSPR nebo nemá přiřazenou licenci, uživatel se vyzve, aby se obrátil na správce, aby resetoval heslo.
* Kontroluje, zda má uživatel správné metody ověřování definované na svém účtu v souladu se zásadami správce.
  * Pokud zásada vyžaduje jenom jednu metodu, ověřte, že má uživatel definovaná příslušná data alespoň pro jednu z metod ověřování povolených zásadami správce.
    * Pokud metody ověřování nejsou nakonfigurované, uživatel se doporučuje požádat správce, aby heslo resetoval.
  * Pokud zásada vyžaduje dvě metody, ověřte, že má uživatel definovaná příslušná data pro alespoň dvě metody ověřování povolené zásadami správce.
    * Pokud metody ověřování nejsou nakonfigurované, uživatel se doporučuje požádat správce, aby heslo resetoval.
  * Pokud se uživateli přiřadí role správce Azure, vynutily se zásady silného hesla dvou bran. Další informace najdete v tématu [rozdíly v zásadách resetování správců](concept-sspr-policy.md#administrator-reset-policy-differences).
* Zkontroluje, jestli je uživatelské heslo spravované místně, například pokud tenant Azure AD používá federované, předávací ověřování nebo synchronizaci hodnot hash hesel:
  * Pokud je nakonfigurován zpětný zápis SSPR a heslo uživatele je spravováno místně, uživatel může pokračovat v ověřování a resetování hesla.
  * Pokud není zpětný zápis SSPR nasazený a heslo uživatele je spravované místně, uživateli se zobrazí výzva, aby se obrátil na správce, aby resetoval heslo.

Pokud se všechny předchozí kontroly úspěšně dokončí, uživatel se provede procesem resetování nebo změnou hesla.

> [!NOTE]
> SSPR může posílat e-mailová oznámení uživatelům v rámci procesu resetování hesla. Tyto e-maily se odesílají pomocí předávací služby SMTP, která funguje v režimu aktivní-aktivní napříč několika oblastmi.
>
> Služba SMTP relay přijímá a zpracovává tělo e-mailu, ale neukládá ji. Tělo SSPR e-mailu, které může potenciálně obsahovat informace poskytnuté zákazníkem, není uloženo v protokolech služby SMTP relay. Protokoly obsahují pouze metadata protokolu.

Pokud chcete začít pracovat s SSPR, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: povolení samoobslužného resetování hesla (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Vyžadovat, aby se uživatelé zaregistrovali při přihlášení

Můžete povolit, aby uživatel po přihlášení k aplikacím pomocí Azure AD dokončil registraci SSPR. Tento pracovní postup obsahuje následující aplikace:

* Microsoft 365
* portál Azure
* Přístupový panel
* Federované aplikace
* Vlastní aplikace s využitím Azure AD

Pokud nepotřebujete registraci, nebudou se uživatelé během přihlašování vyzváni, ale můžou se zaregistrovat ručně. Uživatelé můžou na [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) přístupovém panelu přejít nebo vybrat odkaz **Registrace pro resetování hesla** na kartě **profil** .

![Registrační možnosti pro SSPR v Azure Portal][Registration]

> [!NOTE]
> Uživatelé můžou zavřít portál pro registraci SSPR tak, že vyberete **Zrušit** nebo zavřít okno. Jsou ale vyzváné k registraci pokaždé, když se přihlásí, dokud nedokončí jejich registraci.
>
> Toto přerušení, které se má zaregistrovat pro SSPR, neruší připojení uživatele, pokud už se přihlásilo.

## <a name="reconfirm-authentication-information"></a>Znovu potvrdit ověřovací informace

Aby bylo zajištěno, že metody ověřování jsou správné, když jsou potřeba k resetování nebo změně hesla, můžete vyžadovat, aby uživatelé po uplynutí určité doby ověřili informace zaregistrované v informacích. Tato možnost je k dispozici pouze v případě, že povolíte možnost **vyžadovat registraci uživatelů při přihlášení** .

Platné hodnoty pro vyzvání uživatele k potvrzení registrovaných metod jsou od *0* do *730* dnů. Nastavením hodnoty *0* znamená, že uživatelé nebudou nikdy požádáni o potvrzení ověřovacích informací.

## <a name="authentication-methods"></a>Metody ověřování

Když je uživatel povolený pro SSPR, musí zaregistrovat aspoň jednu metodu ověřování. Důrazně doporučujeme, abyste vybrali dvě nebo více metod ověřování, aby uživatelé měli větší flexibilitu v případě, že k jedné metodě nezískají přístup, když ji potřebují. Další informace najdete v tématu [co jsou metody ověřování?](concept-authentication-methods.md).

Pro SSPR jsou k dispozici následující metody ověřování:

* Oznámení v mobilní aplikaci
* Kód mobilní aplikace
* E-mail
* Mobilní telefon
* Telefon do kanceláře
* Bezpečnostní otázky

Uživatelé mohou resetovat heslo pouze v případě, že zaregistrovali metodu ověřování, kterou správce povolil.

> [!WARNING]
> Účty přiřazené rolím *správce* Azure se vyžadují k použití metod definovaných v části [Správce zásady resetování zásad](concept-sspr-policy.md#administrator-reset-policy-differences).

![Výběr metod ověřování v Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Vyžaduje se počet požadovaných metod ověřování.

Můžete nakonfigurovat počet dostupných metod ověřování, které uživatel musí zadat pro resetování nebo odemknutí hesla. Tuto hodnotu můžete nastavit buď na *jednu* , nebo na *dvě*.

Uživatelé mohou a by měli registrovat více metod ověřování. Opět doporučujeme, aby uživatelé zaregistrovali dvě nebo více metod ověřování, aby měli větší flexibilitu v případě, že k jedné metodě nezískají přístup, když ji potřebují.

Pokud uživatel nemá při pokusu o použití SSPR zaregistrovaný minimální počet požadovaných metod, zobrazí se mu chybová stránka, která je směruje, aby požádala správce o resetování hesla. Pokud jste pro SSPR zaregistrovali existující uživatele a nemůžete použít tuto funkci, pečlivě se ujistěte, že zvýšíte počet metod vyžadovaných od jedné až dvou. Další informace naleznete v následující části ke [změně metod ověřování](#change-authentication-methods).

#### <a name="mobile-app-and-sspr"></a>Mobilní aplikace a SSPR

Při použití mobilní aplikace jako metody pro resetování hesla, jako je například aplikace Microsoft Authenticator, platí následující požadavky:

* Když správci vyžadují, aby se k resetování hesla použila jedna metoda, je jako jediná dostupná možnost ověřovací kód.
* Když správci použijí k resetování hesla dvě metody, uživatelé budou moci kromě jakýchkoli dalších povolených metod použít také oznámení **nebo** ověřovací kód.

| Počet metod požadovaných k resetování | Jednu | Dvě |
| :---: | :---: | :---: |
| Dostupné funkce mobilní aplikace | Kód | Kód nebo oznámení |

Uživatelé nemají možnost registrovat svou mobilní aplikaci při registraci pro Samoobslužné resetování hesla [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Uživatelé mohou zaregistrovat svou mobilní aplikaci na [https://aka.ms/mfasetup](https://aka.ms/mfasetup) nebo v rámci registrace souhrnných bezpečnostních údajů na adrese [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> Ověřovací aplikaci nelze vybrat jako jedinou metodu ověřování pouze v případě, že je požadována pouze jedna metoda. Podobně platí, že při vyžadování dvou metod nelze vybrat aplikaci ověřovatele a pouze jednu další metodu.
>
> Při konfiguraci zásad SSPR, které obsahují ověřovací aplikaci jako metodu, by měla být vybraná aspoň jedna další metoda, když je potřeba jedna metoda a při konfiguraci dvou metod je potřeba vybrat aspoň dvě další metody.
>
> Důvodem je, že aktuální prostředí pro registraci SSPR neobsahuje možnost Registrovat ověřovací aplikaci. Možnost registrace ověřovací aplikace je součástí nového [kombinovaného prostředí pro registraci](./concept-registration-mfa-sspr-combined.md).
>
> Povolení zásad, které používají pouze aplikaci ověřovatele (když je vyžadována jedna metoda) nebo ověřovací aplikace a pouze jedna další metoda (když jsou požadovány dvě metody), by mohlo vést k tomu, že uživatelé budou zablokováni v registraci pro SSPR, dokud nejsou nakonfigurováni tak, aby používali nové kombinované možnosti registrace.

### <a name="change-authentication-methods"></a>Změna metod ověřování

Pokud začnete se zásadou, která má jenom jednu požadovanou metodu ověřování pro resetování nebo odemknutí, a změníte ji na dvě metody, co se stane?

| Počet registrovaných metod | Vyžaduje se počet metod. | Výsledek |
| :---: | :---: | :---: |
| 1 nebo více | 1 | **Může** resetovat nebo odemknout |
| 1 | 2 | **Nejde** resetovat ani odemknout |
| 2 nebo více | 2 | **Může** resetovat nebo odemknout |

Změna dostupných metod ověřování může také způsobit problémy pro uživatele. Pokud změníte typy metod ověřování, které může uživatel použít, může nechtěně zabránit uživatelům, aby mohli používat SSPR, pokud nemají k dispozici minimální množství dat.

Vezměte v úvahu následující vzorový scénář:

1. Původní zásada je nakonfigurovaná se dvěma požadovanými metodami ověřování. Používá jenom telefonní číslo do kanceláře a bezpečnostní otázky.
1. Správce změní zásadu tak, aby už nepoužívala bezpečnostní otázky, ale umožňuje použití mobilního telefonu a alternativního e-mailu.
1. Uživatelé bez naplnění polí mobilní telefon nebo alternativní e-mailové adresy teď nemůžou resetovat hesla.

## <a name="notifications"></a>Oznámení

Pro zlepšení povědomí o událostech hesla vám SSPR umožňuje nakonfigurovat oznámení pro uživatele i správce identit.

### <a name="notify-users-on-password-resets"></a>Upozornit uživatele na resetování hesla

Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé, kteří resetují heslo, obdrží e-mail s upozorněním, že jejich heslo bylo změněno. E-mail se pošle prostřednictvím portálu SSPR do svých primárních a alternativních e-mailových adres, které jsou uložené v Azure AD. Nikdo jiný není upozorněn na událost resetování.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Oznamovat všem správcům, kteří resetují hesla jiní správci

Pokud je tato možnost nastavená na **hodnotu Ano**, všechny ostatní správce Azure obdrží e-mail na svou primární e-mailovou adresu uloženou v Azure AD. E-mail je upozorní, že jiný správce změnil heslo pomocí SSPR.

Vezměte v úvahu následující vzorový scénář:

* Existují čtyři správci v prostředí.
* Správce *A* resetuje heslo pomocí SSPR.
* Správci *B*, *C* a *D* obdrží e-mail s upozorněním na resetování hesla.

## <a name="on-premises-integration"></a>Místní integrace

Pokud máte hybridní prostředí, můžete nakonfigurovat Azure AD Connect pro zápis událostí změny hesla zpět z Azure AD do místního adresáře.

![Ověřování zpětného zápisu hesla je povolené a funguje.][Writeback]

Azure AD kontroluje vaše aktuální hybridní připojení a poskytuje jednu z následujících zpráv v Azure Portal:

* Váš místní klient zpětného zápisu je spuštěný.
* Služba Azure AD je online a je připojená k vašemu místnímu klientovi zpětného zápisu. Vypadá to ale, že nainstalovaná verze Azure AD Connect je zastaralá. Zvažte [upgrade Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , abyste měli jistotu, že máte nejnovější funkce připojení a důležité opravy chyb.
* Bohužel nemůžeme kontrolovat stav místního klienta zpětného zápisu, protože nainstalovaná verze Azure AD Connect je zastaralá. [Upgradujte Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , abyste mohli kontrolovat stav připojení.
* Bohužel to vypadá, že se teď nemůžeme připojit k místnímu klientovi zpětného zápisu. [Řešení potíží s Azure AD Connect](./troubleshoot-sspr-writeback.md) k obnovení připojení.
* Bohužel se nemůžeme připojit k vašemu místnímu klientovi zpětného zápisu, protože zpětný zápis hesla není správně nakonfigurovaný. [Nakonfigurujte zpětný zápis hesla](./tutorial-enable-sspr-writeback.md) pro obnovení připojení.
* Bohužel to vypadá, že se teď nemůžeme připojit k místnímu klientovi zpětného zápisu. To může být způsobeno dočasnými problémy na našem konci. Pokud potíže potrvají, [vyřešte potíže s Azure AD Connect](./troubleshoot-sspr-writeback.md) k obnovení připojení.

Pokud chcete začít se zpětným zápisem SSPR, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: povolení zpětného zápisu pro Samoobslužné resetování hesla (SSPR)](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zápis hesel zpátky do místního adresáře

Zpětný zápis hesla můžete povolit pomocí Azure Portal. Můžete také dočasně zakázat zpětný zápis hesla, aniž byste museli Azure AD Connect znovu nakonfigurovat.

* Pokud je tato možnost nastavená na **hodnotu Ano**, je zpětný zápis povolen. Federované, předávací ověřování nebo hesla pro synchronizaci hodnot hash hesel můžou resetovat hesla uživatelů.
* Pokud je tato možnost nastavená na **ne**, zpětný zápis se zakáže. Federované, předávací ověřování nebo synchronizace hodnot hash hesel uživatele nemůžou resetovat hesla.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Povolení odemknutí účtů uživateli bez resetování hesla

Ve výchozím nastavení Azure AD odemkne účty, když provede resetování hesla. Pro zajištění flexibility můžete zvolit, že chcete uživatelům umožnit odemknutí místních účtů, aniž byste museli resetovat heslo. Pomocí tohoto nastavení můžete tyto dvě operace oddělit.

* Pokud je nastaveno na **Ano**, uživatelé budou mít možnost resetovat heslo a odemknout účet nebo odemknout účet, aniž by museli resetovat heslo.
* Pokud je nastavená na **ne**, uživatelé můžou provádět jenom kombinované resetování hesla a operace odemknutí účtu.

### <a name="on-premises-active-directory-password-filters"></a>Filtry hesel pro místní službu Active Directory

SSPR provádí ekvivalent resetování hesla iniciované správcem ve službě Active Directory. Pokud použijete filtr hesel třetí strany k vynucení vlastních pravidel hesla a požadujete, aby se tento filtr hesel kontroloval během samoobslužného resetování hesla služby Azure AD, ujistěte se, že řešení filtru hesel třetí strany je nakonfigurované tak, aby se použilo ve scénáři resetování hesla správce. Ve výchozím nastavení je [ochrana heslem Azure AD pro Active Directory Domain Services](concept-password-ban-bad-on-premises.md) podporovaná.

## <a name="password-reset-for-b2b-users"></a>Resetování hesla pro uživatele B2B

Resetování a změna hesla jsou plně podporované ve všech konfiguracích B2B (Business-to-Business). Resetování hesla uživatele B2B je podporované v následujících třech případech:

* **Uživatelé z partnerské organizace s existujícím klientem Azure AD**: Pokud organizace, se kterou jste partnerem, má stávajícího TENANTA Azure AD, zaplatíme, že v tomto tenantovi jsou povolené zásady pro resetování hesla. Aby se resetování hesla fungovalo, partnerská organizace potřebuje jenom jistotu, že je povolená služba Azure AD SSPR. Za Microsoft 365 zákazníky se neúčtují žádné další poplatky.
* **Uživatelé, kteří se přihlásí prostřednictvím** samoobslužné registrace: Pokud je organizace, které jste partner použili k tomu, aby se do tenanta dostali pomocí [samoobslužné](../enterprise-users/directory-self-service-signup.md) funkce pro registraci, umožníme jim resetovat heslo pomocí e-mailu, který zaregistrovali.
* **Uživatelé B2B**: Všichni noví uživatelé B2B, kteří vytvořili pomocí nových [možností Azure AD B2B](../external-identities/what-is-b2b.md) , můžou resetovat hesla také pomocí e-mailu, který zaregistrovali během procesu pozvání.

Pokud chcete tento scénář vyzkoušet, přečtěte si https://passwordreset.microsoftonline.com některý z těchto partnerských uživatelů. Pokud mají definovaný alternativní e-mail nebo ověřovací e-mail, resetování hesla funguje podle očekávání.

> [!NOTE]
> Účtům Microsoft, kterým byl udělen přístup hosta k vašemu tenantovi služby Azure AD, jako jsou například Hotmail.com, Outlook.com nebo jiné osobní e-mailové adresy, nemůžou využívat Azure AD SSPR. Je potřeba resetovat heslo pomocí informací, které najdete v části [když se nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít pracovat s SSPR, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: povolení samoobslužného resetování hesla (SSPR)](tutorial-enable-sspr.md)

V následujících článcích najdete další informace o resetování hesla prostřednictvím Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Dostupné metody ověřování Azure AD a požadované množství"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Konfigurace možností registrace SSPR v Azure Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Místní integrace pro SSPR v Azure Portal"

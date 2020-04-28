---
title: Samoobslužné resetování hesla s hloubkou podrobně-Azure Active Directory
description: Jak funguje Samoobslužné resetování hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b19c80378aa40a7f791a3eb61130b013217ddee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74848574"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to funguje: Samoobslužné resetování hesla služby Azure AD

Jak funguje Samoobslužné resetování hesla (SSPR)? Co tato možnost znamená v rozhraní? Pokud chcete získat další informace o Azure Active Directory (Azure AD) SSPR, pokračujte ve čtení.

## <a name="how-does-the-password-reset-portal-work"></a>Jak funguje portál pro resetování hesla?

Když uživatel přejde na portál pro resetování hesla, spustí se pracovní postup, který určí:

   * Jak má být stránka lokalizována?
   * Je uživatelský účet platný?
   * Jakou organizaci uživatel patří?
   * Kde je spravované heslo uživatele?
   * Má uživatel licenci používat tuto funkci?

Další informace o logice za stránku pro resetování hesla najdete v následujících krocích:

1. Uživatel vybere **nepřístup k vašemu účtu** nebo přímo do [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)něj nepřejde.
   * V závislosti na národním prostředí prohlížeče se prostředí vykreslí v příslušném jazyce. Prostředí pro resetování hesla je lokalizováno do stejného jazyka, který podporuje sada Office 365.
   * Pokud chcete zobrazit portál pro resetování hesla v jiném lokalizovaném jazyce, přidejte na konec adresy URL pro resetování hesla "? MKT =", který následuje po lokalizaci [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)do španělštiny.
2. Uživatel zadá ID uživatele a předá CAPTCHA.
3. Azure AD ověřuje, že uživatel může tuto funkci používat, pomocí následujících kontrol:
   * Kontroluje, jestli má uživatel povolenou tuto funkci a má přiřazenou licenci Azure AD.
     * Pokud uživatel nemá povolenou tuto funkci nebo má přiřazenou licenci, uživateli se zobrazí výzva, aby se obrátil na správce, aby resetoval heslo.
   * Kontroluje, zda má uživatel správné metody ověřování definované na svém účtu v souladu se zásadami správce.
     * Pokud zásada vyžaduje jenom jednu metodu, zajistí, aby měl uživatel příslušná data definovaná pro aspoň jednu z metod ověřování povolených zásadami správce.
       * Pokud nejsou metody ověřování nakonfigurované, doporučuje se uživateli kontaktovat správce, aby heslo resetoval.
     * Pokud zásada vyžaduje dvě metody, zajistí, aby měl uživatel příslušná data definovaná pro alespoň dvě metody ověřování povolené zásadami správce.
       * Pokud nejsou metody ověřování nakonfigurované, doporučuje se uživateli kontaktovat správce, aby heslo resetoval.
     * Pokud se uživateli přiřadí role správce Azure, vynutily se zásady silného hesla dvou bran. Další informace o této zásadě najdete v části [správce – rozdíly v zásadách resetování zásad](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Zkontroluje, jestli je uživatelské heslo spravované místně (federované, předávací ověřování nebo hodnota hash hesla se synchronizuje).
     * Pokud je nasazen zpětný zápis a heslo uživatele je spravováno místně, uživatel může pokračovat v ověřování a resetování hesla.
     * Pokud není zpětný zápis nasazený a heslo uživatele je spravováno místně, bude uživatel vyzván, aby se obrátil na správce, aby resetoval heslo.
4. Je-li zjištěno, že uživatel může úspěšně obnovit heslo, bude uživatel proveden procesem obnovení.

## <a name="authentication-methods"></a>Metody ověřování

Pokud je povolená možnost SSPR, musíte pro metody ověřování vybrat alespoň jednu z následujících možností. Někdy uslyšíte tyto možnosti označované jako "brány". Důrazně doporučujeme **vybrat dvě nebo více metod ověřování** , aby uživatelé měli větší flexibilitu v případě, že k nim nebudou mít přístup, když ji potřebují. Další podrobnosti o níže uvedených metodách najdete v článku [co jsou metody ověřování?](concept-authentication-methods.md).

* Oznámení mobilní aplikace
* Kód mobilní aplikace
* E-mailu
* Mobilní telefon
* Telefon do kanceláře
* Bezpečnostní otázky

Uživatelé můžou resetovat heslo jenom v případě, že mají data přítomná v metodách ověřování, které správce povolil.

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru k dispozici pro MFA a SSPR uživatele v bezplatných nebo zkušebních klientech Azure AD. Tato změna nemá vliv na zprávy SMS. Telefonní hovor bude dál k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv jenom na bezplatné nebo zkušební klienty Azure AD.

> [!WARNING]
> Účtům přiřazené role správce Azure se budou vyžadovat použití metod definovaných v části [Správce zásady resetování zásad](concept-sspr-policy.md#administrator-reset-policy-differences).

![Výběr metod ověřování v Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Vyžaduje se počet požadovaných metod ověřování.

Tato možnost určuje minimální počet dostupných metod ověřování nebo brány, které musí uživatel projít, aby bylo možné resetování nebo odemknutí hesla. Dá se nastavit buď na jednu, nebo na dvě.

Pokud správce povolí tuto metodu ověřování, může se rozhodnout, že dodáte další metody ověřování.

Pokud uživatel nemá registrovány minimální požadované metody, uvidí chybovou stránku, která je směruje, aby požádala správce o resetování hesla.

#### <a name="mobile-app-and-sspr"></a>Mobilní aplikace a SSPR

Při použití mobilní aplikace, jako je například aplikace Microsoft Authenticator, jako metodu pro resetování hesla, byste měli mít na paměti následující upozornění:

* Když správci vyžadují, aby se k resetování hesla použila jedna metoda, je jako jediná dostupná možnost ověřovací kód.
* Když správci použijí k resetování hesla dvě metody, uživatelé můžou použít **buď** oznámení, **nebo** ověřovací kód kromě jakýchkoli dalších povolených metod.

| Počet metod požadovaných k resetování | Jeden | Dva |
| :---: | :---: | :---: |
| Dostupné funkce mobilní aplikace | kód | Kód nebo oznámení |

Uživatelé nemají možnost registrovat svou mobilní aplikaci při registraci pro Samoobslužné resetování hesla ze [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)služby. Uživatelé můžou svou mobilní aplikaci [https://aka.ms/mfasetup](https://aka.ms/mfasetup)zaregistrovat v nebo ve verzi Preview nové registrace bezpečnostních údajů na adrese [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Než budou mít uživatelé přístup k novému prostředí v [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo), musíte povolit [sblíženou registraci pro Samoobslužné resetování hesla a službu Azure Multi-Factor Authentication (Public Preview)](concept-registration-mfa-sspr-converged.md) .

> [!IMPORTANT]
> Ověřovací aplikaci nelze vybrat jako jedinou metodu ověřování při konfiguraci zásady s 1 bránou. Podobně platí, že při konfiguraci zásad 2 bran nelze vybrat aplikaci ověřovatele a pouze jednu další metodu.
> Při konfiguraci zásad SSPR, které jako metodu obsahují ověřovací aplikaci, je třeba při konfiguraci zásad s jedním bránou vybrat aspoň další metodu a při konfiguraci zásady pro 2 brány by se měly vybrat aspoň dvě další metody.
> Důvodem tohoto požadavku je skutečnost, že aktuální prostředí pro registraci SSPR neobsahuje možnost Registrovat ověřovací aplikaci. Možnost registrace ověřovací aplikace je součástí nové [sblížené registrace pro Samoobslužné resetování hesla a Azure Multi-Factor Authentication (Public Preview)](concept-registration-mfa-sspr-converged.md).
> Když povolíte zásady, které budou používat jenom aplikaci ověřovatele (u zásad pro 1 bránu), nebo aplikaci ověřovatele a jenom jednu další metodu (pro zásady 2-brány), může dojít k tomu, že se uživatelům zablokují registraci pro SSPR, dokud nebudou nakonfigurované pro použití nového prostředí pro registraci.

### <a name="change-authentication-methods"></a>Změna metod ověřování

Pokud začnete se zásadou, která má jenom jednu požadovanou metodu ověřování pro resetování nebo odemknutí, a změníte ji na dvě metody, co se stane?

| Počet registrovaných metod | Vyžaduje se počet metod. | Výsledek |
| :---: | :---: | :---: |
| 1 nebo více | 1 | **Může** resetovat nebo odemknout |
| 1 | 2 | **Nejde** resetovat ani odemknout |
| 2 nebo více | 2 | **Může** resetovat nebo odemknout |

Pokud změníte typy metod ověřování, které může uživatel použít, může nechtěně zabránit uživatelům, aby mohli používat SSPR, pokud nemají k dispozici minimální množství dat.

Příklad:
1. Původní zásada je nakonfigurovaná se dvěma požadovanými metodami ověřování. Používá jenom telefonní číslo do kanceláře a bezpečnostní otázky. 
2. Správce změní zásadu tak, aby už nepoužívala bezpečnostní otázky, ale umožňuje použití mobilního telefonu a alternativního e-mailu.
3. Uživatelé bez naplnění polí mobilní telefon nebo alternativní e-mailová pole nemůžou resetovat hesla.

## <a name="registration"></a>Registrace

### <a name="require-users-to-register-when-they-sign-in"></a>Vyžadovat, aby se uživatelé zaregistrovali při přihlášení

Povolení této možnosti vyžaduje, aby uživatel dokončil registraci resetování hesla, pokud se přihlásí k aplikacím pomocí Azure AD. Tento pracovní postup obsahuje následující aplikace:

* Office 365
* portál Azure
* Přístupový panel
* Federované aplikace
* Vlastní aplikace s využitím Azure AD

Pokud je vyžadováno registraci zakázané, uživatelé se můžou zaregistrovat ručně. Můžou na přístupovém [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) panelu buď navštívit, nebo vybrat odkaz **zaregistrovat k resetování hesla** na kartě **profil** .

> [!NOTE]
> Uživatelé můžou zavřít portál pro registraci resetování hesel tak, že vyberete **Zrušit** nebo zavřít okno. Jsou ale vyzvání k registraci pokaždé, když se přihlásí, dokud nedokončí jejich registraci.
>
> Toto přerušení neruší připojení uživatele, pokud jsou již přihlášena.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Nastavte počet dní, než se uživatelům zobrazí výzva k potvrzení ověřovacích informací.

Tato možnost určuje dobu mezi nastavením a opětovným potvrzením ověřovacích informací a je k dispozici pouze v případě, že povolíte možnost **vyžadovat registraci uživatelů při přihlášení** .

Platné hodnoty jsou 0 až 730 dnů, přičemž "0" znamená, že uživatelé nebudou nikdy požádáni o potvrzení ověřovacích informací.

## <a name="notifications"></a>Oznámení

### <a name="notify-users-on-password-resets"></a>Upozornit uživatele na resetování hesla

Pokud je tato možnost nastavená na **hodnotu Ano**, uživatelé, kteří resetují heslo, obdrží e-mail s upozorněním, že jejich heslo bylo změněno. E-mail se pošle prostřednictvím portálu SSPR do svých primárních a alternativních e-mailových adres, které jsou v souboru v Azure AD. Nikdo jiný není upozorněn na událost resetování.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Oznamovat všem správcům, kteří resetují hesla jiní správci

Pokud je tato možnost nastavená na **hodnotu Ano**, *všichni správci* obdrží e-mail na svou primární e-mailovou adresu v souboru ve službě Azure AD. E-mail je upozorní, že jiný správce změnil heslo pomocí SSPR.

Příklad: v prostředí jsou čtyři správci. Správce A resetuje heslo pomocí SSPR. Správci B, C a D obdrží e-mail s upozorněním na resetování hesla.

## <a name="on-premises-integration"></a>Místní integrace

Pokud instalujete, konfigurujete a povolíte Azure AD Connect, máte k dispozici následující další možnosti pro místní integraci. Pokud jsou tyto možnosti šedé, zpětný zápis nebyl správně nakonfigurován. Další informace najdete v tématu [Konfigurace zpětného zápisu hesla](howto-sspr-writeback.md).

![Ověřování zpětného zápisu hesla je povolené a funguje.][Writeback]

Na této stránce najdete rychlý stav místního klienta zpětného zápisu. na základě aktuální konfigurace se zobrazí jedna z následujících zpráv:

* Váš místní klient zpětného zápisu je spuštěný.
* Služba Azure AD je online a je připojená k vašemu místnímu klientovi zpětného zápisu. Vypadá to ale, že nainstalovaná verze Azure AD Connect je zastaralá. Zvažte [upgrade Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , abyste měli jistotu, že máte nejnovější funkce připojení a důležité opravy chyb.
* Bohužel nemůžeme kontrolovat stav místního klienta zpětného zápisu, protože nainstalovaná verze Azure AD Connect je zastaralá. [Upgradujte Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) , abyste mohli kontrolovat stav připojení.
* Bohužel to vypadá, že se teď nemůžeme připojit k místnímu klientovi zpětného zápisu. [Řešení potíží s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) k obnovení připojení.
* Bohužel se nemůžeme připojit k vašemu místnímu klientovi zpětného zápisu, protože zpětný zápis hesla není správně nakonfigurovaný. [Nakonfigurujte zpětný zápis hesla](howto-sspr-writeback.md) pro obnovení připojení.
* Bohužel to vypadá, že se teď nemůžeme připojit k místnímu klientovi zpětného zápisu. To může být způsobeno dočasnými problémy na našem konci. Pokud potíže potrvají, [vyřešte potíže s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) k obnovení připojení.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zápis hesel zpátky do místního adresáře

Tento ovládací prvek určuje, zda je pro tento adresář povolen zpětný zápis hesla. Pokud je zpětný zápis zapnutý, indikuje stav služby pro místní zpětný zápis. Tento ovládací prvek je užitečný, pokud chcete dočasně zakázat zpětný zápis hesla, aniž byste museli Azure AD Connect znovu nakonfigurovat.

* Pokud je přepínač nastavený na **hodnotu Ano**, pak je povolen zpětný zápis a federované, předávací ověřování nebo synchronizace hodnot hash hesel pomocí algoritmu hash hesla mohou resetovat hesla.
* Pokud je přepínač nastavený na **ne**, pak se zpětný zápis zakáže a uživatelé synchronizace hodnot hash hesel můžou resetovat hesla.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Povolení odemknutí účtů uživateli bez resetování hesla

Tento ovládací prvek určuje, zda uživatelé, kteří navštíví portál pro resetování hesla, by měli mít možnost odemknout své místní účty služby Active Directory, aniž by museli resetovat heslo. Ve výchozím nastavení Azure AD odemkne účty, když provede resetování hesla. Toto nastavení slouží k oddělení těchto dvou operací.

* Pokud je nastaveno na **Ano**, uživatelé budou mít možnost resetovat heslo, odemknout účet nebo odemknout účet, aniž by museli resetovat heslo.
* Pokud je nastavená na **ne**, uživatelé můžou provádět jenom kombinované obnovení hesla a operaci odemknutí účtu.

### <a name="on-premises-active-directory-password-filters"></a>Filtry hesel pro místní službu Active Directory

Samoobslužné resetování hesla služby Azure AD provádí ekvivalent resetování hesla iniciované správcem ve službě Active Directory. Pokud k vynucení vlastních pravidel hesel používáte filtr hesel třetí strany a požadujete, aby se tento filtr hesel kontroloval během samoobslužného resetování hesla služby Azure AD, ujistěte se, že řešení filtru hesel třetí strany je nakonfigurované tak, aby se používalo ve scénáři pro resetování hesla správce. [Služba Azure AD Password Protection pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md) je ve výchozím nastavení podporovaná.

## <a name="password-reset-for-b2b-users"></a>Resetování hesla pro uživatele B2B

Resetování a změna hesla jsou plně podporované ve všech konfiguracích B2B (Business-to-Business). Resetování hesla uživatele B2B je podporované v následujících třech případech:

* **Uživatelé z partnerské organizace s existujícím klientem Azure AD**: Pokud organizace, které spolupracujete, má stávajícího tenanta Azure AD, *respektuje, jestli jsou v tomto tenantovi povolené zásady pro resetování hesla*. Aby se resetování hesla fungovalo, partnerská organizace potřebuje jenom jistotu, že je povolená služba Azure AD SSPR. Zákazníkům Office 365 se neúčtují žádné další poplatky a dají se povolit podle kroků uvedených v příručce [Začínáme se správou hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) .
* **Uživatelé, kteří se zaregistrují prostřednictvím** samoobslužné registrace: Pokud je organizace, které jste spolupracuje s použitím funkce [samoobslužné registrace](../users-groups-roles/directory-self-service-signup.md) , přihlášena k tenantovi, umožníme jim resetovat heslo pomocí e-mailu, který si zaregistrovali.
* **Uživatelé B2B**: Všichni noví uživatelé B2B, kteří vytvořili pomocí nových [možností Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) , budou moct resetovat hesla taky pomocí e-mailu, který zaregistrovali během procesu pozvání.

Pokud chcete tento scénář vyzkoušet, přečtěte si https://passwordreset.microsoftonline.com některý z těchto partnerských uživatelů. Pokud mají definovaný alternativní e-mail nebo ověřovací e-mail, resetování hesla funguje podle očekávání.

> [!NOTE]
> Účtům Microsoft, kterým byl udělen přístup hosta k vašemu tenantovi Azure AD, jako jsou například Hotmail.com, Outlook.com nebo jiné osobní e-mailové adresy, nemůžou využívat Azure AD SSPR. Je potřeba resetovat heslo pomocí informací, které najdete v části [když se nemůžete přihlásit k vašemu účet Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.

## <a name="next-steps"></a>Další kroky

V následujících článcích najdete další informace o resetování hesla prostřednictvím Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco přerušeno. Návody řešit potíže s SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Dostupné metody ověřování Azure AD a požadované množství"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Konfigurace opětovného zápisu hesla na místní integraci a informace o řešení potíží"

---
title: Podrobné prolomení samoobslužného hesla – Azure Active Directory
description: Jak funguje samoobslužné resetování hesla
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848574"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to funguje: Samoobslužné resetování hesla Azure AD

Jak funguje samoobslužné resetování hesla (SSPR)? Co tato možnost znamená v rozhraní? Pokračujte ve čtení a dozvíte se více o azure active directory (Azure AD) SSPR.

## <a name="how-does-the-password-reset-portal-work"></a>Jak funguje portál pro resetování hesla?

Když uživatel přejde na portál pro obnovení hesla, spustí se pracovní postup, který určí:

   * Jak by měla být stránka lokalizována?
   * Je uživatelský účet platný?
   * Do jaké organizace uživatel patří?
   * Kde je heslo uživatele spravováno?
   * Je uživatel oprávněn tuto funkci používat?

Přečtěte si následující kroky, abyste se dozvěděli o logice za stránkou pro resetování hesla:

1. Uživatel vybere odkaz **Nelze získat přístup k účtu** [https://aka.ms/sspr](https://passwordreset.microsoftonline.com)nebo přejde přímo na stránku .
   * Na základě národního prostředí prohlížeče je prostředí vykresleno v příslušném jazyce. Možnost obnovení hesla je lokalizována do stejných jazyků, které podporuje Office 365.
   * Chcete-li zobrazit portál pro obnovení hesla v jiném lokalizovaném jazyce, připojíte adresu URL na konec adresy [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us)URL pro obnovení hesla s následujícím příkladem lokalizace do španělštiny .
2. Uživatel zadá ID uživatele a předá captcha.
3. Azure AD ověří, že uživatel je schopen používat tuto funkci provedením následující kontroly:
   * Zkontroluje, zda má uživatel tuto funkci povolenou a má přiřazenou licenci Azure AD.
     * Pokud uživatel nemá tuto funkci povolenou nebo nemá přiřazenou licenci, je uživatel požádán, aby kontaktoval svého správce a resetoval své heslo.
   * Zkontroluje, zda má uživatel správné metody ověřování definované na svém účtu v souladu se zásadami správce.
     * Pokud zásada vyžaduje pouze jednu metodu, pak zajišťuje, že uživatel má příslušná data definovaná alespoň pro jednu z metod ověřování povolených zásadami správce.
       * Pokud metody ověřování nejsou nakonfigurovány, doporučujese uživateli kontaktovat správce a resetovat heslo.
     * Pokud zásada vyžaduje dvě metody, pak zajišťuje, že uživatel má příslušná data definovaná alespoň pro dvě metody ověřování povolené zásadami správce.
       * Pokud metody ověřování nejsou nakonfigurovány, doporučujese uživateli kontaktovat správce a resetovat heslo.
     * Pokud je uživateli přiřazena role správce Azure, bude vynucena silná zásada hesla se dvěma branami. Další informace o této zásadě naleznete v části [Správce obnovit zásady rozdíly](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Zkontroluje, zda je heslo uživatele spravováno místně (federované, předávací ověřování nebo synchronizovaná hash hesla).
     * Pokud je nasazen zpětný zápis a heslo uživatele je spravováno místně, může uživatel pokračovat v ověřování a resetování hesla.
     * Pokud zpětný zápis není nasazen a heslo uživatele je spravováno místně, je uživatel požádán, aby kontaktoval svého správce a resetoval své heslo.
4. Pokud je zjištěno, že uživatel je schopen úspěšně obnovit své heslo, pak je uživatel veden procesem obnovení.

## <a name="authentication-methods"></a>Metody ověřování

Pokud je povoleno ověřování dat, je nutné vybrat alespoň jednu z následujících možností pro metody ověřování. Někdy slyšíte tyto možnosti označované jako "brány". Důrazně doporučujeme **zvolit dvě nebo více metod ověřování,** aby vaši uživatelé měli větší flexibilitu v případě, že nemají přístup k jedné, když ji potřebují. Další podrobnosti o níže uvedených metodách naleznete v článku [Co jsou metody ověřování?](concept-authentication-methods.md).

* Oznámení mobilní aplikace
* Kód mobilní aplikace
* E-mail
* Mobilní telefon
* Telefon do kanceláře
* Bezpečnostní otázky

Uživatelé mohou obnovit své heslo pouze v případě, že mají data v metodách ověřování, které správce povolil.

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru dostupné uživatelům vícefaktorové mfa a spr pr ve volných a zkušebních klientech Azure AD. Sms zprávy nejsou touto změnou ovlivněny. Telefonní hovor bude i nadále k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv pouze na bezplatné nebo zkušební klienty Azure AD.

> [!WARNING]
> Účty přiřazené role správce Azure budou nutné k použití metod, jak jsou definovány v části [Správce obnovit zásady rozdíly](concept-sspr-policy.md#administrator-reset-policy-differences).

![Výběr metod ověřování na webu Azure Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Počet požadovaných metod ověřování

Tato možnost určuje minimální počet dostupných metod ověřování nebo bran, kterými musí uživatel projít, aby resetoval nebo odemkl své heslo. Může být nastavena na jednu nebo dvě.

Uživatelé mohou zadat další metody ověřování, pokud správce tuto metodu ověřování povolí.

Pokud uživatel nemá zaregistrovány minimální požadované metody, zobrazí se mu chybová stránka, která je nasměruje k žádosti, aby správce resetoval své heslo.

#### <a name="mobile-app-and-sspr"></a>Mobilní aplikace a sspr

Při použití mobilní aplikace, jako je aplikace Microsoft Authenticator, jako metoda pro resetování hesla, byste si měli být vědomi následujících upozornění:

* Pokud správci vyžadují, aby k resetování hesla byla použita jedna metoda, ověřovací kód je jedinou dostupnou možností.
* Pokud správci vyžadují dvě metody k resetování hesla, uživatelé mohou používat **buď** oznámení **nebo** ověřovací kód kromě jiných povolených metod.

| Počet metod potřebných k resetování | Jeden | Dva |
| :---: | :---: | :---: |
| Dostupné funkce mobilní aplikace | kód | Kód nebo oznámení |

Uživatelé nemají možnost zaregistrovat svou mobilní aplikaci při registraci [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)samoobslužného resetování hesla z aplikace . Uživatelé mohou zaregistrovat [https://aka.ms/mfasetup](https://aka.ms/mfasetup)svou mobilní aplikaci na [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)adrese nebo v novém náhledu registrace bezpečnostních údajů na adrese .

> [!WARNING]
> Musíte povolit [konvergované registrace pro samoobslužné resetování hesla a Azure Multi-Factor Authentication (Public Preview)](concept-registration-mfa-sspr-converged.md) před uživateli budou mít přístup k nové prostředí na . [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo)

> [!IMPORTANT]
> Ověřovací aplikace nelze vybrat jako jedinou metodu ověřování při konfiguraci zásady 1 brány. Podobně ověřovací aplikace a pouze jednu další metodu nelze vybrat při konfiguraci zásady 2 brány.
> Potom při konfiguraci zásad sspr, které zahrnují ověřovací aplikace jako metodu, by měla být při konfiguraci zásady 1 brány vybrána alespoň další další metoda a při konfiguraci zásad y 2-gates by měly být vybrány alespoň dvě další metody.
> Důvodem tohoto požadavku je, že aktuální prostředí registrace sspr nezahrnuje možnost zaregistrovat ověřovací aplikace. Možnost registrace ověřovací aplikace je součástí nové [konvergované registrace pro samoobslužné resetování hesla a Azure Multi-Factor Authentication (Public Preview).](concept-registration-mfa-sspr-converged.md)
> Povolení zásad, které používají pouze ověřovací aplikaci (pro zásady 1 brány) nebo ověřovací aplikace a pouze jednu další metodu (pro zásady 2 brány), může vést k tomu, že uživatelům bude zablokována registrace pro samota), dokud nebudou nakonfigurováni pro použití nového zkušenosti s registrací.

### <a name="change-authentication-methods"></a>Změna metod ověřování

Pokud začnete se zásadou, která má pouze jednu požadovanou metodu ověřování pro obnovení nebo odemknutí registrované a změníte to na dvě metody, co se stane?

| Počet registrovaných metod | Počet požadovaných metod | Výsledek |
| :---: | :---: | :---: |
| 1 nebo více | 1 | **Možnost** resetování nebo odemknutí |
| 1 | 2 | **Nelze** obnovit nebo odemknout |
| 2 nebo více | 2 | **Možnost** resetování nebo odemknutí |

Pokud změníte typy metod ověřování, které může uživatel použít, může nechtěně zabránit uživatelům v používání sspr, pokud nemají k dispozici minimální množství dat.

Příklad:
1. Původní zásada je nakonfigurována se dvěma požadovanými metodami ověřování. Používá pouze telefonní číslo kanceláře a bezpečnostní otázky. 
2. Správce změní zásady tak, aby již nepoužívaly bezpečnostní otázky, ale umožňuje použití mobilního telefonu a alternativního e-mailu.
3. Uživatelé bez mobilního telefonu nebo alternativních e-mailových polí, která jsou naplněna, nemohou resetovat svá hesla.

## <a name="registration"></a>Registrace

### <a name="require-users-to-register-when-they-sign-in"></a>Vyžadovat, aby se uživatelé při přihlášení registrovali

Povolení této možnosti vyžaduje, aby uživatel dokončil registraci resetování hesla, pokud se přihlásí k libovolným aplikacím pomocí služby Azure AD. Tento pracovní postup zahrnuje následující aplikace:

* Office 365
* portál Azure
* Přístupový panel
* Federované aplikace
* Vlastní aplikace využívající Azure AD

Pokud je vyžadována registrace zakázána, uživatelé se mohou zaregistrovat ručně. Mohou buď [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) navštívit, nebo vybrat odkaz **Registrovat pro resetování hesla** na kartě **Profil** na přístupovém panelu.

> [!NOTE]
> Uživatelé mohou zrušit registrační portál pro obnovení hesla výběrem **možnosti zrušit** nebo zavřením okna. Ale oni jsou vyzváni k registraci pokaždé, když se přihlásí, dokud nedokončí svou registraci.
>
> Toto přerušení nepřeruší připojení uživatele, pokud jsou již přihlášeni.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Nastavte počet dní, po jejichž potvrzení budou uživatelé vyzváni k opětovnému potvrzení ověřovacích údajů.

Tato možnost určuje dobu mezi nastavením a opětovnou potvrzovacím ověřením a je k dispozici pouze v případě, že povolíte možnost **Vyžadovat registraci uživatelů při přihlášení.**

Platné hodnoty jsou 0 až 730 dní, s "0", což znamená, že uživatelé nejsou nikdy požádáni o opětovné potvrzení jejich ověřovací informace.

## <a name="notifications"></a>Oznámení

### <a name="notify-users-on-password-resets"></a>Upozornit uživatele na resetování hesla

Pokud je tato možnost nastavena na **ano**, uživatelé, kteří resetují své heslo, obdrží e-mail s upozorněním, že jejich heslo bylo změněno. E-mail se odesílá prostřednictvím portálu sspr na jejich primární a alternativní e-mailové adresy, které jsou v souboru ve službě Azure AD. Nikdo jiný není upozorněn na událost reset.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Upozornit všechny správce, když ostatní správci resetují svá hesla

Pokud je tato možnost nastavena na **Ano**, obdrží *všichni správci* e-mail na svou primární e-mailovou adresu v souboru ve službě Azure AD. E-mail je upozorní, že jiný správce změnil své heslo pomocí nástroje Pro obnovení hesla.

Příklad: V prostředí jsou čtyři správci. Správce Resetuje své heslo pomocí nástroje Pro obnovení hesla. Správci B, C a D obdrží e-mail s upozorněním na resetování hesla.

## <a name="on-premises-integration"></a>Místní integrace

Pokud nainstalujete, nakonfigurujete a povolíte Azure AD Connect, máte následující další možnosti pro místní integrace. Pokud jsou tyto možnosti šedě, zpětný zápis nebyl správně nakonfigurován. Další informace naleznete [v tématu Konfigurace zpětného zápisu hesla](howto-sspr-writeback.md).

![Ověření zpětného zápisu hesla je povoleno a funguje][Writeback]

Tato stránka poskytuje rychlý stav místního klienta zpětného zápisu, jedna z následujících zpráv se zobrazí na základě aktuální konfigurace:

* Místní klient zpětného zápisu je v provozu.
* Azure AD je online a je připojené k místnímu klientovi zpětného zápisu. Vypadá to však, že nainstalovaná verze Služby Azure AD Connect je zastaralá. Zvažte [upgrade Azure AD Connect,](../hybrid/how-to-upgrade-previous-version.md) abyste zajistili, že máte nejnovější funkce připojení a důležité opravy chyb.
* Bohužel nemůžeme zkontrolovat stav vašeho místního zpětného zápisu, protože nainstalovaná verze Služby Azure AD Connect je zastaralá. [Upgradujte Azure AD Connect,](../hybrid/how-to-upgrade-previous-version.md) abyste mohli zkontrolovat stav připojení.
* Bohužel to vypadá, že se teď nemůžeme připojit k vašemu místnímu klientovi zpětného zápisu. [Poradce při potížích s azure ad připojení](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) obnovit připojení.
* Bohužel se nemůžeme připojit k místnímu klientovi zpětného zápisu, protože zpětný zápis hesla nebyl správně nakonfigurován. [Nakonfigurujte zpětný zápis hesla](howto-sspr-writeback.md) k obnovení připojení.
* Bohužel to vypadá, že se teď nemůžeme připojit k vašemu místnímu klientovi zpětného zápisu. To může být způsobeno dočasnými problémy na naší straně. Pokud problém přetrvává, [poradce při potížích Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) obnovit připojení.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zápis hesel do místního adresáře

Tento ovládací prvek určuje, zda je pro tento adresář povolen zpětný zápis hesla. Pokud je zpětný zápis zapnutý, označuje stav místní služby zpětného zápisu. Tento ovládací prvek je užitečný, pokud chcete dočasně zakázat zpětný zápis hesla bez nutnosti překonfigurovat Azure AD Connect.

* Pokud je přepínač nastaven na **hodnotu Ano**, je zpětný zápis povolen a federované, předávací ověřování nebo synchronizovaní uživatelé s hodnotou hash hesla mohou resetovat svá hesla.
* Pokud je přepínač nastaven na **hodnotu Ne**, je zpětný zápis zakázán a federované, předávací ověřování nebo synchronizovaní uživatelé s hodnotou hash hesla nebudou moci resetovat svá hesla.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Povolit uživatelům odemknutí účtů bez resetování hesla

Tento ovládací prvek určuje, zda by uživatelé, kteří navštíví portál pro obnovení hesla, měli mít možnost odemknout své místní účty služby Active Directory, aniž by museli resetovat své heslo. Ve výchozím nastavení Azure AD odemkne účty při obnovení hesla. Toto nastavení slouží k oddělení těchto dvou operací.

* Pokud je nastavena na **Ano**, pak uživatelé mají možnost obnovit své heslo a odemknout účet, nebo odemknout svůj účet, aniž by museli resetovat heslo.
* Pokud je nastavena na **ne**, mohou uživatelé provádět pouze kombinovanou operaci resetování hesla a odemknutí účtu.

### <a name="on-premises-active-directory-password-filters"></a>Místní filtry hesel služby Active Directory

Samoobslužné resetování hesla služby Azure AD provádí ekvivalent resetování hesla iniciovaného správcem ve službě Active Directory. Pokud používáte filtr hesel jiného výrobce k vynucení vlastních pravidel hesel a požadujete, aby byl tento filtr hesel zkontrolován během samoobslužného resetování hesla služby Azure AD, ujistěte se, že řešení filtru hesel jiného výrobce je nakonfigurované tak, aby se použilo v admin heslo reset scénář. [Azure AD ochrana heslem pro Windows Server Active Directory](concept-password-ban-bad-on-premises.md) je ve výchozím nastavení podporovaná.

## <a name="password-reset-for-b2b-users"></a>Reset hesla pro uživatele B2B

Resetování a změny hesla jsou plně podporovány ve všech konfiguracích mezi podniky (B2B). B2B uživatel resetování hesla je podporována v následujících třech případech:

* **Uživatelé z partnerské organizace s existujícím tenantem Azure AD:** Pokud organizace, se kterou spolupracujete, má existujícího klienta Azure AD, *respektujeme všechny zásady pro resetování hesla, které jsou v tomto tenantovi povolené*. Aby resetování hesla fungovalo, partnerská organizace se musí ujistit, že je povoleno automatické resetování hesla Azure AD. Pro zákazníky Office 365 se neplatí žádné další poplatky a povolit je podle kroků v našem [Průvodci Začínáme s heslem.](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)
* **Uživatelé, kteří se zaregistrují prostřednictvím** samoobslužné registrace: Pokud organizace, se kterou spolupracujete, používá funkci [samoobslužné registrace,](../users-groups-roles/directory-self-service-signup.md) aby se dostala do tenanta, necháme jim resetovat heslo pomocí e-mailu, který zaregistroval.
* **Uživatelé B2B**: Všichni noví uživatelé B2B vytvořené pomocí nových [funkcí Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) budou také moci resetovat svá hesla pomocí e-mailu, který zaregistrovali během procesu pozvání.

Chcete-li otestovat https://passwordreset.microsoftonline.com tento scénář, přejděte na jeden z těchto partnerských uživatelů. Pokud mají definovaný alternativní e-mail nebo ověřovací e-mail, resetování hesla funguje podle očekávání.

> [!NOTE]
> Účty Microsoft, kterým byl udělen přístup hosta k tenantovi Azure AD, jako jsou ty z Hotmail.com, Outlook.com nebo jiné osobní e-mailové adresy, nepoužívají azure ad sspr. Potřebují resetovat své heslo pomocí informací, které najdete v článku [Když se nemůžete přihlásit ke svému účtu Microsoft.](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

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
* [Myslím, že je něco rozbité. Jak lze vyřešit probléms sspr?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Dostupné metody ověřování Azure AD a požadované množství"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-on-premises-integration-writeback.png "Informace o zpětném zápisu hesla pro místní integraci a informace o řešení potíží"

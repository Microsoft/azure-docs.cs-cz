---
title: Azure Active Directory hesla pomocí samoobslužné služby do hloubky
description: Jak resetování hesla pomocí samoobslužné služby práce
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: c2b2ba0e238887531297f6ac2a486d42d885794d
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55079082"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Jak to funguje: Azure AD samoobslužné resetování hesla

Jak samoobslužné služby pro resetování hesla (SSPR)? Co tato možnost znamená rozhraní? Pokračujte ve čtení a zjistěte další informace o samoobslužné resetování HESLA Azure Active Directory (Azure AD).

|     |
| --- |
| Oznámení mobilní aplikace a kód mobilní aplikace jako metody pro hesla pomocí samoobslužné služby Azure AD obnovení jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>Jak resetovat heslo portálu?

Když uživatel přejde na portál pro resetování hesla, pracovní postup je vydáno k určení:

   * Jak by měl být lokalizována stránky?
   * Je platný uživatelský účet?
   * Organizace, které uživatel patří do?
   * Kde se heslo uživatele spravuje?
   * Je uživatel licenci k použití funkce?

Čtení následujícím postupem Další informace o logice heslo resetovat stránky:

1. Uživatel vybere **nemá přístup k účtu** propojení nebo přejde přímo na [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * Podle národního prostředí prohlížeč, prostředí se vykreslí v příslušném jazyce. Prostředí pro resetování hesla je lokalizován do stejných jazyků, které podporují Office 365.
   * Chcete-li zobrazit resetování hesla portálu v jiné lokalizovaného jazyka přidejte "? mkt =" konec heslo resetovat adresy URL v příkladu, který následuje lokalizace do španělštiny [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. Uživatel zadá ID uživatele a předává testu captcha.
3. Azure AD ověří, že uživatel je možné použít tuto funkci provedením následující kontroly:
   * Kontroluje, zda uživatel má povolení této funkce a Azure AD má přiřazenou licenci.
     * Pokud uživatel nemá povolení této funkce nebo mít přiřazenou licenci, uživateli se zobrazí výzva, kontaktovat svého správce resetovat heslo.
   * Kontroluje, zda uživatel má správné ověřování metody definované na svém účtu v souladu se zásadami správce.
     * Pokud tato zásada vyžaduje jenom jedna metoda, pak zajišťuje, že uživatel má příslušná data definovaná pro nejméně jednu z metod ověřování ve zásad správce povolené.
       * Pokud nejsou nakonfigurovaná metody ověřování, uživatel se doporučuje pro kontaktování správce resetovat heslo.
     * Pokud tato zásada vyžaduje dvě metody, pak zajišťuje, že uživatel má příslušná data definovaná pro minimálně dvě metody ověřování povolené zásady správce.
       * Pokud nejsou nakonfigurovaná metody ověřování, uživatel se doporučuje pro kontaktování správce resetovat heslo.
     * Pokud roli Správce služby Azure je přiřazena uživateli je vynutit zásady silných hesel dvě brány. Další informace o těchto zásadách najdete v části [správce resetovat zásady rozdíly](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Kontroluje, je-li heslo uživatele spravovaly místně (federované předávací ověřování nebo synchronizaci hodnoty hash hesla).
     * Pokud je nasazený zpětný zápis a heslo uživatele je spravovaná místně, uživatel může pokračovat k ověření a resetování hesla.
     * Pokud není nasazený zpětný zápis a heslo uživatele je spravovaná místně, je uživatel vyzván k obraťte se na svého správce resetovat heslo.
4. Pokud je zjištěno, že se uživatel úspěšně resetoval svoje heslo, uživatel je proveden procesem jeho resetování.

## <a name="authentication-methods"></a>Metody ověřování

Pokud je povolené samoobslužné resetování HESLA, musíte vybrat aspoň jednu z následujících možností pro metody ověřování. Někdy uslyšíte. Tyto možnosti uvedené jako "brány." Důrazně doporučujeme vám **zvolit dvě nebo více metod ověřování** tak, aby vaši uživatelé měli větší flexibilitu v případě, kdy ho potřebují nebudou moct získat přístup k některému.

* Oznámení mobilní aplikace (Preview)
* Kód mobilní aplikace (Preview)
* Email
* Mobilní telefon
* Telefon do kanceláře
* Bezpečnostní otázky

Uživatelům můžete jenom resetování hesla, pokud mají data v metody ověřování, které správce povolil.

> [!WARNING]
> Účty přiřazené role Správce služby Azure se bude vyžadovat použití metod, jak jsou definovány v sekci [správce resetovat zásady rozdíly](concept-sspr-policy.md#administrator-reset-policy-differences).

![Ověřování][Authentication]

### <a name="number-of-authentication-methods-required"></a>Počet metod ověřování požadovaných

Tato možnost určuje minimální počet dostupné metody ověření nebo brány, které uživatel musí projít resetovat nebo odemknout své heslo. Můžete nastavit na jeden nebo dva.

Uživatelé mohou poskytnout další metody ověřování, pokud správce povolí tuto metodu ověřování.

Pokud uživatel nemá zaregistrované minimální požadované metody, zobrazí se jim chybovou stránku, který je nasměruje k požadavku, že správce resetovat své heslo.

#### <a name="mobile-app-and-sspr-preview"></a>Mobilní aplikace a samoobslužné resetování HESLA (ve verzi Preview)

Při použití mobilní aplikace, jako je aplikace Microsoft Authenticator jako metoda pro resetování hesla, měli byste pamatovat na následující:

* Když správci potřebují jednu metodu možné použít k resetování hesla, ověřovací kód je jediná dostupná možnost.
* Když správci vyžadují dvě metody se používá k resetování hesla, uživatelé se můžou použít **buď** oznámení **nebo** ověřovací kód kromě jiných povolené metody.

| Počet metod nutných pro resetování | Jeden | Dva |
| :---: | :---: | :---: |
| Funkce mobilních aplikací, které jsou k dispozici | Kód | Kód nebo oznámení |

Uživatelé nemají možnost zaregistrovat svoje mobilní aplikace při registraci pro resetování hesla pomocí samoobslužné služby z [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup). Uživatelé můžou registrovat svoje mobilní aplikace na [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup), nebo v náhledu nové registrace informace o zabezpečení v [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

> [!WARNING]
> Je nutné povolit [Konvergované registrace pro samoobslužné resetování hesla a ověřování Azure Multi-Factor Authentication (Public preview)](concept-registration-mfa-sspr-converged.md) předtím, než uživatelé budou mít přístup k nové prostředí na [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).

### <a name="change-authentication-methods"></a>Změna metody ověřování

Pokud byste začali s zásadu, která má pouze jeden vyžaduje metodu ověřování pro resetování nebo odemknutí zaregistrované a změníte, že na dvě metody, co se stane?

| Počet metod zaregistrovaný | Počet metod požadovaných | Výsledek |
| :---: | :---: | :---: |
| minimálně 1. | 1 | **Možnost** resetování nebo odemknutí |
| 1 | 2 | **Nelze** resetování nebo odemknutí |
| 2 nebo více | 2 | **Možnost** resetování nebo odemknutí |

Pokud změníte typy metod ověřování, které může uživatel používat, mohou neúmyslně zabráníte uživatelům, bude možné použít samoobslužné resetování HESLA, když nemají minimální množství dat, které jsou k dispozici.

Příklad:
1. Dvě metody ověřování, vyžaduje se nakonfigurují původní zásadou. Používá se pouze telefon do kanceláře a bezpečnostní otázky. 
2. Správce změní zásady tak, aby už použít bezpečnostní otázky, ale umožňuje použití mobilní telefon a alternativní e-mailu.
3. Uživatelé bez mobilní telefon a alternativní e-mailovou polí nemůžete resetovat jejich hesla.

## <a name="registration"></a>Registrace

### <a name="require-users-to-register-when-they-sign-in"></a>Vyžadovat od uživatelů registraci při přihlášení

Když tuto možnost povolíte, musí uživatel k dokončení registrace pro resetování hesla, pokud se přihlásí do všech aplikací pomocí služby Azure AD. To zahrnuje následující aplikace:

* Office 365
* portál Azure
* Přístupový panel
* Federované aplikace
* Vlastní aplikace pomocí Azure AD

Při vyžadování registrace je zakázaná, uživatelé můžou registrovat ručně. Můžete buď návštěvu [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) nebo vyberte **registrace pro resetování hesla** odkaz pod **profilu** kartu na přístupovém panelu.

> [!NOTE]
> Uživatelé můžou zavřít registrační portál pro resetování hesla tak, že vyberete **zrušit** nebo zavřením okna. Ale zobrazí se výzva k registraci pokaždé, když se přihlásí až do dokončení jejich registraci.
>
> Tím nedojde k narušení připojení uživatele Pokud už přihlášení.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Nastavte počet dní, než se uživatelům zobrazí výzva k potvrzení jejich ověřovacích informací

Tato možnost určuje časový úsek mezi nastavením a reconfirming informace o ověřování a je dostupná jenom v případě, že povolíte **vyžadovat od uživatelů registraci při přihlašování** možnost.

Platné hodnoty jsou 0 až 730 dnů, "0", což znamená, že jsou uživatelé nikdy vyzváni k potvrzení jejich ověřovacích informací.

## <a name="notifications"></a>Oznámení

### <a name="notify-users-on-password-resets"></a>Upozornit uživatele na resetování hesla

Pokud je tato možnost nastavená na **Ano**, resetování hesel uživateli obdržet e-mail s upozorněním, že se změnil heslo. E-mail je odeslán přes portál pro samoobslužného resetování HESLA k jejich primární a alternativní e-mailové adresy, které jsou v souboru ve službě Azure AD. Nikdo jiný proběhne resetování události.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Upozornit všechny správce na resetování hesla jiného správce

Pokud je tato možnost nastavená na **Ano**, pak *všichni správci* dostávat e-mailu na jejich primární e-mailovou adresu v souboru ve službě Azure AD. E-mailu je upozorní, že jiný správce změnil heslo pomocí samoobslužného resetování HESLA.

Příklad: Existují čtyři správci v prostředí. Správce A resetuje heslo pomocí samoobslužného resetování HESLA. Správci B, C a D dostávat e-mailu výstrahy je resetování hesla.

## <a name="on-premises-integration"></a>Místní integrace

Pokud instalace, konfigurace a povolení služby Azure AD Connect, máte následující další možnosti pro místní integrace. Pokud tyto možnosti zobrazují šedě, pak zpětný zápis není nakonfigurovaná správně. Další informace najdete v tématu [konfigurací zpětného zápisu hesla](howto-sspr-writeback.md).

![Zpětný zápis][Writeback]

Tato stránka poskytuje rychlé stav v místním klientovi zpětného zápisu, jeden z následujících zpráv se zobrazí na základě aktuální konfigurace:

* On-premises klienta zpětného zápisu je zprovozněný.
* Azure AD je online a je připojený k vašemu klientovi zpětného zápisu místní. Ale pravděpodobně nainstalovanou verzi Azure AD Connect je zastaralá. Vezměte v úvahu [Azure AD Connect upgradovat](../hybrid/how-to-upgrade-previous-version.md) abyste měli jistotu, že máte nejnovější funkce připojení a důležité opravy chyb.
* Bohužel jsme nemůže zjistit stav klienta zpětného zápisu v místním, protože nainstalovaná verze služby Azure AD Connect je zastaralá. [Upgrade služby Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) být schopni zkontrolovat stav připojení.
* Bohužel to vypadá nemůžeme připojit k vašemu klientovi zpětného zápisu v místním hned teď. [Řešení potíží s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) obnovení připojení.
* Bohužel nemůžeme připojit k vašemu klientovi zpětného zápisu v místním protože zpětný zápis hesla není nakonfigurovaná správně. [Nakonfigurovat zpětný zápis hesla](howto-sspr-writeback.md) obnovení připojení.
* Bohužel to vypadá nemůžeme připojit k vašemu klientovi zpětného zápisu v místním hned teď. To může být dočasné problémy na naší straně. Pokud se problém nevyřeší, [Poradce při potížích s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) obnovení připojení.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapisovat hesla zpět do místního adresáře

Tento ovládací prvek určuje, jestli je povolený zpětný zápis hesla pro tento adresář. Je-li zpětný zápis na, označuje stav místní službu zpětného zápisu. To je užitečné, pokud chcete dočasně zakázat zpětný zápis hesel, aniž byste museli změnit konfiguraci služby Azure AD Connect.

* Pokud přepínač nastavený na **Ano**, pak je povolený zpětný zápis a federovaný, předávací ověřování, nebo uživatelům synchronizaci hodnot hash hesel se moct resetovat svá hesla.
* Pokud přepínač nastavený na **ne**, pak je zakázáno zpětný zápis a federovaný, předávací ověřování, nebo uživatelům synchronizaci hodnot hash hesel, je moct resetovat svá hesla.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Povolit uživatelům odemčení účtů bez resetování hesel

Tento ovládací prvek určuje, jestli si uživatelé, kteří navštíví portál pro resetování hesla by měly mít možnost Odemknout své účty v místním Active Directory bez resetování hesla. Ve výchozím nastavení Azure AD odemyká účty při resetování hesla funguje. Toto nastavení použijte k oddělení tyto dvě operace. 

* Pokud hodnotu **Ano**, pak uživatelům se zobrazí možnost resetovat své heslo a odemknout účet nebo odemknout svůj účet bez nutnosti k resetování hesla.
* Pokud hodnotu **ne**, pak uživatelé jsou pouze nebudou moct provádět obnovení kombinované hesla a odemknutí účtu operace.

## <a name="password-reset-for-b2b-users"></a>Resetování hesla pro uživatele B2B

Resetování hesla a změny jsou plně podporovány ve všech konfiguracích business-to-business (B2B). Resetování hesla uživatele B2B je podporována v následujících třech případech:

   * **Uživatelé v organizaci partnera poskytujícího s existujícím tenantem Azure AD**: Pokud má organizace partnerství s existujícím tenantovi Azure AD, jsme *respektovat jakékoli zásady pro resetování hesla jsou povolené v tomto tenantovi*. Pro resetování hesel v práci abyste měli jistotu, že je povolené samoobslužné resetování HESLA Azure AD stejně musí organizaci partnera poskytujícího. Neplatí žádné další poplatky pro zákazníky s Office 365, a to se dá nastavit pomocí následujících kroků v našich [Začínáme se správou hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) průvodce.
   * **Uživatelé, kteří se zaregistrují prostřednictvím** samoobslužnou registraci: Organizace se stanete-li použít [samoobslužnou registraci](../users-groups-roles/directory-self-service-signup.md) funkce do tenanta, informujeme je resetovat heslo pomocí e-mailu jsou registrované.
   * **Uživatele B2B**: Žádné nové uživatele B2B, které jsou vytvořené pomocí nových [možnosti Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) budete také moct resetovat heslo pomocí e-mailu, registruje se během procesu pozvánku.

K otestování tohoto scénáře, přejděte na https://passwordreset.microsoftonline.com s jedním z těchto partnerů. Pokud mají alternativní e-mailu nebo e-mail pro ověření definované, resetování hesla funguje podle očekávání.

> [!NOTE]
> Účty Microsoft, kterým byl udělen přístup hosta pro vašeho tenanta Azure AD, například Hotmail.com, Outlook.com nebo jiné osobní e-mailové adresy nejsou možné použít samoobslužné resetování HESLA Azure AD. Potřebují k resetování hesla pomocí informací v nalezen [když nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.

## <a name="next-steps"></a>Další postup

V následujících článcích najdete další informace o resetování hesla prostřednictvím Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Dostupné metody ověřování Azure AD a požadované množství"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Místní integrace konfigurace zpětného zápisu hesla a informace o odstraňování potíží"

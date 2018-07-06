---
title: Resetování hesla pomocí samoobslužné služby, jak to funguje – Azure Active Directory
description: Podrobné informace o resetování hesel samoobslužné služby Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: f2d0b009c4451a4108222ac7aa7954ba6dd86699
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37869003"
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Samoobslužné resetování hesla ve podrobné informace o službě Azure AD

Jak samoobslužné služby pro resetování hesla (SSPR)? Co tato možnost znamená rozhraní? Pokračujte ve čtení a zjistěte další informace o samoobslužné resetování HESLA Azure Active Directory (Azure AD).

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
2. Uživatel zadá ID uživatele a předává testu captcha.
3. Azure AD ověří, že uživatel je možné použít tuto funkci provedením následující kontroly:
   * Kontroluje, zda uživatel má povolení této funkce a Azure AD má přiřazenou licenci.
     * Pokud uživatel nemá povolení této funkce nebo mít přiřazenou licenci, uživateli se zobrazí výzva, kontaktovat svého správce resetovat heslo.
   * Kontroluje, zda má uživatel právo ověřovací data definovaná na svém účtu v souladu se zásadami správce.
     * Pokud tato zásada vyžaduje pouze jedinou nevýhodu, pak zajišťuje, že uživatel má příslušná data definovaná pro nejméně jeden z problémů ve zásad správce povolené.
       * Pokud uživatel před obrovskou výzvou – není nakonfigurováno, se doporučuje uživatele kontaktovat svého správce resetovat heslo.
     * Pokud zásady vyžadují dva údaje, pak zajišťuje, že uživatel má příslušná data definovaná pro alespoň dva problémy ve zásad správce povolené.
       * Pokud uživatel před obrovskou výzvou – není nakonfigurováno, se doporučuje uživatele kontaktovat svého správce resetovat heslo.
   * Kontroluje, je-li heslo uživatele spravovaly místně (federované předávací ověřování nebo synchronizaci hodnoty hash hesla).
     * Pokud je nasazený zpětný zápis a heslo uživatele je spravovaná místně, uživatel může pokračovat k ověření a resetování hesla.
     * Pokud není nasazený zpětný zápis a heslo uživatele je spravovaná místně, je uživatel vyzván k obraťte se na svého správce resetovat heslo.
4. Pokud je zjištěno, že se uživatel úspěšně resetoval svoje heslo, uživatel je proveden procesem jeho resetování.

## <a name="authentication-methods"></a>Metody ověřování

Pokud je povolené samoobslužné resetování HESLA, musíte vybrat aspoň jednu z následujících možností pro metody ověřování. Někdy uslyšíte. Tyto možnosti uvedené jako "brány." Důrazně doporučujeme, abyste zvolili alespoň dvě metody ověřování, aby vaši uživatelé měli větší flexibilitu.

* Email
* Mobilní telefon
* Telefon do kanceláře
* Bezpečnostní otázky

![Ověřování][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Jaké pole se používají v adresáři pro ověřovací data?

* **Telefon do kanceláře**: odpovídá Telefon do kanceláře.
    * Uživatelé nebudou moct nastavit toto pole. Musí být definovány správcem.
* **Mobilní telefon**: odpovídá telefon pro ověření (není viditelné veřejně) nebo mobilní telefon (veřejně viditelné).
    * Služba telefon pro ověření hledá nejprve a pak se mobilního telefonu, pokud se telefon pro ověření není k dispozici.
* **Alternativní e-mailovou adresu**: odpovídá ověřovací e-mail (není viditelné veřejně) nebo alternativní e-mailu.
    * Služby nejprve hledá e-mail pro ověření a nezdaří zpět na alternativní e-mailu.

Ve výchozím nastavení telefonní číslo do kanceláře atributy cloud a mobilní telefon synchronizovaní s vaší cloudovou adresářovou z vašeho místního adresáře pro ověřovací data.

Uživatelé mohou pouze resetování hesla, pokud mají data v metody ověřování, které správce aktivoval a vyžaduje.

Pokud uživatelé nechtějí zadání čísla mobilního telefonu viditelný v adresáři, ale chtějí používat pro resetování hesla, Správci by neměl naplnit v adresáři. Uživatelé by pak naplnit jejich **telefon pro ověření** atribut prostřednictvím [registrační portál pro resetování hesel](https://aka.ms/ssprsetup). Správci mohou zobrazit tyto informace v profilu uživatele, ale nebude publikován jinde.

### <a name="the-number-of-authentication-methods-required"></a>Počet metod ověřování požadovaných

Tato možnost určuje minimální počet dostupné metody ověření nebo brány, které uživatel musí projít resetovat nebo odemknout své heslo. Můžete nastavit na jeden nebo dva.

Uživatelé mohou poskytnout další metody ověřování, pokud správce povolí tuto metodu ověřování.

Pokud uživatel nemá zaregistrované minimální požadované metody, zobrazí se jim chybovou stránku, který je nasměruje k požadavku, že správce resetovat své heslo.

#### <a name="change-authentication-methods"></a>Změna metody ověřování

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
3. Uživatelé bez mobilní telefon a alternativní e-mailovou polí nemůžete resetovat svá hesla.

### <a name="how-secure-are-my-security-questions"></a>Zabezpečené jsou mé bezpečnostní otázky?

Pokud používáte bezpečnostní otázky, doporučujeme používat je společně s jinou metodu. Bezpečnostní otázky může být méně bezpečné než jiné metody, protože někteří lidé znát odpovědi na otázky jiným uživatelem.

> [!NOTE] 
> Bezpečnostní otázky jsou uloženy privátně a bezpečně na objekt uživatele v adresáři a jako odpověď získat jenom uživatelé během registrace. Neexistuje žádný způsob, jak správci číst nebo upravovat uživatele otázek a odpovědí.
>

### <a name="security-question-localization"></a>Lokalizace bezpečnostní otázku

Všechny předdefinované dotazy, které následují jsou lokalizovány do úplnou sadu Office 365 jazyky a jsou založené na národním prostředí uživatele prohlížeče:

* V jakém městě jste potkali svého manžela nebo manželku (partnera nebo partnerku)?
* V jakém městě se potkali vaši rodiče?
* V jakém městě žije váš nejbližší sourozenec?
* V jakém městě se narodil váš otec?
* V jakém městě jste měli první práci?
* V jakém městě se narodila vaše matka?
* V jakém městě jste byli na Nový rok 2000?
* Jaké je příjmení vašeho oblíbeného učitele ze střední školy?
* Jaký je název univerzity, na kterou jste se hlásili, ale nechodili na ni?
* Jak se jmenuje město, kde jste měli svatební hostinu?
* Jaký je oblíbený sport vašeho otce?
* Jaké je vaše oblíbené jídlo?
* Jaké je jméno a příjmení vaší babičky z matčiny strany?
* Jak se za svobodna jmenovala vaše matka?
* Co je měsíci a roce svého nejstaršího sourozence? (např. listopad 1985)
* Jaké je křestní jméno vašeho nejstaršího sourozence?
* Jaké je jméno a příjmení vašeho dědečka z otcovy strany?
* Jaké je křestní jméno vašeho nejmladšího sourozence?
* Do jaké školy jste chodili v šesté třídě?
* Jaké měl jméno a příjmení váš nejlepší přítel v dětství?
* Jaké měl jméno a příjmení váš první partner nebo partnerka?
* Jaké bylo příjmení vašeho oblíbeného učitele ze základní školy?
* Jaká byla značka a model vašeho prvního auta nebo motorky?
* Jaký byl název první školy, do které jste chodili?
* Jaký název měla nemocnice, ve které jste se narodili?
* Jak se jmenovala ulice, kde jste v dětství bydleli?
* Jak se jmenoval váš dětský idol?
* Jaké bylo jméno vašeho oblíbeného plyšáka?
* Jak se jmenovalo vaše první domácí zvířátko?
* Jakou jste měli v dětství přezdívku?
* Jaký byl váš oblíbený sport na střední škole?
* Jaká byla vaše první práce?
* Jaké byly poslední čtyři číslice vašeho telefonu v dětství?
* Jaké bylo v dětství vaše vysněné povolání?
* Jakou nejznámější osobnost jste kdy potkali?

### <a name="custom-security-questions"></a>Vlastní bezpečnostní otázky

Vlastní bezpečnostní otázky nejsou lokalizovány pro různá národní prostředí. Všechny vlastní otázky se zobrazují ve stejném jazyce jako jsou zapsány v rozhraní administrativního uživatele, i v případě, že národní prostředí uživatele prohlížeče se liší. Pokud potřebujete lokalizovaná dotazy, měli byste použít předdefinované dotazy.

Maximální délka vlastní bezpečnostní otázky je 200 znaků.

Chcete-li zobrazit portálu pro resetování hesel a dotazy v různých lokalizovaný jazyk připojit "? mkt =<Locale>" konec heslo resetovat adresy URL v příkladu, který následuje lokalizace do španělštiny [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).

### <a name="security-question-requirements"></a>Požadavky na bezpečnostní otázku

* Limit počtu znaků minimální odpovědí je tři znaky.
* Limit počtu znaků maximální odpovědí je 40 znaků.
* Uživatele nelze odpovězte na stejnou otázku více než jednou.
* Uživatelé nelze zadat stejnou odpověď pro více než jednu otázku.
* Všechny znakové sady lze definovat na otázky a odpovědi, včetně znaků Unicode.
* Počet otázek musí být větší než nebo rovna počtu otázek, které byly zapotřebí k registraci.

## <a name="registration"></a>Registrace

### <a name="require-users-to-register-when-they-sign-in"></a>Vyžadovat od uživatelů registraci při přihlášení

Pokud chcete povolit tuto možnost, má uživatel, který je povolen pro resetování hesla k dokončení registrace pro resetování hesla, pokud se přihlásí do aplikace pomocí Azure AD. To zahrnuje následující:

* Office 365
* Azure Portal
* Přístupový panel
* Federované aplikace
* Vlastní aplikace pomocí Azure AD

Při vyžadování registrace je zakázaná, uživatelé můžou ručně registrovat své kontaktní informace. Můžete buď návštěvu [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) nebo vyberte **registrace pro resetování hesla** odkaz pod **profilu** kartu na přístupovém panelu.

> [!NOTE]
> Uživatelé můžou zavřít registrační portál pro resetování hesla tak, že vyberete **zrušit** nebo zavřením okna. Ale zobrazí se výzva k registraci pokaždé, když se přihlásí až do dokončení jejich registraci.
>
> Tím nedojde k narušení připojení uživatele Pokud už přihlášení.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Nastavte počet dní, než se uživatelům zobrazí výzva k potvrzení jejich ověřovacích informací

Tato možnost určuje časový úsek mezi nastavením a reconfirming informace o ověřování a je dostupná jenom v případě, že povolíte **vyžadovat od uživatelů registraci při přihlašování** možnost.

Platné hodnoty jsou 0 až 730 dnů, "0", což znamená, že jsou uživatelé nikdy vyzváni k potvrzení jejich ověřovacích informací.

## <a name="notifications"></a>Oznámení

### <a name="notify-users-on-password-resets"></a>Upozornit uživatele na resetování hesla

Pokud je tato možnost nastavená na **Ano**, pak uživatel, který je resetování hesel obdrží e-mail s upozorněním, že se změnil heslo. E-mail je odeslán přes portál pro samoobslužného resetování HESLA k jejich primární a alternativní e-mailové adresy, které jsou v souboru ve službě Azure AD. Nikdo jiný obdrží oznámení o obnovení události.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Upozornit všechny správce na resetování hesla jiného správce

Pokud je tato možnost nastavená na **Ano**, pak *všichni správci* dostávat e-mailu na jejich primární e-mailovou adresu v souboru ve službě Azure AD. E-mailu je upozorní, že jiný správce změnil heslo pomocí samoobslužného resetování HESLA.

Příklad: Existují čtyři správci v prostředí. Správce A resetuje heslo pomocí samoobslužného resetování HESLA. Správci B, C a D dostanete e-mail, který upozorní je resetování hesla.

## <a name="on-premises-integration"></a>Místní integrace

Pokud instalace, konfigurace a povolení služby Azure AD Connect, máte následující další možnosti pro místní integrace. Pokud tyto možnosti zobrazují šedě, pak zpětný zápis není nakonfigurovaná správně. Další informace najdete v tématu [konfigurací zpětného zápisu hesla](howto-sspr-writeback.md#configure-password-writeback).

![Zpětný zápis][Writeback]

Tato stránka poskytuje rychlé stav v místním klientovi zpětného zápisu se zobrazí jedna z následujících zpráv na základě aktuální konfigurace:

* On-premises klienta zpětného zápisu je zprovozněný.
* Azure AD je online a je připojený k vašemu klientovi zpětného zápisu místní. Ale pravděpodobně nainstalovanou verzi Azure AD Connect je zastaralá. Vezměte v úvahu [Azure AD Connect upgradovat](./../connect/active-directory-aadconnect-upgrade-previous-version.md) abyste měli jistotu, že máte nejnovější funkce připojení a důležité opravy chyb.
* Bohužel jsme nemůže zjistit stav klienta zpětného zápisu v místním, protože nainstalovaná verze služby Azure AD Connect je zastaralá. [Upgrade služby Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) být schopni zkontrolovat stav připojení.
* Bohužel to vypadá nemůžeme připojit k vašemu klientovi zpětného zápisu v místním hned teď. [Řešení potíží s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) obnovení připojení.
* Bohužel nemůžeme připojit k vašemu klientovi zpětného zápisu v místním protože zpětný zápis hesla není nakonfigurovaná správně. [Nakonfigurovat zpětný zápis hesla](howto-sspr-writeback.md#configure-password-writeback) obnovení připojení.
* Bohužel to vypadá nemůžeme připojit k vašemu klientovi zpětného zápisu v místním hned teď. To může být dočasné problémy na naší straně. Pokud se problém nevyřeší, [Poradce při potížích s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) obnovení připojení.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zapisovat hesla zpět do místního adresáře

Tento ovládací prvek určuje, jestli je povolený zpětný zápis hesla pro tento adresář. Je-li zpětný zápis na, označuje stav místní službu zpětného zápisu. To je užitečné, pokud chcete dočasně zakázat zpětný zápis hesel, aniž byste museli změnit konfiguraci služby Azure AD Connect.

* Pokud přepínač nastavený na **Ano**, pak je povolený zpětný zápis a federovaný, předávací ověřování, nebo uživatelům synchronizaci hodnot hash hesel se moct resetovat svá hesla.
* Pokud přepínač nastavený na **ne**, pak je zakázáno zpětný zápis a federovaný, předávací ověřování, nebo uživatelům synchronizaci hodnot hash hesel, je moct resetovat svá hesla.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Povolit uživatelům odemčení účtů bez resetování hesel

Tento ovládací prvek určuje, jestli si uživatelé, kteří navštíví portál pro resetování hesla by měly mít možnost Odemknout své účty v místním Active Directory bez resetování hesla. Ve výchozím nastavení Azure AD odemyká účty při resetování hesla funguje. Toto nastavení použijte k oddělení tyto dvě operace. 

* Pokud hodnotu **Ano**, pak uživatelům se zobrazí možnost resetovat své heslo a odemknout účet nebo odemknout svůj účet bez nutnosti k resetování hesla.
* Pokud hodnotu **ne**, pak uživatelé jsou pouze nebudou moct provádět obnovení kombinované hesla a odemknutí účtu operace.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Jak pro resetování hesla pro uživatele B2B?
Resetování hesla a změny jsou plně podporovány ve všech konfiguracích business-to-business (B2B). Resetování hesla uživatele B2B je podporována v následujících třech případech:

   * **Uživatelé v organizaci partnera poskytujícího s existujícím tenantem Azure AD**: Pokud má organizace partnerství s existujícím tenantovi Azure AD, jsme *respektovat jakékoli zásady pro resetování hesla jsou povolené v tomto tenantovi*. Pro resetování hesel v práci abyste měli jistotu, že je povolené samoobslužné resetování HESLA Azure AD stejně musí organizaci partnera poskytujícího. Neplatí žádné další poplatky pro zákazníky s Office 365, a to se dá nastavit pomocí následujících kroků v našich [Začínáme se správou hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) průvodce.
   * **Uživatelé, kteří se zaregistrují prostřednictvím** samoobslužnou registraci: Pokud organizace partnerství s používané [samoobslužnou registraci](../users-groups-roles/directory-self-service-signup.md) funkce do tenanta, informujeme je resetovat heslo pomocí e-mailu jsou registrované.
   * **Uživatele B2B**: jakékoli nové uživatele B2B, které jsou vytvořené pomocí nových [možnosti Azure AD B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) budete také moct resetovat heslo pomocí e-mailu, registruje se během procesu pozvánku.

K otestování tohoto scénáře, přejděte na http://passwordreset.microsoftonline.com s jedním z těchto partnerů. Pokud mají alternativní e-mailu nebo e-mail pro ověření definované, resetování hesla funguje podle očekávání.

> [!NOTE]
> Účty Microsoft, kterým byl udělen přístup hosta pro vašeho tenanta Azure AD, například Hotmail.com, Outlook.com nebo jiné osobní e-mailové adresy nejsou možné použít samoobslužné resetování HESLA Azure AD. Potřebují k resetování hesla pomocí informací v nalezen [když nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.

## <a name="next-steps"></a>Další postup

V následujících článcích najdete další informace o resetování hesla prostřednictvím Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
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

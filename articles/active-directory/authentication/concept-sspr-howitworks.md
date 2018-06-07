---
title: Resetování hesla pomocí samoobslužné služby, jak to funguje – Azure Active Directory
description: Podrobné informace pro vytvoření nového hesla samoobslužné služby Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 42471687e0a56ff0bacf5c9effecb0e2466b4690
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593587"
---
# <a name="self-service-password-reset-in-azure-ad-deep-dive"></a>Samoobslužné služby v Azure AD podrobné informace pro vytvoření nového hesla

Jak resetování hesla pomocí samoobslužné služby pracovní (SSPR)? Co tato možnost znamená v rozhraní? Pokračujte ve čtení na další informace o SSPR Azure Active Directory (Azure AD).

## <a name="how-does-the-password-reset-portal-work"></a>Jak resetování hesla portálu pracovní?

Když uživatel přejde na portál resetovat heslo, pracovní postup je spuštěna Chcete-li zjistit:

   * Jak by měly být lokalizovány stránky?
   * Je platný uživatelský účet?
   * Jaké organizace uživatel nepatří do?
   * Kde je pro uživatele heslo spravované?
   * Je daný uživatel licenci pro použití funkce?

Čtení následujícími kroky Další informace o logiku za heslo resetovat stránky:

1. Uživatel vybere **nelze získat přístup k účtu** odkaz nebo přejde přímo na [ https://aka.ms/sspr ](https://passwordreset.microsoftonline.com).
   * Podle národního prostředí prohlížeče, možnosti je vykreslen v příslušný jazyk. Prostředí resetování hesla je lokalizované do stejné jazyky, které podporuje Office 365.
2. Uživatel zadá ID uživatele a předá test captcha.
3. Azure AD ověřuje, že je uživatel moci pomocí této funkce pomocí tohoto postupu následující kontroly:
   * Kontroluje, zda uživatel má povolení této funkce a Azure AD má přiřazenou licenci.
     * Pokud uživatel nemá povolení této funkce nebo mají přiřazené licence, je uživatel požádán o obrátit na správce svého resetovat heslo.
   * Kontroluje, zda má uživatel práva challenge dat definovaných na svůj účet v souladu se zásadami správce.
     * Pokud zásady vyžaduje pouze jednu výzvu, pak zajišťuje, že uživatel má příslušná data definované pro minimálně jeden z problémů ve zásad správce povolené.
       * Pokud není nakonfigurováno výzvy k zadání uživatelského, pak uživatel by měl obrátit na správce svého resetovat heslo.
     * Pokud zásady vyžaduje dva problémy, pak zajišťuje, že uživatel má příslušná data definované pro alespoň dvě z problémů ve zásad správce povolené.
       * Pokud není nakonfigurováno výzvy k zadání uživatelského, pak uživatel by měl obrátit na správce svého resetovat heslo.
   * Kontroluje, zda je heslo uživatele spravovány místně (federované, předávací ověřování nebo hodnoty hash hesla synchronizovat).
     * Pokud je nasazený zpětný zápis a heslo uživatele se spravovaná místně, uživatel je moci pokračovat k ověření a obnovit své heslo.
     * Pokud není nasazena zpětný zápis a heslo uživatele je spravovaná místně, je uživatel požádán o obrátit na správce svého resetovat heslo.
4. Pokud je zjištěno, že je uživatel moct úspěšně obnovit své heslo, pak může uživatel v průvodci procesem resetování.

## <a name="authentication-methods"></a>Metody ověřování

Pokud je povoleno SSPR, musíte vybrat alespoň jeden z následujících možností pro metody ověřování. Někdy uslyšíte. Tyto možnosti se označuje jako "brány." Důrazně doporučujeme, abyste zvolili alespoň dvě metody ověřování, tak, aby vaši uživatelé větší flexibilitu.

* Email
* Mobilní telefon
* Telefon do kanceláře
* Bezpečnostní otázky

![Ověřování][Authentication]

### <a name="what-fields-are-used-in-the-directory-for-the-authentication-data"></a>Jaké pole se používají v adresáři pro ověřování dat?

* **Telefon do kanceláře**: odpovídá Telefon do kanceláře.
    * Uživatelé se nemohou nastavte pole. Musí být definovány správcem.
* **Mobilní telefon**: odpovídá (nejsou viditelné veřejně) Telefon pro ověření nebo mobilního telefonu (veřejně viditelný).
    * Služba vypadá nejdřív pro telefon pro ověření a poté vrátí zpět na mobilní telefon, pokud se telefon pro ověření není k dispozici.
* **Alternativní e-mailovou adresu**: odpovídá (nejsou viditelné veřejně) ověřování e-mailu nebo alternativní e-mailu.
    * Služba nejprve hledá e-mailu, ověřování a pak dojde k navrácení alternativní e-mailu.

Ve výchozím nastavení Telefon do kanceláře atributy cloudu a mobilních telefonních jsou synchronizovány do cloudového adresáře z místního adresáře pro data ověřování.

Uživatelé mohou pouze obnovit své heslo, pokud mají data obsažená v metod ověřování, které správce povolil a vyžaduje.

Pokud uživatelé nechtějí své mobilní telefonní číslo viditelné v adresáři, ale chtějí používat pro resetování hesla, Správci by neměl naplnit v adresáři. Uživatelé by pak naplnit jejich **telefon pro ověření** atribut prostřednictvím [portál pro registraci a resetování hesla](https://aka.ms/ssprsetup). Správci uvidí tyto informace v profilu uživatele, ale nebude publikován jinde.

### <a name="the-number-of-authentication-methods-required"></a>Počet požadované metody ověřování

Tato možnost určuje minimální počet dostupné metody ověření nebo brány, které uživatel musí projít resetovat nebo odemknutí své heslo. Je můžete nastavit na jeden nebo dva kusy.

Uživatelé mohou k poskytování další metody ověřování, pokud správce povolí tuto metodu ověřování.

Pokud uživatel nemá minimální požadované metody zaregistrovaný, zobrazí se jim chybová stránka, která přesměruje je, aby vyžadovala, aby správce obnovit své heslo.

#### <a name="change-authentication-methods"></a>Změna metody ověřování

Pokud spustíte zásadám, který má jenom jednu vyžaduje metodu ověřování pro resetovat nebo odemknout zaregistrován a změníte, na dvě metody, co se stane?

| Počet metody zaregistrován | Počet metod požadovaných | Výsledek |
| :---: | :---: | :---: |
| 1 nebo více | 1 | **Možnost** resetovat nebo odemknutí |
| 1 | 2 | **Nelze** resetovat nebo odemknutí |
| 2 nebo více | 2 | **Možnost** resetovat nebo odemknutí |

Pokud změníte typy metod ověřování, které může uživatel používat, může nechtěně přestat uživatelé nebudou moci používat SSPR, když nemají minimální množství dat, které jsou k dispozici.

Příklad: 
1. Původní zásad je nakonfigurovaná s vyžaduje dvě metody ověřování. Používá pouze office telefonní číslo a bezpečnostní otázky. 
2. Správce změn zásad už pomocí bezpečnostních otázek, ale umožňuje použití mobilního telefonu a alternativní e-mailu.
3. Uživatelé bez mobilní telefon a naplněno pole alternativní e-mailovou nelze resetovat jejich hesla.

### <a name="how-secure-are-my-security-questions"></a>Do jaké míry jsou mé bezpečnostní otázky?

Pokud chcete použít bezpečnostní otázky, doporučujeme, abyste je mohli používat společně s jinou metodu. Bezpečnostní otázky může být méně bezpečné než jiné metody, protože někteří uživatelé možné, že znáte odpovědi na otázky jiným uživatelem.

> [!NOTE] 
> Bezpečnostní otázky jsou uloženy a s lepším zabezpečením na objekt uživatele v adresáři a pouze může být odpovědi uživatele během registrace. Neexistuje žádný způsob pro správce pro čtení nebo upravit uživatele otázek a odpovědí.
>

### <a name="security-question-localization"></a>Lokalizace bezpečnostní otázku

Předdefinované dotazy, které následují lokalizace do všech jazyků Office 365 a jsou založené na prohlížeči uživatele národního prostředí:

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
* Co je narozeninách měsíci a roce vašeho nejstaršího sourozence? (Příklad: listopad 1985)
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

Vlastní bezpečnostní otázky nejsou lokalizovány pro různá národní prostředí. Všechny vlastní otázky se zobrazují ve stejném jazyce jako jsou zapsány v rozhraní správce i v případě národního prostředí uživatele prohlížeče se liší. Pokud potřebujete lokalizovaná otázky, měli byste použít předdefinované dotazy.

Maximální délka vlastní bezpečnostní otázku je 200 znaků.

Chcete-li zobrazit portálu pro resetování hesel a dotazy v jiné lokalizované jazyk připojit "? mkt =<Locale>" na konec heslo resetovat URL příklad, který následuje lokalizace do španělštiny [ https://passwordreset.microsoftonline.com/?mkt=es-us ](https://passwordreset.microsoftonline.com/?mkt=es-us).

### <a name="security-question-requirements"></a>Požadavky na zabezpečení otázku

* Limit znak minimální odpovědí je tři znaky.
* Odpověď maximální povolený počet znaků je 40 znaků.
* Uživatele nelze odpovězte na stejné otázku více než jednou.
* Uživatelé nemůže poskytovat stejné odpověď na otázku více než jeden.
* Všechny znakovou sadu lze definovat otázky a odpovědi, včetně znaky znakové sady Unicode.
* Počet otázek definovaných musí být větší než nebo rovna počtu otázek, které by bylo potřeba zaregistrovat.

## <a name="registration"></a>Registrace

### <a name="require-users-to-register-when-they-sign-in"></a>Vyžadovat registraci při přihlášení uživatelů

Chcete-li tuto možnost, má uživatel, který je povolen pro resetování hesla dokončit registraci k resetování hesla, pokud přihlášení do aplikace pomocí Azure AD. To zahrnuje následující:

* Office 365
* Azure Portal
* Přístupový panel
* Federovaným aplikacím
* Vlastní aplikace pomocí Azure AD

Při vyžadování registrace je zakázaná, uživatelé mohou registrovat pořád spustit ručně své kontaktní informace. Můžete buď návštěvu [ https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup) nebo vyberte **registrace pro resetování hesla** v části **profil** karta na přístupovém panelu.

> [!NOTE]
> Uživatele můžete zavřít portálu registrace resetování hesel výběrem **zrušit** nebo ukončením okna. Ale bude vyzván k registraci při každém přihlášení, dokud se jejich registraci dokončit.
>
> Pokud už jsou podepsané tím není přerušení připojení uživatele.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Nastavit počet dní, než jsou uživatelé vyzváni k potvrzení jejich informace o ověřování

Tato možnost určuje dobu, po mezi nastavení a reconfirming informace o ověřování a je k dispozici pouze v případě, že povolíte **vyžadovat, aby registraci při přihlašování uživatelé** možnost.

Platné hodnoty jsou 0 až 730 dní s "0", což znamená, že jsou uživatelé nikdy vyzváni k potvrzení jejich informace o ověřování.

## <a name="notifications"></a>Oznámení

### <a name="notify-users-on-password-resets"></a>Upozornit uživatele na resetování hesla

Pokud je tato možnost nastavena na **Ano**, pak uživatel, který je resetování hesla obdrží e-mail s upozorněním, že jejich heslo bylo změněno. E-mail je odeslán prostřednictvím portálu SSPR k jejich primární a alternativní e-mailové adresy, které jsou v souboru ve službě Azure AD. Nikdo jiný po upozornění na resetování této události.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Upozornit všechny správce, když jiní správci resetování hesel

Pokud je tato možnost nastavena na **Ano**, pak *všichni správci* obdrží e-mail na jeho primární e-mailovou adresu v souboru ve službě Azure AD. E-mailu je upozorní, že jiný správce má při použití SSPR změnit své heslo.

Příklad: Existují čtyři správci v prostředí. Správce A resetuje heslo pomocí SSPR. Správci B, C a D obdrží e-mail, který upozorní je resetování hesla.

## <a name="on-premises-integration"></a>Místní integrace

Je-li instalovat, konfigurovat a povolit Azure AD Connect, máte následující další možnosti pro místní integrace. Pokud tyto možnosti jsou šedě, pak zpětný zápis nebyla nakonfigurována správně. Další informace najdete v tématu [konfigurace zpětný zápis hesla](howto-sspr-writeback.md#configure-password-writeback).

![Zpětný zápis][Writeback]

Tato stránka obsahuje rychlý stav klienta pro zpětný zápis místní jednu z následujících zpráv se zobrazí, na základě aktuální konfigurace:

* Váš klient místní zpětný zápis je spuštěná.
* Azure AD je online a připojený k vašeho klienta místní zpětný zápis. Ale vypadá nainstalovaná verze služby Azure AD Connect je zastaralý. Vezměte v úvahu [upgrade Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) zajistit, že máte nejnovější funkce možností připojení a důležitých oprav chyb.
* Bohužel jsme nemůže zjistit stav klienta místní zpětný zápis, protože je zastaralý. nainstalovaná verze služby Azure AD Connect. [Upgrade Azure AD Connect](./../connect/active-directory-aadconnect-upgrade-previous-version.md) moct Kontrola stavu připojení.
* Bohužel vypadá nemůžeme připojit k vaší místní zpětného zápisu klienta hned teď. [Řešení potíží s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) k obnovení připojení.
* Bohužel nelze připojit do klienta zpětný zápis místní protože zpětný zápis hesla není správně nakonfigurován. [Nakonfigurovat zpětný zápis hesla](howto-sspr-writeback.md#configure-password-writeback) k obnovení připojení.
* Bohužel vypadá nemůžeme připojit k vaší místní zpětného zápisu klienta hned teď. Může to být způsobeno dočasnou problémy na naší straně. Pokud potíže potrvají, [řešení potíží s Azure AD Connect](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) k obnovení připojení.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Zpětný zápis hesla do místního adresáře

Tento ovládací prvek určuje, zda je pro tento adresář zapnutá zpětný zápis hesla. Pokud zpětného zápisu na, označuje stav zpětného zápisu služby místně. To je užitečné, pokud chcete dočasně zakázat zpětný zápis hesel, aniž by museli znovu konfigurovat Azure AD Connect.

* Pokud je přepínač nastavený na **Ano**, pak je povolit zpětný zápis a federované, předávací ověřování, nebo uživatelé synchronizovat hash hesla je moct resetovat jejich hesla.
* Pokud je přepínač nastavený na **ne**, pak je zakázán zpětný zápis a federované, předávací ověřování, nebo uživatelé hash synchronizovat hesla nejsou schopna resetovat jejich hesla.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Povolit uživatelům bez resetování hesla pro odemknutí účtů

Tento ovládací prvek určuje, zda má být poskytnut uživatele, kteří navštěvují portál pro resetování hesel možnost Odemknout účet místní služby Active Directory, aniž by bylo nutné obnovit své heslo. Ve výchozím nastavení Azure AD odemkne účty, když provádí resetování hesla. Pomocí tohoto nastavení k oddělení těchto dvou operací. 

* Pokud nastavena na **Ano**, pak mít možnost obnovit své heslo a odemknout účet a odemknout svůj účet bez nutnosti k resetování hesla uživatele.
* Pokud nastavena na **ne**, pak jsou pouze mít uživatelé k provedení resetování kombinované hesla a odemknutí účtu v operaci.

## <a name="how-does-password-reset-work-for-b2b-users"></a>Jak resetování hesla práce B2B uživatelů?
Resetování hesla a změny jsou plně podporuje všechny konfigurace business-to-business (B2B). Resetování hesla uživatele B2B je podporována v následujících třech případech:

   * **Uživatelům v organizaci partnera pomocí existujícího klienta Azure AD**: Pokud má organizace se stanete existujícího klienta Azure AD, jsme *respektují jsou povolené ať zásady resetování hesel na tohoto tenanta*. Pro resetování hesel k práci abyste měli jistotu, že je povolena Azure AD SSPR právě musí partnerské organizace. Není k dispozici bez dalších poplatků zákazníků Office 365 a může být povoleno podle kroků v našem [Začínáme se správou hesel](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) průvodce.
   * **Uživatelé, kteří si zaregistrovat prostřednictvím** samoobslužné registrace: Pokud organizace se stanete použité [samoobslužné registrace](../active-directory-self-service-signup.md) funkci nahrát do klienta, můžeme dát jim resetování hesla s e-mailu se zaregistrovat.
   * **Uživatelé B2B**: všechny nové uživatele B2B vytvořily s použitím nové [funkce Azure AD s B2B](../active-directory-b2b-what-is-azure-ad-b2b.md) bude také moct resetovat vlastní hesla s e-mailu, budou během procesu pozvání zaregistrovány.

Chcete-li tento scénář otestovat, přejděte na http://passwordreset.microsoftonline.com s jedním z těchto uživatelů partnera. Pokud mají alternativní e-mailu nebo e-mailu ověřování definované, resetování hesla funguje podle očekávání.

> [!NOTE]
> Účty Microsoft, kterým byl udělen přístup hosta pro vašeho klienta Azure AD, jako jsou ty z Hotmail.com, Outlook.com nebo jiné osobní e-mailové adresy, nejsou schopna použít Azure AD SSPR. Potřebují resetovat heslo pomocí informací v nalezen [při nemůžete se přihlásit ke svému účtu Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) článku.

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
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Místní konfiguraci zpětný zápis hesla integrace a informace o odstraňování potíží"

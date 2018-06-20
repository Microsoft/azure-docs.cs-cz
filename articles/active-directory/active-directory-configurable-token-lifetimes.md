---
title: Konfigurovat životnosti tokenu v Azure Active Directory | Microsoft Docs
description: Zjistěte, jak nastavit dobu platnosti pro tokeny vydané službou Azure AD.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: hirsin
ms.custom: aaddev
ms.reviewer: anchitn
ms.openlocfilehash: 95a3c1d3effe6d4b73354fb3ef871aeb7767bfb4
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228560"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Konfigurovat životnosti tokenu v Azure Active Directory (Public Preview)
Můžete zadat dobu životnosti tokenem vydaným službou Azure Active Directory (Azure AD). Můžete nastavit životnosti tokenu pro všechny aplikace ve vaší organizaci, pro aplikaci víceklientské (více organizace) nebo pro objekt určité služby ve vaší organizaci.

> [!IMPORTANT]
> Po sluchu od zákazníků ve verzi Preview jsme plánování nahradit tuto funkci pomocí nové funkce v Azure Active Directory podmíněného přístupu.  Po dokončení nové funkce bude tato funkce nakonec zastaralá po určité době oznámení.  Pokud použijete zásady konfigurovat doba platnosti tokenu, připravte přepnout do nové funkce podmíněného přístupu, jakmile je k dispozici. 
>
>

Objekt zásady ve službě Azure AD, představuje sadu pravidel, které vynucuje u jednotlivých aplikací, nebo na všechny aplikace v organizaci. Každý typ zásad se strukturou jedinečnou sadu vlastností, které se použijí pro objekty, na které jsou přiřazeny.

Zásady můžete určit jako výchozí zásady pro vaši organizaci. Zásady se použijí pro všechny aplikace v organizaci, tak dlouho, dokud není přepsána zásady s vyšší prioritou. Také můžete přiřadit zásady pro konkrétní aplikace. Pořadí podle priority se liší podle typu zásady.

> [!NOTE]
> Zásada Konfigurovat životnost tokenu není podporována pro SharePoint Online.  I když můžete mít možnost vytvářet tuto zásadu pomocí prostředí PowerShell, nebudou SharePoint Online vědomí tuto zásadu. Odkazovat [SharePoint Online blog](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) Další informace o konfiguraci časové limity nečinnosti relace.
>* Výchozí doba života pro SharePoint Online přístupový token je 1 hodina. 
>* Výchozí maximální neaktivní doba SharePoint Online aktualizace tokenu je 90 dnů.
>

## <a name="token-types"></a>Typy tokenů

Můžete nastavit dobu životnosti tokenu zásady pro tokeny obnovení, tokeny přístupu, tokeny relace a tokeny typu ID.

### <a name="access-tokens"></a>Přístupové tokeny
Klienti používat pro přístup k chráněnému prostředku přístupových tokenů. Přístupový token lze použít pouze pro konkrétní kombinaci uživatele, klienta a prostředků. Přístupové tokeny nejde odvolat a jsou platné až do vypršení jejich platnosti. Škodlivý objektu actor, který má získat token přístupu můžete použít pro rozsah celé jeho životnosti. Úprava životnost přístupový token je kompromis mezi zlepšení výkonu systému a zvýšení množství času, klient zachová přístup po uživatelský účet je zakázán. Vylepšené systému výkonu se dosáhne snižuje počet, kolikrát klient potřebuje získat nový přístupový token.  Výchozí hodnota je 1 hodina – po 1 hod., klient musí použít obnovovací token (obvykle bezobslužná) získat nové obnovovací token a přístup k tokenu. 

### <a name="refresh-tokens"></a>Obnovovacích tokenů
Když klient získá přístupový token pro přístup k chráněnému prostředku, obdrží klient také token obnovení. Token obnovení slouží k získání nového přístupu nebo aktualizace tokenu páry když vyprší platnost aktuální přístupový token. Token obnovení je vázán na kombinaci uživatele a klienta. Token obnovení může být [kdykoliv odvolat](develop/active-directory-token-and-claims.md#token-revocation), a kontroluje platnost tokenu pokaždé, když se používá token.  

Je důležité rozlišovat mezi důvěrné klienty a veřejné, protože to ovlivní, jak dlouho je možné obnovovacích tokenů. Další informace o různých typech klientů najdete v tématu [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Token životnosti s tokeny obnovení důvěrné klienta
Důvěrné klienti jsou aplikace, které můžete bezpečně uložit heslo klienta (tajný klíč). Mohou prokázat, že požadavky přicházejí od aplikace zabezpečené klienta a nikoli z škodlivý objektu actor. Například webová aplikace je důvěrné klienta, protože tajný klíč klienta může ukládat na webovém serveru. Nevystavené. Protože tyto toky jsou bezpečnější, je výchozí doby platnosti tokenů aktualizace vydané tyto toky `until-revoked`, nelze změnit pomocí zásad a nebude na resetování hesla dobrovolná odvolán.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Token životnosti s tokeny obnovení veřejné klienta

Veřejné klientů nelze bezpečně uložit heslo klienta (tajný klíč). Aplikace pro iOS nebo Android nelze například obfuskováním tajný klíč z vlastníka prostředku, bude považován za veřejné klienta. Nastavení zásad pro prostředky zabrání získat nový pár tokenu přístupu nebo aktualizace tokeny obnovení z veřejné klientů starší než během zadaného období. (K tomu použít vlastnost aktualizovat Token maximální neaktivní doba (`MaxInactiveTime`).) Zásady můžete používat i nastavit dobu, jejichž překročení se už přijata obnovovacích tokenů. (K tomu použijte vlastnost aktualizovat Token maximální stáří.) Můžete upravit dobu životnosti tokenu obnovení řídit, kdy a jak často je potřeba zadat přihlašovací údaje, místo se bezobslužně k novému ověření, při použití veřejných klientskou aplikaci uživatele.

### <a name="id-tokens"></a>ID tokeny
ID tokeny jsou předány weby a nativních klientů. ID tokeny obsahovat profil informací o uživateli. ID token je vázána na konkrétní kombinaci uživatele a klienta. ID tokeny považovány za platný až do vypršení jejich platnosti. Obvykle aplikace webového odpovídá uživatele je vydán dobu platnosti relace v aplikaci na dobu životnosti tokenu ID pro uživatele. Můžete upravit dobu životnosti tokenu ID řídit, jak často webové aplikace skončí relace aplikace a jak často se vyžaduje, aby uživatel nové ověření vyžadováno s Azure AD (bezobslužná nebo interaktivní).

### <a name="single-sign-on-session-tokens"></a>Tokeny jedné relace přihlášení
Když se uživatel ověřuje s Azure AD, se relaci přihlašování (SSO) naváže prohlížeče uživatele a Azure AD. Token jednotného přihlašování, ve formátu souboru cookie, představuje tuto relaci. Všimněte si, že token relace jednotného přihlašování není vázána na konkrétní prostředek nebo klientskou aplikaci. Tokeny relace jednotného přihlašování se dají odvolávat a jejich platnost kontroluje pokaždé, když se používají.

Azure AD používá dva typy tokenů relace jednotného přihlašování: trvalé a zajišťováno. Trvalé relace tokeny jsou uloženy jako trvalé soubory cookie v prohlížeči. Tokeny zajišťováno relace jsou uloženy jako soubory cookie relace. (Souborů cookie relací jsou zničen při zavření prohlížeče.) Obvykle je uložena token zajišťováno relace. Ale když uživatel vybere **zůstat přihlášeni** políčko při ověřování tokenu trvalé relace je uložen.

Zajišťováno relace tokeny mají životnost 24 hodin. Trvalé tokeny mají životnost 180 dní. Token relace jednotného přihlašování se používá v rozsahu období platnosti, když je platnosti prodloužit jiný 24 hodin nebo 180 dnů, v závislosti na typ tokenu. Pokud token relace jednotného přihlašování se nepoužívá v rozsahu období platnosti, bude považován za platnost a již byla přijata.

Zásady můžete nastavit dobu, po první relaci token vydán nad kterým je token relace již povolena. (K tomu použijte vlastnost relace tokenu maximální stáří.) Můžete upravit dobu životnosti tokenu relace řídit, kdy a jak často je uživatel muset zadat přihlašovací údaje, místo bezobslužně ověřovaného, pokud používáte webovou aplikaci.

### <a name="token-lifetime-policy-properties"></a>Vlastnosti zásad životnost tokenu
Životnost tokenu zásad je typ objektu zásad, který obsahuje pravidla životnost tokenu. Pomocí vlastností zásad pro řízení zadaný token životnosti. Pokud je nastavené žádné zásady, systém vynucuje výchozí hodnota doby platnosti.

### <a name="configurable-token-lifetime-properties"></a>Vlastnosti konfigurovat životnost tokenu
| Vlastnost | Řetězec vlastnosti zásad | Ovlivňuje | Výchozí | Minimální | Maximum |
| --- | --- | --- | --- | --- | --- |
| Životnost tokenu přístupu |AccessTokenLifetime |Přístupové tokeny, tokeny typu ID, typu SAML2 tokeny |1 hodina |10 minut |1 den |
| Aktualizace tokenu maximální doba neaktivní |MaxInactiveTime |Obnovovacích tokenů |90 dnů |10 minut |90 dnů |
| Single-Factor aktualizace tokenu maximální stáří |MaxAgeSingleFactor |Obnovovacích tokenů (pro všechny uživatele) |Dokud odvolat |10 minut |Dokud odvolat<sup>1</sup> |
| Maximální stáří tokenu Multi-Factor aktualizace |MaxAgeMultiFactor |Obnovovacích tokenů (pro všechny uživatele) |Dokud odvolat |10 minut |Dokud odvolat<sup>1</sup> |
| Maximální stáří tokenu Single-Factor relace |MaxAgeSessionSingleFactor<sup>2</sup> |Tokeny relace (trvalá nebo zajišťováno) |Dokud odvolat |10 minut |Dokud odvolat<sup>1</sup> |
| Maximální stáří tokenu Multi-Factor relace |MaxAgeSessionMultiFactor<sup>3</sup> |Tokeny relace (trvalá nebo zajišťováno) |Dokud odvolat |10 minut |Dokud odvolat<sup>1</sup> |

* <sup>1</sup>365 dnů je maximální délku explicitní, které lze nastavit pro tyto atributy.
* <sup>2</sup>Pokud **MaxAgeSessionSingleFactor** není nastaven, má tato hodnota **MaxAgeSingleFactor** hodnotu. Pokud ani parametr je nastaven, vlastnost přijímá výchozí hodnota (dokud odvolat).
* <sup>3</sup>Pokud **MaxAgeSessionMultiFactor** není nastaven, má tato hodnota **MaxAgeMultiFactor** hodnotu. Pokud ani parametr je nastaven, vlastnost přijímá výchozí hodnota (dokud odvolat).

### <a name="exceptions"></a>Výjimky
| Vlastnost | Ovlivňuje | Výchozí |
| --- | --- | --- |
| Aktualizace tokenu maximální stáří (vydán pro federované uživatele, kteří mají dostatečná odvolání informace<sup>1</sup>) |Obnovovacích tokenů (vydán pro federované uživatele, kteří mají dostatečná odvolání informace<sup>1</sup>) |12 hodin |
| Aktualizace tokenu neaktivní doba Max (vydán pro důvěrné klientů) |Obnovovacích tokenů (vydán pro důvěrné klientů) |90 dnů |
| Aktualizace tokenu maximální stáří (vydán pro důvěrné klientů) |Obnovovacích tokenů (vydán pro důvěrné klientů) |Dokud odvolat |

* <sup>1</sup>federovaný uživatelé, kteří mají dostatečná odvolání informace zahrnují všechny uživatele, kteří nemají atribut "LastPasswordChangeTimestamp" synchronizované. Tito uživatelé mají Tento krátký maximální stáří, protože AAD se nepodařilo ověřit při zrušení tokeny, které jsou svázané s původním pověření (například hesla, která byla změněna) a musí v další často se ujistit, že uživatel a přidružené tokeny stále dobrý stojící zkontrolovat. Správci klientů ke zlepšení toto prostředí, musíte zajistit, že probíhá synchronizace. atribut "LastPasswordChangeTimestamp" (to je možné nastavit v objektu user pomocí prostředí Powershell nebo prostřednictvím nebude služba AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Vyhodnocení zásad a stanovení priorit
Můžete vytvořit a pak mu přiřaďte zásadu životnost tokenu na konkrétní aplikaci, pro vaši organizaci a objekty služby. Víc zásad mohou vztahovat na konkrétní aplikaci. Životnost tokenu zásadu, projeví se řídí následujícími pravidly:

* Pokud zásady je explicitně přiřazen k objektu služby, se vynutí.
* Pokud žádné zásady explicitně přiřazeny k objektu zabezpečení služby, je vyžadována zásadu explicitně přiřazená k organizaci nadřazeného objektu služby.
* Pokud objekt služby nebo organizace, není explicitně přiřazena žádná zásada, je vyžadována u zásady přiřazené k aplikaci.
* Pokud žádné zásady byl přiřazen objekt služby, organizace nebo objekt aplikace, je vyžadována výchozí hodnoty. (Podívejte se na tabulku v [konfigurovat životnost tokenu vlastnosti](#configurable-token-lifetime-properties).)

Další informace o vztah mezi objekty aplikací a hlavní objekty služby najdete v tématu [aplikace a služby hlavní objekty ve službě Azure Active Directory](active-directory-application-objects.md).

V době, kdy se používá token je vyhodnocena token platnosti. Zásada s nejvyšší prioritou na aplikaci, která je přistupuje projeví.

Všechny timespans použít se zde jsou formátovaného podle jazyka C# [časový interval](https://msdn.microsoft.com/library/system.timespan) objekt - D.HH:MM:SS.  Tak bude 80 dní až 30 minut `80.00:30:00`.  Úvodní D můžete vyřadit, pokud nula, takže 90 minut by `00:90:00`.  

> [!NOTE]
> Tady je příklad scénáře.
>
> Uživatel požaduje přístup k dva webové aplikace: webové aplikace A a B. webové aplikace
> 
> Faktory:
> * Obě webové aplikace jsou ve stejné organizaci nadřazené.
> * Token 1 zásad životního cyklu relace tokenu maximální stáří osm hodin je nastaven jako výchozí nadřazené organizace.
> * Webové aplikace A je použití regular webovou aplikaci a není propojený s žádné zásady.
> * Webovou aplikaci B se používá pro vysoce citlivých procesů. Jeho instanční objekt se propojí tokenu 2 zásad životního cyklu, které má relace tokenu maximální stáří 30 minut.
>
> Na 12:00 PM uživatel spustí novou relaci prohlížeče a pokusí o přístup k webové aplikaci A. Uživatel se přesměruje na Azure AD a se zobrazí výzva k přihlášení. Tím se vytvoří soubor cookie, který obsahuje token relace v prohlížeči. Uživatel je přesměrován zpět na webovou aplikaci A k tokenu ID, která umožňuje uživatelům přístup k aplikaci.
>
> Ve 12:15 se uživatel pokusí o přístup k webové aplikaci B. Prohlížeč přesměruje do služby Azure AD, který zjistí soubor cookie relace. Objekt B aplikace webové služby je propojený s tokenu 2 zásad životního cyklu, ale je také součástí nadřazené organizace s výchozí Token 1 zásad životního cyklu. Tokenu 2 zásad životního cyklu začne platit, protože zásady připojené k objekty služby mají vyšší prioritu než výchozích zásad organizace. Token relace původně vydán v posledních 30 minut, takže bude považován za platný. Uživatel je přesměrován zpět na webovou aplikaci B k tokenu ID, která jim udělí přístup.
>
> : 00: 00 uživatel pokusí o přístup k webové aplikaci A. Uživatel se přesměruje do služby Azure AD. Webové aplikace A není propojený s žádnými zásadami, ale protože je v organizaci s výchozí Token 1 zásad životního cyklu, tyto zásady projeví. Je zjištěna souboru cookie relace, který byl původně vydán během posledních 8 hodin. Uživatel je bezobslužně přesměrován zpět do webové aplikace A s ID nový token. Uživatel není vyžadován pro ověření.
>
> Okamžitě následně uživatel pokusí o přístup k webové aplikaci B. Uživatel se přesměruje do služby Azure AD. Jako dříve, tokenu 2 životnost zásad se projeví. Vzhledem k tomu, že byl token vydán víc než 30 minutami, bude uživatel vyzván k znovu zadejte své přihlašovací údaje. Jsou vydávány ID token a relace zcela nový token. Uživatele můžete poté přistoupit B. webové aplikace
>
>

## <a name="configurable-policy-property-details"></a>Podrobnosti vlastnost konfigurovat zásady
### <a name="access-token-lifetime"></a>Životnost tokenu přístupu
**Řetězec:** AccessTokenLifetime

**Ovlivňuje:** přístupové tokeny, tokeny typu ID

**Souhrn:** tato zásada určuje, jak dlouho přístup a tokeny typu ID pro tento prostředek jsou považovány za platné. Snižuje dobu životnosti tokenu přístupu vlastnost snižuje riziko přístupového tokenu nebo ID token používá škodlivý objektu actor pro delší dobu. (Tyto tokeny nelze odvolat.) O kompromisu je nepříznivě ovlivňuje výkon, protože tokeny vyměnit častěji.

### <a name="refresh-token-max-inactive-time"></a>Aktualizace tokenu maximální doba neaktivní
**Řetězec:** MaxInactiveTime

**Ovlivňuje:** obnovovacích tokenů

**Souhrn:** tato zásada určuje, kolik token obnovení může být než klienta můžete už použít k načtení nový pár tokenu přístupu nebo aktualizace, při pokusu o přístup k tomuto prostředku. Vzhledem k tomu, že nový token obnovení je obvykle vrácena, pokud se používá token obnovení, zabraňuje tato zásada přístupu, pokud se klient pokusí o přístup k jakémukoli prostředku pomocí aktuální obnovovací token během zadaném časovém období.

Tato zásada vynutí uživatelů, kteří nebyly aktivní na jejich klienta k novému ověření, k načtení nový token obnovení.

Vlastnost aktualizovat Token maximální neaktivní doba musí být nastavená na hodnotu nižší než maximální stáří tokenu Single-Factor a vlastnosti Multi-Factor aktualizovat Token maximální stáří.

### <a name="single-factor-refresh-token-max-age"></a>Single-Factor aktualizace tokenu maximální stáří
**Řetězec:** MaxAgeSingleFactor

**Ovlivňuje:** obnovovacích tokenů

**Souhrn:** této zásady ovládací prvky, jak dlouho může uživatel používat token obnovení získat nový pár tokenu přístupu nebo obnovení po jejich poslední úspěšně ověření pomocí pouze jeden faktor. Jakmile je uživatel ověří a obdrží token nové aktualizace, uživatel může použít tok tokenu obnovení pro zadané časové období. (To platí, dokud, aktuální token obnovení nebude odvolaný, a není zbývající nepoužité po dobu delší dobu, než je neaktivní.) V tomto bodě že uživatel musí k novému ověření pro příjem nové obnovovací token.

Snižuje maximální stáří vynutí se tak uživatelé mohli ověřit častěji. Protože jedním Multi-Factor authentication je považován za méně bezpečné než vícefaktorového ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo menší než vlastnost Multi-Factor aktualizovat Token maximální stáří.

### <a name="multi-factor-refresh-token-max-age"></a>Maximální stáří tokenu Multi-Factor aktualizace
**Řetězec:** MaxAgeMultiFactor

**Ovlivňuje:** obnovovacích tokenů

**Souhrn:** této zásady ovládací prvky, jak dlouho může uživatel používat token obnovení získat nový pár tokenu přístupu nebo obnovení po jejich poslední úspěšně ověření pomocí několika faktory. Jakmile je uživatel ověří a obdrží token nové aktualizace, uživatel může použít tok tokenu obnovení pro zadané časové období. (To je hodnota true, dokud nebude odvolaný aktuální obnovovací token a není po dobu delší než neaktivní doba.) V tomto okamžiku jsou uživatelé přinucení k novému ověření pro příjem nové obnovovací token.

Snižuje maximální stáří vynutí se tak uživatelé mohli ověřit častěji. Protože jedním Multi-Factor authentication je považován za méně bezpečné než vícefaktorového ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo větší než vlastnost Single-Factor aktualizovat Token maximální stáří.

### <a name="single-factor-session-token-max-age"></a>Maximální stáří tokenu Single-Factor relace
**Řetězec:** MaxAgeSessionSingleFactor

**Ovlivňuje:** tokeny relace (trvalá nebo zajišťováno)

**Souhrn:** této zásady ovládací prvky, jak dlouho může uživatel používat token relace získat nové ID a token relace po jejich poslední úspěšně ověření pomocí pouze jeden faktor. Jakmile je uživatel ověří a obdrží token novou relaci, uživatel může použít tok tokenu relace pro zadané časové období. (To platí, dokud, aktuální relace tokenu není odvolaný a jestli nevypršela platnost.) Po zadaném časovém období že uživatel musí k novému ověření, přijmout token novou relaci.

Snižuje maximální stáří vynutí se tak uživatelé mohli ověřit častěji. Protože jedním Multi-Factor authentication je považován za méně bezpečné než vícefaktorového ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo menší než vlastnost Multi-Factor relace tokenu maximální stáří.

### <a name="multi-factor-session-token-max-age"></a>Maximální stáří tokenu Multi-Factor relace
**Řetězec:** MaxAgeSessionMultiFactor

**Ovlivňuje:** tokeny relace (trvalá nebo zajišťováno)

**Souhrn:** této zásady ovládací prvky jak dlouho může uživatel používat token relace se získat od poslední úspěšně musí ověřit pomocí několika faktory nové ID a relace token. Jakmile je uživatel ověří a obdrží token novou relaci, uživatel může použít tok tokenu relace pro zadané časové období. (To platí, dokud, aktuální relace tokenu není odvolaný a jestli nevypršela platnost.) Po zadaném časovém období že uživatel musí k novému ověření, přijmout token novou relaci.

Snižuje maximální stáří vynutí se tak uživatelé mohli ověřit častěji. Protože jedním Multi-Factor authentication je považován za méně bezpečné než vícefaktorového ověřování, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo větší než vlastnost Single-Factor relace tokenu maximální stáří.

## <a name="example-token-lifetime-policies"></a>Příkladem zásad životnost tokenu
Mnoho scénářů je možné ve službě Azure AD při vytváření a správa životnosti tokenu pro aplikace, objekty služby a celkové organizaci. V této části jsme provede několik běžných scénářů zásady, které můžete použít nové pravidel pro:

* Životnost tokenu
* Token maximální doba neaktivní
* Maximální stáří tokenu

V příkladech dozvíte, jak:

* Správa organizace výchozí zásady
* Vytvoření zásady pro přihlášení k webové
* Vytvořit zásadu pro nativní aplikaci, která volá webové rozhraní API
* Spravovat pokročilé zásady

### <a name="prerequisites"></a>Požadavky
V následujících příkladech vytvářet, aktualizovat, propojení a odstranit zásady pro aplikace, objekty služby a celkové organizaci. Pokud jste ještě Azure AD, doporučujeme Další informace o [jak získat klienta Azure AD](active-directory-howto-tenant.md) předtím, než budete pokračovat v těchto příkladech.  

Chcete-li začít, proveďte následující kroky:

1. Stáhněte si nejnovější [Azure AD PowerShell modulu verze Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
2. Spustit `Connect` příkaz pro přihlášení k účtu správce služby Azure AD. Spusťte tento příkaz pokaždé, když zahájit novou relaci.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Pokud chcete zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Tento příkaz spusťte po většinu operací v následujících scénářích. Spuštěním příkazu také vám pomůže získat ** ** zásad.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Příklad: Správa organizace výchozí zásady
V tomto příkladu vytvoříte zásadu, která umožňuje uživatelům přihlásit se méně často v rámci celé organizace. K tomuto účelu vytvořte zásady životnost tokenu pro Single-Factor aktualizovat tokeny, které se použije v organizaci. Zásady se použijí na každou aplikaci ve vaší organizaci a pro všechny hlavní služby, který ještě nemá zásady nastavené.

1. Vytvořte zásadu životnost tokenu.

    1.  Sada aktualizace Single-Factor Token, který má "dokud odvolaný." Token nevyprší, dokud je odvolat přístup. Vytvořte následující definice zásady:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Pokud chcete vytvořit zásadu, spusťte následující příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Chcete zobrazit nové zásady a získat nastavení zásad **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Aktualizujte zásady.

    Můžete rozhodnout, že na první zásadu, kterou můžete nastavit v tomto příkladu není striktní, protože vaše služba vyžaduje. Pokud chcete nastavit vaše Single-Factor aktualizace tokenu vyprší za dva dny, spusťte následující příkaz:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Příklad: Vytvoření zásady pro přihlášení k webové

V tomto příkladu vytvoříte zásadu, která vyžaduje, aby uživatelé ověření častěji ve vaší webové aplikaci. Tato zásada nastaví dobu platnosti tokenů přístupu nebo ID a maximální stáří token relace Multi-Factor instanční objekt vaší webové aplikace.

1. Vytvořte zásadu životnost tokenu.

    Tato zásada pro webové přihlášení, nastaví přístup nebo ID životnost tokenu a stáří tokenu maximální single-factor relace do dvou hodin.

    1.  Chcete-li vytvořit zásadu, spusťte tento příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Chcete zobrazit nové zásady a získání zásady **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Přiřaďte zásady instančního objektu. Je také nutné získat **ObjectId** z instanční objekt. 

    1.  Pokud chcete zobrazit všechna firemní objekty služby, se můžete dotazovat buď [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) nebo [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Navíc můžete otestovat v [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/)a [program Průzkumník systému Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) pomocí účtu Azure AD.

    2.  Pokud máte **ObjectId** vaše služba objektu zabezpečení, spusťte následující příkaz:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Příklad: Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API
V tomto příkladu vytvoříte zásadu, která vyžaduje, aby uživatelé ověření méně často. Zásady také prodlouží dobu, kterou uživatel může být neaktivní, než uživatel musí novému ověření. Zásady se použijí pro webové rozhraní API. Pokud nativní aplikaci požádá o webové rozhraní API jako prostředek, je tato zásada použitá.

1. Vytvořte zásadu životnost tokenu.

    1.  Pokud chcete vytvořit zásadu striktní pro webové rozhraní API, spusťte následující příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Chcete zobrazit nové zásady a získání zásady **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Přiřaďte zásady webového rozhraní API. Je také nutné získat **ObjectId** vaší aplikace. Nejlepší způsob, jak najít vaší aplikace **ObjectId** je použití [portál Azure](https://portal.azure.com/).

   Pokud máte **ObjectId** vaší aplikace, spusťte následující příkaz:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Příklad: Správa pokročilé zásady
V tomto příkladu můžete vytvořit několik zásady, se dozvíte, jak funguje s prioritou systému. Rovněž můžete naučit ke správě víc zásad, které jsou použity na více objektů.

1. Vytvořte zásadu životnost tokenu.

    1.  Pokud chcete vytvořit výchozí zásadu organizace, která nastaví dobu životnosti tokenu aktualizovat Single-Factor na 30 dní, spusťte následující příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Chcete zobrazit nové zásady a získat nastavení zásad **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Přiřaďte zásady instančního objektu.

    Teď máte zásadu, která platí pro celou organizaci. Můžete chtít zachovat tuto zásadu 30denní pro objekt konkrétní služby, ale změnit výchozí zásady organizace na horní limit počtu "dokud odvolaný."

    1.  Pokud chcete zobrazit všechna firemní objekty služby, se můžete dotazovat buď [Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) nebo [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Navíc můžete otestovat v [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/)a [program Průzkumník systému Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) pomocí účtu Azure AD.

    2.  Pokud máte **ObjectId** vaše služba objektu zabezpečení, spusťte následující příkaz:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Nastavte `IsOrganizationDefault` příznak na hodnotu false:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Vytvořte novou zásadu výchozí organizace:

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Nyní máte původní zásadou propojené s instanční objekt a nové zásady nastaven jako výchozí zásady vaší organizace. Je důležité si pamatovat, že zásady uplatněné na objekty služby mají přednost před výchozí zásady organizace.

## <a name="cmdlet-reference"></a>Reference k rutinám

### <a name="manage-policies"></a>Správa zásad

Následující rutiny můžete použít ke správě zásad.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Vytvoří novou zásadu.

```PowerShell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Pole stringified formátu JSON, který obsahuje všechny zásady pravidla. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Řetězec název zásady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |V případě hodnoty true, nastaví jako výchozí zásady organizace zásady. Pokud je hodnota false, neprovede žádnou akci. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zásad. Pro token životnosti vždy používejte "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Nepovinné] |Nastaví alternativní ID pro tuto zásadu. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Získá všechny zásady služby Azure AD nebo zadanou zásadu.

```PowerShell
Get-AzureADPolicy
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> [Nepovinné] |**ObjectId (Id)** chcete zásad. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Získá všechny aplikace a objekty služby, které jsou propojeny s zásadu.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** chcete zásad. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualizuje existující zásady.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** chcete zásad. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Řetězec název zásady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Nepovinné] |Pole stringified formátu JSON, který obsahuje všechny zásady pravidla. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Nepovinné] |V případě hodnoty true, nastaví jako výchozí zásady organizace zásady. Pokud je hodnota false, neprovede žádnou akci. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Nepovinné] |Typ zásad. Pro token životnosti vždy používejte "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Nepovinné] |Nastaví alternativní ID pro tuto zásadu. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Odstraní zadanou zásadu.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** chcete zásad. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zásady aplikací
Následující rutiny můžete použít pro zásady aplikací.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Přidat AzureADApplicationPolicy
Odkazy zadanou zásadu k aplikaci.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** zásad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Získá zásady, která je přiřazena k aplikaci.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** aplikace. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Odebere zásadu z aplikace.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** zásad. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zásady instančních objektů
Následující rutiny můžete použít pro hlavní zásady služby.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Zadaná zásada odkazuje na objekt služby.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** zásad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Získá všechny zásady pro propojený objekt zadaná služba.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** aplikace. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Odebere zásadu ze zadané instanční objekt.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** zásad. | `-PolicyId <ObjectId of Policy>` |

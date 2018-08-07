---
title: Konfigurovatelné životností tokenů ve službě Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak nastavit dobu života pro tokeny vystavené službou Azure AD.
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
ms.openlocfilehash: 0c4edb4fbf7271331affb2559018e53480aa7a85
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39577158"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Konfigurovatelné životností tokenů ve službě Azure Active Directory (Public Preview)
Můžete určit dobu životnosti tokenu vydaného službou Azure Active Directory (Azure AD). Můžete nastavit Životnost tokenů pro všechny aplikace ve vaší organizaci, aplikaci s více tenanty (více organizace) nebo konkrétní objekt služby ve vaší organizaci.

> [!IMPORTANT]
> Po sluchu od zákazníků ve verzi preview, plánujeme tato funkce nahraďte novou funkci v Azure Active Directory podmíněného přístupu.  Po dokončení nové funkce se tato funkce nakonec přestanou po určité době oznámení.  Pokud používáte zásady konfigurovat doba platnosti tokenu, buďte připraveni přepnout na novou funkci podmíněný přístup, až bude k dispozici. 
>
>

Objekt zásad ve službě Azure AD, představuje sadu pravidel, která vynucují u jednotlivých aplikací, nebo na všechny aplikace v organizaci. Každý typ zásad obsahuje strukturu jedinečnou sadu vlastností, které se použijí pro objekty, které jsou přiřazeny.

Zásady můžete určit jako výchozí zásady pro vaši organizaci. Zásady platí pro všechny aplikace v organizaci, tak dlouho, dokud není přepsán zásadu s vyšší prioritou. Také můžete přiřadit zásady pro konkrétní aplikace. Pořadí podle priority se liší podle typu zásad.

> [!NOTE]
> Zásada konfigurovatelné životnost tokenu není podporována pro SharePoint Online.  I když máte možnost vytvořit tyto zásady pomocí Powershellu, nebude SharePoint Online potvrdit tuto zásadu. Odkazovat [Sharepointu Online blogu](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) Další informace o konfiguraci vypršení časového limitu nečinnosti relace.
>* Výchozí doba života pro SharePoint Online přístupový token je 1 hodina. 
>* Výchozí maximální neaktivní doba z Sharepointu Online obnovovací token je 90 dní.
>

## <a name="token-types"></a>Typy tokenů

Můžete nastavit zásady životnosti tokenu pro obnovovací tokeny, přístupové tokeny, relace tokeny a ID tokenů.

### <a name="access-tokens"></a>Přístupové tokeny
Klienti používají přístupové tokeny pro přístup k chráněnému prostředku. Přístupový token lze použít pouze pro konkrétní kombinaci uživatelů, klienta a prostředků. Přístupové tokeny se nedá odvolat a platí až do vypršení jejich platnosti. Škodlivý objekt actor, který se má získat přístupový token lze použít pro rozsah svého životního cyklu. Úprava životnost přístupového tokenu je kompromis mezi zlepšuje výkon systému a zvýšení množství času, klient zůstane přístup po zakázání účtu uživatele. Vylepšení výkonu. dosáhnete tím, že snižuje počet, kolikrát klient potřebuje získat nový přístupový token.  Výchozí hodnota je 1 hodina - po 1 hodině, klient musí použít obnovovací token (obvykle bezobslužná) získání nového tokenu obnovení a přístup k tokenu. 

### <a name="refresh-tokens"></a>Obnovovacích tokenů
Když klient získá přístupový token pro přístup k chráněnému prostředku, obdrží klient také obnovovací token. Obnovovací token slouží k získání nového přístupu nebo obnovení tokenu páry když vyprší platnost aktuálního tokenu přístupu. Token obnovení je vázán na uživatele i klienta. Obnovovací token dá [kdykoli odvolat](develop/v1-id-and-access-tokens.md#token-revocation), a kontroluje platnost tokenu pokaždé, když se používá token.  

Je důležité rozlišovat mezi důvěrní klienti a veřejné klienty, protože to ovlivňuje, jak dlouho je možné obnovovací tokeny. Další informace o různých typů klientů najdete v tématu [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Životnost tokenů s důvěrnému klientovi obnovovacích tokenů
Důvěrní klienti jsou aplikace, které můžete bezpečně ukládat hesla klienta (tajný klíč). Mohou prokázat, že požadavky pocházejí z aplikace zabezpečené klienta a nikoli z škodlivý objekt actor. Například webové aplikace je důvěrnému klientovi, protože ho uložíte tajný klíč klienta na webovém serveru. Není dostupná. Vzhledem k tomu, že tyto toky jsou bezpečnější, je výchozí dobu života obnovovací tokeny vydané pro tyto toky `until-revoked`, nelze změnit pomocí zásad a se neodvolají na resetování hesla dobrovolná.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Životnost tokenů s veřejným klientem obnovovacích tokenů

Veřejní klienti nemohou bezpečně uložit heslo klienta (tajný klíč). Aplikace iOS/Android nelze například obfuskaci tajného kódu od vlastníka prostředku, bude považován za veřejným klientem. Nastavení zásad pro prostředky a zabránit tak tokeny obnovení z veřejní klienti starší než během zadaného období získat nový token pár přístupu nebo obnovení. (K tomu použít vlastnost aktualizovat Token maximální neaktivní čas (`MaxInactiveTime`).) Zásady můžete použít také k nastavení období, jejichž překročení se už přijímají tokeny obnovení. (K tomu použijte vlastnost aktualizovat Token maximální stáří.) Můžete upravit dobu života obnovovací token řídit, kdy a jak často uživatel musí znovu zadat přihlašovací údaje místo se bez upozornění znovu neověřuje, při použití aplikace veřejným klientem.

### <a name="id-tokens"></a>Tokeny typu ID
ID tokeny jsou předány do webů a nativní klienty. Tokeny typu ID obsahovat profil informací o uživateli. ID tokenu je vázán na konkrétní kombinaci klienta a uživatele. Tokeny typu ID považovány za platný až do vypršení jejich platnosti. Obvykle, webové aplikace odpovídá uživatele pro uživatele vydané životnost relace v aplikaci na životnost tokenu ID. Můžete nastavit životnost tokenu ID řídit, jak často webové aplikace vyprší platnost relace aplikace a jak často vyžaduje uživatelům ověřit pomocí služby Azure AD (tiše nebo interaktivní).

### <a name="single-sign-on-session-tokens"></a>Tokeny relace přihlášení
Při ověření uživatele službou Azure AD, je navázat relaci přihlašování (SSO) s prohlížeči a Azure AD daného uživatele. Token jednotného přihlašování, ve formě souboru cookie, představuje tuto relaci. Všimněte si, že token relace jednotného přihlašování není vázán na konkrétní prostředek/klientské aplikace. Možné odvolat tokeny relace jednotného přihlašování a jejich platnost proběhne pokaždé, když se používají.

Používá dva typy tokenů relace jednotného přihlašování Azure AD: nonpersistent a trvalé. Trvalé relace tokeny jsou uloženy jako trvalé soubory cookie v prohlížeči. Tokeny nonpersistent relace jsou uloženy jako soubory cookie relace. (Souborů cookie relací jsou zničeny při zavření prohlížeče.) Obvykle je uložena token nonpersistent relace. Ale když uživatel vybere **zůstat přihlášeni** uložené zaškrtávací políčko při ověřování tokenu relace.

Zajišťováno relace tokeny mají životnost 24 hodin. Trvalé tokeny mají životnost 180 dnů. Pokaždé, když se používá token relace jednotného přihlašování v období své platnosti doby platnosti je rozšířeno jiného 24 hodin nebo 180 dní, v závislosti na typ tokenu. Pokud není v období své platnosti tokenu relace jednotného přihlašování, bude považován za vypršela platnost a již přijali.

Zásady můžete nastavit dobu, po prvním tokenu relace byl vydán nad rámec, který je token relace už přijat. (K tomu použijte vlastnost maximální stáří tokenu relace.) Můžete upravit životnost tokenu relace řídit, kdy a jak často je potřeba znovu zadat přihlašovací údaje místo tiše ověřovaného, při použití webovou aplikaci uživatele.

### <a name="token-lifetime-policy-properties"></a>Vlastnosti zásad životnost tokenu
Zásady životnosti tokenu je typ objektu zásad, která obsahuje dobu životnosti tokenu pravidla. Pomocí vlastností zásady můžete řídit zadané životností tokenů. Pokud nejsou nastavené žádné zásady, vynutí systém výchozí hodnotu doby života.

### <a name="configurable-token-lifetime-properties"></a>Vlastnosti konfigurovatelné doby platnosti tokenu
| Vlastnost | Řetězec vlastnosti zásad | Ovlivňuje | Výchozí | Minimální | Maximum |
| --- | --- | --- | --- | --- | --- |
| Životnost tokenu přístupu |AccessTokenLifetime |Přístupové tokeny, tokeny typu ID, SAML2 tokeny |1 hodina |10 minut |1 den |
| Neaktivní Token maximální čas aktualizace |MaxInactiveTime |Obnovovacích tokenů |90 dnů |10 minut |90 dnů |
| Jednofaktorovému obnovovací Token maximální stáří |MaxAgeSingleFactor |Obnovovacích tokenů (pro všechny uživatele) |Dokud odvolána |10 minut |Dokud odvolat<sup>1</sup> |
| Maximální stáří Token obnovení služby Multi-Factor Authentication |MaxAgeMultiFactor |Obnovovacích tokenů (pro všechny uživatele) |Dokud odvolána |10 minut |Dokud odvolat<sup>1</sup> |
| Maximální stáří Token Jednofaktorovému relace |MaxAgeSessionSingleFactor<sup>2</sup> |Relace tokeny (trvalá nebo nonpersistent) |Dokud odvolána |10 minut |Dokud odvolat<sup>1</sup> |
| Maximální stáří tokenu relace služby Multi-Factor Authentication |MaxAgeSessionMultiFactor<sup>3</sup> |Relace tokeny (trvalá nebo nonpersistent) |Dokud odvolána |10 minut |Dokud odvolat<sup>1</sup> |

* <sup>1</sup>365 dnů je delší explicitní lze nastavit pro tyto atributy.
* <sup>2</sup>Pokud **MaxAgeSessionSingleFactor** není nastaven, používá tuto hodnotu **MaxAgeSingleFactor** hodnotu. Pokud ani parametr je nastaven, vlastnost má výchozí hodnotu (až do odvolaný).
* <sup>3</sup>Pokud **MaxAgeSessionMultiFactor** není nastaven, používá tuto hodnotu **MaxAgeMultiFactor** hodnotu. Pokud ani parametr je nastaven, vlastnost má výchozí hodnotu (až do odvolaný).

### <a name="exceptions"></a>Výjimky
| Vlastnost | Ovlivňuje | Výchozí |
| --- | --- | --- |
| Aktualizovat Token maximální stáří (vydané pro federované uživatele, kteří mají informace o odvolání nedostatečná<sup>1</sup>) |Obnovovacích tokenů (vydané pro federované uživatele, kteří mají informace o odvolání nedostatečná<sup>1</sup>) |12 hodin |
| Aktualizovat Token maximální aktivní čas (vydané pro důvěrní klienti) |Obnovovacích tokenů (vydané pro důvěrní klienti) |90 dnů |
| Aktualizovat Token maximální stáří (vydané pro důvěrní klienti) |Obnovovacích tokenů (vydané pro důvěrní klienti) |Dokud odvolána |

* <sup>1</sup>federativní, kteří mají dostatečná odvolání informace zahrnují všechny uživatele, kteří nemají "LastPasswordChangeTimestamp" atribut synchronizovaný. Tito uživatelé jsou uvedeny Tento krátký maximální stáří, protože nelze ověřit, kdy se mají odvolat tokeny, které jsou vázané na staré přihlašovací údaje (třeba hesla, které se změnily) a musí vrátit se změnami zpět více často zajistit stále existují uživateli a přidružené tokenů AAD v pořádku  po ruce. Správci tenanta na zdokonalování tohoto prostředí, musíte zajistit, že se synchronizují atribut "LastPasswordChangeTimestamp" (to je možné nastavit v objektu user pomocí Powershellu nebo prostřednictvím AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Vyhodnocení zásad a stanovení priorit
Můžete vytvořit a přiřaďte zásady životnosti tokenu na konkrétní aplikaci, organizaci a instanční objekty. Více zásad může být platí pro konkrétní aplikaci. Zásady životnosti tokenu, projeví se řídí následujícími pravidly:

* V případě, že instančnímu objektu služby je explicitně přiřazené zásady, se nevynutí.
* V případě, že instančnímu objektu služby není explicitně přiřazena žádná zásada, zásady explicitně přiřazeny mateřské organizace instančního objektu se nevynutí.
* V případě, že instančnímu objektu služby nebo organizace, není explicitně přiřazena žádná zásada, přiřazené k aplikaci zásady se nevynutí.
* Pokud žádná zásada byla přiřazena k objektu služby, organizace nebo objekt aplikace, je vyžadována výchozí hodnoty. (Viz tabulka [vlastnosti konfigurovatelné doby platnosti tokenu](#configurable-token-lifetime-properties).)

Další informace o vztahu mezi objekty aplikací a instančních objektů najdete v tématu [aplikace a instanční objekty v Azure Active Directory](develop/app-objects-and-service-principals.md).

V době, kdy se používá token, který je vyhodnocen token platnosti. Zásada s nejvyšší prioritou v aplikaci, která se právě využívají se projeví.

Všechny časových rozpětí tady použít se formátují podle jazyka C# [TimeSpan](https://msdn.microsoft.com/library/system.timespan) objekt - D.HH:MM:SS.  Takže 80 dnů a 30 minut bude `80.00:30:00`.  Nejlepší D můžete vyřadit, pokud je nula, proto 90 minut by se `00:90:00`.  

> [!NOTE]
> Tady je příklad scénáře.
>
> Uživatel chce, aby pro přístup k dvě webové aplikace: webové aplikace A a B. webové aplikace
> 
> Faktory:
> * Pro obě webové aplikace jsou ve stejné organizaci nadřazené.
> * Token 1 zásad životního cyklu se relace Token maximální stáří osm hodin je nastaven jako výchozí mateřské organizace.
> * Webová aplikace A je běžné použití webová aplikace a není propojený s všechny zásady.
> * Webové aplikace B se používá pro vysoce citlivé a procesy. Jeho instanční objekt se propojí s Token doby života zásad 2, který má relace Token maximální stáří 30 minut.
>
> V 12:00 PM uživatel spustí nová relace prohlížeče a pokusí o přístup k webové aplikaci A. Uživatel se přesměruje do služby Azure AD a se zobrazí výzva k přihlášení. Tím se vytvoří soubor cookie, který obsahuje token relace v prohlížeči. Uživatel je přesměrován zpět na webovou aplikaci A pomocí ID tokenu, který mu umožní přístup k aplikaci.
>
> V 12:15 PM uživatel pokusí o přístup k webové aplikaci služby serveru B. Prohlížeč přesměruje do služby Azure AD, který zjistí souboru cookie relace. Instanční objekt služby webové aplikace B je propojený s Token 2 zásady životního cyklu, ale je také součástí mateřské organizace, se výchozí Token 1 zásad životního cyklu. Token doby života zásad 2 se projeví protože zásady připojené k instanční objekty mají vyšší prioritu než výchozí zásady organizace. Tokenu relace byla původně vystavil v posledních 30 minut, takže budou považovány za platné. Uživatel je přesměrován zpět do webové aplikace B s tokenem ID, která jim uděluje přístup.
>
> V 1:00 PM uživatel pokusí o přístup k webové aplikaci A. Uživatel je přesměrován do služby Azure AD. Webové aplikace A není propojený s všechny zásady, ale protože je v organizaci s výchozí Token 1 zásad životního cyklu, tato zásada projeví. Zjistil se souboru cookie relace, který byl původně vydaný během posledních osmi hodin. Uživatel je tiše přesměrován zpět na webovou aplikaci A pomocí nového tokenu ID. Uživatel nevyžaduje ověření.
>
> Ihned poté uživatel pokusí o přístup k webové aplikaci služby serveru B. Uživatel je přesměrován do služby Azure AD. Jako dříve, Token 2 dobu života zásad se projeví. Protože byl token vydán před více než 30 minut, bude uživatel vyzván k zadejte znovu své přihlašovací údaje. ID tokenu a tokenu zcela nové relace jsou vydávány. Uživatel mohlo dostat přístup k webové aplikaci služby serveru B.
>
>

## <a name="configurable-policy-property-details"></a>Podrobnosti vlastnost Konfigurovatelné zásady
### <a name="access-token-lifetime"></a>Životnost tokenu přístupu
**Řetězec:** AccessTokenLifetime

**Ovlivňuje:** přístupové tokeny, tokeny typu ID

**Shrnutí:** tato zásada určuje, jak dlouho přístup a tokeny typu ID pro tento prostředek jsou považovány za platné. Snížení vlastnost životnost tokenu přístupu snižuje riziko přístupového tokenu nebo tokenu ID používá škodlivý objekt actor delší dobu. (Tyto tokeny se nedá odvolat,.) Nutný kompromis je nepříznivě ovlivňuje výkon, protože tokeny mají vyměnit častěji.

### <a name="refresh-token-max-inactive-time"></a>Neaktivní Token maximální čas aktualizace
**Řetězec:** MaxInactiveTime

**Ovlivňuje:** obnovovacích tokenů

**Shrnutí:** tato zásada určuje, kolik obnovovací token může být předtím, než klient může již nebudete používat k načtení nový pár tokenů přístupu nebo obnovení při pokusu o přístup k tomuto prostředku. Protože nového tokenu obnovení se obvykle vrátí, když se obnovovací token používá, zabraňuje tato zásada přístupu, pokud se klient pokusí získat přístup k jakémukoli prostředku pomocí aktuální obnovovací token během zadaného časového období.

Tato zásada vynutí, aby uživatelé, kteří se na svého klienta donutit k načtení nového tokenu obnovení k aktivní.

Na hodnotu nižší než maximální stáří Token Jednofaktorovému a vlastností služby Multi-Factor Authentication aktualizovat Token maximální stáří musí být nastavena vlastnost aktualizovat Token maximální neaktivní čas.

### <a name="single-factor-refresh-token-max-age"></a>Jednofaktorovému obnovovací Token maximální stáří
**Řetězec:** MaxAgeSingleFactor

**Ovlivňuje:** obnovovacích tokenů

**Shrnutí:** této zásady ovládací prvky, jak dlouho může uživatel používat obnovovací token získat nový token pár přístupu nebo obnovení po jejich poslední úspěšně ověřen pomocí pouze jednomu faktoru. Poté, co uživatel ověří a přijme nový token obnovení, uživatel může použít tok tokenu obnovení pro zadané časové období. (To je PRAVDA aktuální obnovovací token nebyl odvolán, a pokud není ponecháno nevyužité po dobu delší než neaktivní doba.) V tu chvíli se uživatel musí přijímat nového tokenu obnovení donutit k.

Omezení maximálního stáří donutí uživatele bude možné ověřit častěji. Vzhledem k tomu, že jednofaktorovému ověřování je považován za méně bezpečné než ověřování službou Multi-Factor Authentication, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo nižší než vlastnost službou Multi-Factor Authentication aktualizovat Token maximální stáří.

### <a name="multi-factor-refresh-token-max-age"></a>Maximální stáří Token obnovení služby Multi-Factor Authentication
**Řetězec:** MaxAgeMultiFactor

**Ovlivňuje:** obnovovacích tokenů

**Shrnutí:** této zásady ovládací prvky, jak dlouho může uživatel používat k získání nový pár tokenů přístupu nebo obnovení po jejich poslední úspěšně ověřen pomocí několika faktory obnovovací token. Poté, co uživatel ověří a přijme nový token obnovení, uživatel může použít tok tokenu obnovení pro zadané časové období. (To je PRAVDA aktuální obnovovací token nebyl odvolán, a pokud není po dobu delší než neaktivní doba.) Uživatelé jsou od tohoto okamžiku musí donutit přijímat nového tokenu obnovení.

Omezení maximálního stáří donutí uživatele bude možné ověřit častěji. Vzhledem k tomu, že jednofaktorovému ověřování je považován za méně bezpečné než ověřování službou Multi-Factor Authentication, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo větší než vlastnost Jednofaktorovému aktualizovat Token maximální stáří.

### <a name="single-factor-session-token-max-age"></a>Maximální stáří Token Jednofaktorovému relace
**Řetězec:** MaxAgeSessionSingleFactor

**Ovlivňuje:** relace tokeny (trvalá nebo nonpersistent)

**Shrnutí:** této zásady ovládací prvky, jak dlouho může uživatel používat k získání nové ID a tokenu relace po jejich poslední úspěšně ověřen pomocí pouze jednomu faktoru tokenu relace. Poté, co uživatel ověří a obdrží token novou relaci, uživatel může použít tok tokenu relace pro zadané časové období. (To platí za předpokladu, tokenu aktuální relace není odvolaný a nevypršela platnost.) Po zadaném časovém období uživatel bude muset donutit k získání tokenu novou relaci.

Omezení maximálního stáří donutí uživatele bude možné ověřit častěji. Vzhledem k tomu, že jednofaktorovému ověřování je považován za méně bezpečné než ověřování službou Multi-Factor Authentication, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo nižší než vlastnost Token maximální stáří relace služby Multi-Factor Authentication.

### <a name="multi-factor-session-token-max-age"></a>Maximální stáří tokenu relace služby Multi-Factor Authentication
**Řetězec:** MaxAgeSessionMultiFactor

**Ovlivňuje:** relace tokeny (trvalá nebo nonpersistent)

**Shrnutí:** tento ovládací prvky zásad jak dlouho může uživatel používat k získání nové ID a relace token od poslední úspěšně musí ověřit pomocí několika faktory tokenu relace. Poté, co uživatel ověří a obdrží token novou relaci, uživatel může použít tok tokenu relace pro zadané časové období. (To platí za předpokladu, tokenu aktuální relace není odvolaný a nevypršela platnost.) Po zadaném časovém období uživatel bude muset donutit k získání tokenu novou relaci.

Omezení maximálního stáří donutí uživatele bude možné ověřit častěji. Vzhledem k tomu, že jednofaktorovému ověřování je považován za méně bezpečné než ověřování službou Multi-Factor Authentication, doporučujeme nastavit tuto vlastnost na hodnotu, která je rovna nebo větší než vlastnost Jednofaktorovému relace Token maximální stáří.

## <a name="example-token-lifetime-policies"></a>Příklad zásady životnosti tokenu
Mnoho scénářů je možné v Azure AD, kdy můžete vytvářet a spravovat životností tokenů pro aplikace, instančních objektů a vaše celkové organizace. V této části provedeme několik běžných scénářů zásady, které vám umožňují ukládat nová pravidla pro:

* Životnost tokenu
* Neaktivní token maximální čas
* Token maximální stáří

V příkladech, přečtěte si postup:

* Spravovat zásady vaší organizace
* Vytvořit zásadu pro webové přihlášení
* Vytvořit zásadu pro nativní aplikaci, která volá webové rozhraní API
* Spravovat pokročilé zásady

### <a name="prerequisites"></a>Požadavky
V následujících příkladech vytvářet, aktualizovat, propojení a odstranit zásady pro aplikace, instančních objektů a vaše celkové organizace. Pokud začínáte s Azure AD, doporučujeme vám seznámit [získání tenanta služby Azure AD](develop/quickstart-create-new-tenant.md) předtím, než budete pokračovat v těchto příkladech.  

Abyste mohli začít, proveďte následující kroky:

1. Stáhněte si nejnovější [Azure AD PowerShell Module verze Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
2. Spustit `Connect` příkaz pro přihlášení k účtu správce Azure AD. Spusťte tento příkaz pokaždé, když spustíte novou relaci.

    ```PowerShell
    Connect-AzureAD -Confirm
    ```

3. Pokud chcete zobrazit všechny zásady, které byly vytvořeny ve vaší organizaci, spusťte následující příkaz. Tento příkaz spusťte po většinu operací v následujících scénářích. Spuštěním příkazu také vám pomůže využít vaše ** ** vaše zásady.

    ```PowerShell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Příklad: Správa organizace výchozí zásady
V tomto příkladu vytvoříte zásadu, která umožňuje uživatelům přihlásit se méně často napříč celou organizací. K tomuto účelu vytvořte zásadu životnost tokenu Jednofaktorovému aktualizovat tokeny, které používá ve vaší organizaci. Zásady platí pro každou aplikaci ve vaší organizaci a každý instanční objekt, který ještě nemá zásady nastavené.

1. Vytvoření zásady životnosti tokenu.

    1.  Sada Jednofaktorovému obnovovací Token na "do odvolán." Token platnost pasu nevyprší, dokud přístup odvolat. Vytvořte následující definice zásad:

        ```PowerShell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2.  Chcete-li vytvořit zásadu, spusťte následující příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3.  Chcete zobrazit nové zásady a získání zásady **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Aktualizujte zásady.

    Můžete se rozhodnout, že první zásady, kterou jste nastavili v tomto příkladu není strict, jak je vaše služba vyžaduje. Chcete-li nastavit vaše Jednofaktorovému aktualizaci tokenu vyprší za dva dny, spusťte následující příkaz:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId FROM GET COMMAND> -DisplayName "OrganizationDefaultPolicyUpdatedScenario" -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Příklad: Vytvoření zásady pro webové přihlášení

V tomto příkladu vytvoříte zásadu, která vyžaduje, aby uživatelé ověření častěji ve webové aplikaci. Tyto zásady nastaví Životnost tokenů přístupu nebo ID a maximální stáří tokenu relace služby Multi-Factor Authentication instančnímu objektu služby vaší webové aplikace.

1. Vytvoření zásady životnosti tokenu.

    Tato zásada pro webové přihlášení, nastaví přístup/ID, životnosti tokenu a tokenu stáří maximální relace jednofaktorovému až dvou hodin.

    1.  Chcete-li vytvořit zásadu, spusťte tento příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Chcete zobrazit nové zásady a získání zásady **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2.  Přiřaďte zásady instančního objektu služby. Je také potřeba získat **ObjectId** instančního objektu vaší služby. 

    1.  Pokud chcete zobrazit všechna firemní instanční objekty, můžete dát dotaz na buď [Microsoft Graphu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) nebo [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Také, můžete ho otestovat v [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/)a [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer) pomocí svého účtu Azure AD.

    2.  Pokud máte **ObjectId** z instančního objektu služby, spusťte následující příkaz:

        ```PowerShell
        Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Příklad: Vytvoření zásady pro nativní aplikaci, která volá webové rozhraní API
V tomto příkladu vytvoříte zásadu, která vyžaduje, aby uživatelé ověření méně často. Zásada také prodlouží dobu, kterou uživatel může být neaktivní, než uživatel musí donutit. Zásady se použijí pro webové rozhraní API. Když nativní aplikace požádá o webové rozhraní API jako prostředek, je tato zásada použitá.

1. Vytvoření zásady životnosti tokenu.

    1.  Chcete-li vytvořit přísné zásady pro webové rozhraní API, spusťte následující příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2.  Chcete zobrazit nové zásady a získání zásady **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Přiřaďte zásady pro vaše webové rozhraní API. Je také potřeba získat **ObjectId** vaší aplikace. Nejlepší způsob, jak najít vaši aplikaci **ObjectId** , je použít [webu Azure portal](https://portal.azure.com/).

   Pokud máte **ObjectId** vaší aplikace, spusťte následující příkaz:

        ```PowerShell
        Add-AzureADApplicationPolicy -Id <ObjectId of the Application> -RefObjectId <ObjectId of the Policy>
        ```


### <a name="example-manage-an-advanced-policy"></a>Příklad: Spravovat pokročilé zásady
V tomto příkladu vytvoříte několik zásad, k seznámení s fungováním systému priority. Také můžete zjistíte, jak spravovat více zásad, které se použijí u několika objektů.

1. Vytvoření zásady životnosti tokenu.

    1.  Vytvoření výchozí zásady organizace, které nastaví Jednofaktorovému aktualizovat Token doby života na 30 dnů, spusťte následující příkaz:

        ```PowerShell
        New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2.  Chcete zobrazit nové zásady a získání zásady **ObjectId**, spusťte následující příkaz:

        ```PowerShell
        Get-AzureADPolicy
        ```

2. Přiřaďte zásady instančnímu objektu služby.

    Teď máte zásadu, která platí pro celou organizaci. Můžete chtít zachovat toto 30denní zásady pro konkrétní objekt služby, ale změňte výchozí zásady organizace na horní limit počtu "až do odvolaný."

    1.  Pokud chcete zobrazit všechna firemní instanční objekty, můžete dát dotaz na buď [Microsoft Graphu](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/serviceprincipal#properties) nebo [Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity). Také, můžete ho otestovat v [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/)a [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer) pomocí svého účtu Azure AD.

    2.  Pokud máte **ObjectId** z instančního objektu služby, spusťte následující příkaz:

            ```PowerShell
            Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
            ```
        
3. Nastavte `IsOrganizationDefault` příznak na hodnotu false:

    ```PowerShell
    Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Vytvořte novou organizaci výchozí zásadu:

    ```PowerShell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Teď máte původní zásadou, které jsou propojeny s instančního objektu služby a nová zásada je nastavená na výchozí zásady vaší organizace. Je dobré si uvědomit, že zásady uplatněné na instanční objekty mají přednost před výchozích zásad organizace.

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
| <code>&#8209;Definition</code> |Pole JSON převedený na řetězec, který obsahuje všechny zásady pravidla. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Řetězec název zásady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Při hodnotě true se nastaví jako výchozí zásady organizace zásady. Pokud má hodnotu false, nemá žádný účinek. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zásad. Životnost tokenů vždy stahujte "TokenLifetimePolicy." | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Volitelné] |Nastaví alternativní ID zásad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Získá všechny zásady Azure AD nebo zadanou zásadu.

```PowerShell
Get-AzureADPolicy
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> [Volitelné] |**ID objektu (Id)** z zásady, které chcete. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Získá všechny aplikace a instanční objekty, které jsou propojeny k zásadě.

```PowerShell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** z zásady, které chcete. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Aktualizuje existující zásady.

```PowerShell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** z zásady, které chcete. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Řetězec název zásady. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Volitelné] |Pole JSON převedený na řetězec, který obsahuje všechny zásady pravidla. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Volitelné] |Při hodnotě true se nastaví jako výchozí zásady organizace zásady. Pokud má hodnotu false, nemá žádný účinek. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Volitelné] |Typ zásad. Životnost tokenů vždy stahujte "TokenLifetimePolicy." |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Volitelné] |Nastaví alternativní ID zásad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Odstraní zadanou zásadu.

```PowerShell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** z zásady, které chcete. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zásady aplikací
Následující rutiny můžete použít pro zásady aplikací.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Přidat AzureADApplicationPolicy
Propojí určené zásady na aplikaci.

```PowerShell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ID objektu** zásad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Získá zásady, které je přiřazeno k aplikaci.

```PowerShell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** aplikace. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Odebere zásadu z aplikace.

```PowerShell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ID objektu** zásad. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zásady instančních objektů
Následující rutiny můžete použít pro zásady instančních objektů.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Propojí určené zásadě instančnímu objektu služby.

```PowerShell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ID objektu** zásad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Získá všechny zásady, propojená se zadaným instančního objektu.

```PowerShell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** aplikace. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
Odebere zásadu ze zadaného objektu služby.

```PowerShell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Popis | Příklad: |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ID objektu (Id)** aplikace. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ID objektu** zásad. | `-PolicyId <ObjectId of Policy>` |

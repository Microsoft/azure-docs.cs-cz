---
title: Zvýšení odolnosti při ověřování a autorizaci v klientských aplikacích, které vyvíjíte
titleSuffix: Microsoft identity platform
description: Pokyny pro zvýšení odolnosti při ověřování a autorizaci v klientských aplikacích s využitím platformy Microsoft Identity Platform
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: bc3b041e44fad66a4edc6ff34c0e534dc423de86
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226586"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Zvýšení odolnosti při ověřování a autorizaci v klientských aplikacích, které vyvíjíte

V této části najdete informace o tom, jak zajistit odolnost při sestavování v klientských aplikacích, které používají platformu Microsoft identity a Azure Active Directory k přihlašování uživatelů a provádění akcí jménem těchto uživatelů.

## <a name="use-the-microsoft-authentication-library-msal"></a>Použití knihovny Microsoft Authentication Library (MSAL)

[Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) je klíčovou součástí [platformy Microsoft Identity](../develop/index.yml). Zjednodušuje a spravuje načítání, správu, ukládání a obnovování tokenů a používá osvědčené postupy pro odolnost. MSAL je navržený tak, aby povoloval zabezpečené řešení bez toho, aby se Vývojáři museli starat o podrobnosti implementace.

MSAL ukládá tokeny do mezipaměti a používá vzor získání tichého tokenu. Také automaticky serializaci mezipaměť tokenů na platformách, které nativně poskytují zabezpečené úložiště jako Windows UWP, iOS a Android. Vývojáři můžou přizpůsobit chování serializace při použití [Microsoft. identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.NET](../develop/msal-net-token-cache-serialization.md), [MSAL pro Java](../develop/msal-java-token-cache-serialization.md)a [MSAL pro Python](../develop/msal-python-token-cache-serialization.md).

![Obrázek zařízení s aplikací a s využitím MSAL k volání identity Microsoftu](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

Při použití MSAL se automaticky podporuje ukládání tokenů do mezipaměti, aktualizace a tiché získávání. Pomocí jednoduchých vzorů můžete získat tokeny nezbytné pro moderní ověřování. Podporujeme mnoho jazyků a na stránce [ukázek](../develop/sample-v2-code.md) si můžete najít ukázku, která odpovídá vašemu jazyku a scénáři.

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

MSAL může v některých případech aktivně aktualizovat tokeny. Když Microsoft Identity vystaví dlouhodobé tokeny, může klientovi odeslat informace na optimální dobu k aktualizaci tokenu ("aktualizovat \_ v"). MSAL bude proaktivně aktualizovat token na základě těchto informací. Aplikace bude i nadále běžet, i když je starý token platný, ale bude mít delší časový rámec, během kterého by bylo možné provést další úspěšné získání tokenu.

### <a name="stay-up-to-date"></a>Stále v obraze

Vývojáři by měli mít proces aktualizace na nejnovější verzi MSAL. Ověřování je součástí zabezpečení vaší aplikace a vaše aplikace musí zůstat aktuální s vylepšeními zabezpečení obsaženými v nových verzích MSAL. To je obvykle dobrým zvykem knihoven v rámci průběžného vývoje a tím se zajistí, že budete mít nejaktuálnější kód s ohledem na odolnost aplikace. Vzhledem k tím, že se Microsoft Identity dál bude inovovat na způsoby, jak zajistit větší odolnost aplikací, budou aplikace, které používají nejnovější MSAL, připravené na tyto inovace.

[Podívejte se na nejnovější verzi MSAL.js a poznámky k verzi.](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Podívejte se na nejnovější verzi MSAL .NET a poznámky k verzi.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[Podívejte se na nejnovější verzi MSAL Pythonu a poznámky k verzi.](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[Podívejte se na nejnovější verzi MSAL verze Java a poznámky k verzi.](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Podívejte se na nejnovější verzi MSAL iOS a verze macOS a poznámky k verzi.](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[Podívejte se na nejnovější verzi Androidu MSAL a poznámky k verzi.](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[Podívejte se na nejnovější verzi MSAL úhlu a poznámky k verzi.](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Podívejte se na nejnovější verzi Microsoft. identity. Web a poznámky k verzi.](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Použití odolných vzorů pro zpracování tokenů

Pokud nepoužíváte MSAL, můžete použít tyto odolné vzory pro zpracování tokenů. Tyto osvědčené postupy jsou implementovány automaticky knihovnou MSAL. 

Obecně platí, že aplikace, která používá moderní ověřování, zavolá koncový bod, který načte tokeny, které ověřují uživatele nebo autorizuje aplikaci, aby vyvolala chráněná rozhraní API. MSAL je určeno pro zpracování podrobností o ověřování a implementuje několik vzorů pro zlepšení odolnosti tohoto procesu. Pomocí pokynů v této části můžete implementovat osvědčené postupy, pokud se rozhodnete použít jinou knihovnu než MSAL. Pokud používáte MSAL, získáte všechny tyto osvědčené postupy zdarma, protože je MSAL automaticky implementuje.

### <a name="cache-tokens"></a>Tokeny mezipaměti

Aplikace by měly správně dodržet tokeny mezipaměti od identity Microsoftu. Pokud vaše aplikace obdrží tokeny, odpověď HTTP obsahující tokeny obsahuje také vlastnost "expires_in", která oznamuje aplikaci, jak dlouho ukládat do mezipaměti a znovu použít token. Je důležité, aby aplikace používaly vlastnost "expires_in" k určení životnosti tokenu. Aplikace se nikdy nepokouší dekódovat přístupový token rozhraní API.

![Aplikace, která volá Microsoft identity, ale volání prochází přes mezipaměť tokenů na zařízení, na kterém je aplikace spuštěná.](media/resilience-client-app/token-cache.png)

Použití tokenu uloženého v mezipaměti zabraňuje zbytečnému provozu mezi vaší aplikací a identitou Microsoftu. díky omezení počtu volání pro získání tokenu je vaše aplikace méně náchylná k chybám získání tokenu. Tokeny uložené v mezipaměti také zlepšují výkon vaší aplikace, protože aplikace musí blokovat získání tokenů méně. Uživatel může zůstat přihlášený k vaší aplikaci po dobu životnosti tohoto tokenu.

### <a name="serialize-and-persist-tokens"></a>Serializace a trvalé tokeny

Aplikace by měly bezpečně serializovat mezipaměť tokenů, aby se zachovaly tokeny mezi instancemi aplikace. Tokeny lze znovu použít, pokud jsou v rámci své platné životnosti. [Aktualizační tokeny](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token)a stále [přístupové tokeny](../develop/access-tokens.md)jsou vydávány po celou řadu hodin. Tímto platným časem může uživatel mnohokrát spustit aplikaci. Když se vaše aplikace spustí, měli byste zjistit, jestli existuje platný přístup nebo obnovovací token, který se dá použít. Tím se zvýší odolnost aplikace a výkon tak, aby se zabránilo zbytečným voláním společnosti Microsoft identity.

![Aplikace, která volá Microsoft identity, ale volání prochází přes mezipaměť tokenů a také úložiště tokenů na zařízení, na kterém je aplikace spuštěná.](media/resilience-client-app/token-store.png)

Úložiště trvalého tokenu by mělo být řízené a zašifrované na identitu vlastnícího uživatele nebo procesu. Na platformách, jako jsou mobilní, Windows a Mac, by měl vývojář využít výhody integrovaných funkcí pro ukládání přihlašovacích údajů.

### <a name="acquire-tokens-silently"></a>Tiché získání tokenů

Proces ověřování uživatele nebo načtení autorizace pro volání rozhraní API může vyžadovat více kroků v rámci identity Microsoftu. Například když se uživatel přihlásí poprvé, bude muset zadat přihlašovací údaje a provést službu Multi-Factor Authentication prostřednictvím textové zprávy. Každý krok přidá závislost na prostředku, který poskytuje tuto službu. Nejlepší možností pro uživatele a druhý s minimálními závislostmi je pokus o získání tokenu v tichém režimu, aby před vyžádáním interakce uživatele nedocházelo k těmto dalším krokům.

![Diagram znázorňující různé služby v rámci identity Microsoftu, které může být potřeba spustit, aby se dokončil proces ověřování nebo autorizace uživatele](media/resilience-client-app/external-dependencies.png)

Získání tokenu se tiše spouští pomocí platného tokenu z mezipaměti tokenů aplikace. Pokud není k dispozici žádný platný token, aplikace by se měla pokusit získat token pomocí obnovovacího tokenu, pokud je k dispozici, a koncového bodu tokenu. Pokud není k dispozici žádná z těchto možností, aplikace by měla získat token pomocí parametru "Prompt = None". Tato akce použije koncový bod autorizace, ale nezobrazuje žádné uživatelské rozhraní pro uživatele. Pokud identita Microsoftu může aplikaci tokenu poskytnout bez interakce s uživatelem, bude. Pokud žádný z těchto metod nevede k tokenu, musí se uživatel znovu interaktivně ověřit.

> [!NOTE]
> Obecně platí, že se aplikace nesmí používat výzvy jako "přihlašovací jméno" a "souhlas", protože vynutí interakci s uživatelem i v případě, že se nevyžaduje žádná interakce.

## <a name="handle-service-responses-properly"></a>Správné zpracování odpovědí služby

Zatímco aplikace by měly zpracovávat všechny chybové odpovědi, existují některé odpovědi, které mohou ovlivnit odolnost. Pokud vaše aplikace obdrží kód odpovědi HTTP 429, moc velký počet požadavků, Microsoft Identity omezuje vaše požadavky. Pokud vaše aplikace bude i nadále dělat příliš mnoho požadavků, bude i nadále omezené, aby vaše aplikace přijímala tokeny. Aplikace by se neměla pokoušet získat token znovu až po uplynutí doby v sekundách v poli Retry-After Response. Přijetí odpovědi 429 je často známkou toho, že aplikace neukládá do mezipaměti a správně znovu nepoužívá tokeny. Vývojáři by si měli projít, jak se tokeny ukládají do mezipaměti a znovu použijí v aplikaci.

Když aplikace obdrží kód odpovědi HTTP 5xx, aplikace nesmí zadat rychlou smyčku opakování. Je-li k dispozici, aplikace by měla dodržovat stejné zpracování Retry-After, jako v případě odpovědi 429. Pokud odpověď neposkytne hlavičku Retry-After, doporučujeme implementovat exponenciální back-vypnutý pokus s prvním opakováním alespoň 5 sekund od odpovědi.

V případě, že se žádosti vyprší, pokud se aplikace neopakuje okamžitě. Implementujte exponenciální Back-off opakování s prvním opakováním alespoň 5 sekund po odpovědi.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Vyhodnotit možnosti pro načtení informací souvisejících s autorizací

Mnoho aplikací a rozhraní API potřebuje konkrétní informace o uživateli, aby mohl provádět autorizační rozhodnutí. Existuje několik způsobů, jak může aplikace získat tyto informace. Každá z metod má své výhody a nevýhody. Vývojáři by měli zvážit, abyste zjistili, která strategie je nejvhodnější pro odolnost ve své aplikaci.

### <a name="tokens"></a>Tokeny

Tokeny identity (ID) a přístupové tokeny obsahují standardní deklarace identity, které poskytují informace o předmětu. Ty jsou popsány v [tokenech ID platformy Microsoft Identity Platform](../develop/id-tokens.md) a v [tokenech přístupu Microsoft Identity Platform](../develop/access-tokens.md). Pokud jsou informace, které vaše aplikace potřebuje, již v tokenu, pak nejúčinnějším způsobem, jak tato data načíst, je použít deklarace identity tokenu, protože tím se pozastaví další síťové volání, aby se informace načetly samostatně. Méně síťových volání znamená vyšší celkovou odolnost aplikace.

> [!NOTE]
> Některé aplikace volají koncový bod UserInfo, aby načetly deklarace identity pro uživatele, který se ověřil. Informace, které jsou k dispozici v tokenu ID, který může vaše aplikace obdržet, jsou nadmnožinou informací, které může získat z koncového bodu UserInfo. Vaše aplikace by měla použít token ID k získání informací o uživateli namísto volání koncového bodu UserInfo.

Vývojář aplikace může rozšířit deklarace identity Standard na tokeny pomocí [volitelných deklarací identity](../develop/active-directory-optional-claims.md). Jedna běžná volitelná deklarace identity je [skupin](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims). Existuje několik způsobů, jak přidat deklarace skupin. Možnost Skupina aplikací obsahuje jenom skupiny přiřazené k aplikaci. Mezi možnosti "všechny" nebo "skupiny zabezpečení" patří skupiny ze všech aplikací ve stejném tenantovi, které mohou do tokenu přidat mnoho skupin. Je důležité, abyste efekt vyhodnotili v případě, že to může způsobit negaci efektivity, kterou získal požadavek na skupiny v tokenu, a to díky tomu, že dispozici determinističtější tokenu a dokonce vyžaduje další volání pro získání úplného seznamu skupin.

Místo používání skupin ve vašem tokenu můžete místo toho použít a zahrnout aplikační role. Vývojáři můžou definovat [aplikační role](../develop/howto-add-app-roles-in-azure-ad-apps.md) pro své aplikace a rozhraní API, které může zákazník spravovat z adresáře pomocí portálu nebo rozhraní API. IT specialisté pak můžou přiřadit role různým uživatelům a skupinám a určovat, kdo má přístup k jakému obsahu a funkcím. Když je pro aplikaci nebo rozhraní API vydán token, role přiřazené uživateli budou k dispozici v deklaraci identity rolí v tokenu. Získání těchto informací přímo v tokenu může uložit další volání rozhraní API.

Správci IT můžou také přidávat deklarace identity na základě konkrétních informací v tenantovi. Například společnost může mít rozšíření, které má ID uživatele specifické pro podnik.

Ve všech případech může být přidávání informací z adresáře přímo do tokenu efektivní a zvýšit odolnost aplikací tím, že se sníží počet závislostí aplikace. Na druhé straně neřeší žádné problémy odolnosti proti tomu, aby nebylo možné získat token. Měli byste přidat jenom volitelné deklarace identity pro hlavní scénáře vaší aplikace. Pokud aplikace vyžaduje informace jenom pro funkce správce, je nejlepší, aby aplikace získala tyto informace jenom podle potřeby.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph poskytuje jednotný koncový bod rozhraní API pro přístup k datům Microsoft 365, která popisují vzory produktivity, identity a zabezpečení v organizaci. Aplikace, které používají Microsoft Graph, můžou potenciálně využít některé z informací v Microsoft 365 při autorizačních rozhodnutích.

Pro přístup ke všem Microsoft 365 aplikace vyžadují jenom jeden token. To je odolnější než použití starších rozhraní API, která jsou specifická pro Microsoft 365 komponenty, jako je Microsoft Exchange nebo Microsoft SharePoint, kde je potřeba víc tokenů.

Při použití Microsoft Graph rozhraní API doporučujeme použít sadu [SDK Microsoft Graph](/graph/sdks/sdks-overview). Sady Microsoft Graph SDK jsou navržené tak, aby zjednodušily vytváření vysoce kvalitních, efektivních a odolných aplikací, které přistupují k Microsoft Graph.

V případě autorizačních rozhodnutí by měli vývojáři zvážit, kdy používat deklarace identity, které jsou k dispozici v tokenu, jako alternativu k některým Microsoft Graph volání. Jak je uvedeno výše, můžou vývojáři požádat o skupiny, aplikační role a volitelné deklarace identity ve svých tokenech. Z hlediska odolnosti musí použití Microsoft Graph pro autorizaci vyžadovat další síťová volání, která spoléhají na identitu společnosti Microsoft (k získání tokenu pro přístup k Microsoft Graph) a také Microsoft Graph sám o sobě. Nicméně pokud vaše aplikace již spoléhá na Microsoft Graph jako na svou datovou vrstvu, pak při použití grafu k autorizaci není další riziko, které by bylo potřeba provést.

## <a name="use-broker-authentication-on-mobile-devices"></a>Použití ověřování zprostředkovatele na mobilních zařízeních

Na mobilních zařízeních se pomocí zprostředkovatele ověřování, jako je Microsoft Authenticator, vylepšit odolnost. Zprostředkovatel přináší výhody, které jsou k dispozici, s dalšími možnostmi, jako je například systémový prohlížeč nebo vložené WebView. Zprostředkovatel ověřování může využít [primární obnovovací token](../devices/concept-primary-refresh-token.md) (PRT), který obsahuje deklarace identity uživatele a zařízení a dá se použít k získání tokenů ověřování pro přístup k jiným aplikacím ze zařízení. Když se PRT používá k vyžádání přístupu k aplikaci, jeho deklarace identity zařízení a MFA jsou pro Azure AD důvěryhodné. Tím se zvyšuje odolnost, protože se vyhnete dalším postupům v ověřování zařízení. Uživatelé nebudou mít na stejném zařízení výzvy s více výzvami MFA, což zvyšuje odolnost proti chybám díky omezení závislostí na externích službách a vylepšení uživatelského prostředí.

![Aplikace, která volá Microsoft identity, ale volání prochází přes mezipaměť tokenů a také jako úložiště tokenů a zprostředkovatele ověřování na zařízení, na kterém je aplikace spuštěná.](media/resilience-client-app/authentication-broker.png)

Služba MSAL automaticky podporuje ověřování zprostředkovatele. Další informace o používání služby zprostředkované ověřování najdete na následujících stránkách:

- [Konfigurace jednotného přihlašování v macOS a iOS](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [Postup povolení jednotného přihlašování pro různé aplikace na Androidu pomocí MSAL](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>Přijmout vyhodnocení průběžného přístupu

[Vyhodnocování průběžného přístupu (CAE)](../conditional-access/concept-continuous-access-evaluation.md) je nedávný vývoj, který může zvýšit zabezpečení a odolnost aplikace pomocí dlouhotrvajících tokenů. CAE je rozvinutý oborový standard vyvinutý ve sdílených signálech a pracovních skupinách OpenID Foundation. Pomocí CAE je možné přístupové tokeny odvolat na základě [kritických událostí](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) a [vyhodnocení zásad](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview), ale nemusíte spoléhat na životnost krátkého tokenu. V případě některých rozhraní API prostředků se v reálném čase vyhodnocení rizik a zásad CAE může podstatně zvýšit životnost tokenu až na 28 hodin. V případě, že rozhraní API a aplikace přijímají CAE, bude moct Microsoft Identity vystavovat přístupové tokeny, které jsou odvolatelné a jsou platné po delší dobu. Tyto dlouhodobé tokeny se proaktivně aktualizují pomocí MSAL.

I když je CAE v rané fázi, je možné [vyvíjet klientské aplikace ještě dnes, které budou těžit z CAE,](../develop/app-resilience-continuous-access-evaluation.md) když prostředky (rozhraní API) aplikace používají přijmout CAE. S tím, jak více prostředků přijímá CAE, vaše aplikace bude moci získat tokeny pro tyto prostředky také k dispozici CAE. Rozhraní Microsoft Graph API a sady [Microsoft Graph SDK](/graph/sdks/sdks-overview)budou zobrazovat náhled CAE funkce brzy 2021. Pokud se chcete zapojit do veřejné verze Preview Microsoft Graph s CAE, můžete nás informovat o tom, co vás zajímá: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

Pokud vyvíjíte rozhraní API prostředků, doporučujeme, abyste se účastnili [sdílených signálů a událostí WG](https://openid.net/wg/sse/). S touto skupinou spolupracujeme s tím, aby bylo možné sdílet události zabezpečení mezi poskytovateli identit a prostředků Microsoftu.

## <a name="next-steps"></a>Další kroky

- [Jak používat rozhraní API s povoleným vyhodnocováním průběžného přístupu ve vašich aplikacích](../develop/app-resilience-continuous-access-evaluation.md)
- [Zajištění odolnosti proti sestavení v aplikacích démona](resilience-daemon-app.md)
- [Odolnost sestavení v infrastruktuře pro správu identit a přístupu](resilience-in-infrastructure.md)
- [Odolnost sestavení v systémech CIAM](resilience-b2c.md)

---
title: Jak zpracovat inteligentní ochranu před sledováním (ITP) v Safari | Azure
titleSuffix: Microsoft identity platform
description: Ověřování s jednou stránkou (SPA) v případě, že soubory cookie třetích stran již nejsou povoleny.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753966"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Zpracování ITP v Safari a dalších prohlížečích, kde jsou soubory cookie třetích stran blokované

Mnoho prohlížečů dnes blokuje soubory cookie třetích stran v požadavcích na domény, které nejsou stejné jako ty, které se zobrazují v panelu prohlížeče. Tím dojde k přerušení implicitního toku a k úspěšnému přihlášení uživatelů vyžaduje nové vzory ověřování. Na platformě Microsoft Identity používáme tok autorizace s PKCE a aktualizačními tokeny k udržení přihlášení uživatelů, když jsou soubory cookie třetích stran blokované.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Co je služba Intelligent Tracking Protection (ITP)?

Apple Safari má ve výchozím nastavení funkci ochrany osobních údajů, která se nazývá [inteligentní sledování](https://webkit.org/tracking-prevention-policy/)nebo *ITP*. ITP blokuje soubory cookie třetích stran, soubory cookie v žádostech, které procházejí mezi doménami.

Běžná forma sledování uživatelů se provádí načtením prvku IFRAME na webu třetí strany na pozadí a používáním souborů cookie ke korelaci uživatele po internetu. Tento model je ale také standardním způsobem implementace [implicitního toku](v2-oauth2-implicit-grant-flow.md) v aplikacích s jednou stránkou (jednostránkové). Když prohlížeč zablokuje soubory cookie třetích stran a brání tak sledování uživatelů, jednostránkové jsou také přerušeny.

Safari není samotným blokováním souborů cookie třetích stran, aby se zvýšila ochrana osobních údajů uživatelů. Brave zablokovala soubory cookie třetích stran ve výchozím nastavení a chrom (platforma za Google Chrome a Microsoft Edge) oznámila, že i v budoucnu přestane podporovat soubory cookie třetích stran.

Řešení, které je popsáno v tomto článku, funguje ve všech těchto prohlížečích nebo kdekoli jsou soubory cookie třetích stran blokované.

## <a name="overview-of-the-solution"></a>Přehled řešení

Aby bylo možné pokračovat v ověřování uživatelů v jednostránkové, musí vývojáři aplikací použít [tok autorizačního kódu](v2-oauth2-auth-code-flow.md). V toku kódu ověřování vystaví zprostředkovatel identity kód a SPA uplatňuje kód pro přístupový token a obnovovací token. Když aplikace vyžaduje další tokeny, může použít [tok obnovovacího tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) a získat nové tokeny. MSAL.js 2,0, knihovna Microsoft Identity Platform Library pro jednostránkové, implementuje tok autorizačního kódu pro jednostránkové a s menšími aktualizacemi je náhrada pro MSAL.js 1. x.

V případě platformy Microsoft Identity Platform jednostránkové a nativních klientů se řiďte podobnými pokyny k protokolu:

* Použití [výzvy kódu PKCE](https://tools.ietf.org/html/rfc7636)
    * PKCE se *vyžaduje* pro jednostránkové na platformě Microsoft identity. PKCE se *doporučuje* pro nativní a důvěrné klienty.
* Žádné použití tajného klíče klienta

Jednostránkové mají dvě další omezení:

* [Identifikátor URI pro přesměrování musí být označený jako `spa` typ](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) . Pokud chcete povolit CORS u koncových bodů přihlášení.
* Aktualizace tokenů vydaných prostřednictvím toku autorizačního kódu pro `spa` přesměrování identifikátorů URI má 24hodinový čas, nikoli 90 dní.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram znázorňující tok autorizačního kódu OAuth 2 mezi jednostránkovou aplikací a koncovým bodem služby tokenu zabezpečení." border="false":::

## <a name="performance-and-ux-implications"></a>Důsledky výkonu a uživatelského rozhraní

Některé aplikace používající při pokusu o implicitní tok se přihlásí bez přesměrování otevřením prvku IFRAME přihlášení pomocí `prompt=none` . Ve většině prohlížečů bude tato žádost odpovídat tokenům aktuálně přihlášeného uživatele (za předpokladu, že je již udělen souhlas). Tento vzor identifikovaný aplikace nepotřebovala přesměrování na celou stránku, aby bylo možné uživatele podepsat, zvýšil výkon a uživatelské prostředí – uživatel navštíví webovou stránku a je již přihlášen. Vzhledem k tomu `prompt=none` , že v prvku IFRAME už není možnost, když jsou soubory cookie třetích stran blokované, musí aplikace navštívit přihlašovací stránku v rámci nejvyšší úrovně, aby vystavila autorizační kód.

Existují dva způsoby, jak provést přihlášení:

* **Přesměrování celého stránkování**
    * Při prvním načtení zabezpečeného hesla uživatele přesměrujte na přihlašovací stránku, pokud už neexistuje žádná relace (nebo pokud vypršela platnost relace). Prohlížeč uživatele navštíví přihlašovací stránku, prezentuje soubory cookie, které obsahují relaci uživatele, a pak přesměrují zpátky do aplikace s kódem a tokeny v fragmentu.
    * Přesměrování vede k dvojímu načítání zabezpečeného hesla. Dodržujte osvědčené postupy pro ukládání jednostránkové do mezipaměti, aby se aplikace nestáhnula dvakrát.
    * Zvažte, že v aplikaci máte sekvenci před načtením, která kontroluje přihlašovací relaci a přesměrovává na přihlašovací stránku předtím, než aplikace zcela rozbalí a spustí datovou část JavaScriptu.
* **Automaticky otevíraná okna**
    * Pokud uživatelské prostředí (UX) celého přesměrování stránky pro aplikaci nefunguje, zvažte použití místní nabídky pro zpracování ověřování.
    * Když se po ověření místní nabídka dokončí přesměrování do aplikace, kód v obslužné rutině přesměrování uloží kód a tokeny do místního úložiště, aby se aplikace používala. MSAL.js podporuje automaticky otevíraná okna pro ověřování, stejně jako většina knihoven.
    * Prohlížeče snižují podporu překryvných oken, takže nemusí být spolehlivější možnost. Aby bylo možné vyhovět požadavkům prohlížeče, může být potřeba, aby před vytvořením překryvného okna byla interakce uživatele s heslem.

>[!NOTE]
> Apple [popisuje metodu místní nabídky](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) jako dočasnou opravu kompatibility, která původnímu oknu poskytne přístup k souborům cookie třetích stran. I když Apple může tato převodit oprávnění odebrat, nebude to mít vliv na tyto pokyny. V tomto případě se místní nabídka používá jako navigace první strany na přihlašovací stránce, takže se najde relace a může se zadat ověřovací kód. To by mělo pokračovat v práci do budoucna.

### <a name="a-note-on-iframe-apps"></a>Poznámka k aplikacím IFRAME

Běžným vzorem ve webových aplikacích je použití prvku IFRAME pro vložení jedné aplikace do jiné. Rámec nejvyšší úrovně zpracovává ověřování uživatele a aplikace hostované v prvku IFRAME může důvěřovat, že uživatel je přihlášený a načítá tokeny v tichém režimu pomocí implicitního toku. Získání tichého tokenu už nefunguje, když jsou soubory cookie třetích stran blokované – aplikace vložená do prvku IFRAME se musí přepnout na použití automaticky otevíraných oken pro přístup k uživatelské relaci, protože nemůže přejít na přihlašovací stránku.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Dopad aktualizace tokenů v prohlížeči na zabezpečení

Vydání aktualizačních tokenů do prohlížeče se považuje za bezpečnostní problém. Útoky skriptování XSS (mezi weby) nebo napadené balíčky JS mohou ukrást obnovovací token a používat ho vzdáleně, dokud nevyprší platnost nebo se odvolá. Aby se minimalizovalo riziko odcizených aktualizačních tokenů, jednostránkové se vydávají tokeny platné jenom na 24 hodin. Po 24 hodinách musí aplikace získat nový autorizační kód prostřednictvím rámce nejvyšší úrovně na přihlašovací stránce.

Tento vzor obnovovacího tokenu s omezeným trváním byl vybrán jako rovnováhu mezi zabezpečením a degradované uživatelské prostředí. Bez aktualizačních tokenů nebo souborů cookie třetích stran je tok autorizačního kódu (jak je doporučený [konceptem osvědčených postupů zabezpečení OAuth](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) náročný na to, že se vyžadují nové nebo další tokeny. U každého jediného tokenu je potřeba úplné přesměrování stránky nebo automaticky otevírané okno, pokaždé, když vyprší platnost tokenu (každou hodinu obvykle pro tokeny platformy Microsoft Identity).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [toku autorizačního kódu](v2-oauth2-auth-code-flow.md).

Vyzkoušejte tok autorizačního kódu pomocí rychlého startu [MSAL.js 2,0](quickstart-v2-javascript-auth-code.md).

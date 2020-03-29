---
title: Problémy v aplikaci Internet Explorer & Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Informace o informacích o problémech při používání Knihovny microsoft authentication library pro JavaScript (MSAL.js) s prohlížeči Internet Explorer a Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696091"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Známé problémy v prohlížečích Internet Explorer a Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problémy způsobené bezpečnostními zónami
Měli jsme několik zpráv o problémech s ověřováním v aplikaci IE a Microsoft Edge (od aktualizace *verze prohlížeče Microsoft Edge na 40.15063.0.0*). Sledujeme je a informovali tým Microsoft Edge. Zatímco Microsoft Edge pracuje na řešení, zde je popis často se vyskytující problémy a možná řešení, která mohou být implementována.

### <a name="cause"></a>Příčina
Příčinou většiny těchto problémů je následující. Úložiště relací a místní úložiště jsou rozděleny podle zón zabezpečení v prohlížeči Microsoft Edge. V této konkrétní verzi Microsoft Edge, když je aplikace přesměrována napříč zónami, úložiště relace a místní úložiště jsou vymazány. Konkrétně úložiště relace je vymazána v běžné navigaci prohlížeče a relace a místní úložiště jsou vymazány v režimu InPrivate prohlížeče. Soubor MSAL.js ukládá určitý stav v úložišti relace a spoléhá na kontrolu tohoto stavu během toků ověřování. Když úložiště relace je vymazána, tento stav je ztracena a proto má za následek přerušené prostředí.

### <a name="issues"></a>Problémy

- **Nekonečné smyčky přesměrování a opětovné načtení stránky během ověřování**. Když se uživatelé přihlásí k aplikaci v aplikaci Microsoft Edge, budou přesměrováni zpět z přihlašovací stránky AAD a zapnou se v nekonečné smyčce přesměrování, což vede k opakovanému opětovnému načtení stránky. To je obvykle doprovázeno chybou `invalid_state` v úložišti relace.

- **Nekonečné smyčky tokenů získání a chyba AADSTS50058**. Když se aplikace spuštěná v microsoft edge pokusí získat token pro prostředek, může se uvíznout v nekonečné smyčce volání tokenu získání spolu s následující chybou z aad v trasování sítě:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Vyskakovací okno se nezavře nebo se při použití přihlášení pomocí místního přihlášení k ověření nezavře**. Při ověřování prostřednictvím vyskakovacího okna v aplikaci Microsoft Edge nebo IE(InPrivate), po zadání pověření a přihlášení, pokud více domén napříč zónami zabezpečení jsou zapojeny do navigace, vyskakovací okno nezavře, protože MSAL.js ztratí popisovač vyskakovacíokno.  

### <a name="update-fix-available-in-msaljs-023"></a>Aktualizace: Oprava k dispozici v MSAL.js 0.2.3
Opravy problémů s opakováním smyčky přesměrování ověřování byly vydány v [souboru MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Povolte `storeAuthStateInCookie` příznak v konfiguraci MSAL.js, abyste mohli tuto opravu využít. Ve výchozím nastavení je tento příznak nastaven na hodnotu false.

Pokud `storeAuthStateInCookie` je příznak povolen, msal.js použije soubory cookie prohlížeče k uložení stavu požadavku potřebného pro ověření toků ověření.

> [!NOTE]
> Tato oprava ještě není k dispozici pro obaly msal-angular a msal-angularjs. Tato oprava neřeší problém s automaticky otevíranými okny.

Použijte níže uvedená zástupná řešení.

#### <a name="other-workarounds"></a>Další řešení
Před přijetím těchto zástupných řešení otestujte, že k problému dochází pouze v konkrétní verzi prohlížeče Microsoft Edge a že funguje v ostatních prohlížečích.  
1. Jako první krok k obc: Obejít tyto problémy, ujistěte se, že doména aplikace a všechny další weby zapojené do přesměrování toku ověřování jsou přidány jako důvěryhodné servery v nastavení zabezpečení prohlížeče tak, aby patřily do stejné zóny zabezpečení.
Postup je následující:
    - Otevřete **Internet Explorer** a klikněte na **nastavení** (ikona ozubeného kola) v pravém horním rohu
    - Vybrat **možnosti Internetu**
    - Výběr karty **Zabezpečení**
    - V části **Důvěryhodné servery** klikněte na tlačítko **Weby** a přidejte adresy URL v zobrazeném dialogovém okně.

2. Jak již bylo zmíněno dříve, vzhledem k tomu, že pouze úložiště relace je vymazána během pravidelné navigace, můžete nakonfigurovat MSAL.js použít místní úložiště místo. To lze nastavit `cacheLocation` jako konfigurační parametr při inicializaci MSAL.

Všimněte si, že to nevyřeší problém pro procházení InPrivate, protože relace a místní úložiště jsou vymazány.

## <a name="issues-due-to-popup-blockers"></a>Problémy způsobené blokováním automaticky otevíraných čísel

Existují případy, kdy jsou vyskakovací okna blokována v aplikaci IE nebo Microsoft Edge, například když dojde k druhému vyskakovacímu okna během vícefaktorového ověřování. Dostanete upozornění v prohlížeči, aby pro popup jednou nebo vždy. Pokud se rozhodnete povolit, prohlížeč automaticky otevře vyskakovací okno a vrátí pro něj `null` popisovač. V důsledku toho knihovna nemá popisovač pro okno a neexistuje žádný způsob, jak zavřít vyskakovací okno. Stejný problém se nestane v Prohlížeči Chrome, když vás vyzve k povolení vyskakovacích oken, protože automaticky neotevře vyskakovací okno.

Jako **řešení**, vývojáři budou muset povolit vyskakovací okno v aplikaci IE a Microsoft Edge, než začnou používat svou aplikaci, aby se zabránilo tomuto problému.

## <a name="next-steps"></a>Další kroky
Další informace o [používání souboru MSAL.js v aplikaci Internet Explorer](msal-js-use-ie-browser.md).

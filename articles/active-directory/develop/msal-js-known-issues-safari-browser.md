---
title: Známé problémy v prohlížečích (knihovna Microsoft Authentication Library pro JavaScript) | Azure
description: Další informace o vědět problémy při použití knihovna Microsoft Authentication Library pro JavaScript (MSAL.js) s prohlížečem Safari.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb89b1ef4dbbef234fba3152d7f85bbadfbdc64a
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873882"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Známé problémy v prohlížeči Safari se MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Bezobslužné obnovení tokenu v Safari 12 a ITP 2.0

Apple iOS 12 a operační systémy MacOS 10.14 zahrnuté verzích [prohlížeče Safari 12](https://developer.apple.com/safari/whats-new/). Pro účely zabezpečení a ochrany osobních údajů zahrnuje Safari 12 [inteligentní ochrany před únikem informací 2.0 sledování](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). To v podstatě způsobí, že prohlížeč vyřadit soubory cookie třetích stran nastavena. ITP 2.0 zpracovává soubory cookie, nastavte pomocí zprostředkovatele identity jako soubory cookie třetích stran.

### <a name="impact-on-msaljs"></a>Dopad na MSAL.js

MSAL.js používá k provádění získání tokenu služby tichou a obnovení v rámci skrytého elementu Iframe `acquireTokenSilent` volání. Tiché žádostí o token využívají Iframe mají přístup k relaci ověřeného uživatele reprezentované soubory cookie, nastavením Azure AD. Pomocí ITP 2.0 brání přístupu k tyto soubory cookie, MSAL.js nepodaří nepozorovaně získat a obnovit tokeny a výsledkem je `acquireTokenSilent` selhání.

Neexistuje žádné řešení tohoto problému v tomto okamžiku a jsme vyhodnocují možnosti s komunitou standardy.

### <a name="work-around"></a>Alternativní postup

Ve výchozím nastavení je povolené nastavení ITP v prohlížeči Safari. Toto nastavení můžete zakázat tak, že přejdete do **Předvolby** -> **ochrany osobních údajů** a zrušíte zaškrtnutí **zabránit sledování webů** možnost.

![nastavení prohlížeče Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Je potřeba zpracovat `acquireTokenSilent` selhání s interaktivním získat token volání, které se zobrazí výzva k přihlášení.
Aby se zabránilo opakované přihlášení, můžete implementovat přístup je pro zpracování `acquireTokenSilent` selhání a poskytnout možnost zakázat nastavení ITP v prohlížeči Safari před pokračováním interaktivní volání uživatele. Jakmile je zakázáno, by být úspěšné následné tiché token obnovení.

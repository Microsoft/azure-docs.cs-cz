---
title: Známé problémy prohlížeče Safari (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s informacemi o potížích při použití knihovny Microsoft Authentication Library pro JavaScript (MSAL.js) v prohlížeči Safari.
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
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76696108"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Známé problémy v prohlížeči Safari s MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Obnovení tichého tokenu v Safari 12 a ITP 2,0

Operační systémy Apple iOS 12 a MacOS 10,14 obsahují verzi [prohlížeče Safari 12](https://developer.apple.com/safari/whats-new/). Pro účely zabezpečení a ochrany osobních údajů zahrnuje prohlížeč Safari 12 [inteligentní prevenci sledování 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). To v podstatě způsobí, že prohlížeč přetáhne soubory cookie třetích stran. ITP 2,0 také zpracovává soubory cookie nastavené zprostředkovateli identity jako soubory cookie třetích stran.

### <a name="impact-on-msaljs"></a>Dopad na MSAL.js

MSAL.js používá skrytý prvek IFRAME k provádění pasivního získání a obnovení tokenu v rámci `acquireTokenSilent` volání. Požadavky na tiché tokeny spoléhají na prvek IFRAME, který má přístup k ověřené relaci uživatele reprezentované soubory cookie nastavenými službou Azure AD. Díky ITP 2,0 nebráníte přístupu k těmto souborům cookie, MSAL.js nedokáže tiše získávat a obnovovat tokeny a má za následek `acquireTokenSilent` selhání.

V tuto chvíli neexistují žádné řešení tohoto problému a vyhodnocujeme možnosti pomocí komunity standardů.

### <a name="work-around"></a>Alternativní řešení

Ve výchozím nastavení je nastavení ITP povolené v prohlížeči Safari. Toto nastavení můžete zakázat tak, že přejdete na **Předvolby**  ->  **ochrany osobních údajů** a zrušíte kontrolu volby **zakázat sledování mezi weby** .

![nastavení Safari](./media/msal-js-known-issue-safari-browser/safari.png)

Tyto chyby bude nutné zpracovat `acquireTokenSilent` pomocí interaktivního volání metody získat, které vyzve uživatele k přihlášení.
Aby nedocházelo k opakovanému přihlášení, je možné implementovat přístup k `acquireTokenSilent` selhání a poskytnout uživateli možnost zakázat nastavení ITP v Safari před pokračováním v interaktivním volání. Jakmile je nastavení zakázané, další obnovení s bezobslužným tokenem by mělo být úspěšné.

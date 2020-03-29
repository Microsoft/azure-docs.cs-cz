---
title: Známé problémy prohlížeče Safari (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Informace o informacích o problémech při používání Knihovny microsoft authentication library pro JavaScript (MSAL.js) s prohlížečem Safari.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696108"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Známé problémy v prohlížeči Safari s MSAL.js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Tiché obnovení tokenu v Safari 12 a ITP 2.0

Operační systémy Apple iOS 12 a MacOS 10.14 obsahovaly vydání [prohlížeče Safari 12](https://developer.apple.com/safari/whats-new/). Pro účely zabezpečení a ochrany osobních údajů, Safari 12 obsahuje [inteligentní prevence sledování 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). To v podstatě způsobí, že prohlížeč přepne soubory cookie třetích stran, které jsou nastaveny. ITP 2.0 také zachází se soubory cookie nastavenými poskytovateli identit jako se soubory cookie třetích stran.

### <a name="impact-on-msaljs"></a>Dopad na MSAL.js

MSAL.js používá skrytý iframe k provedení silent token `acquireTokenSilent` získávání a obnovení jako součást volání. Požadavky na tichý token spoléhají na to, že iframe má přístup k relaci ověřeného uživatele reprezentované soubory cookie nastavenými službou Azure AD. S ITP 2.0 brání přístupu k těmto souborům cookie, MSAL.js nedokáže `acquireTokenSilent` tiše získat a obnovit tokeny a to má za následek selhání.

V tuto chvíli neexistuje žádné řešení tohoto problému a vyhodnocujeme možnosti s komunitou standardů.

### <a name="work-around"></a>Obejít

Ve výchozím nastavení je v prohlížeči Safari povoleno nastavení ITP. Toto nastavení můžete zakázat tak, že přejdete na položku Ochrana**osobních údajů** **v předvolbách** -> a porušíte zaškrtnutí **možnosti Zabránit sledování mezi servery.**

![nastavení safari](./media/msal-js-known-issue-safari-browser/safari.png)

Budete muset zpracovat `acquireTokenSilent` chyby s interaktivní volání tokenu získat, který vyzve uživatele k přihlášení.
Chcete-li se vyhnout opakovanému přihlášení, přístup, `acquireTokenSilent` který můžete implementovat, je zpracovat selhání a poskytnout uživateli možnost zakázat nastavení ITP v Safari před pokračováním interaktivního volání. Jakmile je nastavení zakázáno, následné obnovení tichého tokenu by mělo být úspěšné.

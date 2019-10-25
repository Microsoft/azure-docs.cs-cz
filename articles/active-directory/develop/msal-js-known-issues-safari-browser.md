---
title: Známé problémy v prohlížečích (Microsoft Authentication Library pro JavaScript)
titleSuffix: Microsoft identity platform
description: Seznamte se s informacemi o potížích při použití knihovny Microsoft Authentication Library pro JavaScript (MSAL. js) v prohlížeči Safari.
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
ms.openlocfilehash: 51d800ea2fbbc733a6213d7bc4f61f955612aba0
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803068"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Známé problémy v prohlížeči Safari pomocí MSAL. js 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Obnovení tichého tokenu v Safari 12 a ITP 2,0

Operační systémy Apple iOS 12 a MacOS 10,14 obsahují verzi [prohlížeče Safari 12](https://developer.apple.com/safari/whats-new/). Pro účely zabezpečení a ochrany osobních údajů zahrnuje prohlížeč Safari 12 [inteligentní prevenci sledování 2,0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/). To v podstatě způsobí, že prohlížeč přetáhne soubory cookie třetích stran. ITP 2,0 také zpracovává soubory cookie nastavené zprostředkovateli identity jako soubory cookie třetích stran.

### <a name="impact-on-msaljs"></a>Dopad na MSAL. js

MSAL. js pomocí skrytého prvku IFRAME provede získání a obnovení tichého tokenu v rámci `acquireTokenSilent` volání. Požadavky na tiché tokeny spoléhají na prvek IFRAME, který má přístup k ověřené relaci uživatele reprezentované soubory cookie nastavenými službou Azure AD. Díky ITP 2,0, který brání přístupu k těmto souborům cookie, MSAL. js nedokáže tiše získat a obnovit tokeny a tím dojde k selhání `acquireTokenSilent`.

V tuto chvíli neexistují žádné řešení tohoto problému a vyhodnocujeme možnosti pomocí komunity standardů.

### <a name="work-around"></a>Alternativní řešení

Ve výchozím nastavení je nastavení ITP povolené v prohlížeči Safari. Toto nastavení můžete zakázat tak, že přejdete na **předvolby** -> **ochrany osobních údajů** a zrušíte kontrolu možností pro možnost **zakázat sledování mezi lokalitami** .

![nastavení Safari](./media/msal-js-known-issue-safari-browser/safari.png)

U interaktivního volání metody získat, které vyzve uživatele k přihlášení, budete muset `acquireTokenSilent` selhání zpracovat.
Abyste se vyhnuli opakovaným přihlášením, je přístup, který můžete implementovat, sloužit k tomu, aby `acquireTokenSilent` selhání a poskytoval uživateli možnost zakázat nastavení ITP v Safari před pokračováním v interaktivním volání. Jakmile je nastavení zakázané, další obnovení s bezobslužným tokenem by mělo být úspěšné.

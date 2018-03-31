---
title: Azure Mobile Engagement webové rozhraní API sady SDK | Microsoft Docs
description: Nejnovější aktualizace a postupy pro Web SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8a87d5ac-d8b7-4a0d-bdee-414dbcc561b2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 6d2ae75b384b60d0383c1682a00a4fc0d19d0f43
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Ve webové aplikaci pomocí rozhraní API služby Azure Mobile Engagement
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento dokument je doplňkem k dokumentu, který se dozvíte, jak k [integrovat Mobile Engagement ve webové aplikaci](mobile-engagement-web-integrate-engagement.md). Poskytuje podrobné informace týkající se sestav vaší statistik aplikace pomocí rozhraní API služby Azure Mobile Engagement.

Rozhraní API služby Mobile Engagement poskytuje `engagement.agent` objektu. Výchozí sada SDK webové služby Azure Mobile Engagement alias je `engagement`. Můžete změnit definici tento alias z konfigurace sady SDK.

## <a name="mobile-engagement-concepts"></a>Koncepty Mobile Engagementu
Následující části Upřesnit běžné [koncepty Mobile Engagementu](mobile-engagement-concepts.md) pro webovou platformu.

### <a name="session-and-activity"></a>`Session` a `Activity`
Pokud uživatel zůstane nečinné více než několik sekund mezi dvěma aktivitami, uživatele posloupnost aktivit je rozdělit na dvě odlišné relace. Tyto několik sekund, se nazývají časový limit relace.

Pokud webová aplikace není deklarovat konec aktivity uživatele samostatně (voláním `engagement.agent.endActivity` funkce), server Mobile Engagement automaticky vyprší platnost relace uživatele do tří minut po zavření stránky aplikace. Tomu se říká časový limit relace serveru.

### `Crash`
Ve výchozím nastavení nejsou vytvořeny automatizované sestavy nezachycená výjimek jazyka JavaScript. Však může hlásit havárií ručně pomocí `sendCrash` funkce (viz část o nahlašování dojde k chybě).

## <a name="reporting-activities"></a>Sestavy aktivit
Zprávy o aktivity uživatelů zahrnuje, když uživatel spustí novou aktivitu, a když uživatel končí aktuální aktivita.

### <a name="user-starts-a-new-activity"></a>Uživatel spustí novou aktivitu
    engagement.agent.startActivity("MyUserActivity");

Je třeba volat `startActivity()` Každá aktivita uživatele čas změny. První volání této funkce spustí novou relaci uživatele.

### <a name="user-ends-the-current-activity"></a>Uživatel končí aktuální aktivita
    engagement.agent.endActivity();

Je třeba volat `endActivity()` alespoň jednou po dokončení uživatele jejich poslední aktivita. Tento příkaz informuje Mobile Engagement Web SDK, že uživatel je v současné době nečinnosti a že se musí po vypršení časového limitu relace je třeba ukončit relaci uživatele. Když zavoláte `startActivity()` předtím, než vyprší časový limit relace, relaci jednoduše obnovit.

Protože neexistuje žádný spolehlivý volání pro při zavření okna Navigátor, je to často složité nebo dokonce znemožňují catch konec aktivity uživatele v prostředí webové. To je důvod, proč server Mobile Engagement automaticky vyprší platnost relace uživatele do tří minut po zavření stránky aplikace.

## <a name="reporting-events"></a>Události vytváření sestav
Zprávy o události popisuje relace události a události samostatné.

### <a name="session-events"></a>Události relací
Relace události se obvykle používají k hlášení akcí prováděná uživatelem během relace uživatele.

**Příklad bez doplňující data:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Příklad s doplňující data:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Samostatné události
Na rozdíl od relace události může dojít k událostem samostatné mimo kontext relace.

K tomu použít ``engagement.agent.sendEvent`` místo ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Zasílání zpráv o chybách
Zprávy o chybách obsahuje chyby relace a samostatné chyby.

### <a name="session-errors"></a>Chyby relace
Relace chyby jsou obvykle používají k hlášení chyb, které mají vliv na uživatele během relace uživatele.

**Příklad bez doplňující data:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Příklad s doplňující data:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Samostatné chyby
Na rozdíl od relace chyb může dojít k chybám samostatné mimo kontext relace.

K tomu použít `engagement.agent.sendError` místo `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Úlohy sestav
Zprávy o zahrnuje úlohy vytváření sestav chyb a událostí, ke kterým došlo během úlohy a vytváření sestav dojde k chybě.

**Příklad:**

Pokud chcete monitorovat požadavek AJAX, použijete následující:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Generování sestav chyb během úlohy
Chyby může souviset s probíhající úlohou místo pro aktuální relaci uživatele.

**Příklad:**

Pokud chcete zobrazovat chyby, pokud se nezdaří požadavek AJAX:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Události vytváření sestav během úlohy
Události může souviset s probíhající úlohou místo pro aktuální uživatelskou relaci, Poděkování `engagement.agent.sendJobEvent` funkce.

Tato funkce funguje stejně jako `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Vytváření sestav dojde k chybě
Použití `sendCrash` funkce do sestavy dojde k chybě ručně.

`crashid` Argument je řetězec, který určuje typ havárie.
`crash` Argument je obvykle trasování zásobníku havárie jako řetězec.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Další parametry
Libovolná data můžete připojit k události, chyba, aktivity nebo úlohy.

Data může být jakýkoli objekt JSON (ale ne pole nebo primitivní typ).

**Příklad:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Omezení
Omezení, které platí pro další parametry jsou v oblastech regulárních výrazů pro klíče, hodnotové typy a velikosti.

#### <a name="keys"></a>Klíče
Každý klíč v objektu musí odpovídat následujícímu regulárnímu výrazu:

    ^[a-zA-Z][a-zA-Z_0-9]*

To znamená, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="values"></a>Hodnoty
Hodnoty jsou omezeny na řetězec, počtu a typů logická hodnota.

#### <a name="size"></a>Velikost
Funkce jsou omezeny na 1024 znaků na jednu volání (po Mobile Engagement SDK webové kóduje ho ve formátu JSON).

## <a name="reporting-application-information"></a>Informace o vytváření sestav aplikace
Můžete ručně odesílat zprávy o sledování informace (nebo všechny ostatní informace specifické pro aplikace) pomocí `sendAppInfo()` funkce.

Všimněte si, že tyto údaje lze odeslat postupně. Pro určité zařízení se zachová pouze nejnovější hodnotu pro konkrétní klíč.

Jako funkce událostí můžete použít libovolný objekt JSON abstrahovat informace o aplikaci. Všimněte si, že pole nebo dílčí objekty jsou považovány za plochý řetězce (pomocí serializace JSON).

**Příklad:**

Zde je ukázka kódu pro odesílání pohlaví uživatele a datum narození:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Omezení
Omezení, která se týkají informací o aplikaci nejsou v oblastech regulárních výrazů pro klíče a velikost.

#### <a name="keys"></a>Klíče
Každý klíč v objektu musí odpovídat následujícímu regulárnímu výrazu:

    ^[a-zA-Z][a-zA-Z_0-9]*

To znamená, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="size"></a>Velikost
Informace o aplikaci je omezená na 1024 znaků na jednu volání (po Mobile Engagement SDK webové kóduje ho ve formátu JSON).

V předchozím příkladu je JSON odeslat na server 44 znaků:

    {"birthdate":"1983-12-07","gender":"female"}

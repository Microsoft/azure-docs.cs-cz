---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 922a34bd1a147d407041d2945b14df18a18d7287
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761247"
---
Při klonování konfigurace z jiného slotu nasazení je naklonovaná konfigurace upravitelná. Některé prvky konfigurace následují po prohození obsahu (ne pro konkrétní sloty), zatímco jiné konfigurační prvky zůstávají na stejném slotu po prohození (specifické pro slot). Následující seznamy obsahují nastavení, která se mění při prohození slotů.

**Nastavení, která jsou prohozena**:

* Obecná nastavení, například verze architektury, 32 nebo 64 bitů, webové sokety
* Nastavení aplikace (může být nakonfigurováno na slot)
* Připojovací řetězce (můžou být nakonfigurované tak, aby se nastavily na slot)
* Mapování obslužných rutin
* Veřejné certifikáty
* Obsah webových úloh
* Hybridní připojení *
* Koncové body služby *
* Content Delivery Network Azure *

Funkce označené hvězdičkou (*) jsou plánovány jako neswapé. 

**Neswapá nastavení**:

* Publikování koncových bodů
* Vlastní názvy domén
* Neveřejné certifikáty a nastavení TLS/SSL
* Nastavení škálování
* Plánovače WebJobs
* Omezení IP adresy
* Vždy zapnuto
* Nastavení diagnostiky
* Sdílení prostředků mezi zdroji (CORS)
* Integrace virtuální sítě
* Mapování cest

> [!NOTE]
> Chcete-li tato nastavení vyměnit, přidejte nastavení aplikace `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` do každé pozice aplikace a nastavte její hodnotu na `0` nebo `false` . Tato nastavení jsou buď všechna zaměnitelné, nebo vůbec ne. Nemůžete dělat jenom některá nastavení, která by se dala měnit, a ne ostatní.

> Některá nastavení aplikace, která platí pro nezaměnitelné nastavení, se také nemění. Například vzhledem k tomu, že nastavení diagnostiky nejsou zaměněna, související nastavení aplikace, například `WEBSITE_HTTPLOGGING_RETENTION_DAYS` a `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` , se nemění ani v případě, že se nezobrazují jako nastavení slotu.
>

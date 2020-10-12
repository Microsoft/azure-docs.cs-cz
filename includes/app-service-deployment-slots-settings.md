---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: b4234acb2ce32980a268e389cb31de9a57ed18e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82131542"
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
* Integrace virtuální sítě *
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

> [!NOTE]
> Některá nastavení aplikace, která platí pro nezaměnitelné nastavení, se také nemění. Například vzhledem k tomu, že nastavení diagnostiky nejsou zaměněna, související nastavení aplikace, například `WEBSITE_HTTPLOGGING_RETENTION_DAYS` a `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` , se nemění ani v případě, že se nezobrazují jako nastavení slotu.
>

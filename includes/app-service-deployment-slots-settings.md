---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129694"
---
Při klonování konfigurace z jiného slotu nasazení je naklonovaná konfigurace upravitelná. Některé prvky konfigurace následují po prohození obsahu (ne pro konkrétní sloty), zatímco jiné konfigurační prvky zůstávají na stejném slotu po prohození (specifické pro slot). Následující seznamy obsahují nastavení, která se mění při prohození slotů.

**Nastavení, která jsou**prohozena:

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
* Názvy vlastních domén
* Neveřejné certifikáty a nastavení TLS/SSL
* Nastavení škálování
* Plánovače WebJobs
* Omezení IP adresy
* Stálé připojení
* Nastavení diagnostického protokolu
* Sdílení prostředků mezi zdroji (CORS)

> [!NOTE]
> Některá nastavení aplikace, která platí pro nezaměnitelné nastavení, se také nemění. Například vzhledem k tomu, že nastavení diagnostického protokolu není zaměněno, související `WEBSITE_HTTPLOGGING_RETENTION_DAYS` nastavení `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` aplikací, jako jsou a, se také nemění, i když se nezobrazí jako nastavení slotu.
>

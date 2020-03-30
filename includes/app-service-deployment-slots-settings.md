---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: e00db06346b19ef85eb77626eb2ed169d2224b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71129694"
---
Při klonování konfigurace z jiného slotu nasazení klonované konfigurace je upravitelná. Některé konfigurační prvky sledovat obsah přes swap (není slot specifické), vzhledem k tomu, že ostatní prvky konfigurace zůstávají ve stejném slotu po prohození (slot specifické). Následující seznamy zobrazují nastavení, která se mění při výměně slotů.

**Nastavení, která jsou proměněna**:

* Obecná nastavení, například verze frameworku, 32/64bitové, webové sokety
* Nastavení aplikace (lze nakonfigurovat tak, aby se drželo slotu)
* Připojovací řetězce (lze nakonfigurovat tak, aby se držely slotu)
* Mapování obslužné rutiny
* Veřejné certifikáty
* WebJobs obsah
* Hybridní připojení *
* Integrace virtuální sítě *
* Koncové body služby *
* Síť pro doručování obsahu Azure *

Funkce označené hvězdičkou (*) jsou plánovány jako nezvěsné. 

**Nastavení, která nejsou proměněna**:

* Publikování koncových bodů
* Vlastní názvy domén
* Neveřejné certifikáty a nastavení TLS/SSL
* Nastavení měřítka
* Plánovače webových úloh
* Omezení IP adresy
* Vždy zapnuto
* Nastavení diagnostického protokolu
* Sdílení zdrojů mezi zdroji (CORS)

> [!NOTE]
> Některá nastavení aplikací, která se vztahují na nezvěsněná nastavení, také nejsou zaměněna. Například vzhledem k tomu, že nastavení diagnostického protokolu nejsou prohozeny, související nastavení aplikace se líbí `WEBSITE_HTTPLOGGING_RETENTION_DAYS` a `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` také nejsou proměněna, i když se nezobrazují jako nastavení slotu.
>

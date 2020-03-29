---
title: Sledování stavu prostředků Azure CDN| Dokumenty společnosti Microsoft
description: Zjistěte, jak sledovat stav prostředků Azure CDN pomocí Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593444"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Sledování stavu prostředků Azure CDN
  
Azure CDN resource health je podmnožinou [stavu prostředků Azure](../resource-health/resource-health-overview.md).  Stav prostředků Azure můžete použít ke sledování stavu prostředků CDN a získat užitečné pokyny k řešení problémů.

>[!IMPORTANT] 
>Azure CDN stavu prostředků jenom účty pro stav globální chod CDN a možnosti rozhraní API.  Azure CDN prostředek stavu neověřuje jednotlivé koncové body CDN.
>
>Signály, které dodávají azure cdn stavu prostředků může být až 15 minut zpoždění.

## <a name="how-to-find-azure-cdn-resource-health"></a>Jak najít azure cdn stavu prostředků

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na svůj profil CDN.

2. Klikněte na tlačítko **Nastavení.**

    ![Tlačítko Nastavení](./media/cdn-resource-health/cdn-profile-settings.png)

3. V části *Podpora + řešení potíží*klepněte na **položku Stav prostředků**.

    ![Stav prostředků CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Můžete také najít prostředky CDN uvedené v dlaždici *Stav prostředků* v nápovědě *+ podpora* okno.  Můžete rychle dostat do *nápovědy + podporu* kliknutím na v kroužku **?** v pravém horním rohu portálu.
>
> ![Nápověda a podpora](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Zprávy specifické pro Azure CDN

Stavy související se stavem prostředků Azure CDN najdete níže.

|Zpráva | Doporučená akce |
|---|---|
|Možná jste zastavili, odebrali nebo chybně nakonfigurovali nejméně jeden koncový bod CDN. | Možná jste zastavili, odebrali nebo chybně nakonfigurovali nejméně jeden koncový bod CDN.|
|Je nám líto, ale služba pro správu CDN není aktuálně k dispozici. | Zkontrolujte, zda zde aktualizace stavu; Pokud váš problém přetrvává i po očekávanédobě řešení, obraťte se na podporu.|
|Je nám líto, ale na koncové body CDN můžou mít vliv aktuální problémy s některými z našich poskytovatelů CDN. | Zkontrolujte, zda zde aktualizace stavu; Pomocí nástroje poradce při potížích se dozvíte, jak otestovat svůj původ a koncový bod CDN. Pokud váš problém přetrvává i po očekávanédobě řešení, obraťte se na podporu. |
|Je nám líto, ale při šíření změn konfigurace koncových bodů CDN dochází ke zpožděním. | Zkontrolujte, zda zde aktualizace stavu; Pokud změny konfigurace nejsou plně šířeny v očekávaném čase, obraťte se na podporu.|
|Je nám líto, ale objevují se problémy při načítání doplňkového portálu. | Zkontrolujte, zda zde aktualizace stavu; Pokud váš problém přetrvává i po očekávanédobě řešení, obraťte se na podporu.|
Je nám líto, ale setkáváme se s problémy u některých z našich poskytovatelů CDN. | Zkontrolujte, zda zde aktualizace stavu; Pokud váš problém přetrvává i po očekávanédobě řešení, obraťte se na podporu. |

## <a name="next-steps"></a>Další kroky

- [Přečtěte si přehled stavu prostředků Azure](../resource-health/resource-health-overview.md)
- [Poradce při potížích s kompresí CDN](./cdn-troubleshoot-compression.md)
- [Poradce při potížích se 404 chybami](./cdn-troubleshoot-endpoint.md)
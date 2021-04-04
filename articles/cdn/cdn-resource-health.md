---
title: Monitorování stavu prostředků Azure CDN | Microsoft Docs
description: Naučte se monitorovat stav svých Azure CDNch prostředků pomocí Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778169"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorování stavu prostředků Azure CDN
  
Stav prostředku Azure CDN je podmnožinou [stavu prostředků Azure](../service-health/resource-health-overview.md).  Pomocí služby Azure Resource Health můžete monitorovat stav prostředků CDN a získávat užitečné pokyny k odstraňování problémů.

>[!IMPORTANT] 
>Azure CDN stav prostředků jenom v současnosti účty pro stav globálních doručování CDN a funkcí rozhraní API.  Azure CDN Resource Health neověřuje jednotlivé koncové body CDN.
>
>Signály, které informační stav prostředku Azure CDN, můžou být zpožděné až o 15 minut.

## <a name="how-to-find-azure-cdn-resource-health"></a>Jak najít stav prostředku Azure CDN

1. V [Azure Portal](https://portal.azure.com)přejděte do svého profilu CDN.

2. Klikněte na tlačítko **Nastavení** .

    ![Tlačítko Nastavení](./media/cdn-resource-health/cdn-profile-settings.png)

3. V části *Podpora a řešení potíží* klikněte na **stav prostředků**.

    ![Stav prostředku CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Prostředky CDN uvedené na dlaždici *stav prostředku* můžete najít také v okně *help + Support (podpora* ).  Kliknutím na kruh můžete rychle získat *nápovědu a podporu* .  v pravém horním rohu portálu.
>
> ![Nápověda a podpora](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Zprávy specifické pro Azure CDN

Stavy týkající se Azure CDNho stavu prostředků najdete níže.

|Zpráva | Doporučená akce |
|---|---|
|Možná jste zastavili, odebrali nebo chybně nakonfigurovali nejméně jeden koncový bod CDN. | Možná jste zastavili, odebrali nebo chybně nakonfigurovali nejméně jeden koncový bod CDN.|
|Je nám líto, ale služba pro správu CDN není aktuálně k dispozici. | Pokud chcete aktualizovat stav, vraťte se sem. Pokud váš problém přetrvává i po očekávané době řešení, obraťte se na podporu.|
|Je nám líto, ale na koncové body CDN můžou mít vliv aktuální problémy s některými z našich poskytovatelů CDN. | Pokud chcete aktualizovat stav, vraťte se sem. Pomocí nástroje řešení potíží se dozvíte, jak otestovat původní koncový bod a koncový bod CDN. Pokud váš problém přetrvává i po očekávané době řešení, obraťte se na podporu. |
|Je nám líto, ale při šíření změn konfigurace koncových bodů CDN dochází ke zpožděním. | Pokud chcete aktualizovat stav, vraťte se sem. Pokud vaše změny konfigurace nejsou v očekávaném čase zcela rozšířeny, obraťte se na podporu.|
|Je nám líto, ale objevují se problémy při načítání doplňkového portálu. | Pokud chcete aktualizovat stav, vraťte se sem. Pokud váš problém přetrvává i po očekávané době řešení, obraťte se na podporu.|
Je nám líto, ale setkáváme se s problémy u některých z našich poskytovatelů CDN. | Pokud chcete aktualizovat stav, vraťte se sem. Pokud váš problém přetrvává i po očekávané době řešení, obraťte se na podporu. |

## <a name="next-steps"></a>Další kroky

- [Přečtěte si přehled stavu prostředků Azure](../service-health/resource-health-overview.md)
- [Řešení potíží s kompresí CDN](./cdn-troubleshoot-compression.md)
- [Řešení potíží s 404 chybami](./cdn-troubleshoot-endpoint.md)
---
title: Monitorování stavu prostředků Azure CDN | Dokumentace Microsoftu
description: Zjistěte, jak monitorovat stav svých prostředků Azure CDN pomocí Azure Resource Health.
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593444"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorování stavu prostředků Azure CDN
  
Azure CDN Resource health je podmnožinou [Azure resource health](../resource-health/resource-health-overview.md).  Azure resource health slouží k monitorování stavu prostředků CDN a dostávat podrobné pokyny k odstraňování problémů.

>[!IMPORTANT] 
>Stav prostředku Azure CDN pouze aktuálně účty pro stav globální doručování CDN a funkcí rozhraní API.  Stav prostředku Azure CDN neověřuje jednotlivých koncových bodů CDN.
>
>Signály, které kanálu stavu prostředků Azure CDN může být až 15 minut zpožděné.

## <a name="how-to-find-azure-cdn-resource-health"></a>Postup k zjištění stavu prostředků Azure CDN

1. V [webu Azure portal](https://portal.azure.com), přejděte na svůj profil CDN.

2. Klikněte na tlačítko **nastavení** tlačítko.

    ![Tlačítko Nastavení](./media/cdn-resource-health/cdn-profile-settings.png)

3. V části *podpora a řešení potíží*, klikněte na tlačítko **Resource health**.

    ![Stav prostředku CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Můžete také vyhledat prostředky CDN, které jsou uvedené v *Resource health* v dlaždici *Nápověda a podpora* okno.  Můžete rychle získat *Nápověda a podpora* kliknutím v kroužku **?** v pravém horním rohu portálu.
>
> ![Nápověda a podpora](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN konkrétní zprávy

Stavy související s Azure CDN služba resource health najdete níže.

|Message | Doporučená akce |
|---|---|
|Možná jste zastavili, odebrali nebo chybně nakonfigurovali nejméně jeden koncový bod CDN. | Možná jste zastavili, odebrali nebo chybně nakonfigurovali nejméně jeden koncový bod CDN.|
|Je nám líto, ale služba pro správu CDN není aktuálně k dispozici. | Kontrolujte stav aktualizací; Pokud váš problém nevyřeší ani po očekávané době řešení, obraťte se na podporu.|
|Je nám líto, ale na koncové body CDN můžou mít vliv aktuální problémy s některými z našich poskytovatelů CDN. | Kontrolujte stav aktualizací; Použijte nástroj řešení potíží se naučíte testování původu a koncový bod CDN; Pokud váš problém nevyřeší ani po očekávané době řešení, obraťte se na podporu. |
|Je nám líto, ale při šíření změn konfigurace koncových bodů CDN dochází ke zpožděním. | Kontrolujte stav aktualizací; Pokud se změny konfigurace plně nerozšíří v očekávaném čase, obraťte se na podporu.|
|Je nám líto, ale objevují se problémy při načítání doplňkového portálu. | Kontrolujte stav aktualizací; Pokud váš problém nevyřeší ani po očekávané době řešení, obraťte se na podporu.|
Je nám líto, ale setkáváme se s problémy u některých z našich poskytovatelů CDN. | Kontrolujte stav aktualizací; Pokud váš problém nevyřeší ani po očekávané době řešení, obraťte se na podporu. |

## <a name="next-steps"></a>Další postup

- [Přečtěte si přehled o stavu prostředků Azure](../resource-health/resource-health-overview.md)
- [Řešení potíží s kompresí CDN](./cdn-troubleshoot-compression.md)
- [Řešení potíží s chyby 404](./cdn-troubleshoot-endpoint.md)
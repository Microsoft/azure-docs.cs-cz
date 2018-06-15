---
title: Monitorování stavu prostředků Azure CDN | Microsoft Docs
description: Naučte se monitorovat stav svých prostředků Azure CDN pomocí Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842892"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorování stavu prostředků Azure CDN
  
Stav Azure CDN prostředku je podmnožinou [stavu prostředků Azure](../resource-health/resource-health-overview.md).  Stav prostředků Azure můžete sledovat stav CDN prostředků a přijímat řešitelné pokyny k odstraňování problémů.

>[!IMPORTANT] 
>Stav prostředku Azure CDN pouze aktuálně účty pro stav globální doručování a možnosti rozhraní API.  Stav prostředku Azure CDN neověřuje jednotlivých koncových bodů CDN.
>
>Signály, které kanálu stav prostředku Azure CDN může být až o 15 minut zpožděna.

## <a name="how-to-find-azure-cdn-resource-health"></a>Postup nalezení stav prostředku Azure CDN

1. V [portál Azure](https://portal.azure.com), přejděte na svůj profil CDN.

2. Klikněte **nastavení** tlačítko.

    ![Tlačítko Nastavení](./media/cdn-resource-health/cdn-profile-settings.png)

3. V části *podpory a řešení potíží s*, klikněte na tlačítko **stav prostředku**.

    ![Stav prostředku CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Můžete také vyhledat CDN prostředky uvedené v *stav prostředku* dlaždice v nástroji *Nápověda a podpora* okno.  Můžete rychle získat k *Nápověda a podpora* kliknutím v kroužku **?** v pravém horním rohu portálu.
>
> ![Nápověda a podpora](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN konkrétní zprávy

Stavy související s stav prostředku Azure CDN najdete níže.

|Zpráva | Doporučená akce |
|---|---|
|Vám může mít zastavena, odebráno nebo špatně nakonfigurovaný minimálně jeden koncové body CDN | Vám může mít zastavena, odebráno nebo špatně nakonfigurovaný minimálně jeden koncové body CDN.|
|Je nám líto, služba správy CDN není aktuálně dostupná | Pravidelně kontrolujte stav aktualizací; Pokud váš problém přetrvává po čase očekávané řešení, obraťte se na podporu.|
|Je nám líto, že koncové body CDN může být ovlivněno probíhající problémy s některé z našich poskytovatelů CDN | Pravidelně kontrolujte stav aktualizací; Pomocí nástroje Poradce při potížích se dozvíte, jak k testování počátečního a koncového bodu CDN; Pokud váš problém přetrvává po čase očekávané řešení, obraťte se na podporu. |
|Je nám líto, změny konfigurace koncového bodu CDN pozorují zpoždění šíření | Pravidelně kontrolujte stav aktualizací; Pokud vaše změny konfigurace nebudou rozšířeny plně v očekávaném čase, kontaktujte podporu.|
|Je nám líto, že nemůžeme mají problémy načítání na doplňkovém portálu | Pravidelně kontrolujte stav aktualizací; Pokud váš problém přetrvává po čase očekávané řešení, obraťte se na podporu.|
Je nám líto, že nemůžeme mají problémy se některé z našich poskytovatelů CDN | Pravidelně kontrolujte stav aktualizací; Pokud váš problém přetrvává po čase očekávané řešení, obraťte se na podporu. |

## <a name="next-steps"></a>Další kroky

- [Přečtěte si přehled o stavu prostředků Azure.](../resource-health/resource-health-overview.md)
- [Řešení problémů s kompresí CDN](./cdn-troubleshoot-compression.md)
- [Řešení problémů s chyb 404](./cdn-troubleshoot-endpoint.md)
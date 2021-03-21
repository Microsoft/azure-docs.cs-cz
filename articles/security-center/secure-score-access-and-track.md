---
title: Sledování zabezpečeného skóre v Azure Security Center
description: Přečtěte si o různých způsobech přístupu a sledování zabezpečeného skóre v Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107478"
---
# <a name="access-and-track-your-secure-score"></a>Přístup k zabezpečenému skóre a jejich sledování

Celkové výsledky zabezpečení a také skóre na předplatné můžete najít prostřednictvím Azure Portal nebo programově, jak je popsáno v následujících částech:

> [!TIP]
> Podrobné vysvětlení, jak se počítají vaše skóre, najdete v tématu [výpočty – princip skóre](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Získat zabezpečené skóre z portálu

Security Center zobrazuje vaše skóre na portálu na portálu: Jedná se o první hlavní dlaždici Security Center stránku s přehledem. Kliknutím na tuto dlaždici přejdete na stránku vyhrazeného zabezpečeného skóre, kde se zobrazí skóre rozdělené podle předplatného. Když vyberete jedno předplatné, zobrazí se podrobný seznam doporučení s upřednostněním a potenciální dopad, který by oprava na skóre předplatného. 

Rekapitulace se vaše zabezpečené skóre zobrazuje v následujících umístěních na stránkách portálu Security Center.

- V dlaždici v **přehledu** Security Center (hlavní řídicí panel):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Bezpečné skóre na řídicím panelu Security Center":::

- Na stránce vyhrazená **Zabezpečená skóre** uvidíte bezpečné skóre pro vaše předplatné a skupiny pro správu:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Zabezpečené skóre pro odběry na stránce zabezpečeného skóre Security Center":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Zabezpečená skóre pro skupiny pro správu na stránce zabezpečeného skóre Security Center":::

    > [!NOTE]
    > Všechny skupiny pro správu, pro které nemáte dostatečná oprávnění, zobrazí skóre jako "omezené". 

- V horní části stránky **doporučení** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Stránka s doporučením pro bezpečné skóre Security Center":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Získat zabezpečené skóre z REST API

K vašemu skóre můžete přistupovat prostřednictvím rozhraní API pro zabezpečené skóre. Metody rozhraní API poskytují flexibilitu pro dotazování na data a vytváření vlastních mechanismů generování sestav v rámci vašich zabezpečených výsledků v průběhu času. Můžete například použít [rozhraní API pro zabezpečení skóre](/rest/api/securitycenter/securescores) k získání skóre pro konkrétní předplatné. Kromě toho můžete použít [rozhraní API ovládacích prvků bezpečného řízení skóre](/rest/api/securitycenter/securescorecontrols) k vypsání ovládacích prvků zabezpečení a aktuálního skóre vašich předplatných.

![Načtení jediného zabezpečeného skóre přes rozhraní API](media/secure-score-security-controls/single-secure-score-via-api.png)

Příklady nástrojů postavených na rozhraní API pro zajištění skóre najdete v části [bezpečné skóre naší komunity GitHubu](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Získání zabezpečeného skóre z Azure Resource graphu

Azure Resource Graph poskytuje okamžitý přístup k informacím o prostředcích napříč vašimi cloudovým prostředím pomocí robustních funkcí filtrování, seskupování a řazení. Jedná se o rychlý a efektivní způsob dotazování informací v rámci předplatných Azure prostřednictvím Azure Portal. [Přečtěte si další informace o Azure Resource graphu](../governance/resource-graph/index.yml).

Přístup k zabezpečenému skóre pro více předplatných s využitím Azure Resource graphu:

1. V Azure Portal otevřete **Průzkumníka Azure Resource Graph**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Spouští se stránka pro doporučení pro Azure Resource Graph Explorer * *" :::

1. Zadejte svůj dotaz Kusto (použijte níže uvedené příklady pro doprovodné materiály).

    - Tento dotaz vrátí ID předplatného, aktuální skóre v bodech a jako procento a maximální skóre pro předplatné. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Tento dotaz vrátí stav všech ovládacích prvků zabezpečení. Pro každý ovládací prvek získáte počet prostředků, které nejsou v pořádku, aktuální skóre a maximální skóre. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Vyberte **Spustit dotaz**.


## <a name="tracking-your-secure-score-over-time"></a>Sledování zabezpečeného skóre v průběhu času

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Sestava zabezpečení skóre v průběhu času na stránce sešity

Stránka Security Center sešity obsahuje předem připravenou zprávu pro vizuální sledování skóre vašich předplatných, kontrol zabezpečení a dalších. Další informace [najdete v části vytváření bohatých interaktivních sestav Security Center dat](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Část sestavy zabezpečený počet skóre v čase z Galerie sešitů Azure Security Center":::

### <a name="power-bi-pro-dashboards"></a>Řídicí panely Power BI Pro

Pokud jste Power BI uživatel s účtem pro, můžete pomocí řídicího panelu **zabezpečení skóre v čase** Power BI sledovat vaše zabezpečené skóre v průběhu času a prozkoumat změny.

> [!TIP]
> Tento řídicí panel a další nástroje pro práci programově se zabezpečeným skóre najdete ve vyhrazené oblasti Azure Security Center komunity na GitHubu: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Řídicí panel obsahuje následující dvě sestavy, které vám pomůžou analyzovat stav zabezpečení:

- **Shrnutí prostředků** – poskytuje souhrnná data týkající se stavu vašich prostředků.
- **Shrnutí bezpečného skóre** – poskytuje souhrnná data týkající se průběhu vašeho skóre. Pomocí grafu "bezpečné skóre v průběhu času na předplatné" zobrazíte změny ve výsledku. Pokud si všimnete výrazné změny skóre, podívejte se do tabulky "zjištěné změny, které mohou ovlivnit vaši zabezpečený počet", a zjistěte možné změny, které by mohly způsobit změnu. Tato tabulka uvádí odstraněné prostředky, nově nasazené prostředky nebo prostředky, u kterých se změnil stav zabezpečení pro některá z doporučení.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Volitelné zabezpečené skóre v průběhu času Power BI řídicím panelu pro sledování zabezpečeného skóre v průběhu času a vyšetřování změn":::


## <a name="next-steps"></a>Další kroky

Tento článek popisuje, jak získat přístup k zabezpečenému skóre a jak ho sledovat. Související materiály najdete v následujících článcích:

- [Další informace o různých prvcích doporučení](security-center-recommendations.md)
- [Informace o tom, jak opravit doporučení](security-center-remediate-recommendations.md)
- [Zobrazit nástroje založené na GitHubu pro práci programově se zabezpečeným skóre](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)
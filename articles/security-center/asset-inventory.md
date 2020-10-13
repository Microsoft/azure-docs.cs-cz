---
title: Azure Security Center inventáře prostředků
description: Přečtěte si o zkušenostech se správou prostředků Azure Security Center, která poskytuje úplný přehled o všech vašich Security Center monitorované prostředky.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/22/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 98e4a5097f1ebd26c54d1e0de9bda7ca2055c320
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950733"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Prozkoumejte a spravujte svoje prostředky pomocí inventáře prostředků a nástrojů pro správu.

Stránka inventáře assetů Azure Security Center poskytuje jednu stránku pro zobrazení stav zabezpečení prostředků, ke kterým jste se připojili Security Center. 

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení.

Pokud některý z prostředků obsahuje nevyřízená doporučení, zobrazí se v inventáři.

Pomocí tohoto zobrazení a jeho filtrů můžete tyto otázky vyřešit:

- Které z mých předplatných využívajících Azure Defender mají neplacená doporučení?
- U kterých počítačů s označením "produkční" chybí agent Log Analytics?
- Kolik počítačů označených konkrétní značkou má nezpracované doporučení?
- Kolik prostředků v konkrétní skupině prostředků má zjištění zabezpečení ze služby posouzení ohrožení zabezpečení?

Možnosti správy prostředků pro tento nástroj jsou zásadní a stále se rozšiřují. 

> [!TIP]
> Doporučení zabezpečení na stránce inventáře assetů jsou stejná jako na stránce **doporučení** , ale tady se zobrazují v závislosti na ovlivněném prostředku. Informace o tom, jak vyřešit doporučení, najdete [v tématu Implementace doporučení zabezpečení v Azure Security Center](security-center-recommendations.md).


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Všeobecně dostupná (GA)|
|Stanov|Free|
|Požadované role a oprávnění:|Všichni uživatelé|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Jaké jsou klíčové funkce inventáře prostředků?

Na stránce inventarizace najdete následující nástroje:

- **Souhrny** – před definováním filtrů se v horní části zobrazení inventáře zobrazí výrazný pruh hodnot:

    - **Celkem prostředků**: celkový počet prostředků, které jsou připojené k Security Center.
    - **Chybné prostředky**: prostředky s aktivními bezpečnostními doporučeními. [Přečtěte si další informace o doporučeních zabezpečení](security-center-recommendations.md).
    - **Nemonitorované prostředky**: prostředky s problémy monitorování agenta – mají nasazeného agenta Log Analytics, ale agent neodesílá data nebo má jiné problémy se stavem.

- **Filtry** – více filtrů v horní části stránky nabízí způsob, jak rychle Upřesnit seznam prostředků podle otázky, na kterou se snažíte odpovědět. Pokud byste například chtěli odpovědět na dotaz, který má ve *svém počítači označení "produkční", chybí agent Log Analytics?* můžete zkombinovat filtr **monitorování agenta** s filtrem **značek** , jak je znázorněno v následujícím klipu:

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Filtrování do produkčních prostředků, které nejsou monitorovány":::

    Jakmile použijete filtry, souhrnné hodnoty se aktualizují tak, aby se vztahovaly k výsledkům dotazu. 

- **Možnosti exportu** – inventarizace nabízí možnost Exportovat výsledky vybraných možností filtrování do souboru CSV. Kromě toho můžete exportovat samotný dotaz do Průzkumníka Azure Resource graphu a dále upřesnit, Uložit nebo upravit dotaz KQL.

    ![Možnosti exportu inventáře](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > Dokumentace ke službě KQL poskytuje databázi s ukázkovými daty společně s některými jednoduchými dotazy, aby se pro daný jazyk nezískal "pocit". [Další informace najdete v tomto kurzu pro KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Možnosti správy prostředků** – inventarizace umožňuje provádět složité dotazy zjišťování. Po nalezení prostředků odpovídajících vašim dotazům poskytuje inventář klávesové zkratky pro operace, jako například:

    - Přiřadit značky k filtrovaným prostředkům – zaškrtněte políčka vedle prostředků, které chcete označit.
    - Připojte nové servery k Security Center – použijte tlačítko **Přidat jiné servery než Azure** na panelu nástrojů.
    - Automatizace úloh pomocí Azure Logic Apps – pomocí tlačítka Spustit **aplikaci logiky** spusťte aplikaci logiky na jednom nebo několika prostředcích. Vaše aplikace logiky musí být připravené předem a musí přijmout příslušný typ triggeru (požadavek HTTP). [Přečtěte si další informace o Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).


## <a name="how-does-asset-inventory-work"></a>Jak funguje soupis prostředků?

Inventář [prostředků využívá Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/), službu Azure, která poskytuje možnost Security Center dotazovat se na stav data zabezpečení v rámci několika předplatných.

ARG je navržený tak, aby poskytoval efektivní průzkum prostředků s možností škálování ve velkém měřítku.

Při použití [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)může inventář prostředků rychle získat podrobné přehledy pomocí křížového odkazu na data ASC pomocí dalších vlastností prostředku.


## <a name="how-to-use-asset-inventory"></a>Jak používat inventář assetů

1. Z bočního panelu Security Center vyberte **inventář**.

1. Použijte pole **filtrovat podle názvu** k zobrazení konkrétního prostředku nebo použijte filtry, jak je popsáno níže.

1. Ve filtrech vyberte příslušné možnosti pro vytvoření konkrétního dotazu, který chcete provést.

    :::image type="content" source="./media/asset-inventory/inventory-filters.png" alt-text="Filtrování do produkčních prostředků, které nejsou monitorovány" lightbox="./media/asset-inventory/inventory-filters.png":::

    Ve výchozím nastavení se prostředky seřadí podle počtu aktivních doporučení zabezpečení.

    > [!IMPORTANT]
    > Možnosti v jednotlivých filtrech jsou specifické pro prostředky v aktuálně vybraných předplatných **a** vaše výběry v ostatních filtrech.
    >
    > Pokud jste například vybrali jenom jedno předplatné a předplatné nemá žádné prostředky s nevyřízenými bezpečnostními doporučeními, které by bylo možné opravit (0 špatných prostředků), filtr **doporučení** nebude mít žádné možnosti. 

1. Chcete-li použít **výsledky zabezpečení obsahující** filtr, zadejte bezplatný text z ID, kontrolu zabezpečení nebo CVE jméno hledání ohrožení zabezpečení, které chcete filtrovat na ovlivněné prostředky:

    !["Výsledky zabezpečení obsahují filtr](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **Výsledky zabezpečení obsahují** a filtry **značek** přijímají pouze jednu hodnotu. Chcete-li filtrovat podle více než jednoho, použijte příkaz **Přidat filtry**.

1. Pokud chcete použít filtr **Azure Defenderu** , vyberte jednu nebo víc možností (vypnuto, Zapnuto nebo částečně):

    - **Off** – prostředky, které nejsou chráněné plánem Azure Defenderu. Můžete kliknout pravým tlačítkem na některý z těchto kroků a upgradovat je:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Filtrování do produkčních prostředků, které nejsou monitorovány" lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **On** Prostředky, které jsou chráněné plánem Azure Defenderu
    - **Částečný** – to platí pro **odběry** , které mají zakázané některé plány Azure Defenderu, ale ne všechny. Například následující předplatné má pět plánů programu Azure Defender zakázané. 

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Filtrování do produkčních prostředků, které nejsou monitorovány":::

1. Pokud chcete dále prozkoumávat výsledky dotazu, vyberte prostředky, které vás zajímají.

1. Chcete-li zobrazit aktuálně vybrané možnosti filtru jako dotaz v Průzkumníku grafu prostředků, vyberte možnost **Zobrazit v Průzkumníku grafu prostředků**.

    ![Dotaz na inventář v ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Chcete-li spustit dříve definovanou aplikaci logiky s 

1. Pokud jste definovali nějaké filtry a levou stránku otevřeli, Security Center se výsledky automaticky neaktualizují. Změny prostředků nebudou mít vliv na zobrazené výsledky, pokud ručně nenačtete stránku nebo vyberete **aktualizovat**.


## <a name="faq---inventory"></a>Nejčastější dotazy – inventář

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Proč se nezobrazují všechna předplatná, počítače, účty úložiště atd.?

Zobrazení inventáře obsahuje seznam Security Center propojených prostředků z perspektivy CSPM (Cloud Security stav Management). Filtry nevrátí všechny prostředky ve vašem prostředí; jenom ty s doporučeními pro zbývající (nebo aktivní). 

Například následující snímek obrazovky ukazuje uživatele s přístupem k předplatným 38, ale pouze 10 aktuálně obsahuje doporučení. Takže když se filtrují podle **typu prostředku = odběry**, zobrazí se v inventáři jenom tyto 10 předplatných s aktivními doporučeními:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Filtrování do produkčních prostředků, které nejsou monitorovány":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-azure-defender-or-agent-monitoring-columns"></a>Proč některé z mých zdrojů ve sloupcích Azure Defenderu nebo monitorování agentů zobrazují prázdné hodnoty?

Ne všechny Security Center monitorované prostředky mají agenty. Například Azure Storage účty nebo PaaS prostředky, jako jsou disky, Logic Apps, Data Lake analýzy a centrum událostí.

Když ceny nebo monitorování agentů nejsou pro určitý prostředek relevantní, nezobrazí se v těchto sloupcích inventáře.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Filtrování do produkčních prostředků, které nejsou monitorovány":::

## <a name="next-steps"></a>Další kroky

Tento článek popisuje stránku inventáře prostředků Azure Security Center.

Další informace o souvisejících nástrojích najdete na následujících stránkách:

- [Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)
- [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)
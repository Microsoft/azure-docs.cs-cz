---
title: Azure Security Center inventáře prostředků
description: Přečtěte si o zkušenostech se správou prostředků Azure Security Center, která poskytuje úplný přehled o všech vašich Security Center monitorované prostředky.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: f3a542cd62c3d593dbc0cce7982d47222e9a7c88
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181099"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>Prozkoumejte a spravujte svoje prostředky pomocí inventáře prostředků a nástrojů pro správu.

Stránka inventáře assetů Azure Security Center poskytuje jednu stránku pro zobrazení stav zabezpečení prostředků, ke kterým jste se připojili Security Center. 

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení.

Pokud některý z prostředků obsahuje nevyřízená doporučení, zobrazí se v inventáři.

Pomocí tohoto zobrazení a jeho filtrů můžete tyto otázky vyřešit:

- Které z předplatných na úrovni Standard mají nezpracované doporučení?
- U kterých počítačů s označením "produkční" chybí agent Log Analytics?
- Kolik z mých počítačů označených konkrétní značkou má nezpracované doporučení?
- Kolik prostředků v konkrétní skupině prostředků má zjištění zabezpečení ze služby posouzení ohrožení zabezpečení?

Možnosti správy prostředků pro tento nástroj jsou zásadní a stále se rozšiřují. 


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Preview|
|Stanov|Free|
|Požadované role a oprávnění:|Všichni uživatelé|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>Jaké jsou klíčové funkce inventáře prostředků?

Na stránce inventarizace najdete následující nástroje:

- **Souhrny** – před definováním filtrů se v horní části zobrazení inventáře zobrazí výrazný pruh hodnot:

    - **Celkem prostředků**: celkový počet prostředků, které jsou připojené k Security Center.
    - **Chybné prostředky**: prostředky s aktivními bezpečnostními doporučeními. [Přečtěte si další informace o doporučeních zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-recommendations).
    - **Nemonitorované prostředky**: prostředky s problémy monitorování agenta – mají nasazeného agenta Log Analytics, ale agent neodesílá data nebo má jiné problémy se stavem.

- **Filtry** – více filtrů v horní části stránky nabízí způsob, jak rychle Upřesnit seznam prostředků podle otázky, na kterou se snažíte odpovědět. Pokud byste například chtěli odpovědět na dotaz, který má ve *svém počítači označení "produkční", chybí agent Log Analytics?* můžete zkombinovat filtr **monitorování agenta** s filtrem **značek** , jak je znázorněno v následujícím klipu:

    ![Filtrování do produkčních prostředků, které nejsou monitorovány](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    Jakmile použijete filtry, souhrnné hodnoty se aktualizují tak, aby se vztahovaly k výsledkům dotazu. 

- **Možnosti exportu** – inventarizace nabízí možnost Exportovat výsledky vybraných možností filtrování do souboru CSV. Kromě toho můžete exportovat samotný dotaz do Průzkumníka Azure Resource graphu a dále upřesnit, Uložit nebo upravit dotaz KQL.

    ![Možnosti exportu inventáře](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > Dokumentace ke službě KQL poskytuje databázi s ukázkovými daty společně s některými jednoduchými dotazy, aby se pro daný jazyk nezískal "pocit". [Další informace najdete v tomto kurzu pro KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

- **Možnosti správy prostředků** – inventarizace umožňuje provádět složité dotazy zjišťování. Po nalezení prostředků odpovídajících vašim dotazům poskytuje inventář klávesové zkratky pro operace, jako například:

    - Přiřadit značky k filtrovaným prostředkům – zaškrtněte políčka vedle prostředků, které chcete označit.
    - Připojit nové servery k Security Center – použijte tlačítko **Přidat jiné servery než Azure** na panelu nástrojů


## <a name="how-does-asset-inventory-work"></a>Jak funguje soupis prostředků?

Inventář [prostředků využívá Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/), službu Azure, která poskytuje možnost Security Center dotazovat se na stav data zabezpečení v rámci několika předplatných.

ARG je navržený tak, aby poskytoval efektivní průzkum prostředků s možností škálování ve velkém měřítku.

Při použití [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)může inventář prostředků rychle získat podrobné přehledy pomocí křížového odkazu na data ASC pomocí dalších vlastností prostředku.


## <a name="how-to-use-asset-inventory"></a>Jak používat inventář assetů

1. Z bočního panelu Security Center vyberte **inventář**.

1. Pokud chcete zobrazit konkrétní prostředek, můžete v poli **filtrovat podle názvu** zadat název.

1. Ve filtrech vyberte příslušné možnosti pro vytvoření konkrétního dotazu, který chcete provést.

    ![Filtry inventáře](./media/asset-inventory/inventory-filters.png)

    Ve výchozím nastavení se prostředky seřadí podle počtu aktivních doporučení zabezpečení.

    > [!IMPORTANT]
    > Možnosti v jednotlivých filtrech jsou specifické pro prostředky v aktuálně vybraných předplatných **a** vaše výběry v ostatních filtrech.
    >
    > Pokud jste například vybrali jenom jedno předplatné a předplatné nemá žádné prostředky s nevyřízenými bezpečnostními doporučeními, které by bylo možné opravit (0 špatných prostředků), filtr **doporučení** nebude mít žádné možnosti. 

1. Chcete-li použít **výsledky zabezpečení obsahující** filtr, zadejte bezplatný text z ID, kontrolu zabezpečení nebo CVE jméno hledání ohrožení zabezpečení, které chcete filtrovat na ovlivněné prostředky:

    !["Výsledky zabezpečení obsahují filtr](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **Výsledky zabezpečení obsahují** a filtry **značek** přijímají pouze jednu hodnotu. Chcete-li filtrovat podle více než jednoho, použijte příkaz **Přidat filtry**.

1. Chcete-li použít filtr **cenové úrovně** , vyberte jednu nebo více možností (bezplatné, částečné nebo standardní):

    - **Bezplatné** prostředky, které jsou na cenové úrovni zdarma
    - **Standardní** – prostředky, které jsou na cenové úrovni Standard
    - **Částečně** – to platí pro předplatná, která jsou na cenové úrovni Standard, ale některé z volitelných plánů zabezpečení jsou zakázané. Například následující předplatné je na úrovni Standard, ale má pět prvků úrovně Standard zakázané. 

        ![Předplatné na úrovni Standard (částečný) – cenová úroveň](./media/asset-inventory/pricing-tier-partial.png)

1. Pokud chcete dále prozkoumávat výsledky dotazu, vyberte prostředky, které vás zajímají.

1. V případě potřeby vyberte **Zobrazit v Průzkumníku grafu prostředků** a otevřete dotaz v Průzkumníku grafu prostředků.

    ![Dotaz na inventář v ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Pokud jste definovali nějaké filtry a levou stránku otevřeli, Security Center se výsledky automaticky neaktualizují. Změny prostředků nebudou mít vliv na zobrazené výsledky, pokud ručně nenačtete stránku nebo vyberete **aktualizovat**.


## <a name="faq---inventory"></a>Nejčastější dotazy – inventář

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Proč se nezobrazují všechna předplatná, počítače, účty úložiště atd.?

Zobrazení inventáře obsahuje seznam vašich prostředků z perspektivy Cloud Security stav Management (CSPM). Filtry nevrátí všechny prostředky ve vašem prostředí; jenom ty s doporučeními pro zbývající (nebo aktivní). 

Pokud máte například devět předplatných, ale pouze osm v současnosti mají doporučení, při filtrování podle **typu prostředku = odběry** se zobrazí pouze osm předplatných s aktivními doporučeními:

![Pokud neexistují žádná aktivní doporučení, nevrátí se všechny.](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>Proč některé z mých zdrojů ve sloupcích s cenami nebo monitorováním agentů zobrazují prázdné hodnoty?

Ne všechny Security Center monitorované prostředky mají agenty. Například Azure Storage účty nebo PaaS prostředky, jako jsou disky, Logic Apps, Data Lake analýzy a centrum událostí.

Když ceny nebo monitorování agentů nejsou pro určitý prostředek relevantní, nezobrazí se v těchto sloupcích inventáře.

![Některé zdroje zobrazují prázdné informace v části monitorování agenta nebo sloupce s cenami.](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>Další kroky

Tento článek popisuje stránku inventáře prostředků Azure Security Center.

Další informace o souvisejících nástrojích najdete na následujících stránkách:

- [Azure Resource Graph (ARG)](https://docs.microsoft.com/azure/governance/resource-graph/)

- [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)
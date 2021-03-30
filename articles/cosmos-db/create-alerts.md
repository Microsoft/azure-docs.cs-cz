---
title: Vytváření upozornění pro Azure Cosmos DB pomocí Azure Monitor
description: Přečtěte si, jak nastavit výstrahy pro Azure Cosmos DB pomocí Azure Monitor.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: how-to
ms.date: 07/16/2020
ms.openlocfilehash: e29db7e31438bc7f6ac609384d0d9b92c275e813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339525"
---
# <a name="create-alerts-for-azure-cosmos-db-using-azure-monitor"></a>Vytváření upozornění pro Azure Cosmos DB pomocí Azure Monitor
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Výstrahy slouží k nastavení opakovaných testů pro monitorování dostupnosti a odezvy vašich Azure Cosmos DBch prostředků. Výstrahy vám můžou poslat oznámení ve formě e-mailu nebo spustit funkci Azure Function, když jedna z vašich metrik dosáhne prahové hodnoty nebo pokud se konkrétní událost zaznamená do protokolu aktivit.

Můžete obdržet upozornění na základě metrik nebo událostí protokolu aktivit v účtu Azure Cosmos:

* **Metriky** – výstraha se aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Například když celkový počet spotřebovaných jednotek žádosti překročí 1000 RU/s. Tato výstraha se aktivuje při prvním splnění podmínky a následně v případě, že se už podmínka nesplní. V článku [referenční informace o monitorování dat](monitor-cosmos-db-reference.md#metrics) najdete různé metriky dostupné v Azure Cosmos DB.

* **Události protokolu aktivit** – Tato výstraha se aktivuje, když dojde k určité události. Například při otevření nebo aktualizaci klíčů účtu Azure Cosmos.

Výstrahy můžete nastavit v podokně Azure Cosmos DB nebo ve službě Azure Monitor v Azure Portal. Obě rozhraní nabízejí stejné možnosti. V tomto článku se dozvíte, jak nastavit výstrahy pro Azure Cosmos DB pomocí Azure Monitor.

## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění

V této části se dozvíte, jak vytvořit výstrahu, když obdržíte stavový kód HTTP 429, který se obdrží, když jsou požadavky omezené na rychlost. Například můžete chtít, aby se zobrazila výstraha v případě, že jsou v 100 nebo vyšší míře omezeny požadavky. V tomto článku se dozvíte, jak nakonfigurovat výstrahu pro tento scénář pomocí kódu stavu HTTP. Pomocí podobných kroků můžete nakonfigurovat i jiné typy výstrah, ale stačí zvolit jinou podmínku na základě vašeho požadavku.

1. Přihlaste se k [Azure Portal.](https://portal.azure.com/)

1. V levém navigačním panelu vyberte **monitor** a vyberte **výstrahy**.

1. Kliknutím na tlačítko nové pravidlo výstrahy otevřete podokno vytvořit pravidlo výstrahy.  

1. Vyplňte část **Scope (obor** ):

   * Otevřete podokno **Vybrat prostředek** a proveďte následující konfiguraci:

   * Vyberte název vašeho **předplatného** .

   * Jako **typ prostředku** vyberte **účty Azure Cosmos DB** .

   * **Umístění** vašeho účtu Azure Cosmos.

   * Po vyplnění podrobností se zobrazí seznam účtů Azure Cosmos ve vybraném oboru. Zvolte jednu z nich, pro kterou chcete nakonfigurovat výstrahy, a vyberte **Hotovo**.

1. Vyplňte část **Podmínka** :

   * Otevřete podokno **vybrat podmínku** a otevřete stránku **Konfigurovat logiku signálu** a proveďte následující konfiguraci:

   * Vyberte signál. **Typ signálu** může být **metrika** nebo **Protokol aktivit**. Pro tento scénář vyberte **metriky** . Vzhledem k tomu, že chcete zobrazit výstrahu v případě, že jsou v celkovém počtu jednotek požadavků problémy s omezením četnosti.

   * Vybrat **vše** pro **monitorovací službu**

   * Vyberte **Název signálu**. Pokud chcete zobrazit výstrahu pro stavové kódy HTTP, vyberte signál **Celkový počet jednotek žádostí** .

   * Na další kartě můžete definovat logiku pro aktivaci výstrahy a pomocí grafu zobrazit trendy svého účtu Azure Cosmos. Metrika **celkových jednotek požadavků** podporuje dimenze. Tyto dimenze umožňují filtrovat metriky. Pokud nevyberete žádnou dimenzi, tato hodnota se ignoruje.

   * Jako **název dimenze** vyberte **StatusCode** . Vyberte **Přidat vlastní hodnotu** a nastavte stavový kód na 429.

   * V **logice výstrahy** nastavte **prahovou hodnotu** na **static**. Statická prahová hodnota používá pro vyhodnocení pravidla uživatelsky definovanou prahovou hodnotu, zatímco dynamické prahové hodnoty používají předdefinované algoritmy strojového učení, které se průběžně učí vzor chování metriky a automaticky vypočítávají prahové hodnoty.

   * Nastavte **operátor** na hodnotu **větší než**, **typ agregace** na hodnotu **Total** a **prahovou hodnotu** **100**. Pokud se v této logice klient zobrazuje více než 100 požadavků, které mají kód stavu 429, výstraha se aktivuje. Můžete také nakonfigurovat typ agregace, členitost agregace a frekvenci vyhodnocení podle vašeho požadavku.

   * Po vyplnění formuláře vyberte **Hotovo**. Následující snímek obrazovky ukazuje podrobnosti logiky výstrah:

     :::image type="content" source="./media/create-alerts/configure-alert-logic.png" alt-text="Konfigurace logiky pro příjem výstrah v případě požadavků s omezeným/429":::

1. Vyplňte část **Skupina akcí** :

   * V podokně **vytvořit pravidlo** vyberte existující skupinu akcí nebo vytvořte novou skupinu akcí. Skupina akcí umožňuje definovat akci, která má být provedena při výskytu výstrahy. V tomto příkladu vytvořte novou skupinu akcí pro příjem e-mailových oznámení při aktivaci výstrahy. Otevřete podokno **Přidat skupinu akcí** a vyplňte následující podrobnosti:

   * **Název skupiny akcí** – název skupiny akcí musí být v rámci skupiny prostředků jedinečný.

   * **Krátký název** – krátký název skupiny akcí, tato hodnota je obsažena v oznámeních e-mailu a SMS a určuje, která skupina akcí byla zdrojem oznámení.

   * Vyberte předplatné a skupinu prostředků, ve které bude tato skupina akcí vytvořená.  

   * Zadejte název akce a jako **typ akce** vyberte **zpráva e-mail/SMS/odeslat/hlas** . Následující snímek obrazovky ukazuje podrobnosti typu akce:

     :::image type="content" source="./media/create-alerts/configure-alert-action-type.png" alt-text="Nakonfigurujte typ akce, například e-mailové oznámení pro příjem výstrahy.":::

1. Vyplňte část **Podrobnosti pravidla výstrahy** :

   * Zadejte název pravidla, zadejte nepovinný popis, úroveň závažnosti výstrahy, zvolte, zda chcete pravidlo Povolit při vytváření pravidla, a pak výběrem možnosti **vytvořit pravidlo upozornění** vytvořte výstrahu pravidla metriky.

Po vytvoření bude výstraha aktivní během 10 minut.

## <a name="common-alerting-scenarios"></a>Běžné scénáře upozorňování

Tady je několik scénářů, kdy můžete použít výstrahy:

* Když se aktualizují klíče účtu Azure Cosmos.
* Když data nebo index využití kontejneru, databáze nebo oblasti překročí určitý počet bajtů.
* Když je normalizovaná spotřeba RU/s větší než určité procento. Normalizovaná metrika spotřeby RU poskytuje maximální využití propustnosti v sadě replik. Další informace najdete v článku [Postup monitorování normalizovaného ru/s](monitor-normalized-request-units.md) .  
* Když přidáte, odeberete nebo přejdete do režimu offline.
* Když se vytvoří, odstraní nebo aktualizuje databáze nebo kontejner.
* Při změně propustnosti databáze nebo kontejneru.

## <a name="next-steps"></a>Další kroky

* Jak [monitorovat normalizovanou metriku ru/s](monitor-normalized-request-units.md) v kontejneru Azure Cosmos.
* Jak [monitorovat propustnost nebo využití jednotek žádostí](monitor-request-unit-usage.md) operace v Azure Cosmos DB.
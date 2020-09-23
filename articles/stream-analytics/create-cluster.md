---
title: Rychlý Start k vytvoření clusteru Azure Stream Analytics
description: Naučte se vytvořit cluster Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: a7be204c30d242be991fb9a57d239b69342ace97
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946886"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Rychlý Start: vytvoření vyhrazeného clusteru Azure Stream Analytics pomocí Azure Portal

Pomocí Azure Portal vytvořte cluster Azure Stream Analytics. [Cluster Stream Analytics](cluster-overview.md) je nasazení v jednom tenantovi, které se dá použít pro komplexní a náročné případy použití streamování. V clusteru Stream Analytics můžete spustit více Stream Analytics úloh.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dokončení [rychlého startu: vytvoření Stream Analytics úlohy pomocí Azure Portal](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Vytvoření clusteru Stream Analytics

V této části vytvoříte prostředek clusteru Stream Analytics.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **Vytvořit prostředek**. Do *vyhledávacího pole Hledat na Marketplace* zadejte a vyberte **Stream Analytics cluster**. Pak vyberte **Přidat**.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Stream Analytics výsledek hledání clusteru.":::

1. Na stránce **vytvořit cluster Stream Analytics** zadejte základní nastavení pro nový cluster.

   |Nastavení|Hodnota|Popis |
   |---|---|---|
   |Předplatné|Název předplatného|Vyberte předplatné Azure, které chcete použít pro tento cluster Stream Analytics. |
   |Skupina prostředků|Název skupiny prostředků|Vyberte skupinu prostředků nebo vyberte **vytvořit nové**a zadejte jedinečný název nové skupiny prostředků. |
   |Název clusteru|Jedinečný název|Zadejte název, který identifikuje cluster Stream Analytics.|
   |Umístění|Oblast nejbližší vašim zdrojům dat a jímka|Vyberte geografické umístění pro hostování Stream Analyticsho clusteru. Použijte umístění, které je nejblíže vašim zdrojům dat a jímka pro analýzu s nízkou latencí.|
   |Kapacita jednotky streamování|36 až 216 |Určete velikost clusteru tím, že vydáte odhad počtu Stream Analytics úlohy, kterou plánujete spustit, a celkovou službu SUs, kterou bude úloha vyžadovat. Můžete začít s 36 SUs a později škálovat nebo snížit velikost podle potřeby.|

   ![Vytvoření clusteru](./media/create-cluster/create-cluster.png)

1. Vyberte **Zkontrolovat a vytvořit**. Oddíly **značek** můžete přeskočit.

1. Zkontrolujte nastavení clusteru a pak vyberte **vytvořit**. Vytvoření clusteru je dlouhodobá operace, která může trvat přibližně 60 minut. Počkejte, než se na stránce portálu zobrazí **dokončené vaše nasazení**. Mezitím můžete vytvářet a vyvíjet [Stream Analytics úlohy](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) , které chcete v tomto clusteru spustit, pokud jste to ještě neudělali.

1. Vyberte **Přejít k prostředku** a přejdete na stránku Stream Analytics clusteru.

## <a name="delete-your-cluster"></a>Odstranění clusteru

Pokud neplánujete spouštět žádné úlohy Stream Analytics, můžete cluster Stream Analytics odstranit. Odstraňte cluster pomocí následujících kroků na Azure Portal:

1. V části **Nastavení** vyberte **úlohy Stream Analytics** a zastavte všechny spuštěné úlohy.

1. Přejít na **Přehled** clusteru. Vyberte **Odstranit** a podle pokynů odstraňte cluster.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Azure Stream Analytics. V dalším článku se dozvíte, jak spustit úlohu Stream Analytics ve vašem clusteru:

> [!div class="nextstepaction"]
> [Správa úloh Stream Analytics v clusteru Stream Analytics](manage-jobs-cluster.md)

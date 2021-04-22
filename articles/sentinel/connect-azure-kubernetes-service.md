---
title: Připojení diagnostických protokolů služby Azure Kubernetes (AKS) do Azure Sentinel
description: Naučte se, jak pomocí Azure Policy připojit protokoly diagnostiky služby Azure Kubernetes do Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/22/2021
ms.author: yelevin
ms.openlocfilehash: f1ef860f1b84de84c42996a7523af8ce174d5981
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890793"
---
# <a name="connect-azure-kubernetes-service-diagnostics-logs"></a>Připojit protokoly diagnostiky služby Azure Kubernetes

Služba Azure Kubernetes Service (AKS) je open source služba pro orchestraci kontejnerů, která umožňuje nasazovat, škálovat a spravovat kontejnery Docker a aplikace založené na kontejnerech v prostředí clusteru.

Tento konektor vám umožní streamovat protokoly diagnostiky Azure Kubernetes Service (AKS) do Azure Sentinel, což vám umožní průběžně monitorovat aktivity ve všech vašich instancích. 

Přečtěte si další informace o [monitorování služby Azure Kubernetes](../azure-monitor/containers/container-insights-overview.md) a o [telemetrie diagnostiky AKS](../aks/view-control-plane-logs.md).

## <a name="prerequisites"></a>Požadavky

Postup ingestování protokolů AKS do Azure Sentinel:

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pokud chcete použít Azure Policy k aplikování zásad streamování protokolů na prostředky AKS, musíte mít roli vlastníka pro obor přiřazení zásady.

## <a name="connect-to-azure-kubernetes-service"></a>Připojení ke službě Azure Kubernetes

Tento konektor používá Azure Policy k aplikování jedné konfigurace streamování protokolů služby Azure Kubernetes na kolekci prostředků, která je definovaná jako obor. Typy protokolů ingestované ze služby Azure Kubernetes můžete zobrazit na levé straně stránky konektoru v části **datové typy**.

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. Z Galerie datových konektorů vyberte **Azure Kubernetes Service (AKS)** a pak v podokně náhledu vyberte **Stránka otevřít konektor** .

1. V části **Konfigurace** stránky konektoru rozbalte **protokoly diagnostiky proudu ze služby Azure Kubernetes (AKS) ve velkém měřítku**.

1. Vyberte tlačítko **Průvodce spustit Azure Policy přiřazení** .

    Otevře se Průvodce přiřazením zásad, který jste připraveni vytvořit novou zásadu nazvanou **nasadit – konfigurace nastavení diagnostiky pro službu Azure Kubernetes pro Log Analytics pracovní prostor**.

    1. Na kartě **základy** kliknutím na tlačítko se třemi tečkami v části **obor** vyberte vaše předplatné (případně skupinu prostředků). Můžete také přidat popis.

    1. Na kartě **parametry** ponechejte pole **efekt** a **název nastavení** tak, jak je. V rozevíracím seznamu **Log Analytics pracovního prostoru** vyberte svůj pracovní prostor Azure Sentinel. Zbývající rozevírací pole reprezentují dostupné typy diagnostických protokolů. Ponechte označené jako "true" všechny typy protokolů, které chcete ingestovat.

    1. Zásady se použijí na prostředky přidané v budoucnu. Pokud chcete zásady použít i u stávajících prostředků, vyberte kartu **náprava** a označte zaškrtávací políčko **vytvořit úlohu nápravy** .

    1. Na kartě **Revize + vytvořit** klikněte na **vytvořit**. Vaše zásada je teď přiřazená k oboru, který jste zvolili.

> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) jenom v případě, že se data v posledních 14 dnech pozastavila. Po 14 dnech uplynulo bez příjmu dat se konektor bude zobrazovat jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak pomocí Azure Policy připojit službu Azure Kubernetes ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

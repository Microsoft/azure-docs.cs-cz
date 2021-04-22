---
title: Připojit protokoly diagnostiky Azure Storage účtu do Azure Sentinel
description: Naučte se, jak pomocí Azure Policy připojit protokoly diagnostiky Azure Storage účtu do Azure Sentinel.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 04/21/2021
ms.author: yelevin
ms.openlocfilehash: b4260d31b587f2a20d7bc9d4c4e3e6a0d225a416
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879125"
---
# <a name="connect-azure-storage-account-diagnostics-logs"></a>Připojit protokoly diagnostiky Azure Storage účtu

Azure Storage účet je cloudové řešení pro scénáře moderních úložišť dat. Obsahuje všechny vaše datové objekty: objekty blob, soubory, fronty, tabulky a disky.

Tento konektor vám umožní streamovat protokoly diagnostiky Azure Storage účtů do Azure Sentinel, což vám umožní průběžně monitorovat aktivity a zjišťovat bezpečnostní hrozby ve všech prostředcích úložiště Azure v celé organizaci.

Přečtěte si další informace o [monitorování účtu Azure Storage](../storage/common/storage-analytics-logging.md).

## <a name="prerequisites"></a>Požadavky

Ingestování protokolů diagnostiky Azure Storage účtu do Azure Sentinel:

- V pracovním prostoru Azure Sentinel musíte mít oprávnění ke čtení a zápisu.

- Pokud chcete použít Azure Policy k použití zásad streamování protokolů pro Azure Storage prostředků, musíte mít roli vlastníka pro obor přiřazení zásady.

## <a name="connect-to-azure-storage-account"></a>Připojit k Azure Storagemu účtu

Tento konektor používá Azure Policy k aplikování konfigurace jediného streamování protokolů na kolekci prostředků Azure Storage, která je definovaná jako obor. Dostupné typy protokolů Azure Storage a metriky můžete zobrazit na levé straně stránky konektoru v části **datové typy**.

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**.

1. Z Galerie datových konektorů vyberte **Azure Storage účet** a potom v podokně náhledu vyberte možnost **otevřít stránku konektoru** .

1. V části **Konfigurace** stránky konektoru rozbalte **protokoly diagnostiky proudu z účtu Azure Storage ve velkém měřítku**.

1. Vyberte tlačítko **Průvodce spustit Azure Policy přiřazení** .

    Otevře se Průvodce přiřazením zásad, který je připravený k vytvoření nové zásady nazvané **Konfigurace nastavení diagnostiky pro účty úložiště do Log Analytics pracovního prostoru**.

    1. Na kartě **základy** klikněte na tlačítko se třemi tečkami vedle **oboru** a vyberte své předplatné (případně skupinu prostředků). Můžete také přidat popis.

    1. Na kartě **parametry** :
        - V rozevíracím seznamu **Log Analytics pracovního prostoru** vyberte svůj pracovní prostor Azure Sentinel.
        - V rozevíracím seznamu **služby úložiště pro nasazení** vyberte, typy prostředků úložiště (soubor, tabulka, fronta atd.), do kterých chcete nasadit nastavení diagnostiky.
        - Ponechte pole **název nastavení** a **účinek** na hodnotu.
        - Zbývající rozevírací pole reprezentují dostupný diagnostický protokol a typy metrik. Nechejte označené jako "true" všechny typy, které chcete ingestovat.

    1. Výše uvedené kroky uplatní zásadu na všechny budoucí prostředky úložiště. Pokud chcete zásady použít u stávajících prostředků, vyberte kartu **náprava** a označte zaškrtávací políčko **vytvořit úlohu nápravy** .

    1. Na kartě **Revize + vytvořit** klikněte na **vytvořit**. Vaše zásada je teď přiřazená k oboru, který jste zvolili.

> [!NOTE]
>
> U tohoto konkrétního datového konektoru se indikátory stavu připojení (barevný pruh v galerii datových konektorů a ikony připojení vedle názvů datových typů) budou zobrazovat jako *připojené* (zeleně) jenom v případě, že se data v posledních 14 dnech pozastavila. Po 14 dnech uplynulo bez příjmu dat se konektor bude zobrazovat jako odpojený. Okamžik, kdy se data dostanou, se vrátí do stavu *připojeno* .

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Azure Storage účet ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).

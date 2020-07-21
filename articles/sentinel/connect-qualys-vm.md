---
title: Připojit data správy ohrožení zabezpečení Qualys do Azure Sentinel | Microsoft Docs
description: Naučte se připojit data správy ohrožení zabezpečení Qualys do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 44002a8f4ab3b644e3530ee2d2fc06a7af271fbe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531178"
---
# <a name="connect-your-qualys-vm-to-azure-sentinel-with-azure-function"></a>Připojení virtuálního počítače s Qualys ke službě Azure Sentinel pomocí funkce Azure Functions

Konektor pro správu ohrožení zabezpečení Qualys (VM) umožňuje snadno připojit všechny protokoly řešení zabezpečení [virtuálních počítačů Qualys](https://www.qualys.com/apps/vulnerability-management/) s Azure Sentinel, zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Integrace mezi virtuálními počítači Qualys a službou Azure Sentinel používá Azure Functions k vyžádání dat protokolu pomocí REST API.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="configure-and-connect-qualys-vm"></a>Konfigurace a připojení virtuálního počítače s Qualys

Azure Functions mohou integrovat a vyžádat události a protokoly přímo z virtuálního počítače Qualys a předají je do Azure Sentinel.

1. Na portálu Sentinel Azure klikněte na **datové konektory** a vyberte konektor **pro správu ohrožení zabezpečení Qualys** .

1. Vyberte **stránku otevřít konektor**.

1. Postupujte podle pokynů na stránce **správy ohrožení zabezpečení Qualys** .

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data zobrazí v Log Analytics pod tabulkou **QualysHostDetection_CL** .

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit virtuální počítač s Qualys ke službě Azure Sentinel pomocí aplikací Azure Function App. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

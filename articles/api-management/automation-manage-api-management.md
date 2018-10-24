---
title: Spravovat službu Azure API Management pomocí Azure Automation
description: Další informace o používání služby Azure Automation pro správu Azure API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: fee93d01f0462cbee1a3e1110c56fb57220f8004
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956848"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Správa Azure API Management pomocí Azure Automation
Tato příručka vás seznámí s služby Azure Automation a jak ji můžete použít k zjednodušení správy Azure API Management.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure pro zjednodušení správu cloudu díky automatizaci procesu. Pomocí Azure Automation, ruční, opakované, časově náročné a náchylné úlohy je možné automatizovat zvýšit spolehlivost, efektivitu a času na hodnotu pro vaši organizaci.

Azure Automation poskytuje s vysoce spolehlivé a vysoce dostupné prováděcího modulu, která se škáluje podle vašich potřeb. Ve službě Azure Automation procesy můžete má zahájit ručně, 3. stran systémy, nebo v naplánovaných intervalech, aby úlohy stát přesně v případě potřeby.

Snižte operační režii a uvolnit tak IT a týmu DevOps umožňovaly práci, která vytváří přidanou hodnotu díky přesunu úkoly správy cloudu ke spuštění automaticky službou Azure Automation.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Jak Azure Automation pomáhá spravovat službu Azure API Management?
API Management můžete spravovat ve službě Azure Automation s použitím [rutiny prostředí Windows PowerShell pro rozhraní API služby Azure API Management](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/?view=azurermps-5.5.0#api_management/). V rámci Azure Automation můžete psát skripty pracovního postupu Powershellu provádět mnoho úkolů správy rozhraní API pomocí rutin. Můžete také spárovat tyto rutiny ve službě Azure Automation s rutinami pro dalšími službami Azure, automatizují komplexní úlohy napříč službami Azure a systémech 3. stran.

Tady je několik příkladů použití služby API Management pomocí prostředí Powershell:

* [Ukázky Azure Powershellu pro službu API Management](https://docs.microsoft.com/azure/api-management/powershell-samples)

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy služby Azure Automation a jak ho lze použít ke správě Azure API Management, použijte tyto odkazy na další informace.

* Azure Automation najdete v článku [úvodní kurz](../automation/automation-first-runbook-graphical.md).


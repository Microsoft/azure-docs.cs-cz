---
title: Správa Azure API Management pomocí Azure Automation
description: Přečtěte si, jak se služba Azure Automation dá použít ke správě Azure API Management.
services: api-management, automation
documentationcenter: ''
author: vladvino
manager: eamono
editor: ''
ms.assetid: 2e53c9af-f738-47f8-b1b6-593050a7c51b
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: c808d4659b5987b099dd96d73bb8c18c08fe3c99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86249391"
---
# <a name="managing-azure-api-management-using-azure-automation"></a>Správa služby Azure API Management pomocí služby Azure Automation
Tato příručka vás seznámí s Azure Automationovou službou a jejím použitím ke zjednodušení správy Azure API Management.

## <a name="what-is-azure-automation"></a>Co je Azure Automation?
[Azure Automation](https://azure.microsoft.com/services/automation/) je služba Azure, která zjednodušuje správu cloudu prostřednictvím automatizace procesů. Použití Azure Automation, ručních, opakovaných, dlouhotrvajících a chybově náchylných úloh může být automatizované pro zvýšení spolehlivosti, efektivity a času na hodnotu pro vaši organizaci.

Azure Automation poskytuje vysoce spolehlivý a vysoce dostupný modul pro spouštění pracovních postupů, který se škáluje podle vašich potřeb. V Azure Automation můžou procesy aktivovat ručně, systémy třetích stran nebo v plánovaných intervalech tak, aby úkoly v případě potřeby byly přesně provedeny.

Pomocí Azure Automation můžete snížit provozní režii a uvolnit si pracovníky IT a DevOps, abyste se mohli soustředit na práci, která zvyšuje hodnotu Business.

## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Jak může Azure Automation pomáhat se správou Azure API Management?
API Management je možné spravovat v Azure Automation pomocí [rutin prostředí Windows PowerShell pro rozhraní API Azure API Management](/powershell/module/az.apimanagement). V rámci Azure Automation můžete psát skripty PowerShellového pracovního postupu a provádět spoustu úloh API Management pomocí rutin. Tyto rutiny můžete také spárovat v Azure Automation s rutinami pro další služby Azure, a automatizovat tak složité úlohy napříč službami Azure a systémy třetích stran.

Tady je několik příkladů použití API Management s prostředím PowerShell:

* [Ukázky Azure PowerShellu pro službu API Management](./powershell-samples.md)

## <a name="next-steps"></a>Další kroky
Teď, když jste se seznámili se základy Azure Automation a jak se dají použít ke správě Azure API Management, přečtěte si následující odkazy, kde najdete další informace.

* Podívejte se na úvodní [kurz](../automation/learn/automation-tutorial-runbook-graphical.md)Azure Automation.

---
title: Připojit data pracovního prostoru Google (G Suite) do Azure Sentinel | Microsoft Docs
description: Naučte se používat datový konektor Google Workspace (G Suite) k ingestování událostí aktivity pracovního prostoru Google do Azure Sentinel. Zobrazení dat pracovních prostorů Google v sešitech, vytváření výstrah a vylepšení šetření
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101744686"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Připojit pracovní prostor Google k Azure Sentinel

> [!IMPORTANT]
> Konektor pro pracovní prostor Google je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Datový konektor pro [pracovní prostor Google (dříve G Suite)](https://workspace.google.com/) poskytuje možnost ingestovat události aktivity pracovního prostoru Google do služby Azure Sentinel prostřednictvím REST API. Konektor poskytuje tyto [události](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) v SOC, což vám pomůže prozkoumat potenciální bezpečnostní rizika, analyzovat spolupráci týmu, diagnostikovat problémy s konfigurací, sledovat, kdo se přihlašuje a kdy, analyzuje činnost správce, porozumět tomu, jak uživatelé vytvářejí a sdílí obsah a prohlédli další události ve vaší organizaci.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Požadavky

Pokud chcete shromažďovat data pracovního prostoru Google, musíte mít následující oprávnění a přihlašovací údaje:

- Oprávnění ke čtení a zápisu v pracovním prostoru Sentinel Azure.

- Oprávnění ke čtení sdílených klíčů pro pracovní prostor. [Přečtěte si další informace o klíčích pracovních prostorů](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Oprávnění ke čtení a zápisu Azure Functions k vytvoření Function App. [Přečtěte si další informace o Azure Functions](../azure-functions/index.yml).

- Pro REST API se vyžadují přihlašovací údaje **GooglePickleString** . [Přečtěte si další informace o Google REST API](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Přečtěte si, jak získat přihlašovací údaje](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>Konfigurace a připojení pracovního prostoru Google

Pracovní prostor Google dokáže integrovat a exportovat protokoly přímo do Azure Sentinel pomocí Function App Azure.

> [!NOTE]
> Tento konektor používá Azure Functions pro připojení k rozhraní API sestav Google, aby se události aktivity načetly do Azure Sentinel. To může vést k dodatečným nákladům příjmu dat. Podrobnosti najdete na stránce s [cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/) .

1. Na portálu Sentinel Azure klikněte na **datové konektory**. 

1. Z Galerie konektorů vyberte **pracovní prostor Google (G Suite) (Preview)** a vyberte možnost **otevřít stránku konektoru**.

1. Postupujte podle kroků popsaných v části **Konfigurace** stránky konektor.

## <a name="validate-connectivity-and-find-your-data"></a>Ověření připojení a hledání dat

Může trvat až 20 minut, než budete moci zobrazit ingestovaná data v Azure Sentinel.

Po navázání úspěšného připojení se data zobrazí v **protokolech** v části **vlastní protokoly** v následujících tabulkách:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Tento datový konektor závisí na analyzátoru založeném na funkci Kusto, která bude fungovat podle očekávání. [Pomocí těchto kroků](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) vytvořte alias funkcí **GWorkspaceActivityReports** Kusto.

V některých užitečných vzorových dotazech se můžete podívat na kartu **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit pracovní prostor Google ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.

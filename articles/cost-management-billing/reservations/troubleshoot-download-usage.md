---
title: Řešení potíží se stahováním podrobnosti o využití rezervací Azure
description: Tento článek vám pomůže pochopit, proč je stažení údajů o využití rezervovaných instancí na webu Azure Portal nedostupné.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147317"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Řešení potíží se stahováním podrobnosti o využití rezervací Azure

Tento článek vám pomůže pochopit, proč je stažení údajů o využití rezervovaných instancí na webu Azure Portal nedostupné.

## <a name="symptoms"></a>Příznaky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a přejděte k položce **Rezervace**.
1. Vyberte některou rezervaci.
1. Výchozí zobrazení na stránce s přehledem rezervací ukazuje využití za posledních sedm dní. Můžete vybrat **Stáhnout jako CSV** a stáhnout si podrobné údaje o využití rezervace.
1. Když změníte časový rozsah, možnost **Stáhnout jako CSV** přestane být k dispozici.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Příklad ukazující nedostupnou možnost Stáhnout jako CSV" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Příčina

Kvůli technickým omezením Azure nepodporuje stahování dat za dobu delší než sedm dnů. Dlouhá období u zákazníků s velkým počtem rezervací generují obrovské objemy dat. Vracení dat prostřednictvím rozhraní API klade velké nároky na prostředky Azure.

## <a name="solution"></a>Řešení

Chápeme, že si zákazníci chtějí stahovat data za delší období. Momentálně ale neexistuje způsob, jak údaje o využití rezervací za delší dobu stáhnout.

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích najdete v tématu [Co jsou rezervace Azure?](save-compute-costs-reservations.md).
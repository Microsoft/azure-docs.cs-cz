---
title: Připojit virtuální plochu Windows ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data virtuálních klientů Windows ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380287"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Připojit data virtuálních klientů Windows ke službě Azure Sentinel

Tento článek popisuje, jak můžete monitorovat prostředí virtuálních počítačů s Windows (WVD) pomocí služby Azure Sentinel.

Například monitorování prostředí virtuálních počítačů s Windows vám umožní poskytovat další vzdálenou práci pomocí virtualizovaných ploch a současně zachovat stav zabezpečení vaší organizace.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Data virtuálních klientů Windows v Azure Sentinel

Data virtuálních klientů Windows v Azure Sentinel obsahují tyto typy:


|Data  |Popis  |
|---------|---------|
|**Protokoly událostí Windows**     |  Protokoly událostí Windows z prostředí WVD se streamují do pracovního prostoru Log Analytics s povolenou službou Azure Sentinel stejným způsobem jako protokoly událostí Windows z jiných počítačů s Windows, mimo prostředí WVD. <br><br>Nainstalujte agenta Log Analytics do počítače s Windows a nakonfigurujte protokoly událostí systému Windows, které se mají odeslat do pracovního prostoru Log Analytics.<br><br>Další informace naleznete v tématu:<br>- [Instalace agenta Log Analytics do počítačů se systémem Windows](/azure/azure-monitor/agents/agent-windows)<br>- [Shromažďovat zdroje dat protokolu událostí systému Windows pomocí agenta Log Analytics](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Připojit události zabezpečení systému Windows](connect-windows-security-events.md)       |
|**Upozornění programu Microsoft Defender pro koncový bod (MDE)**     |  Pokud chcete nakonfigurovat MDE pro virtuální plochu Windows, použijte stejný postup jako u všech ostatních koncových bodů Windows. <br><br>Další informace naleznete v tématu: <br>- [Nastavení programu Microsoft Defender pro nasazení koncového bodu](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Připojení dat z Microsoft 365 Defenderu k Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Diagnostika virtuálních klientů Windows**     | Diagnostika virtuálních počítačů s Windows je funkce služby Virtual Desktop PaaS v systému Windows, která protokoluje informace vždy, když někdo přiřazenou roli virtuálních klientů Windows používá tuto službu. <br><br>Každý protokol obsahuje informace o tom, která role virtuálního počítače s Windows se v aktivitě účastnila, všechny chybové zprávy, které se zobrazí během relace, informace o tenantovi a informace o uživateli. <br><br>Funkce diagnostiky vytvoří protokoly aktivit pro uživatele i pro akce správy. <br><br>Další informace najdete v tématu [použití Log Analytics pro funkci diagnostiky ve virtuálním počítači s Windows](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019).        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Připojit data virtuálních klientů Windows

Pokud chcete začít ingestovat data virtuálních klientů Windows do služby Azure Sentinel, postupujte podle pokynů v dokumentaci k virtuálním plochám Windows.

Další informace najdete v tématu [nabízení dat virtuálních ploch Windows do pracovního prostoru Log Analytics](/azure/virtual-desktop/diagnostics-log-analytics).

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení spouštějte dotazy v Azure Sentinel proti svým Log Analyticsm datům.

Podívejte se například na ukázkové dotazy v [dokumentaci k virtuálním plochám Windows](/azure/virtual-desktop/diagnostics-log-analytics).


Funkce Sentinel Azure taky poskytuje integrované dotazy v oblasti **Obecné**  >  **protokoly**  >  **virtuálních počítačů s Windows** :

[![Integrované dotazy k virtuálním plochám Windows ve službě Azure Sentinel ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Další kroky


Další informace najdete v [glosáři Azure monitor pro virtuální počítače s Windows](/azure/virtual-desktop/azure-monitor-glossary).

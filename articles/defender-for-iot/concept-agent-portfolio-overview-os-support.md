---
title: Přehled portfolia agenta a podpora operačního systému (Preview)
description: Azure Defender pro IoT poskytuje velké portfolio agentů na základě typu zařízení.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: d2e463051d0897afe52981ea2d50ddd1f06bb54d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383418"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Přehled portfolia agenta a podpora operačního systému (Preview)

Azure Defender pro IoT poskytuje velké portfolio agentů na základě typu zařízení. 

## <a name="standalone-agent"></a>Samostatný Agent

Samostatný agent pokrývá většinu operačních systémů Linux, které je možné nasadit jako binární balíček, nebo jako zdrojový kód, který je možné začlenit jako součást firmwaru, a umožňuje úpravy a přizpůsobení na základě zákaznických potřeb. Příklad podpory operačního systému: 

| Operační systém | AMD | ARM32v7 |
|--|--|--|
| Debian 9 | ✓ | ✓ |
| Ubuntu 18.04 | ✓ |  |
| Ubuntu 20.04 | ✓ |  |

Pokud chcete získat další informace, podporu operačního systému nebo požádat o přístup ke zdrojovému kódu, abyste ho mohli začlenit jako součást firmwaru zařízení, kontaktujte svého správce účtů nebo pošlete e-mail na adresu <defender_micro_agent@microsoft.com> . 

## <a name="azure-rtos-micro-agent"></a>Agent Azure RTO Micro

Agent Azure Defender pro IoT Micro poskytuje komplexní a zjednodušené řešení zabezpečení pro zařízení, která používají Azure RTO. Azure Defender pro IoT Micro agent poskytuje pokrytí pro běžné hrozby a potenciální škodlivé aktivity na zařízeních s operačním systémem v reálném čase (RTO). Mikroagenti jsou vestavěná v rámci součásti Azure RTO NetX Duo a monitorují aktivitu sítě v zařízení. 

Agent Azure Defender pro IoT Micro je integrovaný jako součást součásti Azure RTO NetX Duo a sleduje činnost v síti zařízení. Mikroagent se skládá z komplexního a zjednodušeného řešení zabezpečení, které poskytuje pokrytí pro běžné hrozby a potenciální škodlivé aktivity na zařízeních s operačním systémem v reálném čase (RTO).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o nástroji [Standalone Micro Agent (Preview)](concept-standalone-micro-agent-overview.md).
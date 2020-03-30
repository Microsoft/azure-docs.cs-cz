---
title: Architektura Azure FarmBeats
description: Popisuje architekturu Azure FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: d64c2175072d9979cfda2ea5f75beb34d3ad0d6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482371"
---
# <a name="integration-patterns"></a>Vzory integrace

Azure FarmBeats je nabídka mezi podniky dostupná na Azure Marketplace. FarmBeats umožňuje agregaci zemědělských datových sad napříč poskytovateli a generování užitečných přehledů vytvořením modelů umělé inteligence (AI) nebo strojového učení (ML) pomocí datových sad.

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Následující části popisují vzor integrace pro Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Proč se integrovat s Azure FarmBeats?

Tato část je zaměřena na partnery, kteří chtějí integrovat své datové systémy (jako jsou senzory, drony, meteorologické stanice) do Azure FarmBeats.

Azure FarmBeats je rozšiřitelná nabídka, která zemědělským firmám umožňuje přidávat různé historické a reálné zemědělské datové sady do jedné platformy. Azure FarmBeats pomáhá zemědělské mu normalizovat, kontextualizovat a agregovat svá data v kontextu farmy.

Tím, že se stanete datovým partnerem Azure FarmBeats, můžete otevřít své systémy k širšímu přijetí a oslovit více zákazníků s vašimi nabídkami dat. Azure FarmBeats poskytuje rozšiřitelnou vrstvu rozhraní API nazvanou Datahub, která vám pomůže systematicky ingestovat data z vašich zařízení do standardizovaného schématu.

Jakmile budou data dostupná v rámci instance Azure FarmBeats vašich zákazníků, vaši zákazníci si mohou kromě vašich dat vytvořit bohatší analýzy a nástroje.

## <a name="next-steps"></a>Další kroky

Další informace o integraci dat senzorů naleznete v [integraci dat senzorů](sensor-partner-integration-in-azure-farmbeats.md) a integraci partnerů snímků naleznete v [tématu integrace partnerů snímků](imagery-partner-integration-in-azure-farmbeats.md).

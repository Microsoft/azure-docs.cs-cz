---
title: Podpora funkcí a vyřazení z provozu
titleSuffix: Azure Defender for IoT
description: Defender pro IoT bude dál podporovat jazyky C, C# a Edge až do 1. března 2022.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: a33e7ff230292d4ceb14610e3cf00935dc1a67a0
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493165"
---
# <a name="feature-support-and-retirement"></a>Podpora funkcí a vyřazení z provozu

Tento článek popisuje Azure Defender pro funkce IoT a podporu různých možností v rámci programu Defender pro IoT.

## <a name="defender-for-iot-c-c-and-edge-defender-iot-micro-agent-deprecation"></a>Defender pro IoT C, C# a Edge Defender – IoT-Micro-agent nepoužívá se

Nový mikroagent nahradí aktuální jazyk C, C# a Edge Defender – IoT-Micro-agent.  

Nový mikroagent je založený na znalostech a zkušenostech shromážděných z vývoje Defenderu – IoT-Micro-Agent pro vývoj, zákazníky a partnery se svými názory se čtyřmi důležitými vylepšeními: 

- **Hodnota zabezpečení hloubky**: nový agent bude spuštěn na úrovni hostitele, který bude poskytovat lepší přehled o základních operacích zařízení a umožňuje lepší pokrytí zabezpečení.

- **Vylepšený výkon zařízení a snížené nároky** na kapacitu: dosahování malých pamětí RAM a paměti paměti ROM a také nízké spotřeby procesoru.  

- **Plug and Play**: nový mikroagent už nemá závislosti na úrovni jádra a všechny jeho závislosti softwaru jsou k dispozici jako součást jeho balíčku. Mikroagent podporuje běžné architektury procesoru.

- **Snadné nasazení**: Micro Agent podporuje různé distribuční modely, prostřednictvím zdrojového kódu a jako binární balíček. 

### <a name="timeline"></a>Časová osa 

Defender pro IoT bude dál podporovat jazyky C, C# a Edge až do 1. března 2022. 

## <a name="micro-agent-preview-support"></a>Podpora pro verzi Micro agent Preview

Během verze Preview může mikroagentům docházet k zásadním změnám bez předchozího upozornění.

## <a name="next-steps"></a>Další kroky

Podívejte [se na Defender pro rozhraní API pro senzory IoT a konzolu pro správu](references-work-with-defender-for-iot-apis.md).
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
ms.openlocfilehash: 2779a73d3a5f77e3a3b144309baf6d2788565443
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809772"
---
# <a name="feature-support-and-retirement"></a>Podpora funkcí a vyřazení z provozu

Tento článek popisuje Azure Defender pro funkce IoT a podporu různých možností v rámci programu Defender pro IoT.

## <a name="defender-for-iot-c-c-and-edge-security-module-deprecation"></a>Defender pro zastaralost modulu zabezpečení IoT C, C# a Edge

Nový mikroagent nahradí aktuální modul zabezpečení jazyka C, C# a Edge.  

Nový mikroagent je založený na znalostech a zkušenostech shromážděných z ukončení vývoje modulu zabezpečení, zákazníků a partnerů, kteří se dozvědí o čtyři důležitá vylepšení: 

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
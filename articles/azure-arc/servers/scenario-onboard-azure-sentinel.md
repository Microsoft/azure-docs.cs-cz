---
title: Zprovoznění serveru s povolenou službou Azure ARC do Azure Sentinel
description: Naučte se, jak přidat servery s podporou ARC Azure do Azure Sentinel a aktivně monitorovat jejich stav zabezpečení.
ms.date: 11/16/2020
ms.topic: conceptual
ms.openlocfilehash: 2364ba72ac5b10ec4e1f433cc6d591c3ca389ecd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584743"
---
# <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Připojení serverů s povoleným obloukem Azure do Azure Sentinel

Tento článek je určený k tomu, aby vám pomohla připojit server s podporou Azure ARC do [Azure Sentinel](../../sentinel/overview.md) a zahájit shromažďování událostí souvisejících se zabezpečením. Služba Azure Sentinel poskytuje jedno řešení pro detekci výstrah, viditelnost hrozeb, proaktivní lov a reakci na hrozby napříč podnikem.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste splnili následující požadavky:

- [Pracovní prostor služby Log Analytics](../../azure-monitor/logs/data-platform-logs.md). Další informace o Log Analytics pracovních prostorech najdete v tématu [navrhování Azure Monitorch protokolů nasazení](../../azure-monitor/logs/design-logs-deployment.md).

- [Ve vašem předplatném je povolená](../../sentinel/quickstart-onboard.md)možnost Azure Sentinel.

- Jste počítač nebo server je připojený k serverům s podporou ARC Azure.

## <a name="onboard-azure-arc-enabled-servers-to-azure-sentinel"></a>Připojení serverů s povoleným obloukem Azure do Azure Sentinel

Azure Sentinel se dodává s řadou konektorů pro řešení Microsoftu, které jsou k dispozici, a poskytuje integraci v reálném čase. U fyzických a virtuálních počítačů můžete nainstalovat agenta Log Analytics, který shromáždí protokoly a předává je do Azure Sentinel. Servery s podporou ARC podporují nasazení Log Analytics agenta pomocí následujících metod:

- Pomocí architektury rozšíření virtuálních počítačů.

    Tato funkce na serverech s podporou ARC Azure umožňuje nasadit rozšíření virtuálního počítače Log Analytics agenta do jiného serveru než Azure s Windows nebo Linux. Rozšíření virtuálních počítačů je možné spravovat pomocí následujících metod na hybridních počítačích nebo serverech spravovaných servery s podporou ARC:

    - [Azure Portal](manage-vm-extensions-portal.md)
    - Rozhraní příkazového [řádku Azure](manage-vm-extensions-cli.md)
    - [Azure PowerShell](manage-vm-extensions-powershell.md)
    - [Šablony Azure správce prostředků](manage-vm-extensions-template.md)

- Použití Azure Policy.

    Pomocí tohoto přístupu použijete Azure Policy [nasadit Log Analytics agenta do systému Linux nebo integrované zásady Windows Azure ARC](../../governance/policy/samples/built-in-policies.md#monitoring) pro audit, pokud je na serveru s povoleným Arc agent Log Analytics nainstalován. Pokud není agent nainstalovaný, automaticky ho nasadí pomocí úlohy nápravy. Případně, pokud plánujete monitorovat počítače pomocí Azure Monitor pro virtuální počítače, místo toho použijte k instalaci a konfiguraci agenta Log Analytics [možnost povolit Azure monitor pro virtuální počítače](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

K instalaci agenta Log Analytics pro Windows nebo Linux doporučujeme použít Azure Policy.

Až budou servery s povoleným obloukem připojené, vaše data začnou streamovat do Azure Sentinel a jsou připravená na to, abyste mohli začít pracovat s. Protokoly můžete zobrazit v [vestavěných sešitech](../../sentinel/quickstart-get-visibility.md) a začít sestavovat dotazy v Log Analytics a [Prozkoumat data](../../sentinel/tutorial-investigate-cases.md).

## <a name="next-steps"></a>Další kroky

Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md).
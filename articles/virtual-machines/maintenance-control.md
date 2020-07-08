---
title: Přehled řízení údržby pro virtuální počítače Azure pomocí Azure Portal
description: Naučte se řídit, kdy se na virtuální počítače Azure používá údržba pomocí řízení údržby.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 950f4cfda73b40db0de8ba035868573cda1a5017
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84675789"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Správa aktualizací platformy pomocí řízení údržby 

Spravujte aktualizace platforem, které nevyžadují restart, pomocí řízení údržby. Azure často aktualizuje svoji infrastrukturu, aby vylepšila spolehlivost, výkon, zabezpečení a spouštěla nové funkce. Většina aktualizací je pro uživatele transparentní. Některé citlivé úlohy, jako jsou hraní her, streamování médií a finanční transakce, neumožňují tolerovat zamrznutí nebo odpojení virtuálních počítačů za účelem údržby ani pár sekund. Řízení údržby vám dává možnost počkat na aktualizace platformy a použít ji v průběhu 35ého okna. 

Řízení údržby vám umožní určit, kdy se mají aktualizace použít pro izolované virtuální počítače a vyhrazené hostitele Azure.

Pomocí řízení údržby můžete:
- Dávka se aktualizuje do jednoho balíčku aktualizace.
- Počkejte až 35 dní, než se aktualizace použijí. 
- Automatizujte aktualizace platforem pro okno údržby pomocí Azure Functions.
- Konfigurace údržby pracují v rámci předplatných a skupin prostředků. 

## <a name="limitations"></a>Omezení

- Virtuální počítače musí být na [vyhrazeném hostiteli](./linux/dedicated-hosts.md)nebo být vytvořené pomocí [izolované velikosti virtuálního počítače](./linux/isolation.md).
- Po 35 dnech se automaticky použije aktualizace.
- Uživatel musí mít přístup k **přispěvateli prostředků** .

## <a name="management-options"></a>Možnosti správy

Konfigurace údržby můžete vytvořit a spravovat pomocí kterékoli z následujících možností:

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Údržba a aktualizace](maintenance-and-updates.md).

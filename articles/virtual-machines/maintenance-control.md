---
title: Přehled řízení údržby pro virtuální počítače Azure pomocí Azure Portal
description: Naučte se řídit, kdy se na virtuální počítače Azure používá údržba pomocí řízení údržby.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: 290a1e8da4e9b3e8eff171ab2d5837bfc9c381b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552418"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Správa aktualizací platformy pomocí řízení údržby 

Spravujte aktualizace platforem, které nevyžadují restart, pomocí řízení údržby. Azure často aktualizuje svoji infrastrukturu, aby vylepšila spolehlivost, výkon, zabezpečení a spouštěla nové funkce. Většina aktualizací je pro uživatele transparentní. Některé citlivé úlohy, jako jsou hraní her, streamování médií a finanční transakce, neumožňují tolerovat zamrznutí nebo odpojení virtuálních počítačů za účelem údržby ani pár sekund. Řízení údržby vám dává možnost počkat na aktualizace platformy a použít ji v průběhu 35ého okna. 

Řízení údržby vám umožní určit, kdy se mají aktualizace použít pro izolované virtuální počítače a vyhrazené hostitele Azure.

Pomocí řízení údržby můžete:
- Dávka se aktualizuje do jednoho balíčku aktualizace.
- Počkejte až 35 dní, než se aktualizace použijí. 
- Automatizujte aktualizace platforem konfigurací plánu údržby nebo pomocí [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Konfigurace údržby pracují v rámci předplatných a skupin prostředků. 

## <a name="limitations"></a>Omezení

- Virtuální počítače musí být na [vyhrazeném hostiteli](./dedicated-hosts.md)nebo být vytvořené pomocí [izolované velikosti virtuálního počítače](isolation.md).
- Pokud je deklarován plán údržby, musí být nejméně 2 hodiny.
- Po 35 dnech se automaticky použije aktualizace.
- Uživatel musí mít přístup k **přispěvateli prostředků** .

## <a name="management-options"></a>Možnosti správy

Konfigurace údržby můžete vytvořit a spravovat pomocí kterékoli z následujících možností:

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

Ukázku Azure Functions najdete v tématu [Plánování aktualizací údržby s ovládáním a správou údržby a Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Údržba a aktualizace](maintenance-and-updates.md).

---
title: Připojit data událostí zabezpečení systému Windows ke službě Azure Sentinel | Microsoft Docs
description: Naučte se připojit data událostí zabezpečení systému Windows ke službě Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: c74cc5cc65f16eb38ead7c09b5e662cd2463af35
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555290"
---
# <a name="connect-windows-security-events"></a>Připojení událostí zabezpečení systému Windows 



Můžete streamovat všechny události zabezpečení ze serverů Windows připojených k pracovnímu prostoru Azure Sentinel. Toto připojení umožňuje zobrazit řídicí panely, vytvořit vlastní výstrahy a vylepšit šetření. Získáte tak lepší přehled o síti vaší organizace a zlepšíte možnosti vaší operace zabezpečení.  Můžete vybrat události ke streamování:

- **Všechny události** – všechny události zabezpečení systému Windows a AppLockeru.
- **Běžné** – standardní sada událostí pro účely auditování. V této sadě je zahrnutý úplný záznam pro audit uživatele. Například tato sada obsahuje události přihlášení uživatele i odhlášení uživatele (ID události 4634). Zahrnujeme i akce auditování, jako jsou změny skupiny zabezpečení, klíčové operace protokolu Kerberos řadiče domény a další události, které jsou doporučeny pro oborové organizace.

Do společné sady byly zahrnuty události s velmi nízkým objemem, jako je hlavní motivace a jejich výběr přes všechny události, je snížit objem a Nefiltrovat konkrétní události.
- **Minimální** – malá sada událostí, která může poukazovat na potenciální hrozby. Když tuto možnost povolíte, nebudete moct mít úplný záznam pro audit.  Tato sada se zabývá pouze událostmi, které mohou indikovat úspěšné porušení a důležité události, které mají velmi nízký objem. Například tato sada obsahuje úspěšné a neúspěšné přihlášení (ID události 4624, 4625), ale neobsahuje informace o odhlášení, které jsou důležité pro auditování, ale nejsou smysluplné pro detekci a mají poměrně velký objem. Většina objemů dat v této sadě je události přihlášení a událost vytvoření procesu (ID události 4688).
- **Žádné** – žádné události zabezpečení nebo AppLockeru.

> [!NOTE]
> 
> - Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.
> - Pokud jsou Azure Security Center a Azure Sentinel spuštěné ve stejném pracovním prostoru, konektory událostí zabezpečení se můžou připojit jenom z Azure Security Center nebo pomocí Azure Sentinel. Pokud chcete tyto události spravovat z Azure Sentinel, doporučujeme, abyste ji odpojili od Azure Security Center a připojili ji jenom k Azure Sentinel.


Následující seznam uvádí úplný rozpis ID událostí zabezpečení a zámků aplikací pro každou sadu:

| Datová vrstva | Shromážděné indikátory událostí |
| --- | --- |
| Poskytuje | 1102, 4624, 4625, 4657, 4663, 4688,, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, |
| | 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| Společné | 1, čl. 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1108, 4608, 4610, 4611, 4614, 4622, |
| |  4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, |
| | 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, |
| | 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, |
| | 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, |
| | 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, |
| | 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Nastavení konektoru událostí zabezpečení systému Windows

Úplné integrace událostí zabezpečení systému Windows s Sentinel Azure:

1. Na portálu Sentinel Azure vyberte **datové konektory** a pak klikněte na dlaždici **události zabezpečení** . 
1. Vyberte datové typy, které chcete streamovat.
1. Klikněte na **Aktualizovat**.
6. Pokud chcete použít příslušné schéma v Log Analytics pro události zabezpečení systému Windows, vyhledejte **SecurityEvent**.

## <a name="validate-connectivity"></a>Ověřit připojení

Může to trvat přibližně 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit události zabezpečení systému Windows ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).


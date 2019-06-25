---
title: Připojení dat událostí zabezpečení Windows Azure Sentinelu ve verzi Preview | Dokumentace Microsoftu
description: Informace o připojení k Azure ověřovacích dat událostí zabezpečení Windows.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 36d38aa82b4f0ec8d7d9ef6ebb1145b1fcc334df
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190576"
---
# <a name="connect-windows-security-events"></a>Připojení událostí zabezpečení systému Windows 

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Všechny události zabezpečení z Windows serverů, připojený k pracovnímu prostoru Sentinelu Azure můžete Streamovat. Toto připojení umožňuje zobrazit řídicí panely, vytvářet vlastní výstrahy a zlepšit šetření. To poskytuje lepší přehled o síti vaší organizace a zlepšuje schopnosti operace zabezpečení.  Můžete vybrat události datového proudu:

- **Všechny události** – všechny Windows zabezpečení a událostí nástroje AppLocker.
- **Běžné** – standardní sadu událostí, pro účely auditování. Záznam pro audit úplné uživatelské je zahrnuta v této sadě. Tato sada obsahuje například uživatelských přihlášení a odhlášení uživatele (událost ID 4634). Zahrnujeme auditování akce, jako je změnami skupin zabezpečení, operace protokolu Kerberos řadič klíče domény a další události, které doporučuje organizace odvětví.

Události, které mají velmi malé množství byly součástí běžnou sadu jako hlavní motivace rozhodnout se, že všechny události přímo je snížit objem a nechcete vyfiltrovat konkrétní události.
- **Minimální** -malou sadu událostí, které může znamenat potenciální hrozby. Když tuto možnost povolíte, nebudete moci mít úplný záznam pro audit.  Tato sada zahrnuje pouze události, které mohou uvádět úspěšné porušení zabezpečení a důležité události, které obsahují velmi malé množství. Například tato sada obsahuje úspěšné i neúspěšné přihlášení uživatele (událost ID 4624 4625), ale neobsahuje odhlášení, který je důležitý pro auditování, ale ne smysl pro zjišťování a má relativně velkým objemem. Většina objemu dat této sady se události přihlášení a proces vytvoření události (událost ID 4688).
- **Žádný** – žádné události zabezpečení ani Applockeru.

> [!NOTE]
> 
> - Data se uloží v zeměpisné oblasti pracovního prostoru, na kterém je spuštěný Sentinelu Azure.

Následující seznam obsahuje kompletní přehled zabezpečení a AppLocker ID událostí pro každou sadu:

| Datová vrstva | Shromažďovat události ukazatelů |
| --- | --- |
| Minimální | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Společné | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

## <a name="set-up-the-windows-security-events-connector"></a>Nastavení konektoru události zabezpečení Windows

Umožňuje plnou integraci události zabezpečení Windows s Azure Sentinelu:

1. Na portálu Azure Sentinelu vyberte **datové konektory** a potom klikněte na **události zabezpečení Windows** dlaždici. 
1. Vyberte typy dat, které chcete Streamovat.
1. Klikněte na tlačítko **aktualizace**.
6. Chcete-li použít příslušné schéma v Log Analytics pro události zabezpečení Windows, vyhledejte **SecurityEvent**.

## <a name="validate-connectivity"></a>Ověření připojení

Může trvat přibližně 20 minut, než vaše protokoly spuštění se zobrazí v Log Analytics. 



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste zjistili, jak se připojit k Azure Sentinelu události zabezpečení Windows. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).


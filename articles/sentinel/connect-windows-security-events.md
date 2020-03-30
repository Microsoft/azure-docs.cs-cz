---
title: Připojení dat událostí zabezpečení Windows k Azure Sentinelu| Dokumenty společnosti Microsoft
description: Přečtěte si, jak připojit data událostí zabezpečení Windows k Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2020
ms.author: yelevin
ms.openlocfilehash: 30055ba1befc68d015e3e3162d8db11a2916f3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124969"
---
# <a name="connect-windows-security-events"></a>Připojení událostí zabezpečení systému Windows 

Konektor Události zabezpečení umožňuje streamovat všechny události zabezpečení ze systémů Windows (servery a pracovní stanice, fyzické a virtuální) do pracovního prostoru Azure Sentinel. To vám umožní zobrazit události zabezpečení systému Windows v řídicích panelech, použít je při vytváření vlastních výstrah a spolehnout se na ně při vylepšování vyšetřování, poskytuje vám větší přehled o síti vaší organizace a rozšiřuje se operace zabezpečení Schopnosti. Můžete vybrat události, které chcete streamovat z následujících sad:<a name="event-sets"></a>

- **Všechny události** – všechny události zabezpečení systému Windows a nástroje AppLocker.
- **Běžné** – standardní sada událostí pro účely auditu. V této sadě je zahrnuta úplná stopa auditu uživatele. Například obsahuje události přihlášení uživatele i odhlašování uživatele (ID událostí 4624, 4634). Existují také audity akcí, jako jsou změny skupiny zabezpečení, operace protokolu Kerberos řadiče domény klíče a další typy událostí v souladu s přijatými osvědčenými postupy.

    Sada **běžných** událostí může obsahovat některé typy událostí, které nejsou tak běžné.  Důvodem je, že hlavním bodem **společné** sady je snížit objem událostí na lépe zvládnutelnou úroveň při zachování funkce úplného auditu.

- **Minimální** – malá sada událostí, které mohou naznačovat potenciální hrozby. Tato sada neobsahuje úplný záznam auditu. Vztahuje se pouze na události, které by mohly naznačovat úspěšné porušení, a další důležité události, které mají velmi nízkou míru výskytu. Obsahuje například úspěšné a neúspěšné přihlášení uživatelů (ID událostí 4624, 4625), ale neobsahuje informace o odhlášení (4634), které jsou sice důležité pro auditování, ale nejsou smysluplné pro zjišťování porušení zabezpečení a mají relativně vysoký objem. Většina objemu dat této sady se skládá z událostí přihlášení a událostí vytváření procesů (ID události 4688).

- **Žádné** – žádné události zabezpečení nebo nástroje AppLocker. (Toto nastavení slouží k zakázání konektoru.)

    Následující seznam obsahuje úplný rozpis ID událostí zabezpečení a suty aplikací pro každou sadu:

    | Sada událostí | Shromážděná ID událostí |
    | --- | --- |
    | **Minimální** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Společné** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702 , 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793 , 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003 , 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> Kolekce událostí zabezpečení v rámci jednoho pracovního prostoru lze nakonfigurovat buď z Azure Security Center nebo Azure Sentinel, ale ne obojí. Pokud zařazujete Azure Sentinel v pracovním prostoru, který už běží Azure Security Center a je nastavena na shromažďování událostí zabezpečení, máte dvě možnosti:
> - Ponechte kolekci událostí zabezpečení v Azure Security Center tak, jak je. Tyto události budete moct dotazovat a analyzovat v Azure Sentinelu i v Azure Security Center. Nebudete však moci sledovat stav připojení konektoru nebo změnit jeho konfiguraci v Azure Sentinelu. Pokud je to pro vás důležité, zvažte druhou možnost.
>
> - [Zakažte shromažďování událostí zabezpečení](../security-center/security-center-enable-data-collection.md) v Centru zabezpečení Azure a pak přidejte konektor Událostí zabezpečení v Azure Sentinelu. Stejně jako u první možnosti budete moct dotazovat a analyzovat události v Azure Sentinelu i Azure Security Center, ale teď budete moct monitorovat stav připojení konektoru nebo změnit jeho konfiguraci v Azure Sentinelu a jenom v něj.

## <a name="set-up-the-windows-security-events-connector"></a>Nastavení konektoru Události zabezpečení systému Windows

Sbírání událostí zabezpečení Windows v Azure Sentinelu:

1. V navigační nabídce Azure Sentinel vyberte **Datové konektory**. V seznamu konektorů klikněte na **události zabezpečení**a potom na tlačítko **Otevřít stránku konektoru** v pravo dole. Potom postupujte podle pokynů na obrazovce na kartě **Pokyny,** jak je popsáno ve zbývající části této části.

1. Ověřte, zda máte příslušná oprávnění popsaná v části **Požadavky**.

1. Stáhněte a nainstalujte [agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (označovaný také jako Microsoft Monitoring Agent nebo MMA) na počítačích, pro které chcete streamovat události zabezpečení do Azure Sentinelu.

    Pro virtuální počítače Azure:
    
    1. Klikněte **na Nainstalovat agenta na Azure Windows Virtual Machine**a potom na odkaz, který se zobrazí níže.
    1. U každého virtuálního počítače, který chcete připojit, klikněte v seznamu vpravo na jeho název a potom klikněte na **Připojit**.

    Pro počítače s Windows, které nejsou azure (fyzické, virtuální on-prem nebo virtuální v jiném cloudu):

    1. Klikněte **na Nainstalovat agenta na počítači s Windows, který není Azure,** a potom na odkaz, který se zobrazí níže.
    1. Klikněte na příslušné odkazy ke stažení, které se zobrazí vpravo v části **Počítače se systémem Windows**.
    1. Pomocí staženého spustitelného souboru nainstalujte agenta do vybraných systémů Windows a nakonfigurujte jej pomocí **ID pracovního prostoru a klíčů,** které se zobrazí pod výše uvedenými odkazy ke stažení.

    > [!NOTE]
    >
    > Chcete-li povolit systémům Windows bez nezbytnépřipojení k internetu k stále streamování událostí do Azure Sentinelu, stáhněte a nainstalujte **bránu OMS** na samostatný počítač pomocí odkazu v pravém dolním bodě, aby působiljako proxy server.  Stále budete muset nainstalovat agenta Log Analytics do každého systému Windows, jehož události chcete shromažďovat.
    >
    > Další informace o tomto scénáři naleznete v [dokumentaci **brány Log Analytics** ](../azure-monitor/platform/gateway.md).

    Další možnosti instalace a další podrobnosti naleznete v [dokumentaci **agenta Log Analytics** ](../azure-monitor/platform/agent-windows.md).

1. Vyberte sadu událostí ([Vše, Běžné nebo Minimální),](#event-sets)kterou chcete streamovat.

1. Klikněte na **Aktualizovat**.

1. Chcete-li použít příslušné schéma v Log Analytics pro `SecurityEvent` události zabezpečení systému Windows, zadejte okno dotazu.

## <a name="validate-connectivity"></a>Ověřit připojení

Může trvat přibližně 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli, jak připojit události zabezpečení systému Windows k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Můžete začít zjišťovat hrozby pomocí Azure Sentinelu pomocí [integrovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.


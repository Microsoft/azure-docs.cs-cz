---
title: Připojit data událostí zabezpečení systému Windows ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat konektory událostí zabezpečení ke streamování všech událostí zabezpečení ze systémů Windows do vašeho pracovního prostoru služby Azure Sentinel. 
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: a16afcafa03ef2ab8642316db560e30a473a526b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90883717"
---
# <a name="connect-windows-security-events"></a>Připojení událostí zabezpečení systému Windows 

Konektor události zabezpečení umožňuje streamovat všechny události zabezpečení ze systémů Windows (serverů a pracovních stanic, fyzických a virtuálních) do pracovního prostoru služby Azure Sentinel. To vám umožní zobrazit události zabezpečení Windows ve vašich řídicích panelech, abyste je mohli používat při vytváření vlastních výstrah, a spoléhat na ně, aby využívala vaše šetření, což vám poskytne lepší přehled o síti vaší organizace a rozšíření možností operací zabezpečení. Můžete vybrat, které události se mají streamovat mezi následujícími sadami: <a name="event-sets"></a>

- **Všechny události** – všechny události zabezpečení systému Windows a AppLockeru.
- **Běžné** – standardní sada událostí pro účely auditování. V této sadě je zahrnutý úplný záznam pro audit uživatele. Obsahuje například přihlašovací události uživatelů i odhlášení uživatelů (ID událostí 4624, 4634). K dispozici jsou také akce auditování, jako jsou například změny skupiny zabezpečení, klíčové operace protokolu Kerberos řadiče domény a další typy událostí v souladu s doporučenými osvědčenými postupy.

    **Společná** sada událostí může obsahovat některé typy událostí, které nejsou tak běžné.  Důvodem je to, že hlavním bodem **společné** sady je snížení objemu událostí na více spravovatelných úrovní a zároveň zachování úplného záznamu pro audit.

- **Minimální** – malá sada událostí, která může poukazovat na potenciální hrozby. Tato sada neobsahuje úplný záznam pro audit. Pokrývá jenom události, které můžou indikovat úspěšné porušení předpisů, a další důležité události, které mají velmi nízké míry výskytu. Obsahuje například úspěšná a Nezdařená přihlášení uživatelů (ID událostí 4624, 4625), ale neobsahuje informace o odhlášení (4634), které jsou důležité pro zjišťování porušení a mají poměrně vysoký objem. Většina dat v této sadě je tvořená událostmi přihlášení a událostmi vytvoření procesu (ID události 4688).

- **Žádné** – žádné události zabezpečení nebo AppLockeru. (Toto nastavení slouží k zakázání konektoru.)

    Následující seznam uvádí úplný rozpis ID událostí zabezpečení a zámků aplikací pro každou sadu:

    | Sada událostí | Shromážděná ID událostí |
    | --- | --- |
    | **Minimální** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
    | **Společné** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729 , 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137 , 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |

> [!NOTE]
> Kolekce událostí zabezpečení v rámci jednoho pracovního prostoru se dá nakonfigurovat buď z Azure Security Center, nebo z kontextu Azure Sentinel, ale ne z obou. Pokud se připojujete ke službě Azure Sentinel v pracovním prostoru, který už načítá výstrahy Azure Defenderu z Azure Security Center a je nastavená na shromažďovat události zabezpečení, máte dvě možnosti:
> - Ponechte shromažďování událostí zabezpečení v Azure Security Center tak, jak je. Tyto události budete moct dotazovat a analyzovat v Azure Sentinel i v Azure Defenderu. Nebudete ale moct monitorovat stav připojení konektoru nebo změnit jeho konfiguraci v konfiguraci Azure Sentinel. Pokud je to pro vás důležité, zvažte druhou možnost.
>
> - [Zakažte shromažďování událostí zabezpečení](../security-center/security-center-enable-data-collection.md) v Azure Security Center a pak přidejte konektor události zabezpečení do Azure Sentinel. Stejně jako u první volby budete moct dotazovat a analyzovat události v konfiguraci Azure Sentinel i v Azure Defenderu nebo v programu ASC, ale teď budete moct monitorovat stav připojení konektoru nebo změnit jeho konfiguraci v rámci a jenom v Azure Sentinel.

## <a name="set-up-the-windows-security-events-connector"></a>Nastavení konektoru událostí zabezpečení systému Windows

Shromažďování událostí zabezpečení systému Windows v rámci služby Azure Sentinel:

1. V navigační nabídce Azure Sentinel vyberte **datové konektory**. V seznamu konektorů klikněte na **události zabezpečení**a pak na tlačítku pro **otevření konektoru** na pravé straně. Pak postupujte podle pokynů na obrazovce na kartě **pokyny** , jak je popsáno ve zbývající části tohoto oddílu.

1. Ověřte, zda máte příslušná oprávnění, jak je popsáno v části **požadavky**.

1. Stáhněte a nainstalujte [agenta Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (také označovaného jako Microsoft Monitoring Agent nebo MMA) na počítačích, pro které chcete streamovat události zabezpečení do Azure Sentinel.

    Pro Azure Virtual Machines:
    
    1. Klikněte na **nainstalovat agenta na virtuálním počítači Azure s Windows**a pak na odkaz, který se zobrazuje níže.
    1. Pro každý virtuální počítač, ke kterému se chcete připojit, klikněte na jeho název v seznamu, který se zobrazí na pravé straně, a pak klikněte na **připojit**.

    Pro počítače s Windows mimo Azure (fyzický, virtuální Prem nebo Virtual v jiném cloudu):

    1. Klikněte na **nainstalovat agenta na počítači s Windows mimo Azure**a pak na odkaz, který se zobrazuje níže.
    1. Klikněte na příslušné odkazy ke stažení, které se zobrazí na pravé straně v části **počítače se systémem Windows**.
    1. Pomocí staženého spustitelného souboru nainstalujte agenta do systémů Windows podle svého výběru a nakonfigurujte ho pomocí **ID a klíčů pracovního prostoru** , které se zobrazí pod výše uvedenými odkazy pro stažení.

    > [!NOTE]
    >
    > Pokud chcete, aby systémy Windows bez nezbytného připojení k Internetu pořád mohly streamovat události do služby Azure Sentinel, Stáhněte a nainstalujte **bránu OMS** na samostatném počítači pomocí odkazu v pravém dolním rohu, který bude fungovat jako proxy server.  I nadále bude nutné nainstalovat agenta Log Analytics do každého systému Windows, jehož události chcete shromáždit.
    >
    > Další informace o tomto scénáři najdete v dokumentaci ke službě [ **Log Analytics Gateway** ](../azure-monitor/platform/gateway.md).

    Další možnosti instalace a další podrobnosti najdete v [dokumentaci k **agentovi Log Analytics** ](../azure-monitor/platform/agent-windows.md).

1. Vyberte, kterou sadu událostí ([všechny, společné nebo minimální](#event-sets)) chcete streamovat.

1. Klikněte na **Aktualizovat**.

1. Chcete-li použít příslušné schéma v Log Analytics pro události zabezpečení systému Windows, zadejte `SecurityEvent` do okna dotazu.

## <a name="validate-connectivity"></a>Ověřit připojení

Může to trvat přibližně 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics. 

### <a name="configure-the-security-events-connector-for-anomalous-rdp-login-detection"></a>Konfigurace konektoru události zabezpečení pro detekci přihlášení neobvyklé RDP

> [!IMPORTANT]
> Zjišťování přihlášení protokolu RDP neobvyklé je aktuálně ve verzi Public Preview.
> Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel může použít Machine Learning (ML) k datům událostí zabezpečení k identifikaci přihlašovací aktivity neobvyklé protokol RDP (Remote Desktop Protocol) (RDP). Mezi scénáře patří:

- **Neobvyklá IP** adresa: v posledních 30 dnech se IP adresa vyskytuje jenom zřídka nebo nikdy nedodržena.

- **Neobvyklé geografické umístění** – IP adresa, město, země a ASN se v posledních 30 dnech nezjistily zřídka nebo nikdy.

- **Nový uživatel** – nový uživatel se přihlásí z IP adresy a geografického umístění, nebo z toho, z nichž se neočekávalo, že se bude zobrazovat na základě dat od 30 dní předem.

**Pokyny ke konfiguraci**

1. Musíte shromažďovat přihlašovací data protokolu RDP (ID události 4624) prostřednictvím konektoru dat **událostí zabezpečení** . Ujistěte se, že jste pro streamování do Azure Sentinel vybrali [sadu událostí](#event-sets) kromě možnosti žádná.

1. Na portálu Sentinel Azure klikněte na **Analytics**a pak klikněte na kartu **šablony pravidel** . Vyberte pravidlo **zjišťování přihlášení (Preview) neobvyklé protokolu RDP** a přesuňte posuvník **stav** na **povoleno**.

    > [!NOTE]
    > Vzhledem k tomu, že algoritmus strojového učení vyžaduje po dobu 30 dnů, aby se vytvořil základní profil chování uživatele, je nutné, aby bylo možné shromáždit data událostí zabezpečení před tím, než bude možné zjistit případné incidenty.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak připojit události zabezpečení systému Windows ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.


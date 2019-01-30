---
title: Ověření Azure Stack jako klíčové koncepty služby | Dokumentace Microsoftu
description: Popisuje klíčové pojmy v Azure stacku ověření jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1f5c47dd3453c0c8f02f1b0a87e5f2fff123f8be
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242803"
---
# <a name="validation-as-a-service-key-concepts"></a>Ověření jako klíčové koncepty služby

Tento článek popisuje klíčové pojmy v ověřování jako služba (VaaS).

## <a name="solutions"></a>Řešení

Řešení VaaS představuje řešení Azure Stack konkrétní hardware kusovník (BoM). Řešení VaaS funguje jako kontejner pro pracovní postupy, které spustily řešení Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Vytvoření řešení na portálu VaaS

1. Přihlaste se k [VaaS portál](https://azurestackvalidation.com).
2. Na řídicím panelu řešení, vyberte na **nové řešení**.
3. Zadejte název řešení. Návrhy pojmenování, naleznete v tématu [zásady vytváření názvů pro řešení VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Vyberte **Uložit** k vytvoření řešení.

## <a name="workflows"></a>Pracovní postupy

Pracovní postup VaaS pracuje v rámci kontextu VaaS řešení. Představuje sadu testovacích sad, které vykonávají funkce pro nasazení služby Azure Stack. Pracovní postup musí být vytvořené pro všechny aktualizace softwaru nebo nasazení řešení Azure Stack.

Pracovní postupy jsou rozděleny podle typu scénář testování. Při testování neoficiální **průchodu testů** pracovního postupu umožňuje vybrat testy ze všech dostupných VaaS dokumentaci. Při testování oficiální **ověření** pracovních postupů cílit na konkrétní scénáře testování vybraných microsoftem.

![Dlaždice VaaS pracovního postupu](media/tile_all-workflows.png)

> [!NOTE]
> **Ověření řešení** pracovního postupu v současné době podporuje dva scénáře: [Ověření OEM balíčky](azure-stack-vaas-validate-oem-package.md) a [ověření aktualizace softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md).

Další informace o typech pracovních postupů, najdete v části [co je ověření jako služba pro službu Azure Stack?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Začínáme s pracovními postupy VaaS

1. Na řídicím panelu řešení vytvořte nové řešení nebo vyberte existující. Tím se aktualizuje a umožní dlaždice pracovního postupu.
2. K vytvoření nového pracovního postupu, vyberte na **Start** na kteroukoli dlaždici. Informace specifické pro každý pracovní postup najdete v následujících článcích:
    - Průchod testu: [Rychlé zprovoznění: Použít ověření jako portál služby k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md)
    - Ověření řešení: [Ověření nové řešení Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Ověření řešení: [Ověření aktualizace softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md)
    - Ověření řešení: [Ověření balíčky výrobce OEM](azure-stack-vaas-validate-oem-package.md)

3. Ke správě nebo monitorování stávajícím pracovním postupu, vyberte na **spravovat** na dlaždici pracovního postupu. Vyberte název pracovního postupu a jeho používání **upravit** tlačítko Zobrazit vlastnosti nebo změňte společné parametry testu.

Další informace o parametry a vlastnosti pracovního postupu najdete v tématu [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Testy

Test v VaaS se skládá z sadu akcí, které spustily řešení s Azure Stack. Testy mají různé účely zamýšlený identifikovat podle kategorie, jako je funkční a spolehlivosti a cílit na jeden nebo více služeb Azure stacku. Každý test definuje vlastní sadu parametrů, z nichž některé jsou určena pomocí společných parametrů pracovního postupu obsahující.

Další informace o správě a monitorování testů, naleznete v tématu [monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md).

Další informace o parametry testu, naleznete v tématu [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agenti

VaaS agent jednotky provádění testů. Dva druhy agentů VaaS testy:

- **Cloudového agenta**. To je k dispozici v VaaS výchozí agent. Není vyžadováno žádné nastavení, ale to vyžaduje ve vázané na připojení pro vaše prostředí a koncové body služby Azure Stack musí jít přeložit z Internetu. Některé testy nejsou kompatibilní s agentem cloudu.
- A **místní agent**. To umožňuje spustit ověření ve scénářích, kde není v vázané na připojení pro vaše prostředí vhodná. Některé testy vyžadují spuštění prostřednictvím místního agenta.

Místní agenti nejsou vázané na konkrétního řešení Azure Stack nebo VaaS. Jako osvědčený postup by měl spustit mimo prostředí Azure Stack.

Pokyny k přidání místního agenta najdete v tématu [nasazení místní agent](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Další postup

- [Osvědčené postupy pro ověření jako služba](azure-stack-vaas-best-practice.md)
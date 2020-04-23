---
title: Vytváření incidentů z výstrah v Azure Sentinelu | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvářet incidenty z výstrah v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 1593b96ae8412632120e8977635a4193996ca88d
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025114"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Automatické vytváření incidentů z výstrah zabezpečení společnosti Microsoft

Výstrahy aktivované v řešeních zabezpečení Microsoftu, která jsou připojená k Azure Sentinelu, jako je Microsoft Cloud App Security a Azure Advanced Threat Protection, nevytvářejí incidenty v Azure Sentinelu automaticky. Ve výchozím nastavení při připojení řešení Microsoft u Azure Sentinelu se všechny výstrahy generované v této službě uloží jako nezpracovaná data v Azure Sentinelu v tabulce Výstrahy zabezpečení ve vašem pracovním prostoru Azure Sentinel. Tato data pak můžete použít jako jakákoli jiná nezpracovaná data, která připojíte k Sentinelu.

Azure Sentinel můžete snadno nakonfigurovat tak, aby automaticky vytvářel incidenty při každém spuštění výstrahy v připojeném řešení zabezpečení Microsoftu podle pokynů v tomto článku.

## <a name="prerequisites"></a>Požadované součásti
Chcete-li povolit vytváření incidentů pomocí výstrah služby zabezpečení, je nutné [připojit řešení zabezpečení společnosti Microsoft.](connect-data-sources.md#data-connection-methods)

## <a name="using-microsoft-security-incident-creation-analytic-rules"></a>Použití analytických pravidel pro vytváření incidentů zabezpečení společnosti Microsoft

Pomocí předdefinovaných pravidel dostupných v Azure Sentinelu si můžete vybrat, která propojená řešení zabezpečení Microsoftu by měla automaticky vytvářet incidenty Azure Sentinelu v reálném čase. Můžete také upravit pravidla definovat konkrétnější možnosti pro filtrování, které z výstrah generovaných řešením zabezpečení Microsoft u měl vytvořit incidenty v Azure Sentinelu. Můžete například automaticky vytvářet incidenty Azure Sentinelu pouze z výstrah Azure Security Center s vysokou závažností.

1. Na webu Azure Portal v části Azure Sentinel vyberte **Analytics**.

1. Výběrem karty **Šablony pravidel** zobrazíte všechna předdefinovaná analytická pravidla.

    ![Šablony pravidel](media/incidents-from-alerts/rule-templates.png)

1. Zvolte šablonu pravidel analýzy **zabezpečení společnosti Microsoft,** kterou chcete použít, a klikněte na **tlačítko Vytvořit pravidlo**.

    ![Pravidlo analýzy zabezpečení](media/incidents-from-alerts/security-analytics-rule.png)

1. Můžete upravit podrobnosti pravidla a zvolit filtrování výstrah, které vytvoří incidenty podle závažnosti výstrahy nebo podle textu obsaženého v názvu výstrahy.  
      
    Pokud například zvolíte **Azure Security Center** v poli **služby zabezpečení Microsoftu** a zvolíte **Vysoká** v poli **Filtr podle závažnosti,** budou incidenty v Azure Sentinelu automaticky vytvářet jenom výstrahy Azure Security Center s vysokou závažností.  

    ![Vytvořit průvodce pravidlem](media/incidents-from-alerts/create-rule-wizard.png)

1. Můžete také vytvořit nové pravidlo **zabezpečení společnosti Microsoft,** které filtruje výstrahy z různých služeb zabezpečení společnosti Microsoft, kliknutím na **tlačítko +Vytvořit** a výběrem **pravidla pro vytváření incidentů společnosti Microsoft**.

    ![Pravidlo pro vytvoření incidentu](media/incidents-from-alerts/incident-creation-rule.png)

  Můžete vytvořit více než jedno analytické pravidlo **zabezpečení společnosti Microsoft** pro každý typ **služby zabezpečení společnosti Microsoft.** Tím se nevytvoří duplicitní incidenty, protože každé pravidlo se používá jako filtr. I v případě, že výstraha odpovídá více než jedno analytické pravidlo **zabezpečení Microsoft,** vytvoří pouze jeden incident Azure Sentinel.

## <a name="enable-incident-generation-automatically-during-connection"></a>Povolit automatické generování incidentů během připojení
 Když připojíte řešení zabezpečení Microsoftu, můžete vybrat, jestli chcete, aby výstrahy z řešení zabezpečení automaticky generovaly incidenty v Azure Sentinelu automaticky.

1. Připojte zdroj dat řešení zabezpečení společnosti Microsoft. 

   ![Generovat bezpečnostní incidenty](media/incidents-from-alerts/generate-security-incidents.png)

1. V části **Vytvořit incidenty** vyberte **Povolit,** chcete-li povolit výchozí analytické pravidlo, které automaticky vytváří incidenty z výstrah generovaných v připojené službě zabezpečení. Toto pravidlo pak můžete upravit v části **Analytics** a potom **v části Aktivní pravidla**.

## <a name="next-steps"></a>Další kroky

- Abyste mohli začít pracovat s Azure Sentinelem, potřebujete předplatné Microsoft Azure. Pokud nemáte předplatné, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Zjistěte, jak [založit data do Azure Sentinelu](quickstart-onboard.md)a [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).

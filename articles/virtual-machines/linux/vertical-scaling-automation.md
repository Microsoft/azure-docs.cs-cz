---
title: Vertikální škálování virtuálních počítačů Azure s využitím Azure Automation | Dokumentace Microsoftu
description: Vertikální škálování virtuálního počítače s Linuxem v reakci na monitorování výstrahy se službou Azure Automation.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7190f13f9d41afffcbbc1104f533b0d0586a0d6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486034"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Vertikální škálování virtuálního počítače Azure s Linuxem pomocí Azure Automation
Vertikální škálování je proces zvýšením nebo snížením prostředky počítače v reakci na zatížení. V Azure to můžete provést tak, že změníte velikost virtuálního počítače. To může pomoct v těchto scénářích

* Pokud virtuální počítač nepoužívá často, můžete změnit jeho velikost na menší velikost chcete snížit měsíční náklady
* Pokud virtuální počítač se zobrazují zátěž ve špičce, můžete velikost na větší velikost, zvýšit jeho kapacity

Osnova – pokyny k tomu je jako níže

1. Nastavení přístupu k virtuálním počítačům Azure Automation.
2. Import runbooků Azure Automation vertikální škálování do vašeho předplatného
3. Přidání webhooku do runbooku
4. Přidání výstrahy pro váš virtuální počítač

## <a name="scale-limitations"></a>Omezení škálování

Kvůli velikosti první virtuální počítač, velikostí, které je možné škálovat, může být omezen z důvodu aktuální virtuální počítač nasazený v dostupnost dalších velikostí v clusteru. V runboocích publikované automation použité v tomto článku jsme postará o tento případ a pouze v rámci škálování následující dvojice velikost virtuálního počítače. To znamená, že virtuální počítač Standard_D1v2 není náhle být vertikálně kapacitu až na Standard_G5 úměrná Basic_A0. Také se nepodporuje omezené virtuální počítač velikosti škálovat směrem nahoru nebo dolů. 

Je možné škálovat mezi následující páry velikosti:

* [Řady A-Series](#a-series)
* [Řady B-Series](#b-series)
* [Řady D-Series](#d-series)
* [E-Series](#e-series)
* [Řada F-Series](#f-series)
* [Řada G-Series](#g-series)
* [Řada H-Series](#h-series)
* [Řada L-Series](#l-series)
* [Řada M-Series](#m-series)
* [N-Series](#n-series)

### <a name="a-series"></a>A-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>B-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>E-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>Řada F

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>G-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>H-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>N-Series

| Počáteční velikost. | Vertikální navýšení kapacity velikosti | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Nastavení přístupu k virtuálním počítačům Azure Automation.
První věc, kterou je třeba provést je vytvořit účet Azure Automation, který bude hostitelem použít k instancím Škálovací sady virtuálního počítače škálovací sady runbook. Služba Automation nedávno zavedli funkci "Účet Spustit jako", takže nastavení nahoru instanční objekt pro automatické spouštění sady runbook jménem uživatele velmi snadné. Další informace najdete v článku níže:

* [Ověření runbooků pomocí účtu Spustit v Azure jako](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Import runbooků Azure Automation vertikální škálování do vašeho předplatného
Sady runbook, které jsou potřeba pro vertikální škálování virtuálního počítače jsou již publikován v galerii Runbooků Azure Automation. Je potřeba importovat do vašeho předplatného. Můžete se dozvíte, jak importování sad runbook najdete v následujícím článku.

* [Galerie runbooků a modulů pro Azure Automation](../../automation/automation-runbook-gallery.md)

Na obrázku níže jsou uvedeny sady runbook, které je potřeba importovat

![Import runbooků](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Přidání webhooku do runbooku
Po importu sad runbook, které potřebujete k přidání webhooku do sady runbook, tak můžou být aktivované výstrahy z virtuálního počítače. Podrobnosti o vytvoření webhooku pro vaše sada Runbook můžete přečíst tady

* [Webhooky Azure Automation](../../automation/automation-webhooks.md)

Ujistěte se, že zkopírujete webhook před jeho zavřením dialogu webhooku, protože ji budete potřebovat v další části.

## <a name="add-an-alert-to-your-virtual-machine"></a>Přidání výstrahy pro váš virtuální počítač
1. Vyberte nastavení virtuálního počítače
2. Vyberte "Pravidla upozornění"
3. Vyberte "Přidat upozornění"
4. Vyberte metriku, která se aktivuje upozornění na
5. Vyberte podmínku, která splněny se upozornění aktivuje
6. V kroku 5 vyberte prahovou hodnotu pro podmínku. musí být splněny
7. Vybrat takovou dobu nad tím, které bude služba monitorování zkontrolujte podmínku a prahovou hodnotu v kroky 5 a 6
8. Vložte webhook, který jste zkopírovali v předchozí části.

![Přidání upozornění do virtuálního počítače 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Přidání upozornění k virtuálnímu počítači 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)


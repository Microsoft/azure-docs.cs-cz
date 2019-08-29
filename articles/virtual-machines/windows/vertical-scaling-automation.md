---
title: Použití Azure Automation ke vertikálnímu škálování virtuálních počítačů s Windows | Microsoft Docs
description: Vertikální škálování virtuálního počítače s Windows v reakci na monitorování výstrah pomocí Azure Automation
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71af54f0a1dd7ecd4d4f0dc6f7a465439993db39
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100172"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Vertikální škálování virtuálních počítačů s Windows pomocí Azure Automation

Vertikální škálování je proces zvýšení nebo snížení počtu prostředků počítače v reakci na zatížení. V Azure to můžete dosáhnout změnou velikosti virtuálního počítače. To může pomáhat v následujících scénářích:

* Pokud se virtuální počítač nepoužívá často, můžete jeho velikost zmenšit na menší velikost, aby se snížily měsíční náklady.
* Pokud se na virtuálním počítači zobrazuje zatížení ve špičce, můžete změnit jeho velikost na větší velikost, abyste zvýšili jeho kapacitu.

Postup, jak to provést, je následující:

1. Nastavit Azure Automation pro přístup k Virtual Machines
2. Import sad Runbook se Azure Automation vertikálním škálováním do předplatného
3. Přidání Webhooku do Runbooku
4. Přidání upozornění k virtuálnímu počítači


## <a name="scale-limitations"></a>Omezení škálování

Vzhledem k velikosti prvního virtuálního počítače se můžou velikosti, na které se dá škálovat, omezit kvůli dostupnosti dalších velikostí v clusteru, na kterém je nasazený aktuální virtuální počítač. V publikovaných runbookůch Automation používaných v tomto článku se postará o tento případ a jenom škálovat v rámci dvojice velikostí virtuálních počítačů. To znamená, že virtuální počítač s Standard_D1v2 se do Standard_G5 nebude po horizontálním navýšení kapacity škálovat až na Basic_A0. Také omezené velikosti virtuálních počítačů se škálují nahoru/dolů nejsou podporované. 

Můžete si vybrat, jestli chcete škálovat mezi následujícími páry velikostí:

* [Řada a-Series](#a-series)
* [Řady B-Series](#b-series)
* [Řady D-Series](#d-series)
* [Řady E](#e-series)
* [Řada F-Series](#f-series)
* [Řada G](#g-series)
* [Řada H-Series](#h-series)
* [Řady L](#l-series)
* [Řada M-Series](#m-series)
* [Řada N-Series](#n-series)

### <a name="a-series"></a>A-Series

| Počáteční velikost | Škálování velikosti | 
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

| Počáteční velikost | Škálování velikosti | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D-Series

| Počáteční velikost | Škálování velikosti | 
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

| Počáteční velikost | Škálování velikosti | 
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

| Počáteční velikost | Škálování velikosti | 
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

| Počáteční velikost | Škálování velikosti | 
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

| Počáteční velikost | Škálování velikosti | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L-Series

| Počáteční velikost | Škálování velikosti | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M-Series

| Počáteční velikost | Škálování velikosti | 
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

| Počáteční velikost | Škálování velikosti | 
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
| Standard_NV12s_v3 |Standard_NV48s_v3 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Nastavit Azure Automation pro přístup k Virtual Machines
První věc, kterou potřebujete udělat, je vytvořit účet Azure Automation, který bude hostovat Runbooky používané pro škálování virtuálního počítače. Nedávno Služba Automation zavedla funkci "účet Spustit jako", která umožňuje nastavit instanční objekt pro automatické spouštění Runbooků v zastoupení uživatele velmi snadné. Další informace si můžete přečíst v článku níže:

* [Ověření runbooků pomocí účtu Spustit v Azure jako](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Import sad Runbook se Azure Automation vertikálním škálováním do předplatného
Runbooky, které jsou potřeba pro vertikální škálování virtuálního počítače, jsou už publikované v galerii sady Runbook Azure Automation. Budete je muset naimportovat do svého předplatného. Informace o tom, jak importovat Runbooky, najdete v následujícím článku.

* [Galerie runbooků a modulů pro Azure Automation](../../automation/automation-runbook-gallery.md)

Sady Runbook, které je třeba importovat, jsou uvedeny na obrázku níže.

![Importovat Runbooky](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Přidání Webhooku do Runbooku
Po importu runbooků budete muset přidat Webhook do Runbooku, aby se mohl aktivovat výstrahou z virtuálního počítače. Podrobnosti o tom, jak vytvořit Webhook pro Runbook, si můžete přečíst tady.

* [Azure Automation Webhooky](../../automation/automation-webhooks.md)

Ujistěte se, že jste Webhook zkopírovali před zavřením dialogu Webhooku, protože ho budete potřebovat v další části.

## <a name="add-an-alert-to-your-virtual-machine"></a>Přidání upozornění k virtuálnímu počítači
1. Vybrat nastavení virtuálního počítače
2. Vybrat pravidla pro upozornění
3. Vyberte Přidat výstrahu.
4. Vyberte metriku, na které se má výstraha aktivovat.
5. Vyberte podmínku, která se po splnění vyvolá výstraha.
6. Vyberte prahovou hodnotu pro podmínku v kroku 5. k splnění
7. Vyberte dobu, po kterou služba monitorování zkontroluje podmínku a prahovou hodnotu v krocích 5 & 6.
8. Vložte do Webhooku, který jste zkopírovali z předchozí části.

![Přidat upozornění k virtuálnímu počítači 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Přidat upozornění k virtuálnímu počítači 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)


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
ms.openlocfilehash: d0d0c3683d8855418bdafa204325525c4cd3943c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050779"
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

> [!NOTE]
> Kvůli velikosti první virtuální počítač, velikostí, které je možné škálovat, může být omezen z důvodu aktuální virtuální počítač nasazený v dostupnost dalších velikostí v clusteru. V runboocích publikované automation použité v tomto článku jsme postará o tento případ a pouze v rámci škálování následující dvojice velikost virtuálního počítače. To znamená, že virtuální počítač Standard_D1v2 není náhle být vertikálně kapacitu až na Standard_G5 úměrná Basic_A0. Také se nepodporuje omezené virtuální počítač velikosti škálovat směrem nahoru nebo dolů. Je možné škálovat mezi následující páry velikosti:
> 
> | Velikosti virtuálních počítačů škálování pár |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |

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


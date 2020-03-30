---
title: Přesunutí konfigurace údržby do jiné oblasti Azure
description: Zjistěte, jak přesunout konfiguraci údržby virtuálních počítačů do jiné oblasti Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304456"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Přesunutí konfigurace řízení údržby do jiné oblasti

Podle tohoto článku přesuňte konfiguraci řízení údržby do jiné oblasti Azure. Konfiguraci můžete přesunout z mnoha důvodů. Chcete-li například využít výhod nové oblasti, nasadit funkce nebo služby dostupné v určité oblasti, splnit požadavky na vnitřní zásady a zásady správného řízení nebo v reakci na plánování kapacity.

Řízení údržby s přizpůsobenými konfiguracemi údržby umožňuje řídit způsob použití aktualizací platformy pro virtuální počítače [s Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) a [Linuxem](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) a na vyhrazené hostitele Azure. Existuje několik scénářů pro přesunutí řízení údržby napříč oblastmi:

- Chcete-li přesunout konfiguraci řízení údržby, ale ne prostředky přidružené ke konfiguraci, postupujte podle pokynů v tomto článku.
- Chcete-li přesunout prostředky přidružené k konfiguraci údržby, ale ne samotnou konfiguraci, postupujte [podle těchto pokynů](move-region-maintenance-configuration-resources.md).
- Chcete-li přesunout konfiguraci údržby i prostředky s ní spojené, postupujte nejprve podle pokynů v tomto článku. Poté postupujte podle [těchto pokynů](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Požadavky

Než začnete s přesunem konfigurace řízení údržby:

- Konfigurace údržby jsou přidružené k virtuálním počítačům Azure nebo vyhrazeným hostitelům Azure. Ujistěte se, že prostředky virtuálního montovanebo hostitele existují v nové oblasti, než začnete.
- Identifikovat: 
    - Stávající konfigurace řízení údržby.
    - Skupiny prostředků, ve kterých jsou aktuálně umístěny existující konfigurace. 
    - Skupiny prostředků, do kterých budou konfigurace přidány po přesunutí do nové oblasti. 
    - Prostředky přidružené ke konfiguraci údržby, kterou chcete přesunout.
    - Zkontrolujte, zda jsou prostředky v nové oblasti stejné jako prostředky přidružené k aktuálním konfiguracím údržby. Konfigurace mohou mít stejné názvy v nové oblasti jako ve staré oblasti, ale to není nutné.

## <a name="prepare-and-move"></a>Příprava a přesun 

1. Načíst všechny konfigurace údržby v každém předplatném. Spusťte příkaz [konfigurace údržby az](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) příkazu CLI a proveďte to a nahraďte $subId ID předplatného.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Zkontrolujte seznam vrácených tabulek konfiguračních záznamů v rámci předplatného. Tady je příklad. Seznam bude obsahovat hodnoty pro konkrétní prostředí.

    **Název** | **Umístění** | **Skupina prostředků**
    --- | --- | ---
    Přeskočit údržbu | eastus2 | skupina prostředků konfigurace
    IgniteDemoConfig | eastus2 | skupina prostředků konfigurace
    výchozíMaintenanceConfiguration-eastus | eastus | testovací konfigurace
    

3. Seznam si můžete uložit jako referenci. Při přesouvání konfigurací vám to pomůže ověřit, že všechno bylo přesunuto.
4. Jako odkaz namapujte každou skupinu konfigurace nebo prostředků na novou skupinu prostředků v nové oblasti.
5. Vytvořte nové konfigurace údržby v nové oblasti pomocí [prostředí PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)nebo [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Přidružte konfigurace k prostředkům v nové oblasti pomocí [prostředí PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)nebo [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Ověření přesunu

Po přesunutí konfigurací porovnejte konfigurace a prostředky v nové oblasti s připraveným seznamem tabulek.


## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Po přesunutí zvažte odstranění přesunutých konfigurací údržby ve zdrojové [oblasti, prostředí PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)nebo [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Další kroky

Pokud potřebujete přesunout prostředky přidružené k konfiguracím údržby, postupujte [podle těchto pokynů.](move-region-maintenance-configuration-resources.md) 

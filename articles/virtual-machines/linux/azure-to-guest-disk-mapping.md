---
title: Postup mapování disků Azure na disky hosta virtuálního počítače se systémem Linux
description: Jak určit disky Azure, které Underlay hostované disky virtuálního počítače se systémem Linux.
author: timbasham
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.collection: linux
ms.openlocfilehash: bc6c6273ab3d1a4403763e4ed0a8c491995fb2df
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556719"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>Postup mapování disků Azure na disky hosta virtuálního počítače se systémem Linux

Možná budete muset určit disky Azure, které zálohují disky hosta virtuálního počítače. V některých scénářích můžete porovnat velikost disku nebo svazku s velikostí připojených disků Azure. V případech, kdy je k virtuálnímu počítači připojeno několik disků Azure se stejnou velikostí, musíte použít logickou jednotku (LUN) datových disků. 

## <a name="what-is-a-lun"></a>Co je logická jednotka (LUN)?

Logická jednotka (LUN) je číslo, které se používá k identifikaci konkrétního úložného zařízení. Každému úložnému zařízení je přiřazen jedinečný číselný identifikátor, který začíná nulou. Úplná cesta k zařízení je reprezentovaná číslem sběrnice, číslem ID cíle a logickou jednotkou (LUN). 

Příklad: ***Sběrnice číslo 0, cílové ID 0, LUN 3***

Pro naše cvičení stačí použít logickou jednotku (LUN).

## <a name="finding-the-lun"></a>Hledání logické jednotky (LUN)

Níže uvádíme dvě metody, jak najít logickou jednotku disku v systému Linux.

### <a name="lsscsi"></a>lsscsi

1. Připojení k virtuálnímu počítači
1. `sudo lsscsi`

První uvedený sloupec bude obsahovat logickou jednotku (LUN), formát je [Host: Channel: target:**lun**].

### <a name="listing-block-devices"></a>Výpis blokovaných zařízení

1. Připojení k virtuálnímu počítači
1. `sudo ls -l /sys/block/*/device`

Poslední uvedený sloupec bude obsahovat logickou jednotku (LUN), formát je [hostitel: kanál: cíl:**lun**].

## <a name="finding-the-lun-for-the-azure-disks"></a>Vyhledání logické jednotky (LUN) pro disky Azure

Logickou jednotku (LUN) pro disk Azure můžete najít pomocí Azure Portal Azure CLI.

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>Nalezení logické jednotky disku Azure v Azure Portal

1. V Azure Portal vyberte "Virtual Machines", chcete-li zobrazit seznam Virtual Machines
1. Vybrat virtuální počítač
1. Vybrat disky
1. Vyberte datový disk ze seznamu připojených disků.
1. Logická jednotka disku se zobrazí v podokně Podrobnosti o disku. Logická jednotka zobrazená tady koreluje s logickými jednotkami, které jste vyhledali v hostu pomocí lsscsi, nebo v seznamu blokovaných zařízení.

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>Vyhledání logické jednotky disku Azure pomocí rozhraní příkazového řádku Azure

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```

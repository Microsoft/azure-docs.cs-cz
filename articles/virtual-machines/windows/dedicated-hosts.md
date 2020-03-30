---
title: Přehled vyhrazených hostitelů Azure pro virtuální počítače
description: Přečtěte si další informace o tom, jak lze vyhrazené hostitele Azure použít k nasazení virtuálních počítačů.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 39d1c0ddb4961800e889346ec110ca629ae73546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251558"
---
# <a name="azure-dedicated-hosts"></a>Vyhrazení hostitelé Azure

Azure Dedicated Host je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo více virtuálních počítačů – vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery používané v našich datových centrech, které jsou poskytovány jako zdroj. V rámci oblasti, zóny dostupnosti a domény selhání můžete zřídit vyhrazená hostitelé. Potom můžete virtuální počítače umístit přímo do zřízených hostitelů v jakékoli konfiguraci, která nejlépe vyhovuje vašim potřebám.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Další kroky

- Vyhrazeného hostitele můžete nasadit pomocí [Azure PowerShellu](dedicated-hosts-powershell.md), [portálu](dedicated-hosts-portal.md)a [azure CLI](../linux/dedicated-hosts-cli.md).

- Zde je [nalezena](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)ukázková šablona , která používá zóny i domény selhání pro maximální odolnost proti chybám v oblasti.

- Můžete také ušetřit na nákladech s [rezervovanou instanci vyhrazených hostitelů Azure](../prepay-dedicated-hosts-reserved-instances.md).

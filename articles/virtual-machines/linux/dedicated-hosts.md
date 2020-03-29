---
title: Přehled vyhrazených hostitelů Azure pro virtuální počítače
description: Přečtěte si další informace o tom, jak lze vyhrazené hostitele Azure použít k nasazení virtuálních počítačů.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970729"
---
# <a name="azure-dedicated-hosts"></a>Vyhrazení hostitelé Azure

Azure Dedicated Host je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo více virtuálních počítačů – vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery používané v našich datových centrech, které jsou poskytovány jako zdroj. V rámci oblasti, zóny dostupnosti a domény selhání můžete zřídit vyhrazená hostitelé. Potom můžete virtuální počítače umístit přímo do zřízených hostitelů v jakékoli konfiguraci, která nejlépe vyhovuje vašim potřebám.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Další kroky

- Vyhrazeného hostitele můžete nasadit pomocí [azure cli](dedicated-hosts-cli.md), [portálu](dedicated-hosts-portal.md)a [prostředí PowerShell](../windows/dedicated-hosts-powershell.md).

- Další informace naleznete v přehledu [vyhrazených hostitelů.](dedicated-hosts.md)

- Zde je [nalezena](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)ukázková šablona , která používá zóny i domény selhání pro maximální odolnost proti chybám v oblasti.

- Můžete také ušetřit na nákladech s [rezervovanou instanci vyhrazených hostitelů Azure](../prepay-dedicated-hosts-reserved-instances.md).

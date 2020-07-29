---
title: Přehled vyhrazených hostitelů Azure pro virtuální počítače
description: Přečtěte si další informace o tom, jak se můžou používat vyhrazené hostitele Azure pro nasazení virtuálních počítačů.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 327eebb4ea1804fc19c8f1b82a57dd0a9a994803
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284570"
---
# <a name="azure-dedicated-hosts"></a>Vyhrazení hostitelé Azure

Vyhrazený hostitel Azure je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo víc virtuálních počítačů, které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery, které se používají v našich datových centrech, které jsou poskytované jako prostředek. Můžete zřídit vyhrazené hostitele v rámci oblasti, zóny dostupnosti a domény selhání. Pak můžete virtuální počítače umístit přímo do zřízených hostitelů, v libovolné konfiguraci nejlépe vyhovuje vašim potřebám.


[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Další kroky

- Vyhrazeného hostitele můžete nasadit pomocí [Azure PowerShell](dedicated-hosts-powershell.md), [portálu](dedicated-hosts-portal.md)a [Azure CLI](../linux/dedicated-hosts-cli.md).

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

- Můžete také ušetřit náklady pomocí [rezervované instance vyhrazených hostitelů Azure](../prepay-dedicated-hosts-reserved-instances.md).

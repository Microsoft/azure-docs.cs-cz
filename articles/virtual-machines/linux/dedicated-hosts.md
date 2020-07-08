---
title: Přehled vyhrazených hostitelů Azure pro virtuální počítače
description: Přečtěte si další informace o tom, jak se můžou používat vyhrazené hostitele Azure pro nasazení virtuálních počítačů.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c19b3410e106aaf5fed53aba45d06eac6dd9d601
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "78970729"
---
# <a name="azure-dedicated-hosts"></a>Vyhrazení hostitelé Azure

Vyhrazený hostitel Azure je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo víc virtuálních počítačů, které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery, které se používají v našich datových centrech, které jsou poskytované jako prostředek. Můžete zřídit vyhrazené hostitele v rámci oblasti, zóny dostupnosti a domény selhání. Pak můžete virtuální počítače umístit přímo do zřízených hostitelů, v libovolné konfiguraci nejlépe vyhovuje vašim potřebám.



[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]


## <a name="next-steps"></a>Další kroky

- Vyhrazeného hostitele můžete nasadit pomocí [Azure CLI](dedicated-hosts-cli.md), [portálu](dedicated-hosts-portal.md)a [PowerShellu](../windows/dedicated-hosts-powershell.md).

- Další informace najdete v tématu Přehled [vyhrazených hostitelů](dedicated-hosts.md) .

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

- Můžete také ušetřit náklady pomocí [rezervované instance vyhrazených hostitelů Azure](../prepay-dedicated-hosts-reserved-instances.md).

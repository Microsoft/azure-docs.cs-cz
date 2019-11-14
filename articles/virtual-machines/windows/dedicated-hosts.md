---
title: Přehled vyhrazených hostitelů Azure pro virtuální počítače
description: Přečtěte si další informace o tom, jak se můžou používat vyhrazené hostitele Azure pro nasazení virtuálních počítačů.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: cfcc8aeb3d1cc1f199b19abc16cc54db416a307e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033380"
---
# <a name="preview-azure-dedicated-hosts"></a>Verze Preview: vyhrazené hostitele Azure

Vyhrazený hostitel Azure je služba, která poskytuje fyzické servery – schopné hostovat jeden nebo víc virtuálních počítačů, které jsou vyhrazené pro jedno předplatné Azure. Vyhrazení hostitelé jsou stejné fyzické servery, které se používají v našich datových centrech, které jsou poskytované jako prostředek. Můžete zřídit vyhrazené hostitele v rámci oblasti, zóny dostupnosti a domény selhání. Pak můžete virtuální počítače umístit přímo do zřízených hostitelů, v libovolné konfiguraci nejlépe vyhovuje vašim potřebám.

[!INCLUDE [virtual-machines-common-dedicated-hosts-preview](../../../includes/virtual-machines-common-dedicated-hosts-preview.md)]

[!INCLUDE [virtual-machines-common-dedicated-hosts](../../../includes/virtual-machines-common-dedicated-hosts.md)]

## <a name="next-steps"></a>Další kroky

- Vyhrazeného hostitele můžete nasadit pomocí [Azure PowerShell](dedicated-hosts-powershell.md), [portálu](dedicated-hosts-portal.md)a [Azure CLI](../linux/dedicated-hosts-cli.md).

- [Zde](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)najdete ukázkovou šablonu, která pro maximální odolnost v oblasti používá zóny i domény selhání.

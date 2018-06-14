---
title: Vytvoření plně kvalifikovaný název domény pro virtuální počítač s Windows v portálu Azure | Microsoft Docs
description: Naučte se vytvářet plně kvalifikovaný název domény, nebo plně kvalifikovaný název domény, pro správce prostředků na základě virtuálního počítače na portálu Azure.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8ebc1ef89b24a9aa21f39e5b05051c16351f08cd
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2017
ms.locfileid: "26707405"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>V portálu Azure vytvořit platný plně kvalifikovaný název domény pro virtuální počítač s Windows

Když vytvoříte virtuální počítač (VM) v [portál Azure](https://portal.azure.com), se automaticky vytvoří prostředek veřejné IP pro virtuální počítač. Tuto IP adresu použijete vzdálený přístup k virtuálnímu počítači. I když na portál nevytvoří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), nebo plně kvalifikovaný název domény, můžete vytvořit jeden po vytvoření virtuálního počítače. Tento článek ukazuje postup vytvoření název DNS nebo plně kvalifikovaný název domény.

## <a name="create-a-fqdn"></a>Vytvoření plně kvalifikovaný název domény
Tento článek předpokládá, že jste již vytvořili virtuální počítač. V případě potřeby můžete [vytvoření virtuálního počítače na portálu](quick-create-portal.md) nebo [s prostředím Azure PowerShell](quick-create-powershell.md). Po nastavení a spuštění virtuálního počítače, postupujte takto:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Teď můžete vzdáleně připojit k virtuálnímu počítači pomocí tento název DNS, jako pro protokol RDP (Remote Desktop).

## <a name="next-steps"></a>Další kroky
Nyní, když virtuální počítač veřejný název IP a DNS, můžete nasadit společné architektury aplikace nebo služby, například služby IIS, SQL nebo SharePoint.

Také další informace o [pomocí Resource Manager](../../azure-resource-manager/resource-group-overview.md) tipy k sestavení Azure nasazení.


---
title: Vytvoření plně kvalifikovaného názvu domény pro virtuální počítač s Windows na webu Azure Portal | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit plně kvalifikovaný název domény, nebo plně kvalifikovaný název domény, pro správce prostředků na základě virtuálního počítače na webu Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 885003863b8d5a5a81adc7f0310bbf2238edc68e
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059084"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Vytvořit použitím plně kvalifikovaného názvu domény pro virtuální počítač s Windows na webu Azure Portal

Při vytváření virtuálního počítače (VM) v [webu Azure portal](https://portal.azure.com), automaticky se vytvoří prostředek veřejné IP pro virtuální počítač. Tuto IP adresu použijete vzdálený přístup k virtuálnímu počítači. I když na portálu se nevytvoří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), nebo plně kvalifikovaný název domény, můžete si ho vytvořit po vytvoření virtuálního počítače. Tento článek ukazuje postup vytvoření názvu DNS nebo plně kvalifikovaný název domény.

## <a name="create-a-fqdn"></a>Vytvoření plně kvalifikovaný název domény
Tento článek předpokládá, že jste již vytvořili virtuální počítač. V případě potřeby můžete [vytvoření virtuálního počítače na portálu](quick-create-portal.md) nebo [pomocí Azure Powershellu](quick-create-powershell.md). Jakmile se váš virtuální počítač je v provozu, postupujte takto:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Teď můžete vzdáleně připojit k virtuálnímu počítači pomocí tento název DNS, jako pro protokol RDP (Remote Desktop).

## <a name="next-steps"></a>Další postup
Teď, když váš virtuální počítač má název veřejné IP adresy a DNS, můžete nasadit běžné systémy aplikací nebo služeb, jako je služba IIS, SQL nebo SharePoint.

Také další informace o [pomocí Resource Manageru](../../azure-resource-manager/resource-group-overview.md) tipy na vytváření nasazení Azure.


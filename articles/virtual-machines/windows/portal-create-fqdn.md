---
title: Vytvoření hlavního virtuálního dna pro virtuální počítač s Windows na webu Azure Portal
description: Zjistěte, jak vytvořit plně kvalifikovaný název domény neboli úplný název domény pro virtuální počítač založený na Správci prostředků na webu Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c7bfa510068d71afb7701ab8964f06053d38ac70
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458892"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Vytvoření plně kvalifikovaného názvu domény na webu Azure Portal pro virtuální počítač s Windows

Když na [webu Azure Portal](https://portal.azure.com)vytvoříte virtuální počítač (VM), automaticky se vytvoří veřejný IP prostředek pro virtuální počítač. Tuto IP adresu použijete ke vzdálenému přístupu k virtuálnímu virtuálnímu počítačům. Přestože portál nevytváří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)nebo plně kvalifikovaný název domény, můžete jej vytvořit po vytvoření virtuálního virtuálního soudu. Tento článek ukazuje kroky k vytvoření názvu DNS nebo fQDN.

## <a name="create-a-fqdn"></a>Vytvoření víceneživého nového než vinná na kvo
Tento článek předpokládá, že jste již vytvořili virtuální ho. V případě potřeby můžete [vytvořit virtuální počítač na portálu](quick-create-portal.md) nebo pomocí Azure [PowerShellu](quick-create-powershell.md). Po spuštění virtuálního počítače postupujte takto:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Nyní se můžete vzdáleně připojit k virtuálnímu počítači pomocí tohoto názvu DNS, například pro protokol RDP (RdP).

## <a name="next-steps"></a>Další kroky
Teď, když má váš virtuální počítač veřejný název IP a DNS, můžete nasadit běžné aplikační architektury nebo služby, jako je Služba IIS, SQL nebo SharePoint.

Další informace o [používání Správce prostředků](../../azure-resource-manager/management/overview.md) pro tipy na vytváření nasazení Azure.


---
title: Vytvoření hlavního názevového příkazu pro virtuální počítač s Linuxem na webu Azure Portal
description: Zjistěte, jak vytvořit plně kvalifikovaný název domény neboli úplný název domény pro virtuální počítač založený na Správci prostředků na webu Azure Portal.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: caf7c1c9c0704de1408a322f581019eb74443365
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461493"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Vytvoření plně kvalifikovaného názvu domény na webu Azure portal pro virtuální počítač s Linuxem

Když na [webu Azure Portal](https://portal.azure.com)vytvoříte virtuální počítač (VM), automaticky se vytvoří veřejný IP prostředek pro virtuální počítač. Tuto IP adresu použijete ke vzdálenému přístupu k virtuálnímu virtuálnímu počítačům. Přestože portál nevytvoří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)nebo plně kvalifikovaný název domény, můžete jej přidat po vytvoření virtuálního virtuálního soudu. Tento článek ukazuje kroky k vytvoření názvu DNS nebo fQDN.

## <a name="create-a-fqdn"></a>Vytvoření víceneživého nového než vinná na kvo
Tento článek předpokládá, že jste již vytvořili virtuální ho. V případě potřeby můžete [vytvořit virtuální počítač na portálu](quick-create-portal.md) nebo pomocí azure [cli](quick-create-cli.md). Po spuštění virtuálního počítače postupujte takto:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Nyní se můžete vzdáleně připojit k virtuálnímu virtuálnímu virtuálnímu serveru pomocí tohoto názvu DNS, například pomocí aplikace `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Další kroky
Teď, když váš virtuální počítač má veřejný NÁZEV IP a DNS, můžete nasadit běžné aplikační architektury nebo služby, jako je nginx, MongoDB, Docker atd.

Další informace o [používání Správce prostředků](../../azure-resource-manager/management/overview.md) pro tipy na vytváření nasazení Azure.


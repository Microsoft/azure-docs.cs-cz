---
title: Vytvoření plně kvalifikovaného názvu domény pro virtuální počítač se systémem Linux v Azure Portal
description: Naučte se vytvořit plně kvalifikovaný název domény nebo plně kvalifikovaný název domény pro virtuální počítač založený na Správce prostředků v Azure Portal.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2cd6c249-a737-4a0a-b5ba-e1c09e551b30
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 256e1e60ec8f50df2faea64f31e88d00370b33c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458715"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Vytvoření plně kvalifikovaného názvu domény v Azure Portal pro virtuální počítač se systémem Linux

Když v [Azure Portal](https://portal.azure.com)vytvoříte virtuální počítač, automaticky se vytvoří prostředek veřejné IP adresy pro virtuální počítač. Tuto IP adresu použijete pro vzdálený přístup k virtuálnímu počítači. I když portál nevytváří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)nebo plně kvalifikovaný název domény, můžete ho přidat po vytvoření virtuálního počítače. Tento článek popisuje postup vytvoření názvu DNS nebo plně kvalifikovaného názvu domény.

## <a name="create-a-fqdn"></a>Vytvoření plně kvalifikovaného názvu domény
V tomto článku se předpokládá, že jste už vytvořili virtuální počítač. V případě potřeby můžete [vytvořit virtuální počítač na portálu](quick-create-portal.md) nebo [pomocí rozhraní příkazového řádku Azure CLI](quick-create-cli.md). Když je virtuální počítač v provozu a spuštěný, postupujte podle těchto kroků:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Nyní se můžete vzdáleně připojit k virtuálnímu počítači pomocí tohoto názvu DNS, například pomocí `ssh azureuser@mydns.westus.cloudapp.azure.com`.

## <a name="next-steps"></a>Další kroky
Když teď váš virtuální počítač má veřejnou IP adresu a název DNS, můžete nasadit společné aplikační architektury nebo služby, jako je Nginx, MongoDB, Docker atd.

Můžete si také přečíst další informace o [použití Správce prostředků](../../azure-resource-manager/management/overview.md) pro tipy k vytváření nasazení Azure.


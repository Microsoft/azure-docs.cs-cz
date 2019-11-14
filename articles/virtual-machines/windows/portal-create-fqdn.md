---
title: Vytvoření plně kvalifikovaného názvu domény pro virtuální počítač s Windows v Azure Portal
description: Naučte se vytvořit plně kvalifikovaný název domény nebo plně kvalifikovaný název domény pro virtuální počítač založený na Správce prostředků v Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a2ae5887-76df-485e-ae19-0efd96df8600
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 727636b4b59bf4b31fd7592b1a0f2d52d5dbbfd3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032994"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-windows-vm"></a>Vytvoření plně kvalifikovaného názvu domény v Azure Portal pro virtuální počítač s Windows

Když v [Azure Portal](https://portal.azure.com)vytvoříte virtuální počítač, automaticky se vytvoří prostředek veřejné IP adresy pro virtuální počítač. Tuto IP adresu použijete pro vzdálený přístup k virtuálnímu počítači. I když portál nevytváří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)nebo plně kvalifikovaný název domény, můžete ho vytvořit, až se virtuální počítač vytvoří. Tento článek popisuje postup vytvoření názvu DNS nebo plně kvalifikovaného názvu domény.

## <a name="create-a-fqdn"></a>Vytvoření plně kvalifikovaného názvu domény
V tomto článku se předpokládá, že jste už vytvořili virtuální počítač. V případě potřeby můžete [vytvořit virtuální počítač na portálu](quick-create-portal.md) nebo [pomocí Azure PowerShell](quick-create-powershell.md). Když je virtuální počítač v provozu a spuštěný, postupujte podle těchto kroků:

[!INCLUDE [virtual-machines-common-portal-create-fqdn](../../../includes/virtual-machines-common-portal-create-fqdn.md)]

Nyní se můžete vzdáleně připojit k virtuálnímu počítači pomocí tohoto názvu DNS, například pro protokol RDP (Remote Desktop Protocol) (RDP).

## <a name="next-steps"></a>Další kroky
Když teď váš virtuální počítač má veřejnou IP adresu a název DNS, můžete nasadit společné aplikační architektury nebo služby, jako je IIS, SQL nebo SharePoint.

Můžete si také přečíst další informace o [použití Správce prostředků](../../azure-resource-manager/resource-group-overview.md) pro tipy k vytváření nasazení Azure.


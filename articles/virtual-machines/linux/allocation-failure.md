---
title: "Řešení potíží s přidělováním virtuálního počítače s Linuxem | Microsoft Docs"
description: "Řešení potíží s přidělením při vytváření, restartování nebo změně velikosti virtuálního počítače s Linuxem v Azure"
services: virtual-machines-linux, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resourece-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 626968c463d76abe6becaa85813336567f108d0d
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-linux-vms-in-azure"></a>Řešení potíží s přidělením při vytváření, restartování nebo změně velikosti virtuálních počítačů Linux v Azure
Při vytvoření virtuálního počítače, restartujte zastaveném (deallocated) virtuálních počítačů nebo změnit velikost virtuálního počítače Microsoft Azure přiřadí výpočetní prostředky do vašeho předplatného. Příležitostně zobrazí chyby při provádění těchto operací – ještě před dosažením limity předplatného Azure. Tento článek vysvětluje příčiny některých běžných chyb přidělení a navrhne možné nápravu. Informace mohou být užitečné také při plánování nasazení vašich služeb. Můžete také [řešení potíží s přidělením při vytváření, restartování nebo změně velikosti virtuálních počítačů Windows v Azure](../windows/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]


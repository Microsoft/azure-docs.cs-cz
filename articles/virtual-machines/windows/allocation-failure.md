---
title: "Řešení potíží s přidělováním virtuální počítač s Windows | Microsoft Docs"
description: "Řešení potíží s přidělením při vytváření, restartování nebo změně velikosti virtuální počítač s Windows v Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: bb939e23-77fc-4948-96f7-5037761c30e8
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2016
ms.author: cjiang
ms.openlocfilehash: 2fd99f47cc2051c5b27c3ec8621ae954e9f8ca14
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-windows-vms-in-azure"></a>Řešení potíží s přidělením při vytváření, restartování nebo změně velikosti virtuálních počítačů Windows v Azure
Při vytvoření virtuálního počítače, restartujte zastaveném (deallocated) virtuálních počítačů nebo změnit velikost virtuálního počítače Microsoft Azure přiřadí výpočetní prostředky do vašeho předplatného. Příležitostně zobrazí chyby při provádění těchto operací – ještě před dosažením limity předplatného Azure. Tento článek vysvětluje příčiny některých běžných chyb přidělení a navrhne možné nápravu. Informace mohou být užitečné také při plánování nasazení vašich služeb. Můžete také [řešení potíží s přidělením při vytváření, restartování nebo změně velikosti virtuálních počítačů Linux v Azure](../linux/allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [virtual-machines-common-allocation-failure](../../../includes/virtual-machines-common-allocation-failure.md)]


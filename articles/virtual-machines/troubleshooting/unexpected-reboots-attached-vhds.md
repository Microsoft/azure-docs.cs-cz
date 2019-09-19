---
title: Řešení neočekávaných restartování virtuálních počítačů s připojenými virtuálními pevnými disky na virtuálních počítačích Azure | Microsoft Docs
description: Řešení potíží s neočekávaným restartováním virtuálních počítačů.
keywords: odmítnuté připojení SSH, chyba SSH, Azure SSH, připojení SSH selhalo.
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d3b54941d38424e71ac800d2e750ac9bbc96cde9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086868"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Řešení neočekávaných restartování virtuálních počítačů s připojenými virtuálními pevnými disky

Pokud má virtuální počítač Azure velký počet připojených virtuálních pevných disků, které jsou ve stejném účtu úložiště, můžete překročit cíle škálovatelnosti pro jednotlivé účty úložiště, což způsobí neočekávané restartování virtuálního počítače. Podívejte se na minuty pro účet úložiště (**TotalRequests**/**totalbillablerequests**/**TotalEgress**) pro špičky, které překračují cíle škálovatelnosti pro účet úložiště. V tématu [metriky](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) se dozvíte, jak na základě vašeho účtu úložiště získat pomoc při určování, jestli došlo k omezení PercentThrottlingError.

Obecně platí, že každá jednotlivá operace vstupu a výstupu na virtuálním pevném disku z virtuálního počítače překládá operace **získání stránky** nebo **vložení operací stránkování** na podkladový objekt blob stránky. Proto můžete použít odhadované IOPS pro vaše prostředí, abyste nastavili počet virtuálních pevných disků, které můžete mít v jednom účtu úložiště na základě konkrétního chování vaší aplikace. Microsoft doporučuje mít v jednom účtu úložiště 40 nebo méně disků. Podrobnosti o cílech škálovatelnosti pro účty úložiště najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](../../storage/common/storage-scalability-targets.md) , zejména celkové míry požadavků a celkové šířce pásma pro typ účtu úložiště, který používáte.

Pokud překročíte cíle škálovatelnosti svého účtu úložiště, můžete své virtuální pevné disky umístit do několika účtů úložiště a snížit tak aktivitu v každém jednotlivém účtu.

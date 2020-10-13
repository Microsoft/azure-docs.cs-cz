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
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75358522"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Řešení neočekávaných restartování virtuálních počítačů s připojenými virtuálními pevnými disky

Pokud má virtuální počítač Azure velký počet připojených virtuálních pevných disků, které jsou ve stejném účtu úložiště, můžete překročit cíle škálovatelnosti pro jednotlivé účty úložiště, což způsobí neočekávané restartování virtuálního počítače. Podívejte se na minuty pro účet úložiště (**TotalRequests** / **totalbillablerequests** / **TotalEgress**) pro špičky, které překračují cíle škálovatelnosti pro účet úložiště. V tématu [metriky](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) se dozvíte, jak na základě vašeho účtu úložiště získat pomoc při určování, jestli došlo k omezení PercentThrottlingError.

Obecně platí, že každá jednotlivá operace vstupu a výstupu na virtuálním pevném disku z virtuálního počítače překládá operace **získání stránky** nebo **vložení operací stránkování** na podkladový objekt blob stránky. Proto můžete použít odhadované IOPS pro vaše prostředí, abyste nastavili počet virtuálních pevných disků, které můžete mít v jednom účtu úložiště na základě konkrétního chování vaší aplikace. Microsoft doporučuje mít v jednom účtu úložiště 40 nebo méně disků. Další informace o cílech škálovatelnosti pro účty úložiště úrovně Standard najdete v tématu [cíle škálovatelnosti pro účty úložiště úrovně Standard](../../storage/common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro účty úložiště blob stránky úrovně Premium najdete v tématu [cíle škálovatelnosti pro účty úložiště objektů blob stránky úrovně Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Pokud překročíte cíle škálovatelnosti svého účtu úložiště, můžete své virtuální pevné disky umístit do několika účtů úložiště a snížit tak aktivitu v každém jednotlivém účtu.

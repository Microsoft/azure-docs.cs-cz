---
title: Řešení potíží s neočekávané restartování virtuálních počítačů s připojené virtuální pevné disky na virtuálních počítačích Windows Azure | Microsoft Docs
description: Řešení potíží s neočekávané restartování virtuálních počítačů Windows.
keywords: SSH připojení odmítnuto, ssh chyby, azure ssh, připojení SSH se nezdařilo
services: virtual-machines-windows
author: tamram
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/01/2018
ms.author: tamram
ms.openlocfilehash: 2057ffe2d5ec61e1192ff12291114c43dc2cc049
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Řešení potíží s neočekávané restartování virtuálních počítačů s připojených virtuálních pevných disků

Pokud virtuální počítač Azure (VM) má velký počet připojených virtuálních pevných disků, které jsou ve stejném účtu úložiště, může překročit cíle škálovatelnosti pro účet úložiště jednotlivých způsobuje neočekávaně restartovat virtuální počítač. Zkontrolujte minutu metriky pro účet úložiště (**TotalRequests**/**TotalIngress**/**TotalEgress**) špiček, které překračují cíle škálovatelnosti účtu úložiště. V tématu [metriky způsobit nárůst PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) pro pomoc při určení, zda omezení došlo k na vašem účtu úložiště.

Obecně platí, překládá každé jednotlivé vstupní nebo výstupní operace na virtuální pevný disk z virtuálního počítače do **získat stránky** nebo **Put stránky** operací na základní objekt blob stránky. Proto můžete odhadované IOPS pro vaše prostředí pro optimalizaci kolik virtuální pevné disky vám může mít v jednom úložném účtu na základě konkrétní chování vaší aplikace. Společnost Microsoft doporučuje mít 40 nebo méně disků v jednom úložném účtu. V tématu [a cíle výkonnosti služby Azure Storage Scalability](../../storage/common/storage-scalability-targets.md) podrobnosti o cíle škálovatelnosti pro účty úložiště, zejména rychlost celkový počet požadavků a celková šířka pásma pro typ účtu úložiště používáte.

Pokud jsou překročení cíle škálovatelnosti účtu úložiště, umístíte virtuální pevné disky ve více účtů úložiště ke snížení aktivity v jednotlivé účty.

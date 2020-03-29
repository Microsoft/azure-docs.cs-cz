---
title: Poradce při potížích s neočekávanými restartováními virtuálních počítačí s připojenými virtuálními počítači na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Jak řešit neočekávané restartování virtuálních počítačů.
keywords: Ssh připojení odmítnuto, ssh error, azure ssh, SSH připojení se nezdařilo
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 3a06db1afd130d936af868d0d20632c3ec4fbfd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75358522"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Poradce při potížích s neočekávanými restartováními virtuálních počítačů s připojenými virtuálními počítači

Pokud virtuální počítač Azure (VM) má velký počet připojených virtuálních disponiál, které jsou ve stejném účtu úložiště, můžete překročit cíle škálovatelnosti pro jednotlivé účty úložiště, což způsobí, že virtuální počítač neočekávaně restartovat. Zkontrolujte metriky minut pro účet úložiště **(TotalRequests**/**TotalIngress**/**TotalEgress**) pro špičky, které překračují cíle škálovatelnosti pro účet úložiště. Viz [Metriky zobrazit zvýšení PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) pro pomoc při určování, zda došlo k omezení na vašem účtu úložiště.

Obecně platí, že každá jednotlivá operace vstupu nebo výstupu na virtuálním počítači z virtuálního počítače překládá na **operace Získat stránku** nebo **Umístit stránku** na podkladovém objektu blob stránky. Proto můžete použít odhadované viposlužby pro vaše prostředí vyladit, kolik Virtuální počítače můžete mít v jednom účtu úložiště na základě konkrétní chování vaší aplikace. Společnost Microsoft doporučuje mít 40 nebo méně disků v jednom účtu úložiště. Další informace o cílech škálovatelnosti pro účty standardního úložiště naleznete [v tématu Škálovatelnost cíle pro účty standardníúložiště](../../storage/common/scalability-targets-standard-account.md). Další informace o cílech škálovatelnosti pro účty úložiště objektů blob stránky premium najdete v tématu [Škálovatelnost cíle pro účty úložiště objektů blob stránky premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

Pokud překračujete cíle škálovatelnosti pro váš účet úložiště, umístěte virtuální počítače do více účtů úložiště, abyste snížili aktivitu v jednotlivých účtech.

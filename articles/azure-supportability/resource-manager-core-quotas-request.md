---
title: Azure Resource Manager žádosti o zvýšení kvóty vCPU | Microsoft Docs
description: Azure Resource Manager žádosti o zvýšení kvóty vCPU
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c3248b10b4ad343e8776056d42ec153130f0061f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248659"
---
# <a name="quota-increase-requests"></a>Žádosti o navýšení kvóty

Správce prostředků kvóty vCPU pro virtuální počítače a sady škálování virtuálních počítačů se vynutily na dvou úrovních každého předplatného v každé oblasti. 

První úroveň je celkový regionální limit vCPU (v rámci všech řad virtuálních počítačů) a druhá úroveň je limit vCPU na řadu virtuálních počítačů (například vCPU řady D-Series). Při každém nasazení nového virtuálního počítače nesmí součet nového a stávajícího vCPU využití pro tuto řadu virtuálních počítačů překročit kvótu vCPU schválenou pro tuto řadu virtuálních počítačů. Celkový počet nových a existujících vCPUů nasazených ve všech řadách virtuálních počítačů by neměl přesáhnout celkovou kvótu vCPU schválenou pro předplatné. Pokud dojde k překročení některé z těchto kvót, nasazení virtuálního počítače se nepovolí.
Můžete požádat o zvýšení limitu kvóty vCPU pro řady virtuálních počítačů z Azure Portal. Zvýšení kvóty řady virtuálních počítačů automaticky zvýší celkový rozsah regionálního Vcpuu o stejné množství. 

Při vytvoření nového předplatného se nemusí výchozí celková oblast vCPU rovnat součtu výchozích kvót vCPU pro všechny jednotlivé řady virtuálních počítačů. To může mít za následek předplatné s dostatečnou kvótou pro každou jednotlivou řadu virtuálních počítačů, kterou chcete nasadit, ale ne dostatečnou kvótu pro celkový počet regionálních vCPU pro všechna nasazení. V takovém případě budete muset odeslat žádost o explicitní zvýšení celkového počtu místních vCPU. Celkový počet regionálních vCPU nesmí překročit součet schválené kvóty v rámci všech řad virtuálních počítačů v dané oblasti.

Přečtěte si další informace o kvótách na stránce [vCPU kvóty virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) a [omezení pro předplatné a službu Azure](https://aka.ms/quotalimits) . 


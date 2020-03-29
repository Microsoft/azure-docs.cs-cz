---
title: Požadavky na zvýšení kvóty virtuálních procesorů Azure Resource Manager
description: Požadavky na zvýšení kvóty virtuálních procesorů Azure Resource Manager
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: cdbf7364a275eb246615f398044456645a96d1a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843663"
---
# <a name="quota-increase-requests"></a>Žádosti o navýšení kvóty

Kvóty virtuálních procesorů správce prostředků pro virtuální počítače a škálovací sady virtuálních počítačů jsou vynuceny na dvou úrovních pro každé předplatné v každé oblasti.

První vrstva je limit celkových regionálních virtuálních procesorů napříč všemi řadami virtuálních montovny. Druhá vrstva je limit virtuálních procesorů řady v vm pro všechny, jako jsou virtuální procesory řady D. Kdykoli se má nasadit nový virtuální počítač, nesmí součet nových a existujících virtuálních procesorů pro tuto řadu virtuálních počítačů překročit kvótu virtuálního procesoru schválenou pro tuto konkrétní řadu virtuálních počítačů. Celkový počet nových a existujících virtuálních procesorů nasazených ve všech řadách virtuálních zařízení by navíc neměl překročit celkovou kvótu místních virtuálních procesorů schválenou pro předplatné. Pokud je překročena některá z těchto kvót, nasazení virtuálního počítače nebude povoleno.
Můžete požádat o zvýšení limitu kvóty virtuálních procesorů pro řadu virtuálních počítačích z webu Azure Portal. Zvýšení kvóty řady virtuálních měn automaticky zvýší limit celkových regionálních virtuálních procesorů o stejnou částku.

Při vytvoření nového předplatného se výchozí místní virtuální procesory Total nemusí rovnat součtu výchozích kvót virtuálních procesorů pro všechny jednotlivé řady virtuálních počítače. Tato skutečnost může mít za následek předplatné s dostatečnou kvótou pro každou řadu jednotlivých virtuálních aplikací, které chcete nasadit. Může postrádat dostatečnou kvótu pro celkové místní virtuální procesory pro všechna nasazení. V takovém případě budete muset odeslat žádost o zvýšení limitu celkových místních virtuálních procesorů explicitně. Celkový limit místních virtuálních procesorů nesmí překročit součet schválené kvóty napříč všemi řadami virtuálních montovny pro oblast.

Další informace o kvótách najdete [v tématu Kvóty virtuálních procesorů virtuálních procesorů virtuálních](../../virtual-machines/windows/quotas.md) [procesorů virtuálních procesorů virtuálních zařízení a omezení předplatného a služeb Azure, kvóty a omezení](../../azure-resource-manager/management/azure-subscription-service-limits.md).


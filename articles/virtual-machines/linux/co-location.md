---
title: Společné umístění virtuálních počítačů Azure pro Linux | Microsoft Docs
description: Přečtěte si, jak se ve společném umístění prostředků virtuálních počítačů Azure dá vylepšit latence.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/28/2019
ms.author: cynthn
ms.openlocfilehash: d6d8986117bd2899ea0de0aa6490954aef6c3a56
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850337"
---
# <a name="co-locate-resources-for-improved-latency"></a>Společné umístění prostředků pro lepší latenci

Při nasazování aplikace v Azure vytvoří rozšíření instancí napříč oblastmi nebo zónami dostupnosti latenci sítě, což může mít vliv na celkový výkon vaší aplikace. 

## <a name="preview-proximity-placement-groups"></a>Verze Preview: Skupiny umístění blízkosti

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Další postup

Nasaďte virtuální počítač do [skupiny umístění blízkosti](proximity-placement-groups.md) pomocí Azure CLI.


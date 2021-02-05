---
title: Rezidence dat
description: Popisuje data zasídlí při použití vzdáleného vykreslování Azure.
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576877"
---
# <a name="azure-remote-rendering-data-residency"></a>Zasídlí dat vzdáleného vykreslování Azure 
Tento článek vysvětluje koncept zasídlí dat a způsob, jakým se vztahuje na vzdálené vykreslování Azure. 

## <a name="data-residency"></a>Rezidence dat 
Vzdálené vykreslování Azure používá pro úložiště modelu uživatelem zadané kontejnery Azure Blob. V případě, že se model nepoužívá, zůstane v oblasti Azure Blob Storage, kterou uživatel zadal. Když se model používá pro vykreslování, data se zkopírují do oblasti, kterou uživatel zvolí při spuštění relace vykreslování.

## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět, jak nastavit kontejner Azure Blob Storage pro vzdálené vykreslování Azure, přečtěte si článek [Převod modelu pro vykreslování](../quickstarts/convert-model.md).

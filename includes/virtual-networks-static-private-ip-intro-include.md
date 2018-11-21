---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 5c1caf87f32ddd827b85263ee634d3e15d821124
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52269657"
---
IaaS virtual machines (VM) a instancí rolí PaaS ve virtuální síti automaticky zobrazí privátní IP adresu z rozsahu, který určíte, založené na podsíť, ve které jsou připojené k. Tuto adresu se uchovávají virtuální počítače a instance rolí, dokud jsou vyřazeny z provozu. Můžete vyřadit z provozu virtuálním počítači nebo instanci role zastavením z prostředí PowerShell, rozhraní příkazového řádku Azure nebo na webu Azure portal. V těchto případech se po spuštění instance virtuálního počítače nebo role, je obdrží dostupnou IP adresu z infrastruktury Azure, který nemusí být stejné, který byl dříve. Pokud vypnutí virtuálním počítači nebo instanci role z hostovaného operačního systému zachová IP adresu, kterou měl.  

V některých případech chcete instanci virtuálního počítače nebo role má statickou IP adresu, například pokud váš virtuální počítač spustit DNS nebo bude řadič domény. Můžete to provést nastavením statické privátní IP adresu.


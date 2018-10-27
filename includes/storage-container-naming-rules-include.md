---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeca3550b5fc58694779e7e54ce6ca547ba30e17
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165994"
---
Každý objekt blob v úložišti Azure se musí nacházet v kontejneru. Kontejner je součástí názvu objektu blob. Třeba `mycontainer` je název kontejneru v těchto identifikátorech URI ukázkových objektů blob:

    https://storagesample.blob.core.windows.net/mycontainer/blob1.txt
    https://storagesample.blob.core.windows.net/mycontainer/photos/myphoto.jpg

Název kontejneru musí být platný název DNS, který odpovídá následujícím pravidlům pro pojmenování:

1. Názvy kontejnerů musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a pomlčky (-).
2. Bezprostředně před a za každým znakem pomlčky (-) se musí nacházet písmeno nebo číslice. Názvy kontejnerů nesmí obsahovat po sobě jdoucí pomlčky.
3. Název kontejneru musí být psaný malými písmeny.
4. Názvy kontejnerů musí mít délku 3 až 63 znaků.

> [!IMPORTANT]
> Upozorňujeme, že název kontejneru musí být psaný malými písmeny. Pokud v názvu kontejneru napíšete velké písmeno nebo jinak porušíte pravidla po pojmenování kontejnerů, může se zobrazit chyba 400 (Chybný požadavek). 
> 
> 


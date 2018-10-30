---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226821"
---
V tomto kroku vytvoříte pravidlo brány firewall k otevření portu sondy pro koncový bod s vyrovnáváním zatížení (59999, jak je uvedeno výše) a jiné pravidlo otevřete port naslouchacího procesu skupiny dostupnosti. Protože jste vytvořili koncový bod s vyrovnáváním zatížení na virtuálních počítačích, které obsahují replik skupin dostupnosti, budete muset otevřít port testu a port naslouchacího procesu na příslušných virtuálních počítačích.

1. Na virtuálních počítačích, které jsou hostiteli repliky spusťte **brány Windows Firewall s pokročilým zabezpečením**.

2. Klikněte pravým tlačítkem na **příchozí pravidla**a potom klikněte na tlačítko **nové pravidlo**.

3. Na **typ pravidla** stránce **Port**a potom klikněte na tlačítko **Další**.

4. Na **protokol a porty** stránce **TCP**, typ **59999** v **určité místní porty** pole a potom klikněte na tlačítko  **Další**.

5. Na **akce** stránce, zachovat **povolit připojení** vybrána a potom klikněte na tlačítko **Další**.

6. Na **profilu** stránce, přijměte výchozí nastavení a potom klikněte na tlačítko **Další**.

7. Na **název** stránku, **název** textové pole, zadejte název pravidla, například **vždy na Port naslouchacího procesu pro zjišťování**a potom klikněte na **Dokončit**.

8. Zopakujte předchozí kroky pro port naslouchacího procesu skupiny dostupnosti (jak je uvedeno výše v parametru $EndpointPort skript) a potom zadejte název příslušné pravidlo, jako například **vždy na Port naslouchacího procesu**.


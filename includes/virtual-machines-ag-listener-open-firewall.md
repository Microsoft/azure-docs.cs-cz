---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174953"
---
V tomto kroku vytvoříte pravidlo brány firewall pro otevření portu sondy pro koncový bod s vyrovnáváním zatížení (59999, jak bylo uvedeno výše) a jiné pravidlo pro otevření portu posluchače skupiny dostupnosti. Vzhledem k tomu, že jste vytvořili koncový bod s vyrovnáváním zatížení na virtuálních počítačích, které obsahují repliky skupiny dostupnosti, je třeba otevřít port sondy a port naslouchací proces na příslušných virtuálních počítačích.

1. Na virtuálních počítačích, které jsou hostiteli replik, spusťte **bránu Windows Firewall s pokročilým zabezpečením**.

2. Klepněte pravým tlačítkem myši **na položku Příchozí pravidla**a potom klepněte na příkaz Nové **pravidlo**.

3. Na stránce **Typ pravidla** vyberte **Port**a potom klepněte na tlačítko **Další**.

4. Na stránce **Protokol a porty** vyberte **tcp**, zadejte do pole **Specifické místní porty** položku **59999** a klepněte na tlačítko **Další**.

5. Na stránce **Akce** ponechte **vybrané připojení povolit** a klepněte na tlačítko **Další**.

6. Na stránce **Profil** přijměte výchozí nastavení a klepněte na tlačítko **Další**.

7. Na stránce **Název** zadejte do textového pole **Název** název pravidla, **například Vždy na probe probe posluchače**, a klepněte na tlačítko **Dokončit**.

8. Opakujte předchozí kroky pro port posluchače skupiny dostupnosti (jak je uvedeno dříve v parametru $EndpointPort skriptu) a zadejte příslušný název pravidla, **například Vždy na bloku posluchače**.


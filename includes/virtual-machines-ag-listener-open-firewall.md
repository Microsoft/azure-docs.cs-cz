---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: e128f3c67a41322d9c25a8d6941e937729760bf4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67174953"
---
V tomto kroku vytvoříte pravidlo brány firewall pro otevření portu sondy pro koncový bod s vyrovnáváním zatížení (59999, jak je uvedeno výše), a další pravidlo pro otevření portu naslouchacího procesu skupiny dostupnosti. Vzhledem k tomu, že jste vytvořili koncový bod s vyrovnáváním zatížení na virtuálních počítačích, které obsahují repliky skupin dostupnosti, je nutné otevřít port testu paměti a port naslouchacího procesu na příslušných virtuálních počítačích.

1. Na virtuálních počítačích, které hostují repliky, spusťte **bránu Windows Firewall s pokročilým zabezpečením**.

2. Klikněte pravým tlačítkem na **příchozí pravidla**a potom klikněte na **nové pravidlo**.

3. Na stránce **Typ pravidla** vyberte **port**a pak klikněte na **Další**.

4. Na stránce **protokol a porty** vyberte **TCP**, do pole **konkrétní místní porty** zadejte **59999** a potom klikněte na **Další**.

5. Na stránce **Akce** ponechte vybrané **připojení** a pak klikněte na **Další**.

6. Na stránce **profil** přijměte výchozí nastavení a potom klikněte na tlačítko **Další**.

7. Na stránce **název** zadejte do textového pole **název** název pravidla, například **port testu vždy na naslouchací proces**, a potom klikněte na tlačítko **Dokončit**.

8. Opakujte předchozí kroky pro port naslouchacího procesu skupiny dostupnosti (jak je uvedeno výše v parametru $EndpointPort skriptu) a pak zadejte vhodný název pravidla, například **port naslouchacího procesu Always On**.


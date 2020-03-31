---
title: Vytvoření skupin IP v azure firewall
description: Skupiny IP umožňují seskupit a spravovat IP adresy pro pravidla Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444534"
---
# <a name="create-ip-groups-preview"></a>Vytvořit skupiny IP (náhled)

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Skupiny IP umožňují seskupit a spravovat IP adresy pro pravidla Azure Firewall. Mohou mít jednu IP adresu, více IP adres nebo jeden nebo více rozsahů IP adres.

## <a name="create-an-ip-group"></a>Vytvoření skupiny IP

1. Na domovské stránce portálu Azure vyberte **Vytvořit prostředek**.
2. Do vyhledávacího textového pole zadejte **skupiny IP** a vyberte **položku Skupiny IP**.
3. Vyberte **Vytvořit**.
4. Vyberte své předplatné.
5. Vyberte skupinu prostředků nebo vytvořte novou.
6. Zadejte jedinečný název skupiny IP a vyberte oblast.

6. Vyberte **další: IP adresy**.
7. Zadejte adresu IP, více adres IP nebo rozsahy IP adres.

   Ip adresy lze zadat dvěma způsoby:
   - Můžete je zadat ručně
   - Můžete je importovat ze souboru

   Chcete-li importovat ze souboru, vyberte **Importovat ze souboru**. Soubor můžete přetáhnout do pole nebo vybrat **možnost Vyhledat soubory**. V případě potřeby můžete zkontrolovat a upravit nahrané IP adresy.

   Když zadáte adresu IP, portál ji ověří, aby zkontroloval překrývající se, duplicitní a formátování.

5. Po dokončení vyberte **Zkontrolovat + Vytvořit**.
6. Vyberte **Vytvořit**.


## <a name="next-steps"></a>Další kroky

- [Další informace o skupinách IP](ip-groups.md)
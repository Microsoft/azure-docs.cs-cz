---
title: Vytvoření skupin IP adres v Azure Firewall
description: Skupiny IP adres umožňují seskupovat a spravovat IP adresy pro pravidla Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444534"
---
# <a name="create-ip-groups-preview"></a>Vytvoření skupin IP adres (Preview)

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Skupiny IP adres umožňují seskupovat a spravovat IP adresy pro pravidla Azure Firewall. Můžou mít jednu IP adresu, víc IP adres nebo jeden nebo víc rozsahů IP adres.

## <a name="create-an-ip-group"></a>Vytvoření skupiny IP adres

1. Na domovské stránce Azure Portal vyberte **vytvořit prostředek**.
2. Do textového pole hledání zadejte **skupiny IP adres** a pak vyberte **skupiny IP adres**.
3. Vyberte **Vytvořit**.
4. Vyberte své předplatné.
5. Vyberte skupinu prostředků nebo vytvořte novou.
6. Zadejte jedinečný název pro vaši skupinu IP adres a pak vyberte oblast.

6. Vyberte **Další: IP adresy**.
7. Zadejte IP adresu, více IP adres nebo rozsahy IP adres.

   Existují dva způsoby, jak zadat IP adresy:
   - Můžete je zadat ručně.
   - Můžete je importovat ze souboru.

   Pokud chcete importovat ze souboru, vyberte **Importovat ze souboru**. Můžete buď soubor přetáhnout do pole nebo vybrat **Vyhledat soubory**. V případě potřeby můžete zkontrolovat a upravit nahrané IP adresy.

   Když zadáte IP adresu, portál ji ověří, aby zkontrolovala, jestli nedošlo k překrývání, duplicitám a formátování.

5. Po dokončení vyberte **zkontrolovat + vytvořit**.
6. Vyberte **Vytvořit**.


## <a name="next-steps"></a>Další kroky

- [Další informace o skupinách IP adres](ip-groups.md)
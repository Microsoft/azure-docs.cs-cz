---
title: Vytvoření skupin IP adres v Azure Firewall
description: Skupiny IP adres umožňují seskupovat a spravovat IP adresy pro pravidla Azure Firewall.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85602529"
---
# <a name="create-ip-groups"></a>Vytváření skupin IP adres

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
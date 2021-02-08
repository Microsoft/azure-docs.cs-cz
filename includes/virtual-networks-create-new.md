---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 03/01/2020
ms.author: allensu
ms.custom: include file
ms.openlocfilehash: e1d4d29f8edca87ec1cca0ffced7b3e1bca90717
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808494"
---
## <a name="create-the-virtual-network-and-subnet"></a>Vytvoření virtuální sítě a podsítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **Vytvořit prostředek > Sítě > Virtuální síť** nebo do vyhledávacího pole zadejte **Virtuální síť**.

2. V části **vytvořit virtuální síť** zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vyberte **vytvořit nové**, zadejte **\<resource-group-name>** a pak vyberte OK nebo vyberte existující na **\<resource-group-name>** základě parametrů. |
    | **Podrobnosti o instancích** |                                                                 |
    | Name             | Napište **\<virtual-network-name>**                                    |
    | Oblast           | Vybrali **\<region-name>** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor protokolu IPv4 | Napište **\<IPv4-address-space>** |

5. V části **název podsítě** vyberte slovo **výchozí**.

6. V **Upravit podsíť** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Napište **\<subnet-name>** |
    | Rozsah adres podsítě | Napište **\<subnet-address-range>**

7. Vyberte **Uložit**.

8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.
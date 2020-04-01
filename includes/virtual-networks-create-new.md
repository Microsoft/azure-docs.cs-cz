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
ms.openlocfilehash: 81db46b52c9b4fe800f2fbfeadad966995d66e12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244990"
---
## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. Na levé horní straně obrazovky vyberte **Vytvořit prostředek > síť > virtuální sítě** nebo vyhledejte virtuální **síť** ve vyhledávacím poli.

2. V **části Vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **Základy:**

    | **Nastavení**          | **Hodnotu**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vyberte **Vytvořit nový**, zadejte ** \<>název skupiny prostředků **, pak vyberte OK nebo vyberte existující ** \<>název skupiny prostředků** na základě parametrů. |
    | **Podrobnosti instance** |                                                                 |
    | Name (Název)             | Zadejte>** \<názvu virtuální sítě.**                                    |
    | Region (Oblast)           | Vybrat ** \<>názvu oblasti** |

3. Vyberte kartu **IP adresy** nebo vyberte tlačítko **Další: Ip adresy** v dolní části stránky.

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor IPv4 | Zadejte>** \<adresního prostoru IPv4** |

5. V části **Název podsítě**vyberte **slovo default**.

6. Do **pole Upravit podsíť**zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte>** \<názvu podsítě.** |
    | Rozsah adres podsítě | Zadejte>** \<rozsah adres podsítě**

7. Vyberte **Uložit**.

8. Vyberte kartu **Revize + vytvoření** nebo tlačítko Revize + **vytvoření.**

9. Vyberte **Vytvořit**.
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
ms.openlocfilehash: 43ede7f3340f190f9eb37ca90fb74f2215db9062
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227386"
---
## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síť > virtuální síť** nebo ve vyhledávacím poli vyhledejte **virtuální síť** .

2. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Výběr vašeho předplatného Azure                                  |
    | Skupina prostředků   | Vyberte **vytvořit nový**, zadejte **\<resource-group-name >** a pak vyberte OK. |
    | **Podrobnosti instance** |                                                                 |
    | Název             | Zadejte **\<název virtuální sítě – název >**                                    |
    | Oblast           | Vyberte **\<oblast – název >** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor IPv4 | Zadejte **\<adresní prostor IPv4 >** |

5. V části **název podsítě**vyberte slovo **výchozí**.

6. V **Upravit podsíť**zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **\<název podsítě >** |
    | Rozsah adres podsítě | Zadejte **\<podsíť-Address-rozsah >**

7. Vyberte **Save** (Uložit).

8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.
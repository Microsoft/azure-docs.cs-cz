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
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78244990"
---
## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síť > virtuální síť** nebo ve vyhledávacím poli vyhledejte **virtuální síť** .

2. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Hodnota**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Výběr vašeho předplatného Azure                                  |
    | Skupina prostředků   | Vyberte **vytvořit nový**, zadejte **\<resource-group-name >** , vyberte OK nebo vyberte existující **\<resource-group-name >** na základě parametrů. |
    | **Podrobnosti instance** |                                                                 |
    | Název             | Zadejte **\<název virtuální sítě – název >**                                    |
    | Oblast           | Vyberte **\<oblast – název >** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor IPv4 | Zadejte **\<IPv4-address-space >** |

5. V části **název podsítě**vyberte slovo **výchozí**.

6. V **Upravit podsíť**zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte **\<název podsítě >** |
    | Rozsah adres podsítě | Zadejte **\<podsíť-Address-rozsah >**

7. Vyberte **Save** (Uložit).

8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.
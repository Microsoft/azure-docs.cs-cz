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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78244990"
---
## <a name="create-the-virtual-network"></a>Vytvoření virtuální sítě

V této části vytvoříte virtuální síť a podsíť.

1. V levém horním rohu obrazovky vyberte **vytvořit prostředek > síť > virtuální síť** nebo ve vyhledávacím poli vyhledejte **virtuální síť** .

2. V části **vytvořit virtuální síť**zadejte nebo vyberte tyto informace na kartě **základy** :

    | **Nastavení**          | **Osa**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Podrobnosti o projektu**  |                                                                 |
    | Předplatné     | Vyberte své předplatné Azure.                                  |
    | Skupina prostředků   | Vyberte **vytvořit nový**, zadejte ** \<Resource-Group-Name>**, pak vyberte OK nebo vyberte existující ** \<prostředek-název skupiny>** na základě parametrů. |
    | **Podrobnosti instance** |                                                                 |
    | Název             | Zadejte ** \<název virtuální sítě>**                                    |
    | Oblast           | Vyberte ** \<název oblasti>** |

3. Vyberte kartu **IP adresy** nebo v dolní části stránky vyberte tlačítko **Další: IP adresy** .

4. Na kartě **IP adresy** zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Adresní prostor IPv4 | Zadejte ** \<IPv4 adresu>adresního prostoru.** |

5. V části **název podsítě**vyberte slovo **výchozí**.

6. V **Upravit podsíť**zadejte tyto informace:

    | Nastavení            | Hodnota                      |
    |--------------------|----------------------------|
    | Název podsítě | Zadejte ** \<název podsítě>** |
    | Rozsah adres podsítě | Zadejte ** \<podsíť-Address-rozsah>**

7. Vyberte **Uložit**.

8. Vyberte kartu **Revize + vytvořit** nebo vyberte tlačítko **Revize + vytvořit** .

9. Vyberte **Vytvořit**.
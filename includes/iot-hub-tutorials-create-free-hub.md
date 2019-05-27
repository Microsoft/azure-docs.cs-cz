---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a7a86c6a2661a8a1f30491391fc76f4dc5d71f54
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66163511"
---
Vytvoření IoT Hubu pomocí webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).

1. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.

    ![Výběr instalace služby IoT Hub](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. K vytvoření vlastního centra IoT úrovně Free použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | Vytvořte novou. Tento kurz používá název **tutorials-iot-hub-rg**. |
    | Oblast | Tento kurz používá **Západní USA**. Vy si můžete vybrat oblast, která je vám nejbližší. |
    | Název | Následující snímek obrazovky používá název **tutorials-iot-hub**. Když vytváříte své centrum, musíte si zvolit jedinečný název. |

    ![Nastavení centra 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Úroveň ceny a škálování | F1 Free. V jednom předplatném můžete mít pouze jedno centrum úrovně Free. |
    | Jednotky služby IoT Hub | 1 |

    ![Nastavení centra 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Klikněte na možnost **Vytvořit**. Vytvoření centra může trvat několik minut.

    ![Nastavení centra 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Poznamenejte si zvolený název centra IoT. Tuto hodnotu budete později v kurzu potřebovat.
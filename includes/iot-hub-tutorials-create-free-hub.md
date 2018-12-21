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
ms.openlocfilehash: e3110e4018e214e7e7aa591b811246369c029ecd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38940947"
---
Vytvoření IoT Hubu pomocí webu Azure Portal:

1. Přihlaste se k [portálu Azure](http://portal.azure.com).

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
    | Cenová úroveň a úroveň škálování | F1 Free. V jednom předplatném můžete mít pouze jedno centrum úrovně Free. |
    | Jednotky služby IoT Hub | 1 |

    ![Nastavení centra 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Klikněte na možnost **Vytvořit**. Vytvoření centra může trvat několik minut.

    ![Nastavení centra 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Poznamenejte si zvolený název centra IoT. Tuto hodnotu budete později v kurzu potřebovat.
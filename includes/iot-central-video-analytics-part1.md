---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96763401"
---
Ukázková aplikace zahrnuje dvě simulovaná zařízení a jednu IoT Edge bránu. V následujících kurzech se dozvíte dva přístupy k experimentování s funkcemi brány a jejich pochopení:

* Vytvořte bránu IoT Edge na virtuálním počítači Azure a připojte simulovanou kameru.
* Vytvořte bránu IoT Edge na reálném zařízení, jako je Intel NUC, a připojte skutečnou kameru.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Použití šablony aplikace Azure IoT Central video Analytics k vytvoření aplikace pro maloobchodní obchod
> * Přizpůsobení nastavení aplikace
> * Vytvoření šablony zařízení pro zařízení IoT Edge brány
> * Přidání zařízení brány do aplikace IoT Central

## <a name="prerequisites"></a>Předpoklady

K dokončení této série kurzů potřebujete:

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).
* Pokud používáte skutečnou kameru, potřebujete připojení mezi IoT Edgeovým zařízením a fotoaparátem a potřebujete kanál **protokolu streamování v reálném čase** .

## <a name="initial-setup"></a>Počáteční nastavení

V těchto kurzech aktualizujete a použijete několik konfiguračních souborů. Počáteční verze těchto souborů jsou k dispozici v úložišti GitHub [lva-Gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) . Úložiště obsahuje textový soubor [scratchpad](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) , který můžete stáhnout a použít k nahrání hodnot konfigurace ze služeb, které nasazujete. Tento soubor vám pomůže dokončit pozdější kroky v kurzech.

Vytvořte složku s názvem *lva-Configuration* na místním počítači pro uložení kopií těchto souborů. Potom klikněte pravým tlačítkem na každý z následujících odkazů a zvolte **Uložit jako** a uložte soubor do složky *lva-Configuration* :

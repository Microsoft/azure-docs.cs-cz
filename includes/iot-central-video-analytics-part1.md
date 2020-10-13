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
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91877173"
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

## <a name="prerequisites"></a>Požadavky

K dokončení této série kurzů potřebujete:

* Předplatné Azure. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).
* Pokud používáte skutečnou kameru, potřebujete připojení mezi IoT Edgeovým zařízením a fotoaparátem a potřebujete kanál **protokolu streamování v reálném čase** .

## <a name="initial-setup"></a>Počáteční nastavení

V těchto kurzech aktualizujete a použijete několik konfiguračních souborů. Počáteční verze těchto souborů jsou k dispozici v úložišti GitHub [lva-Gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) . Úložiště obsahuje také textový soubor scratchpad, který můžete stáhnout a použít k nahrání hodnot konfigurace ze služeb, které nasazujete.

Vytvořte složku s názvem *lva-Configuration* na místním počítači pro uložení kopií těchto souborů. Potom klikněte pravým tlačítkem na každý z následujících odkazů a zvolte **Uložit jako** a uložte soubor do složky *lva-Configuration* :

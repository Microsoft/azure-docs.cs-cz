---
title: Předpoklady pro Azure IoT Edge modul | Azure Marketplace
description: Předpoklady pro publikování IoT Edge modulu.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: 610f9bc8d87ac75e2868e247fe1332574480b498
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813860"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Předpoklady pro publikování IoT Edge modulu

Tento článek popisuje předpoklady pro publikování nabídky modulu IoT Edge.  Pokud jste to ještě neudělali, přečtěte si [příručku pro publikování IoT Edge modulů](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Požadavky na publikování

Chcete-li publikovat modul IoT Edge do Azure Marketplace, je nutné splnit následující požadavky:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Přístup k [portál partnerů cloudu](https://cloudpartner.azure.com/). Další informace najdete v tématu [Příručka pro publikování Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Dohoda s [Azure Marketplace podmínkami](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hostování technického assetu IoT Edgeho modulu v Azure Container Registry.  Další informace najdete v tématu [Příprava technického assetu modulu IoT Edge](./cpp-create-technical-assets.md) .
- Vaše metadata modulu IoT Edge jsou připravená k použití. Připravte například následující prostředky:
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevné velikosti obrázků, včetně 40x40px, 90x90px, 115x115px, 255x115px)
    - Podmínky použití a zásad ochrany osobních údajů
    - Výchozí konfigurace modulu, která zahrnuje: Routes, resired požadovaných vlastností, createOptions a proměnných prostředí.
    - Dokumentace k modulům
    - Kontakty podpory


## <a name="next-steps"></a>Další kroky

Jakmile [připravíte technický Asset modulu IoT Edge](./cpp-create-technical-assets.md), budete připraveni [vytvořit nabídku modulu IoT Edge](./cpp-create-offer.md). 

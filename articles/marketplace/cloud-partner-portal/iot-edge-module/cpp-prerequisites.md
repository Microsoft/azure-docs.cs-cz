---
title: Předpoklady modulu Azure IoT Edge | Azure Marketplace
description: Požadavky pro publikování modulu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: b6e021fc452b45edd7b1be9fd5afd77b792b4853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286536"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Požadavky na publikování modulu IoT Edge

Tento článek popisuje požadavky pro publikování nabídky modulu IoT Edge.  Pokud jste tak ještě neučinili, přečtěte si [průvodce publikováním modulů IoT Edge](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Požadavky na publikování

Chcete-li publikovat modul IoT Edge na Azure Marketplace, musíte splnit následující požadavky:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Přístup k [portálu partnerů cloudu](https://cloudpartner.azure.com/). Další informace najdete v [tématu Azure Marketplace a AppSource publikování průvodce](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Smlouva s [podmínkami Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hostujte svůj technický prostředek modulu IoT Edge v registru kontejnerů Azure.  Další informace najdete v tématu [jak připravit technický majetek modulu IoT Edge](./cpp-create-technical-assets.md)
- Připravte si metadata modulu IoT Edge k použití. Připravte například následující prostředky:
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevné velikosti obrázků včetně 40x40px, 90x90px, 115x115px, 255x115px)
    - Termín použití a zásady ochrany osobních údajů
    - Výchozí konfigurace modulu, která zahrnuje: trasy, twin požadované vlastnosti, createOptions a proměnné prostředí.
    - Dokumentace modulu
    - Kontaktní údaje podpory


## <a name="next-steps"></a>Další kroky

Jakmile si [připravíte technický majetek modulu IoT Edge](./cpp-create-technical-assets.md), budete připraveni [vytvořit nabídku modulu IoT Edge](./cpp-create-offer.md). 

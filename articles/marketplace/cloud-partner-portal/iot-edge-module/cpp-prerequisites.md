---
title: Předpoklady pro Azure IoT Edge modul | Azure Marketplace
description: Předpoklady pro publikování IoT Edge modulu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: d1bfc29a0cb031421b4017454bc8a2cdd8be09f9
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142370"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Předpoklady pro publikování IoT Edge modulu

>[!Important]
>Od 13. dubna 2020 začneme přesouvat správu nabídek IoT Edge modulů do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Podle pokynů v tématu [vytvoření IoT Edge modulu nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) můžete spravovat migrované nabídky.

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
    - Kontaktní údaje podpory


## <a name="next-steps"></a>Další kroky

Jakmile [připravíte technický Asset modulu IoT Edge](./cpp-create-technical-assets.md), budete připraveni [vytvořit nabídku modulu IoT Edge](./cpp-create-offer.md). 

---
title: Předpoklady modulu Azure IoT Edge | Azure Marketplace
description: Požadavky pro publikování modulu IoT Edge.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 93e0418004ea65f93ad436eec1ce523424a194ba
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80744985"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Požadavky na publikování modulu IoT Edge

>[!Important]
>března 2020 začneme přesouvat správu nabídek modulu IoT Edge do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření nabídky modulu IoT Edge](https://aka.ms/AzureCreateIoT) pro správu migrovaných nabídek.

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

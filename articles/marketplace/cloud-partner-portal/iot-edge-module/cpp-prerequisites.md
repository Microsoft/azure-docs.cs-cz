---
title: Požadované součásti modulu Azure IoT Edge | Dokumentace Microsoftu
description: Požadavky pro publikování modul IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: a4f1023bdf8a49fccbbda1fd0dc537f83a3acee1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013815"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Publikování požadované součásti modulu IoT Edge

Tento článek popisuje předpoklady pro uvedení nabídky modul IoT Edge.  Pokud jste tak již neučinili, přečtěte si [moduly IoT Edge Průvodce publikováním](../..//iot-edge-module.md).


## <a name="publishing-prerequisites"></a>Požadavky na publikování

Modul IoT Edge publikovat na webu Azure Marketplace, musíte splňovat následující požadavky:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Přístup k [portál partnerů cloudu](https://cloudpartner.azure.com/). Další informace najdete v tématu [Průvodce publikováním webu Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Smlouvy [podmínky Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hostování modulu IoT Edge technické asset ve službě Azure Container Registry.  Další informace najdete v tématu [postup přípravy technické asset modul IoT Edge](./cpp-create-technical-assets.md)
- Máte připravený k použití metadata modulu IoT Edge. Například připravte následující prostředky:
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevnou velikostí, včetně 40x40px 90x90px, 115x115px, 255x115px)
    - Období použití a ochrana osobních údajů zásad
    - Výchozí konfigurace modulu, který obsahuje: trasy, dvojčete požadované vlastnosti, CreateOptions field a proměnných prostředí.
    - Dokumentace k modulu
    - Kontaktní údaje podpory


## <a name="next-steps"></a>Další postup

Jakmile budete mít [připravit váš asset technické modul IoT Edge](./cpp-create-technical-assets.md), budete připravení [vytvořit vaši nabídku modul IoT Edge](./cpp-create-offer.md). 

---
title: Požadované součásti modulu Azure IoT Edge | Dokumentace Microsoftu
description: Požadavky pro publikování modul IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 7271a97dc7ab9de1840d809ded0ba1c2940ed83f
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430935"
---
# <a name="iot-edge-module-publishing-prerequisites"></a>Publikování požadované součásti modulu IoT Edge

Tento článek popisuje předpoklady pro uvedení nabídky modul IoT Edge.

Další informace o moduly IoT Edge a výhody modulu publikování na webu Azure Marketplace, najdete v článku [moduly IoT Edge Průvodce publikováním](https://docs.microsoft.com/azure/marketplace/iot-edge-module).

## <a name="publishing-prerequisites"></a>Požadavky na publikování

Modul IoT Edge publikovat na webu Azure Marketplace, musíte splňovat následující požadavky:

<!-- P2: It would be great to point to the terms of use of CPP here. This can often be a blocker for big companies and these terms of use are not anonymously visible yet.-->
- Přístup k [portál partnerů cloudu](https://cloudpartner.azure.com/). Další informace najdete v tématu [Průvodce publikováním webu Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Smlouvy [podmínky Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace-terms/)
- Hostování modulu IoT Edge technické asset ve službě Azure Container Registry.  Další informace najdete v tématu [postup přípravy technické asset modul IoT Edge](./cpp-create-technical-assets.md)
- Máte připravený k použití metadata modulu IoT Edge. Například (není vyčerpávající seznam):
    - Název
    - Popis (ve formátu HTML)
    - Obrázek loga (formát PNG a pevnou velikostí, včetně 40x40px 90x90px, 115x115px, 255x115px)
    - Období použití a ochrana osobních údajů zásad
    - Výchozí konfigurace modulu, který obsahuje: trasy, dvojčete požadované vlastnosti, CreateOptions field a proměnných prostředí.
    - Dokumentace
    - Kontaktní údaje podpory

## <a name="next-steps"></a>Další postup

- [Příprava technických prostředků modulu IoT Edge](./cpp-create-technical-assets.md)
- [Vytvoření nabídky modul IoT Edge](./cpp-create-offer.md)

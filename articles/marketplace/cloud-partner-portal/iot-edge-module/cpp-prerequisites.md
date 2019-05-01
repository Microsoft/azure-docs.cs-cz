---
title: Požadované součásti modulu Azure IoT Edge | Azure Marketplace
description: Požadavky pro publikování modul IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a5d1d6fdaf07f8b27820021d4d2ac45ec67c9915
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942108"
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

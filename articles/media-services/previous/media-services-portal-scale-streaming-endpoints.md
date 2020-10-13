---
title: Škálování koncových bodů streamování s Azure Portal | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky při škálování koncových bodů streamování pomocí Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 814d6b55e5ae04bf27dfb70d4e7b5ea2d5a4315c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89266812"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Škálování koncových bodů streamování pomocí webu Azure Portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Přehled

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Koncové body streamování **Premium** jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Zákazníci, kteří mají koncový bod streamování **Premium**, ve výchozím nastavení získají jednu jednotku streamování (SU). Koncový bod streamování je možné škálovat přidáním jednotek streamování. Každá jednotka streamování poskytuje aplikaci další kapacitu šířky pásma. Další informace o typech koncových bodů streamování a konfiguraci CDN najdete v tématu [Přehled koncového bodu streamování](media-services-streaming-endpoints-overview.md) .
 
V tomto tématu se dozvíte, jak škálovat koncový bod streamování.

Podrobné informace o cenách najdete v článku [Ceny služby Azure Media Services](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="scale-streaming-endpoints"></a>Škálování koncových bodů streamování

Pokud chcete změnit počet jednotek streamování, udělejte toto:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V okně **Nastavení** vyberte **koncové body streamování**.
3. Klikněte na koncový bod streamování, u kterého chcete škálovat. 

    > [!NOTE] 
    > Škálovat můžete jenom koncové body streamování **Premium** .

4. Přesunutím posuvníku určete počet jednotek streamování.

    ![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Další kroky
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


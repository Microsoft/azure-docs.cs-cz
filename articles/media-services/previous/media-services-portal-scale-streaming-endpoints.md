---
title: Škálování streamování koncové body pomocí portálu Azure | Microsoft Docs
description: Tento kurz vás provede jednotlivými kroky škálování koncových bodů streamování pomocí portálu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 1008b3a3-2fa1-4146-85bd-2cf43cd1e00e
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: f2a14f2622d78a4222a8518172eb1ce8ed9e6637
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33790213"
---
# <a name="scale-streaming-endpoints-with-the-azure-portal"></a>Škálování koncových bodů streamování pomocí webu Azure Portal
## <a name="overview"></a>Přehled

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít účet Azure. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Koncové body streamování **Premium** jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Zákazníci, kteří mají koncový bod streamování **Premium**, ve výchozím nastavení získají jednu jednotku streamování (SU). Koncový bod streamování je možné škálovat přidáním jednotek streamování. Každá jednotka streamování poskytuje aplikaci další kapacitu šířky pásma. Další informace o datových proudů typy koncových bodů a konfigurace CDN najdete v tématu [koncový bod streamování přehled](media-services-streaming-endpoints-overview.md) tématu.
 
Toto téma ukazuje, jak se škálovat koncový bod streamování.

Podrobné informace o cenách najdete v článku [Ceny služby Azure Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).

## <a name="scale-streaming-endpoints"></a>Škálování koncových bodů streamování

Chcete-li změnit počet jednotek streamování, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) zvolte účet Azure Media Services.
2. V **nastavení** vyberte **koncové body streamování**.
3. Klikněte na koncový bod streamování, kterou chcete škálovat. 

    > [!NOTE] 
    > Je možné škálovat jenom **Premium** koncové body streamování.

4. Nastavte posuvník zadat počet jednotek streamování.

    ![Koncový bod streamování](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints3.png)

## <a name="next-steps"></a>Další postup
Prohlédněte si mapy kurzů k Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]


---
title: Odesílání souborů do účtu Azure Media Services z Azure StorSimple | Dokumentace Microsoftu
description: Tento článek poskytuje stručný přehled Azure StorSimple Data Manageru. Článek taky obsahuje odkazy na kurzy, které ukazují, jak extrahovat data ze StorSimple a jako prostředky je odeslat do účtu Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: juliako
ms.openlocfilehash: 2992be9aa289f4f5fda63b9b59d3e80e234dd434
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Odesílání souborů do účtu Azure Media Services z Azure StorSimple | Dokumentace Microsoftu

Tento článek poskytuje stručný přehled Azure StorSimple Data Manageru. Článek taky obsahuje odkazy na kurzy, které ukazují, jak extrahovat data ze StorSimple a jako prostředky je odeslat do účtu Azure Media Services (AMS).

> 
> [!NOTE]
> Azure StorSimple Data Manager je nyní ve verzi Private Preview. 
> 

## <a name="overview"></a>Přehled

Ve službě Media Services můžete digitální soubory nahrát do assetu. Asset může obsahovat video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory titulků (a metadata o těchto souborech.) Jakmile soubory odešlete, bude váš obsah bezpečně uložen v cloudu pro další zpracování a streamování.

[Azure StorSimple](https://docs.microsoft.com/azure/storsimple/) používá cloudové úložiště jako rozšíření místního řešení a automaticky rozprostírá data mezi místní úložiště a cloudové úložiště. Zařízení StorSimple před odesláním do cloudu data deduplikuje a komprimuje, takže odesílání velkých souborů do cloudu je velmi efektivní. Služba [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md) poskytuje rozhraní API, která vám umožní extrahovat data z StorSimple a prezentovat je jako prostředky AMS.

## <a name="get-started"></a>Začínáme

1. [Vytvořte účet Media Services](media-services-portal-create-account.md), do kterého chcete převést prostředky.
2. Zaregistrujte si verzi Preview Data Manageru, jak je popsáno v článku [StorSimple Data Manager](../../storsimple/storsimple-data-manager-overview.md).
3. Vytvořte účet StorSimple Data Manageru.
4. Vytvořte úlohu transformace dat, která po spuštění extrahuje data ze zařízení StorSimple a přenese je do účtu AMS jako prostředky. 

    Při spuštění úlohy se vytvoří fronta úložiště. Tato fronta je naplňována zprávami o transformovaných objektech blob, jakmile jsou připravené. Název této fronty je stejný jako název definice úlohy. Tuto frontu můžete použít k určení, kdy je prostředek připravený, a zavolat požadovanou operaci Media Services, která pro něj má být spuštěná. Tuto frontu můžete například použít k aktivaci funkce Azure, která obsahuje potřebný kód Media Services.

## <a name="see-also"></a>Viz také

[Použití sady .NET SDK k aktivaci úloh v Data Manageru](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Další kroky

Nyní můžete kódovat nahrané assety. Další informace najdete v tématu [Kódování assetů](media-services-portal-encode.md).

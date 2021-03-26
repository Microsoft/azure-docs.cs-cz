---
title: Postup oříznutí videosouborů pomocí Media Services – .NET | Microsoft Docs
description: Oříznutí je proces výběru obdélníkového okna v rámci videa a kódování pouze pixelů v rámci tohoto okna. V tomto tématu se dozvíte, jak oříznout videosoubory pomocí Media Services pomocí rozhraní .NET.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572341"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Postup oříznutí videosouborů pomocí Media Services – .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

K oříznutí vstupního videa můžete použít Media Services. Oříznutí je proces výběru obdélníkového okna v rámci videa a kódování pouze pixelů v rámci tohoto okna. Následující diagram vám pomůže tento proces znázornit.

## <a name="pre-processing-stage"></a>Fáze předběžného zpracování

Oříznutí je fáze předběžného zpracování, takže *parametry oříznutí* v přednastavení kódování se vztahují na *vstupní* video. Kódování je následná fáze a nastavení šířky a výšky se vztahuje na *předem zpracované* video, nikoli na původní video. Při navrhování přednastavení udělejte toto:

1. Vybrat parametry oříznutí na základě původního vstupního videa
1. Vyberte nastavení kódování na základě oříznutého videa.

> [!WARNING]
> Pokud nesouhlasíte s nastavením kódování na oříznuté video, nebude výstup odpovídat vašemu očekávání.

Vstupní video má například rozlišení 1080 pixelů (poměr stran 16:9), ale obsahuje černé pruhy (pole pilíře) vlevo a vpravo, takže aktivní video obsahuje pouze 4:3 Window nebo 1440x1080 pixelů. Můžete oříznout černé pruhy a zakódovat 1440x1080 oblast.

## <a name="transform-code"></a>Transformovat kód

Následující fragment kódu ukazuje, jak napsat transformaci v rozhraní .NET pro oříznutí videí.  Kód předpokládá, že máte místní soubor, se kterým chcete pracovat.

- Vlevo je umístění oříznutí vlevo.
- Horní je nejvyšší poloha oříznutí.
- Šířka je konečná šířka oříznutí.
- Height je konečná výška oříznutí.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```

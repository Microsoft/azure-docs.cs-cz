---
title: Generování miniatur pomocí Media Encoder Standard .NET
description: V tomto článku se dozvíte, jak pomocí rozhraní .NET kódovat Asset a generovat miniatury ve stejnou dobu pomocí Media Encoder Standard.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/01/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 135c163c555bc30bea57ccbdf73bf0414a834da2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109641"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>Jak generovat miniatury pomocí kodéru Standard s .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pomocí Media Encoder Standard můžete vygenerovat jednu nebo více miniatur ze vstupního videa ve formátech obrázků [JPEG](https://en.wikipedia.org/wiki/JPEG) nebo [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) .

## <a name="recommended-reading-and-practice"></a>Doporučené čtení a postupy

Doporučujeme, abyste se seznámili s vlastními transformacemi, a to čtením [způsobu kódování s vlastní transformací – .NET](encode-custom-presets-how-to.md).

## <a name="transform-code-example"></a>Příklad transformačního kódu

Následující příklad kódu vytvoří pouze miniaturu.  Měli byste nastavit následující parametry:

- **počátek** – pozice ve vstupním videu, ze které se má začít generovat miniatury. Hodnota může být ve formátu ISO 8601 (například PT05S, aby začínala v intervalu 5 sekund) nebo počet snímků (například 10 pro začátek na desátém snímku) nebo relativní hodnota pro dobu trvání streamu (například 10% pro začátek na 10% doby trvání proudu). Také podporuje makro {Best}, které dává kodéru pokyn, aby vybral nejlepší miniaturu z prvních několika sekund videa a vytvoří pouze jednu miniaturu bez ohledu na to, jaká další nastavení jsou pro krok a rozsah. Výchozí hodnota je makro {Best}.
- **Krok** – intervaly, ve kterých jsou vygenerovány miniatury. Hodnota může být ve formátu ISO 8601 (například PT05S pro jednu bitovou kopii každých 5 sekund) nebo počet snímků (například 30 pro jednu bitovou kopii každých 30 snímků) nebo relativní hodnota pro dobu trvání proudu (například 10% pro jednu bitovou kopii každých 10% doby trvání streamu). Hodnota kroku bude mít vliv na první vygenerovanou miniaturu, která nemusí být přesně ta zadaná v počátečním čase přednastavení transformace. Důvodem je kodér, který se pokusí vybrat nejlepší miniaturu mezi časem spuštění a pozicí kroku od počátečního času jako první výstup. Vzhledem k tomu, že výchozí hodnota je 10%, znamená to, že pokud má datový proud dlouhou dobu trvání, první vygenerovaná Miniatura může být daleko od té, která je zadána v počátečním čase. Zkuste vybrat rozumnou hodnotu pro krok, pokud se očekává, že se první Miniatura blíží počátečnímu času, nebo nastavte hodnotu rozsahu na 1, pokud je v počátečním čase potřeba jenom jedna miniatura.
- **Rozsah** – pozice relativní vzhledem k transformaci přednastavené transformace ve vstupním videu, při kterém se zastaví generování miniatur. Hodnota může být ve formátu ISO 8601 (například PT5M30S, aby se zastavila 5 minut a 30 sekund od počátečního času), nebo počet snímků (například 300 k zastavení na 300th snímku v době spuštění. Pokud je tato hodnota 1, znamená to, že v době spuštění je jenom jedna miniatura), nebo relativní hodnota pro dobu trvání datového proudu (například 50%, která se zastaví na polovinu doby trvání streamu od počátečního času). Výchozí hodnota je 100%, což znamená, že se na konci streamu zastaví.
- **vrstvy** – kolekce výstupních vrstev obrazu, které má kodér vyprodukovat.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>Další kroky

[Generovat miniatury pomocí REST](media-services-generate-thumbnails-rest.md)

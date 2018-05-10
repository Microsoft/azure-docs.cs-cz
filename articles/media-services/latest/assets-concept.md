---
title: Prostředky ve službě Azure Media Services | Microsoft Docs
description: Tento článek vysvětluje, co jsou prostředky, a jak se používají službou Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>Prostředky

**Asset** obsahuje digitální soubory (včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory titulků) a metadata o těchto souborech. Po digitální soubory jsou odeslány do assetu, mohou být využívána ve službě Media Services kódování a vysílání datového proudu pracovních postupů.

Prostředek se mapuje na kontejner objektů blob ve [účet úložiště Azure](storage-account-concept.md) a soubory v prostředku jsou uloženy jako objekty BLOB bloku v tomto kontejneru. Můžete pracovat se soubory Asset v kontejnerech pomocí klienti SDK úložiště.

Azure Media Services podporuje Blob vrstev, pokud účet používá pro obecné účely v2 (GPv2) úložiště. S GPv2 můžete přesunout soubory do cool nebo studené úložiště. Studené úložiště je vhodný pro archivaci mezzanine soubory, když už nepotřebují (například po jejich byly zakódovány.)

Ve službě Media Services v3 lze vytvořit úlohu vstup z prostředků nebo z adresy URL http (s). Vytvoření asset, který lze použít jako vstup pro úlohu, naleznete v části [vytvořit úlohu vstupní z místního souboru](job-input-from-local-file-how-to.md).

Také, přečtěte si informace o [účty úložiště ve službě Media Services](storage-account-concept.md) a [úlohy a transformace](transform-concept.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Datový proud souboru](stream-files-dotnet-quickstart.md)

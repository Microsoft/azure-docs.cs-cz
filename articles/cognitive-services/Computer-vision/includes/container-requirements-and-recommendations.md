---
title: Požadavky na kontejner a doporučení.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034472"
---
V následující tabulce jsou popsány minimální a doporučené PROCESORy a paměťová jádra, která se mají přidělit pro každý Rozpoznávání textu kontejner.

| Kontejner | Minimální | Doporučené |TPS<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Rozpoznávání textu|1 jádro, 8 GB paměti, 0,5 TPS|2 jádra, 8 GB paměti, 1 TPS|0.5, 1|

* Každé jádro musí mít aspoň 2,6 GHz nebo rychlejší.
* TPS-transakcí za sekundu

Základní a paměť odpovídají `--cpus` nastavení a `--memory` , která se `docker run` používají jako součást příkazu.
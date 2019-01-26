---
title: Pomocí rozhraní příkazového řádku můžete škálovat rezervovaných jednotek médií – Azure | Dokumentace Microsoftu
description: Toto téma ukazuje, jak pomocí rozhraní příkazového řádku škálování zpracování médií pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9f95be8a9d75065deedd3bd7c92907145e966494
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54913049"
---
# <a name="scaling-media-processing"></a>Škálování zpracování médií

Azure Media Services umožňuje škálování zpracování médií ve svém účtu tím, že spravuje rezervované jednotky médií (použité položky). Podrobný přehled najdete v tématu [škálování zpracování médií](../previous/media-services-scale-media-processing-overview.md). 

Tento článek popisuje, jak používat [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) škálování použité položky.

> [!NOTE]
> Pro analýzu zvuku a videa analytických úloh, které jsou aktivovány Media Services v3 nebo Video Indexer důrazně doporučujeme pro účet zřídit s 10 použité položky S3. <br/>Pokud potřebujete více než 10 použité položky S3, otevřete lístek podpory pomocí [webu Azure portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Požadavky 

+ Nainstalujte [rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli). Tento článek vyžaduje použití Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. 

    Můžete také použít [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).
+ [Vytvoření účtu Media Services](create-account-cli-how-to.md).

## <a name="scale-media-reserved-units-with-cli"></a>Škálování médií rezervované jednotky pomocí rozhraní příkazového řádku

1. Spusťte příkaz `login`. Ať už používáte Azure cloud shell nebo místní prostředí příkazového řádku, spusťte tento příkaz.

    ```azurecli
    az login
    ```
    
    Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě budete muset otevřít stránku prohlížeče a postupujte podle pokynů v příkazovém řádku zadejte autorizační kód po přejití do [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) v prohlížeči.
2. Spusťte příkaz `mru`.

    Následující [az ams account naposledy použité položky](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) sets rezervované jednotky médií na "amsaccount" účtu pomocí příkazu **počet** a **typ** parametry.

    ```azurecli
    az account set mru -n amsaccount -g amsResourceGroup --count 10 --type S3
    ```

## <a name="billing"></a>Fakturace

Budou se vám účtovat na číslo, typu a množství času, které jsou zřízené použité položky ve vašem účtu. Poplatky za zda spuštěním jakékoli úlohy. Podrobné vysvětlení najdete v tématu v části Nejčastější dotazy [ceny služby Media Services](https://azure.microsoft.com/pricing/details/media-services/) stránky.   

## <a name="next-step"></a>Další krok

[Analýza videí](analyze-videos-tutorial-with-api.md) 

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

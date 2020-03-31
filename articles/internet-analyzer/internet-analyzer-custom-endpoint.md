---
title: Vytvoření vlastního koncového bodu | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak nakonfigurovat vlastní koncový bod pro měření pomocí prostředku Nástroje pro analýzu Internetu.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6a781e767393f35a1ec22f195ba787e3018d8d1e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76713099"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Měření vlastních koncových bodů, které mají být vyhodnoceny v testech nástroje Internet Analyzer 

Tento článek ukazuje, jak nastavit vlastní koncový bod pro měření jako součást testů nástroje Internet Analyzer. Vlastní koncové body pomáhají vyhodnocovat místní úlohy, úlohy spuštěné na jiných zprostředkovatelích cloudu a vlastní konfigurace Azure.  Porovnání dvou vlastních koncových bodů v jednom testu je možné, pokud jeden koncový bod je prostředek Azure. Další informace o nástroji Internet Analyzer naleznete v [přehledu](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili prostředek Nástroje pro analýzu Internetu a vyberte možnost Vlastní koncový bod. Internet Analyzer předpokládá, že váš vlastní koncový bod je přístupný z Internetu. Další informace naleznete [v tématu Create a Internet Analyzer Resource](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Vytvořit vlastní koncový bod

1. Stáhněte si transparentní jednopixelový testovací obrázek [zde](https://fpc.msedge.net/apc/trans.gif). Tento obrázek o jednom obrazovém bodu je datový mitý prostředek, který klientský JavaScript načte pro měření výkonu.
2. Ve vlastní webové aplikaci nasaďte testovací bitovou kopii ve veřejně přístupné cestě. Cesta by měla fungovat přes protokol HTTPS. 
3. Zkopírujte úplnou adresu URL vlastního https://contoso.com/test/trans.gif) koncového bodu (například do vlastního pole koncového bodu během vytváření testu.

## <a name="next-steps"></a>Další kroky

Přečtěte si [nejčastější dotazy k analyzátoru Internetu](internet-analyzer-faq.md)


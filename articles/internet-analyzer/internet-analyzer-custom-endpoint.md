---
title: Vytvoření vlastního koncového bodu | Microsoft Docs
description: V tomto článku se dozvíte, jak nakonfigurovat vlastní koncový bod pro měření s vaším prostředkem Internet Analyzer.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 6ca71bce726f16eeacc96e10eb654bb3e21c5924
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744081"
---
# <a name="measure-custom-endpoints-to-evaluate-in-your-internet-analyzer-tests"></a>Měření vlastních koncových bodů pro vyhodnocení testů pro Internet Analyzer 

Tento článek ukazuje, jak nastavit vlastní koncový bod pro měření v rámci testů služby Internet Analyzer. Vlastní koncové body vám pomůžou zhodnotit místní úlohy, úlohy běžící na jiných poskytovatelích cloudu a vlastní konfigurace Azure.  Porovnání dvou vlastních koncových bodů v jednom testu je možné, pokud je jeden koncový bod prostředkem Azure. Další informace o nástroji Internet Analyzer najdete v [přehledu](internet-analyzer-overview.md). 

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili prostředek pro Internet Analyzer a vybrali možnost vlastní koncový bod. Internet Analyzer předpokládá, že váš vlastní koncový bod je přístupný z Internetu. Další informace najdete v tématu [vytvoření prostředku nástroje Internet Analyzer](internet-analyzer-create-test-portal.md).


## <a name="create-custom-endpoint"></a>Vytvořit vlastní koncový bod

1. Stáhněte si [sem](https://fpc.msedge.net/apc/trans.gif)transparentní test obrazu na jeden pixel. Tento obrázek s jedním pixelem je prostředek, který klientský JavaScript načte pro měření výkonu.
2. V vlastní webové aplikaci nasaďte testovací obrázek v veřejně přístupné cestě. Cesta by měla fungovat přes protokol HTTPS. 
3. Zkopírujte úplnou adresu URL vlastního koncového bodu (např. `https://contoso.com/test/trans.gif` ) do pole vlastní koncový bod během vytváření testu.

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k Internet Analyzer](internet-analyzer-faq.md)


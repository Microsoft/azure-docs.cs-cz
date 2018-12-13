---
title: Upravit jazykový model v Video Indexer – Azure
titlesuffix: Azure Media Services
description: Tento článek obsahuje přehled, co je jazykový model v Video Indexer a jak ji přizpůsobit.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 12/12/2018
ms.author: anzaman
ms.openlocfilehash: aa2c089659d202222e7dd5e41ae56bf2a683bb1e
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323025"
---
# <a name="customize-a-language-model-with-video-indexer"></a>Upravit jazykový model s Video Indexer

Video Indexer podporuje automatické rozpoznávání díky integraci se sadou Microsoft [Custom Speech Service](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/). Upravte jazykový model tak, že nahrajete přizpůsobení textu, konkrétně text z domény, jejichž slovník byste chtěli modul přizpůsobit. Jakmile tréninku modelu, nová slova v úpravě bude rozpoznán, za předpokladu, že výchozí výslovnost a jazykový model se dozvíte o novou posloupnost slov. Vlastní jazykové modely podporují angličtina, němčina, španělština, arabština, francouzština, hindština, italština, japonština, portugalština, ruština a čínština.

Pojďme se na to, které jsou vysoce specifické, jako je "Kubernetes" (v kontextu služby Kubernetes v Azure), třeba slovo. Protože slovo je nová možnost do nástroje Video Indexer, je rozpoznán jako činnosti "komunit". Potřebujete pro trénování modelu, který má rozpoznat jako "Kubernetes". V ostatních případech slova existovat, ale jazykový model se zobrazí v určitém kontextu není očekáván. Například "container service" není posloupnost 2 slova, která jazykového modelu základě nespecializované rozpozná jako konkrétní sadu slov.

Máte možnost odeslat slov bez kontextu v seznamu do textového souboru. To je považován za částečné přizpůsobení. Alternativně můžete nahrát soubory text dokumentace nebo věty související obsah pro lepší přizpůsobení.

Můžete rozhraní Video Indexer API nebo na webu můžete vytvářet a upravovat vlastní jazykové modely, jak je popsáno v tématech [další kroky](#next-steps) části tohoto tématu.

## <a name="best-practices-for-custom-language-models"></a>Osvědčené postupy pro vlastní jazykové modely

Video Indexer zjišťuje podle pravděpodobnost kombinace slov, tak získat nejlepší:

* Poskytne dostatek skutečných příkladů věty, jakmile bude přečteno.
* Uveďte pouze jedné věty na řádek, ne více. V opačném případě se dozvíte systému pravděpodobnosti napříč věty.
* Je možné převést o jedno slovo jako věty a zvýšit tak word s ostatními, ale systém zjišťuje nejlepší z celé věty.
* Při zavádění nové slova nebo zkratky, pokud je to možné poskytnout tolik příklady použití v úplné věty, aby poskytl největší možné množství kontextu systému.
* Pokuste se vložit několik možností přizpůsobení a naleznete v tématu Jak pracují pro vás.
* Vyhněte se opakování přesné jedné větě více než jednou. Posun oproti zbytku vstup může způsobit.
* Vyhněte se běžné symboly (~, # @ % &) jak získat zahodí. Věty, ve kterých jsou uvedeny také získat zahodí.
* Vyhněte se vložení příliš velká vstupů, jako je například stovky tisíc věty, protože to se doplní účinek zvýšení skóre.

## <a name="next-steps"></a>Další postup

[Přizpůsobení jazykového modelu pomocí rozhraní API](customize-language-model-with-api.md)

[Přizpůsobení jazykového modelu na webu](customize-language-model-with-website.md)

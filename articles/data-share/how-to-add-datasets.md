---
title: Přidání datových sad do existující sdílené složky dat Azure
description: Zjistěte, jak přidat datové sady do existující sdílené složky dat ve sdílené složce Azure a sdílet je se stejnými příjemci.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73490549"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Jak přidat datové sady do existující sdílené složky ve sdílené složce Azure

Tento článek vysvětluje, jak přidat datové sady do již existující sdílené složky dat pomocí služby Azure Data Share. To umožňuje sdílet více dat se stejnými příjemci, aniž byste museli vytvářet novou sdílenou složku.

Informace o tom, jak přidat datové sady při vytváření sdílené složky, najdete v tématu [Sdílení dat](share-your-data.md) kurzu.

## <a name="navigate-to-a-sent-data-share"></a>Přechod na odeslanou sdílenou složku dat

Ve službě Azure Data Share přejděte na odeslanou **+ Add Datasets** sdílenou složku a vyberte kartu **Datové sady.**

![Přidat datové sady](./media/how-to/how-to-add-datasets/add-datasets.png)

V panelu vpravo vyberte typ datové sady, který chcete přidat, a klepněte na tlačítko **Další**. Vyberte odběr a skupinu prostředků dat, která chcete přidat. Pomocí rozbalovacích šipek vyhledejte a zaškrtněte políčko vedle dat, která chcete přidat.

![Přidat datové sady](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Po klepnutí na **tlačítko Přidat datové sady**budou datové sady přidány do sdílené složky. Poznámka: Snímek musí být spuštěn vašimi spotřebiteli, aby mohli zobrazit nové datové sady. Pokud jsou nakonfigurována nastavení snímku, spotřebitelé uvidí nové datové sady po dokončení další naplánovaný snímek. Bez nakonfigurované nastavení snímku musí příjemce ručně aktivovat úplnou nebo přírůstkovou kopii dat, aby mohl přijímat aktualizace. Další informace o snímky naleznete v tématu [snímky](terminology.md).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o tom, jak [přidat příjemce do existující sdílené složky dat](how-to-add-recipients.md).
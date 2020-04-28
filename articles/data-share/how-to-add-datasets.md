---
title: Přidání datových sad do existující sdílené složky Azure
description: Naučte se, jak přidat datové sady do existující sdílené složky v Azure Data Share a sdílet se stejnými příjemci.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 00c96950565b077e65f84e2d8b4977092df5e317
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73490549"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Postup přidání datových sad do existující sdílené složky v Azure Data Share

Tento článek vysvětluje, jak přidat datové sady do již existující sdílené složky pomocí sdílené složky Azure. Díky tomu můžete sdílet více dat se stejnými příjemci, aniž byste museli vytvářet novou sdílenou složku.

Informace o tom, jak přidat datové sady při vytváření sdílené složky, najdete v kurzu [sdílení dat](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Přejít na sdílenou složku odeslaných dat

V části sdílená složka Azure přejděte do odeslané sdílené složky a vyberte kartu datové **sady** . Kliknutím na tlačítko **+ přidat datové sady** přidejte více datových sad.

![Přidat datové sady](./media/how-to/how-to-add-datasets/add-datasets.png)

V panelu na pravé straně vyberte typ datové sady, který chcete přidat, a potom klikněte na **Další**. Vyberte předplatné a skupinu prostředků dat, která chcete přidat. Pomocí šipek rozevíracího seznamu vyhledejte a zaškrtněte políčko vedle dat, která chcete přidat.

![Přidat datové sady](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Po kliknutí na možnost **přidat datové sady**budou datové sady přidány do sdílené složky. Poznámka: abyste mohli zobrazit nové datové sady, musí se snímek aktivovat vašimi příjemci. Pokud je nakonfigurované nastavení snímku, příjemci uvidí nové datové sady po dokončení dalšího naplánovaného snímku. Pokud není nakonfigurované nastavení snímku, příjemce musí ručně aktivovat úplnou nebo přírůstkovou kopii dat, aby mohla aktualizace přijímat. Další informace o snímcích najdete v tématu [snímky](terminology.md).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o tom, jak [Přidat příjemce do existující sdílené složky dat](how-to-add-recipients.md).
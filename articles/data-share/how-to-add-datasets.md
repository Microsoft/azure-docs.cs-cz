---
title: Přidání datových sad do existující sdílené složky v Azure Data Share Preview
description: Přidání datových sad do existující sdílené složky
author: madams0013
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jasonh
ms.openlocfilehash: 75ac837491927881fd8b7547b0f49a069c3f2d47
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257239"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share-preview"></a>Postup přidání datových sad do existující sdílené složky v Azure Data Share Preview

Tento článek vysvětluje, jak přidat datové sady do již existující sdílené složky pomocí Azure Data Share Preview. Díky tomu můžete sdílet více dat se stejnými příjemci, aniž byste museli vytvářet novou sdílenou složku.

Informace o tom, jak přidat datové sady při vytváření sdílené složky, najdete v kurzu [sdílení dat](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Přejít na sdílenou složku odeslaných dat

V Azure Data Share Preview přejděte do odeslané sdílené složky a vyberte kartu **datové sady** . Kliknutím na tlačítko **+ přidat datové sady** přidejte více datových sad.

![Přidat datové sady](./media/how-to/how-to-add-datasets/add-datasets.png)

V panelu na pravé straně vyberte typ datové sady, který chcete přidat, a potom klikněte na **Další**. Vyberte předplatné a skupinu prostředků dat, která chcete přidat. Pomocí šipek rozevíracího seznamu vyhledejte a zaškrtněte políčko vedle dat, která chcete přidat.

![Přidat datové sady](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Po kliknutí na možnost **přidat datové sady**budou datové sady přidány do sdílené složky. Poznámka: Aby bylo možné zobrazit nové datové sady, musí se snímek aktivovat vašimi příjemci. Pokud je nakonfigurované nastavení snímku, příjemci uvidí nové datové sady po dokončení dalšího naplánovaného snímku. Pokud není nakonfigurované nastavení snímku, příjemce musí ručně aktivovat úplnou nebo přírůstkovou kopii dat, aby mohla aktualizace přijímat. Další informace o snímcích najdete v tématu [snímky](terminology.md).

## <a name="next-steps"></a>Další postup
Přečtěte si další informace o tom, jak [Přidat příjemce do existující sdílené složky dat](how-to-add-recipients.md).
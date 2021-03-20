---
title: Přidání datových sad do existující sdílené složky Azure
description: Naučte se, jak přidat datové sady do existující sdílené složky v Azure Data Share a sdílet se stejnými příjemci.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 3bfa911921e9bacde2649ee8c4f0d4bc31b56f54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92910538"
---
# <a name="how-to-add-datasets-to-an-existing-share-in-azure-data-share"></a>Postup přidání datových sad do existující sdílené složky v Azure Data Share

Tento článek vysvětluje, jak přidat datové sady do již existující sdílené složky pomocí sdílené složky Azure. Díky tomu můžete sdílet více dat se stejnými příjemci, aniž byste museli vytvářet novou sdílenou složku.

Informace o tom, jak přidat datové sady při vytváření sdílené složky, najdete v kurzu [sdílení dat](share-your-data.md) .

## <a name="navigate-to-a-sent-data-share"></a>Přejít na sdílenou složku odeslaných dat

V části Azure Data Share přejděte do odeslané sdílené složky a vyberte kartu **datové sady** . Kliknutím na tlačítko **+ přidat datové sady** přidejte více datových sad.

![Snímek obrazovky ukazuje vybrané přidat datové sady.](./media/how-to/how-to-add-datasets/add-datasets.png)

V panelu na pravé straně vyberte typ datové sady, který chcete přidat, a potom klikněte na **Další**. Vyberte předplatné a skupinu prostředků dat, která chcete přidat. Pomocí šipek rozevíracího seznamu vyhledejte a zaškrtněte políčko vedle dat, která chcete přidat.

![Snímek obrazovky se zobrazí v podokně Přidat Blob Storage, kde můžete vybrat data.](./media/how-to/how-to-add-datasets/add-datasets-side.png)

Po kliknutí na možnost **přidat datové sady** budou datové sady přidány do sdílené složky. Poznámka: abyste mohli zobrazit nové datové sady, musí se snímek aktivovat vašimi příjemci. Pokud je nakonfigurované nastavení snímku, příjemci uvidí nové datové sady po dokončení dalšího naplánovaného snímku. Pokud není nakonfigurované nastavení snímku, příjemce musí ručně aktivovat úplnou nebo přírůstkovou kopii dat, aby mohla aktualizace přijímat. Další informace o snímcích najdete v tématu [snímky](terminology.md).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o tom, jak [Přidat příjemce do existující sdílené složky dat](how-to-add-recipients.md).
---
title: Konfigurace mapování datové sady v Azure Data Share
description: Přečtěte si, jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku pomocí sdílené složky Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: eed3e8275400a3e677df53b9d62cf0e0bc70271c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88257829"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku v Azure Data Share

Tento článek vysvětluje, jak nakonfigurovat mapování datových sad pro přijatou sdílenou složku pomocí sdílené složky Azure. Mapování datové sady můžete nakonfigurovat tak, aby určovalo cílové úložiště dat, kam chcete přijímat data, nebo pokud potřebujete změnit cílové úložiště dat.

## <a name="navigate-to-a-received-data-share"></a>Přechod na přijatou sdílenou složku dat

Ve službě sdílení dat Azure přejděte do přijaté sdílené složky a vyberte kartu **datové sady** . 

![Mapování datové sady](./media/dataset-mapping.png "Mapování datové sady") 

Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl. Vyberte možnost **+ mapovat na cíl** a zvolte nové cílové úložiště. Vyberte možnost zrušit **mapování** jako první, pokud je datová sada již namapovaná a chcete změnit cílové úložiště dat.

![Mapovat na cíl](./media/dataset-map-target.png "Mapovat na cíl") 

## <a name="select-a-target-store"></a>Vybrat cílové úložiště

Vyberte cílový typ úložiště dat, do kterého chcete data založit. Pro sdílení na základě snímků se všechna data, která už existují v jakýchkoli dříve mapovaných účtech úložiště, automaticky nepřesouvají do nového cílového úložiště. V případě místního sdílení vyberte v zadaném umístění úložiště dat. Umístění je datové centrum Azure, kde se nachází zdrojové úložiště dat poskytovatele dat.

![Cílový účet úložiště](./media/dataset-map-target-sql.png "Cílové úložiště") 

## <a name="select-a-file-format-sql-sources-only"></a>Vybrat formát souboru (pouze zdroje SQL)

Pokud jsou zdrojová data ze zdroje založeného na jazyce SQL a chcete je přijmout jako soubor, můžete zvolit, v jakém formátu se bude přijímat. 

![Zvolit formát](./media/sql-file-formats.png "Formáty souborů SQL")

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .




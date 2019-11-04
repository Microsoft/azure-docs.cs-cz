---
title: Konfigurace mapování datové sady v Azure Data Share
description: Přečtěte si, jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku pomocí sdílené složky Azure.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 754977788c5f6e5e574500552f670ba9083cf683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490629"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku v Azure Data Share

Tento článek vysvětluje, jak nakonfigurovat mapování datových sad pro přijatou sdílenou složku pomocí sdílené složky Azure. Tuto akci budete chtít provést, pokud jste přijali pozvánku ke sdílení dat, ale rozhodli jste se použít možnost přijmout a konfigurovat později. Mapování datové sady můžete nakonfigurovat v případě, že potřebujete změnit cíl pro data, která jsou s vámi sdílená, nebo pokud chcete přijímat data do SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Přechod na přijatou sdílenou složku dat

Ve službě sdílení dat Azure přejděte do přijaté sdílené složky a vyberte kartu **Podrobnosti** . 

![Mapování datové sady](./media/dataset-mapping.png "Mapování datové sady") 

Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl. Vyberte **Zrušit mapování stávajícího** mapování. Vyberte možnost **+ mapovat na cíl** a zvolte nové cílové úložiště. 

![Mapovat na cíl](./media/dataset-map-target.png "Mapovat na cíl") 

## <a name="select-a-new-destination-store"></a>Vybrat nové cílové úložiště

Vyberte cílový datový typ, ve kterém chcete data vykládat. Všimněte si, že všechna data, která již existují v jakýchkoli dříve mapovaných účtech úložiště, nebudou automaticky přesunuta do nového cíle.

![Cílový účet úložiště](./media/dataset-map-target-sql.png "Cílové úložiště") 

## <a name="select-a-file-format-sql-sources-only"></a>Vybrat formát souboru (pouze zdroje SQL)

Pokud jsou zdrojová data ze zdroje založeného na jazyce SQL, můžete zvolit, v jakém formátu se bude přijímat. 

![Zvolit formát](./media/sql-file-formats.png "Formáty souborů SQL")

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .




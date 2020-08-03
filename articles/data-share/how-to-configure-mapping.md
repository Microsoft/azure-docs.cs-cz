---
title: Konfigurace mapování datové sady v Azure Data Share
description: Přečtěte si, jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku pomocí sdílené složky Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 82de05e7169c5803dd999521f61a33b9dd17b567
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511951"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku v Azure Data Share

Tento článek vysvětluje, jak nakonfigurovat mapování datových sad pro přijatou sdílenou složku pomocí sdílené složky Azure. Tuto akci budete chtít provést, pokud jste přijali pozvánku ke sdílení dat, ale rozhodli jste se přijmout a konfigurovat později, nebo pokud jsou data sdílená místně. Mapování datové sady můžete nakonfigurovat v případě, že potřebujete změnit cíl pro data, která jsou s vámi sdílená, nebo pokud chcete přijímat data do SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Přechod na přijatou sdílenou složku dat

Ve službě sdílení dat Azure přejděte do přijaté sdílené složky a vyberte kartu **Podrobnosti** . 

![Mapování datové sady](./media/dataset-mapping.png "Mapování datové sady") 

Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl. Vyberte **Zrušit mapování stávajícího** mapování. Vyberte možnost **+ mapovat na cíl** a zvolte nové cílové úložiště. 

![Mapovat na cíl](./media/dataset-map-target.png "Mapovat na cíl") 

## <a name="select-a-new-target-store"></a>Výběr nového cílového úložiště

Vyberte cílový datový typ, ve kterém chcete data vykládat. Pro sdílení na základě snímků se všechna data, která už existují v jakýchkoli dříve mapovaných účtech úložiště, automaticky nepřesouvají do nového cílového úložiště. V případě místního sdílení vyberte v zadaném umístění úložiště dat. Umístění je datové centrum Azure, kde se nachází zdrojové úložiště dat poskytovatele dat.

![Cílový účet úložiště](./media/dataset-map-target-sql.png "Cílové úložiště") 

## <a name="select-a-file-format-sql-sources-only"></a>Vybrat formát souboru (pouze zdroje SQL)

Pokud jsou zdrojová data ze zdroje založeného na jazyce SQL, můžete zvolit, v jakém formátu se bude přijímat. 

![Zvolit formát](./media/sql-file-formats.png "Formáty souborů SQL")

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .




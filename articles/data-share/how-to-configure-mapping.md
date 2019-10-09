---
title: Konfigurace mapování datových sad v Azure Data Share Preview
description: Přečtěte si, jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku pomocí Azure Data Share Preview.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169132"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku v Azure Data Share Preview

Tento článek vysvětluje, jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku pomocí Azure Data Share Preview. Tuto akci budete chtít provést, pokud jste přijali pozvánku ke sdílení dat, ale rozhodli jste se použít možnost přijmout a konfigurovat později. V opačném případě budete možná chtít změnit cílový účet úložiště pro přijatá data. 

## <a name="navigate-to-a-received-data-share"></a>Přechod na přijatou sdílenou složku dat

Ve službě sdílení dat Azure přejděte do přijaté sdílené složky a vyberte kartu **Podrobnosti** . 

![](./media/dataset-mapping.png "Mapování datové sady") mapování datové sady 

Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl, a klikněte na tlačítko **+ mapovat k cíli**. Pokud jste už nakonfigurovali cílový účet úložiště a chcete změnit mapování na jiný účet úložiště, možná budete muset mapování nejdřív zrušit. 

![Mapovat na cílovou](./media/dataset-map-target.png "mapu pro cíl") 

## <a name="select-a-new-storage-account"></a>Vyberte nový účet úložiště. 

Vyberte účet úložiště, do kterého chcete data založit. Všimněte si, že všechna data, která už existují v jakýchkoli dříve mapovaných účtech úložiště, se automaticky přesunou na nový účet úložiště.

(./media/map-target.png "Cílové úložiště") ![účtu cílového úložiště] 

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak začít sdílet data, pokračujte do kurzu [sdílení vašich dat](share-your-data.md) .




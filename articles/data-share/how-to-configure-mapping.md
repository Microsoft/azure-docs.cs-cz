---
title: Konfigurace mapování datové sady
description: Konfigurace mapování datové sady
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 581e1eef5f1d64e68a6501f56ce60218281c605d
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789238"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Jak nakonfigurovat mapování datovou sadu pro sdílenou složku přijaté v náhledu dat sdílené složky Azure

Tento článek vysvětluje, jak nakonfigurovat mapování datovou sadu pro sdílenou složku přijatých pomocí Azure Data sdílené složky ve verzi Preview. Bude potřeba udělat, pokud je přijmout pozvánku sdílení dat, ale rozhodli "přijmout a nakonfigurovat později". V opačném případě můžete jednoduše chtít změnit cílový účet úložiště pro přijatá data. 

## <a name="navigate-to-a-received-data-share"></a>Přejděte do sdílené složky přijatých dat.

Ve službě Azure Data sdílet přejděte přijatý sdílené složky a vyberte **podrobnosti** kartu. 

![Datová sada mapování](./media/dataset-mapping.png "mapování datové sady") 

Zaškrtněte políčko vedle datové sady, které chcete přiřadit cíl, aby a klikněte na tlačítko **+ mapu, aby cílový**. Budete muset zrušit mapování první, pokud už jste nakonfiguroval cílový účet úložiště a vy chcete změnit mapování na jiný účet úložiště. 

![Mapa k cíli](./media/dataset-map-target.png "Mapa k cíli") 

## <a name="select-a-new-storage-account"></a>Vyberte nový účet úložiště 

Vyberte účet úložiště, které chcete dat objevil v. Všimněte si, že všechna data, která již existuje v libovolném dříve namapována účty úložiště nebudou automaticky přesunuty do nového účtu úložiště.

![Cílový účet úložiště](./media/map-target.png "cílového úložiště") 

## <a name="next-steps"></a>Další postup

Zjistěte, jak začít, sdílení dat, nadále [sdílet vaše data](share-your-data.md) kurzu.




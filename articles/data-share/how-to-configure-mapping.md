---
title: Konfigurace mapování datové sady ve službě Azure Data Share
description: Zjistěte, jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku pomocí služby Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 1ff432c2073536448c8194ebe537c8bf8cf00663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964239"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku ve sdílené složce Azure

Tento článek vysvětluje, jak nakonfigurovat mapování datové sady pro přijatou sdílenou složku pomocí služby Azure Data Share. Budete to chtít udělat, pokud jste přijali pozvánku ke sdílení dat, ale rozhodli jste se přijmout a nakonfigurovat později nebo pokud jsou data sdílena na místě. Mapování datové sady můžete nakonfigurovat, pokud potřebujete změnit cíl pro data sdílená s vámi nebo pokud chcete přijímat data do serveru SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Přechod na přijatou sdílenou složku dat

Ve službě Azure Data Share přejděte na přijatou sdílenou složku a vyberte kartu **Podrobnosti.** 

![Mapování datové sady](./media/dataset-mapping.png "Mapování datové sady") 

Zaškrtněte políčko vedle datové sady, ke které chcete přiřadit cíl. Vyberte **Zrušit mapování,** chcete-li zrušit mapování existujícího mapování. Výběrem **možnosti + mapa chcete-li cílit** a vyberte nový cílový obchod. 

![Mapa k cíli](./media/dataset-map-target.png "Mapa k cíli") 

## <a name="select-a-new-target-store"></a>Výběr nového cílového obchodu

Vyberte cílový datový typ, ve kterých mají data přistát. Pro sdílení založené na snímcích nebudou všechna data, která již existuje v dříve namapovaných účtech úložiště, automaticky přesunuta do nového cílového úložiště. Pro sdílení na místě vyberte úložiště dat v zadaném umístění. Umístění je datové centrum Azure, kde se nachází zdrojové úložiště dat poskytovatele dat.

![Cílový účet úložiště](./media/dataset-map-target-sql.png "Cílové úložiště") 

## <a name="select-a-file-format-sql-sources-only"></a>Výběr formátu souboru (pouze zdroje SQL)

Pokud zdrojová data pocházejí ze zdroje založeného na SQL, můžete zvolit, ve kterém formátu budou přijata. 

![Zvolte formát](./media/sql-file-formats.png "Formáty souborů SQL")

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak začít sdílet data, [pokračujte ve sdílení dat](share-your-data.md) kurzu.




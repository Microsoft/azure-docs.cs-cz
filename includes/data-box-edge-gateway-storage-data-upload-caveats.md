---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175462"
---
Následující upozornění platí pro data, jak se přesune do Azure.

- Doporučujeme, aby více než jedno zařízení nemělo zapisovat do stejného kontejneru.
- Pokud máte existující objekt Azure (například objekt blob nebo soubor) v cloudu se stejným názvem jako objekt, který se kopíruje, zařízení přepíše soubor v cloudu.
- Prázdná hierarchie adresářů (bez souborů) vytvořená ve složkách sdílené složky se nenahraje do kontejnerů objektů blob.
- Data můžete kopírovat pomocí přetažení pomocí Průzkumníka souborů nebo pomocí příkazového řádku. Pokud je agregovaná velikost kopírovaných souborů větší než 10 GB, doporučujeme použít program hromadného kopírování, například Robocopy nebo rsync. Nástroje hromadného kopírování opakujte operaci kopírování pro občasné chyby a poskytují další odolnost proti chybám.
- Pokud sdílená složky přidružené kontejneru úložiště Azure nahraje objekty BLOB, které neodpovídají typu objektů BLOB definované pro sdílenou složku v době vytvoření, pak tyto objekty BLOB nejsou aktualizovány. Nazařízení například vytvoříte sdílenou složku objektů blob bloku. Přidružte sdílenou složku k existujícímu cloudovému kontejneru, který má objekty BLOB stránky. Aktualizujte tuto sdílenou složku a stáhněte soubory. Upravte některé obnovené soubory, které jsou již uloženy jako objekty BLOB stránky v cloudu. Zobrazí se selhání nahrávání.

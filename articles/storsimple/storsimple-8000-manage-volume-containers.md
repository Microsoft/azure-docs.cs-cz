---
title: Správa kontejnerů svazků pro zařízení řady StorSimple 8000
description: Vysvětluje, jak můžete pomocí stránky StorSimple Device Managerch kontejnerů svazků služby přidat, upravit nebo odstranit kontejner svazků.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 5a6cd904d7baa71c8b383022523909ca30000ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514990"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Použití služby Správce zařízení StorSimple ke správě kontejnerů svazků StorSimple

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak používat službu StorSimple Device Manager k vytváření a správě kontejnerů svazků StorSimple.

Kontejner svazků v Microsoft Azure StorSimpleovém zařízení obsahuje jeden nebo víc svazků, které sdílejí nastavení spotřeby v rámci účtu úložiště, šifrování a šířky pásma. Zařízení může mít několik kontejnerů svazků pro všechny jeho svazky. 

Kontejner svazků má následující atributy:

* **Svazky** – vrstvené nebo místně připojené svazky StorSimple, které jsou obsaženy v kontejneru svazků. 
* **Šifrování** – šifrovací klíč, který se dá definovat pro každý kontejner svazků. Tento klíč se používá k šifrování dat odesílaných ze zařízení StorSimple do cloudu. S uživatelem zadaným klíčem se používá bitový klíč AES-256 s vojenskou známkou. Pro zabezpečení dat doporučujeme vždy povolit šifrování cloudového úložiště.
* **Účet úložiště** – účet úložiště Azure, který se používá k ukládání dat. Všechny svazky nacházející se v kontejneru svazků sdílejí tento účet úložiště. Můžete zvolit účet úložiště z existujícího seznamu nebo vytvořit nový účet při vytváření kontejneru svazků a pak zadat přihlašovací údaje pro přístup k tomuto účtu.
* **Šířka pásma cloudu** – šířka pásma spotřebovaná zařízením při posílání dat ze zařízení do cloudu. Při vytváření tohoto kontejneru můžete vynutili řízení šířky pásma zadáním hodnoty mezi 1 MB/s a 1 000 MB/s. Pokud má zařízení spotřebovat veškerou dostupnou šířku pásma, nastavte toto pole na **neomezeno**. Můžete také vytvořit a použít šablonu šířky pásma pro přidělení šířky pásma na základě plánu.

Následující postupy vysvětlují, jak použít okno **kontejnery svazků** StorSimple k dokončení následujících běžných operací:

* Přidat kontejner svazků
* Úprava kontejneru svazků
* Odstranění kontejneru svazků

## <a name="add-a-volume-container"></a>Přidat kontejner svazků
Chcete-li přidat kontejner svazků, proveďte následující kroky.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Úprava kontejneru svazků
Chcete-li upravit kontejner svazků, proveďte následující kroky.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Odstranění kontejneru svazků
V kontejneru svazků jsou svazky. Dá se odstranit jenom v případě, že se nejdřív odstraní všechny svazky, které jsou v něm obsažené. Chcete-li odstranit kontejner svazků, proveďte následující kroky.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [správě svazků StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Přečtěte si další informace o [používání služby StorSimple Device Manager ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


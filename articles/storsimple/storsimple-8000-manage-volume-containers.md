---
title: Spravovat vaše kontejnery svazků zařízení StorSimple v zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak můžete použít stránku kontejnery svazku služby StorSimple Manager zařízení přidat, upravit nebo odstranit kontejner svazků.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/19/2017
ms.author: alkohli
ms.openlocfilehash: 0f8e00d6d07224f56625482f339e612e68914be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874840"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Použít službu StorSimple Manager zařízení ke správě kontejnery svazků zařízení StorSimple

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak používat službu StorSimple Manager zařízení k vytváření a správě kontejnery svazků zařízení StorSimple.

Kontejner svazků v zařízení s Microsoft Azure StorSimple obsahuje jeden nebo více svazků, které sdílejí účet úložiště, šifrování a nastavení spotřeba šířky pásma. Zařízení může mít několik kontejnery svazků pro všechny jeho svazky. 

Kontejner svazků má následující atributy:

* **Svazky** – vrstvené nebo místně vázaný svazky zařízení StorSimple, které jsou obsaženy v rámci kontejneru svazků. 
* **Šifrování** – šifrovací klíč, který lze definovat pro každý kontejner svazků. Tento klíč se používá pro šifrování dat, který se odesílá z zařízení StorSimple do cloudu. Klíč vojenských úrovni AES 256 bitů se používá s klíčem zadanou uživatelem. K zabezpečení dat, doporučujeme vždy povolit šifrování úložiště cloudu.
* **Účet úložiště** – účet úložiště Azure, který se používá k ukládání dat. Všechny svazky, které se nacházejí v kontejneru svazků sdílet tento účet úložiště. Můžete vybrat účet úložiště z existujícího seznamu nebo vytvořte nový účet při vytváření kontejneru svazku a pak zadejte přihlašovací údaje pro tento účet přístup.
* **Cloud šířky pásma** – šířku pásma spotřebovávají zařízení při odesílání dat ze zařízení do cloudu. Řízení šířky pásma můžete vynutit tak, že zadáte hodnotu v rozmezí 1 MB/s až 1 000 MB/s, při vytváření tohoto kontejneru. Pokud chcete zařízení může používat všechny dostupné šířky pásma, nastavte pole na **neomezený**. Můžete také vytvořit a použít šablonu šířky pásma přidělení šířky pásma na základě plánu.

Následující postupy popisují, jak se StorSimple **kontejnery svazků** okno a dokončete následující běžné operace:

* Přidat kontejner svazků
* Upravit kontejneru svazků
* Odstranit kontejner svazků

## <a name="add-a-volume-container"></a>Přidat kontejner svazků
Proveďte následující postup pro přidání kontejner svazků.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Upravit kontejneru svazků
Proveďte následující kroky k úpravě kontejner svazků.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Odstranit kontejner svazků
Kontejner svazků obsahuje svazky v něm. Lze odstranit pouze v případě, že všechny svazky, které v ní jsou nejprve odstranit. Proveďte následující kroky se odstranit kontejner svazků.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Další kroky
* Další informace o [správu svazků zařízení StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Další informace o [pomocí služby StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


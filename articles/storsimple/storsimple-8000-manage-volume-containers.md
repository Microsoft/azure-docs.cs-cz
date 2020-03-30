---
title: Správa kontejnerů svazků pro zařízení řady StorSimple 8000
description: Vysvětluje, jak můžete použít stránku kontejnerů svazku služby StorSimple Device Manager k přidání, úpravě nebo odstranění kontejneru svazku.
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
ms.openlocfilehash: cbdad3c68848ce552811ee658bb29df74a6fad19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267674"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Použití služby Správce zařízení StorSimple ke správě kontejnerů svazků StorSimple

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak používat službu StorSimple Device Manager k vytváření a správě kontejnerů svazků StorSimple.

Kontejner svazku v zařízení Microsoft Azure StorSimple obsahuje jeden nebo více svazků, které sdílejí nastavení účtu úložiště, šifrování a spotřeby šířky pásma. Zařízení může mít více kontejnerů svazků pro všechny jeho svazky. 

Kontejner svazku má následující atributy:

* **Svazky** – vrstvené nebo místně připnuté svazky StorSimple, které jsou obsaženy v kontejneru svazku. 
* **Šifrování** – šifrovací klíč, který lze definovat pro každý kontejner svazku. Tento klíč se používá k šifrování dat, která je odeslána z vašeho zařízení StorSimple do cloudu. S klíčem zadaným uživatelem se používá vojenský bitový klíč AES-256. Chcete-li zabezpečit data, doporučujeme vždy povolit šifrování cloudového úložiště.
* **Účet úložiště** – účet úložiště Azure, který se používá k ukládání dat. Všechny svazky s bydlištěm v kontejneru svazku sdílejí tento účet úložiště. Můžete vybrat účet úložiště z existujícího seznamu nebo vytvořit nový účet při vytváření kontejneru svazku a pak zadat přístupová pověření pro tento účet.
* **Šířka pásma cloudu** – šířka pásma spotřebovaná zařízením při odesílaných datech ze zařízení do cloudu. Při vytváření tohoto kontejneru můžete vynutit řízení šířky pásma zadáním hodnoty mezi 1 Mb/s a 1 000 Mb/s. Pokud chcete, aby zařízení spotřebovává veškerou dostupnou šířku pásma, nastavte toto pole na **neomezenou**. Můžete také vytvořit a použít šablonu šířky pásma pro přidělení šířky pásma na základě plánu.

Následující postupy vysvětlují, jak pomocí okna **kontejnerů** StorSimple Volume k dokončení následujících běžných operací:

* Přidání kontejneru svazku
* Úprava kontejneru svazků
* Odstranění kontejneru svazku

## <a name="add-a-volume-container"></a>Přidání kontejneru svazku
Chcete-li přidat kontejner svazku, proveďte následující kroky.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Úprava kontejneru svazků
Chcete-li upravit kontejner svazku, proveďte následující kroky.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Odstranění kontejneru svazku
Kontejner svazků obsahuje svazky. Lze jej odstranit pouze v případě, že jsou nejprve odstraněny všechny svazky v něm obsažené. Chcete-li odstranit kontejner svazku, proveďte následující kroky.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Další kroky
* Další informace o [správě svazků StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Další informace o [správě zařízení StorSimple Device Manager pomocí služby StorSimple](storsimple-8000-manager-service-administration.md).


---
title: Správa kontejnerů svazků StorSimple na zařízení StorSimple 8000 series | Dokumentace Microsoftu
description: Vysvětluje, jak můžete na stránku kontejnery svazků služby Správce zařízení StorSimple k přidání, úprava nebo odstraňování kontejneru svazků.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606585"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Správa kontejnerů svazků StorSimple pomocí služby Správce zařízení StorSimple

## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak ve službě Správce zařízení StorSimple můžete vytvářet a spravovat kontejnery svazků StorSimple.

Kontejner svazků v zařízení s Microsoft Azure StorSimple obsahuje jeden nebo více svazků, které sdílejí účet úložiště, šifrování a nastavení využití šířky pásma. Zařízení může mít více kontejnerů svazků pro všechny svazky. 

Kontejner svazků má následující atributy:

* **Svazky** – vrstvené nebo místně připojené svazky zařízení StorSimple, které jsou obsaženy v kontejneru svazků. 
* **Šifrování** – šifrovací klíč, který lze definovat pro každý kontejner svazků. Tento klíč se používá k šifrování dat odesílaných ze zařízení StorSimple do cloudu. Klíč kvality AES-256 bitů se používá s klíčem uživatel zadal. K zabezpečení dat, doporučujeme vždy povolit šifrování cloudového úložiště.
* **Účet úložiště** – účtu úložiště Azure, který se používá k ukládání dat. Všechny svazky, které se nacházejí v kontejneru svazků sdílet tento účet úložiště. Můžete vybrat účet úložiště z existujícího seznamu nebo vytvořte nový účet při vytváření kontejneru svazků a pak zadejte přístupové přihlašovací údaje pro tento účet.
* **Šířku pásma cloudu** – šířka pásma spotřebované zařízení, když je odeslání dat ze zařízení do cloudu. Můžete vynutit řízení šířky pásma a zadat hodnotu mezi 1 MB/s a 1 000 MB/s při vytváření tohoto kontejneru. Pokud chcete, aby zařízení spotřebovalo celou dostupnou šířku pásma, nastavte toto pole **Unlimited**. Můžete také vytvořit a použít šablonu šířky pásma a přidělení šířky pásma na základě plánu.

Následující postupy vysvětlují, jak používat StorSimple **kontejnery svazků** okno dokončete následující běžné operace:

* Přidat kontejner svazků
* Upravit kontejner svazku
* Odstraňování kontejneru svazků

## <a name="add-a-volume-container"></a>Přidat kontejner svazků
Proveďte následující kroky, chcete-li přidat kontejner svazků.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Upravit kontejner svazku
Proveďte následující kroky a upravit kontejner svazku.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Odstraňování kontejneru svazků
Kontejner svazků obsahuje svazky v něm. Můžete odstranit pouze v případě, že se nejprve odstraní všechny svazky, které jsou v něm obsažené. Proveďte následující kroky a odstraňte kontejner svazků.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Další postup
* Další informace o [Správa svazky zařízení StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Další informace o [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).


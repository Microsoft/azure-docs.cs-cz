---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 47ef014f31c628fed9d7b2b005e67e8138d3e7e9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38943302"
---
## <a name="terminology"></a>Terminologie

Marketplace image v Azure má následující atributy:

* **Vydavatel** – organizaci, který image vytvořil. Příklady: Canonical, MicrosoftWindowsServer
* **Nabízejí** – název skupiny související obrázky vytvořené vydavatelem. Příklady: Ubuntu Server WindowsServer
* **Skladová položka** – instance nabídky, jako je hlavní verze produktu distribuci. Příklady: 16.04-LTS, 2016-Datacenter
* **Verze** – číslo verze image SKU. 

K identifikaci Marketplace image, když nasazujete virtuální počítač prostřednictvím kódu programu, zadejte tyto hodnoty jako parametry jednotlivě, nebo některé nástroje přijměte image *URN*. Tyto hodnoty oddělené znak dvojtečky (:) kombinuje název URN: *vydavatele*:*nabízejí*:*Sku*:*verze*. V název URN můžete nahradit čísla verzí "nejnovější", která se vybrala nejnovější verze Image. 

Pokud vydavatel image poskytuje další licenci a podmínky nákupu, musíte přijmout tyto podmínky a povolit programové nasazení. Budete taky muset zadat *koupit plán* parametry při nasazování virtuálního počítače prostřednictvím kódu programu. Zobrazit [nasazení image s podmínkami Marketplace](#deploy-an-image-with-marketplace-terms).
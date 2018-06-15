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
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743359"
---
## <a name="terminology"></a>Terminologie

Image pořízenou prostřednictvím Marketplace v Azure má následující atributy:

* **Vydavatel** -organizace, který vytvořil bitovou kopii. Příklady: Kanonický, MicrosoftWindowsServer
* **Nabízejí** -název skupiny související bitové kopie vytvořené vydavatelem. Příklady: Ubuntu Server, Windows Server
* **Skladová položka** – instanci nabídku, jako je například hlavní verze distribuce. Příklady: 16.04-LTS, 2016 Datacenter
* **Verze** -číslo verze SKU bitové kopie. 

K identifikaci image pořízenou prostřednictvím Marketplace při nasazení virtuálního počítače prostřednictvím kódu programu, zadejte tyto hodnoty jednotlivě jako parametry, nebo některé nástroje přijměte bitovou kopii *URN*. Název URN kombinuje tyto hodnoty oddělené dvojtečkou (:): *vydavatele*:*nabízejí*:*Sku*:*verze*. V názvu URN můžete nahradit číslo verze se "nejnovější", který vybere nejnovější verze bitové kopie. 

Pokud image vydavatele poskytuje další licence a nákup podmínky, musíte přijmout tyto podmínky a povolit programové nasazení. Budete taky muset zadat *koupit plán* parametry při nasazování virtuálního počítače prostřednictvím kódu programu. V tématu [nasazení bitové kopie s podmínkami Marketplace](#deploy-an-image-with-marketplace-terms).
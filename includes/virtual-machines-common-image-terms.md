---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 6c7a9857f6d6d57dc9e314bcb1ef848a326b7ed2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49437134"
---
## <a name="terminology"></a>Terminologie

Marketplace image v Azure má následující atributy:

* **Vydavatel**: organizace, který image vytvořil. Příklady: Canonical, MicrosoftWindowsServer
* **Nabízejí**: název skupiny související obrázky vytvořené vydavatelem. Příklady: Ubuntu Server, WindowsServer
* **Skladová položka**: instance nabídky, jako je hlavní verze produktu distribuci. Příklady: 16.04-LTS, 2016-Datacenter
* **Verze**: číslo verze image SKU. 

K identifikaci Marketplace image, když nasazujete virtuální počítač prostřednictvím kódu programu, zadejte tyto hodnoty jednotlivě jako parametry. Některé nástroje přijmout image *URN*, zahrnující tyto hodnoty oddělené znak dvojtečky (:): *vydavatele*:*nabízejí*:*Sku*: *Verze*. V název URN můžete nahradit čísla verzí "nejnovější", která se vybrala nejnovější verze Image. 

Pokud vydavatel image poskytuje další licenci a podmínky nákupu, musíte přijmout tyto podmínky a povolit programové nasazení. Budete taky muset zadat *koupit plán* parametry při nasazování virtuálního počítače prostřednictvím kódu programu. Zobrazit [nasazení image s podmínkami Marketplace](#deploy-an-image-with-marketplace-terms).
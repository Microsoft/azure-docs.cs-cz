---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: rockboyfor
ms.service: multiple
ms.topic: include
origin.date: 10/09/2018
ms.date: 04/01/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 50d71a3967e61e5d531f4bfeae3582b56230a0d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880540"
---
## <a name="terminology"></a>Terminologie

Marketplace image v Azure má následující atributy:

* **Publisher**: Organizace, která image vytvořila. Příklady: Canonical, MicrosoftWindowsServer
* **Nabízejí**: Název skupiny související obrázky vytvořené vydavatelem. Příklady: UbuntuServer, WindowsServer
* **SKLADOVÁ POLOŽKA**: Instance nabídky, například hlavní vydaná verze distribuce. Příklady: 18.04-LTS, 2019 Datacenter
* **Verze**: Číslo verze image SKU. 

K identifikaci Marketplace image, když nasazujete virtuální počítač prostřednictvím kódu programu, zadejte tyto hodnoty jednotlivě jako parametry. Některé nástroje přijmout image *URN*, která je kombinací těchto hodnot, které jsou odděleny znak dvojtečky (:): *Vydavatel*:*nabízejí*:*Sku*:*verze*. V název URN můžete nahradit čísla verzí "nejnovější", která se vybrala nejnovější verze Image. 

Pokud vydavatel image poskytuje další licenci a podmínky nákupu, musíte přijmout tyto podmínky a povolit programové nasazení. Budete taky muset zadat *koupit plán* parametry při nasazování virtuálního počítače prostřednictvím kódu programu. Zobrazit [nasazení image s podmínkami Marketplace](#deploy-an-image-with-marketplace-terms).

<!-- Update_Description: wording update -->
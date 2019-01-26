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
ms.openlocfilehash: 19a78b772d2813c263017515f18da06fdb20aa70
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082261"
---
## <a name="terminology"></a>Terminologie

Marketplace image v Azure má následující atributy:

* **Publisher**: Organizace, která image vytvořila. Příklady: Canonical, MicrosoftWindowsServer
* **Nabízejí**: Název skupiny související obrázky vytvořené vydavatelem. Příklady: Ubuntu Server, WindowsServer
* **SKLADOVÁ POLOŽKA**: Instance nabídky, například hlavní vydaná verze distribuce. Příklady: 18.04-LTS, 2019 Datacenter
* **Verze**: Číslo verze image SKU. 

K identifikaci Marketplace image, když nasazujete virtuální počítač prostřednictvím kódu programu, zadejte tyto hodnoty jednotlivě jako parametry. Některé nástroje přijmout image *URN*, která je kombinací těchto hodnot, které jsou odděleny znak dvojtečky (:): *Vydavatel*:*nabízejí*:*Sku*:*verze*. V název URN můžete nahradit čísla verzí "nejnovější", která se vybrala nejnovější verze Image. 

Pokud vydavatel image poskytuje další licenci a podmínky nákupu, musíte přijmout tyto podmínky a povolit programové nasazení. Budete taky muset zadat *koupit plán* parametry při nasazování virtuálního počítače prostřednictvím kódu programu. Zobrazit [nasazení image s podmínkami Marketplace](#deploy-an-image-with-marketplace-terms).
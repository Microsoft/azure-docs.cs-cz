---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 98d765e2f6909f00f8dfe76d06aef017aad67adf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174939"
---
## <a name="terminology"></a>Terminologie

Image Marketplace v Azure má následující atributy:

* **Vydavatel**: Organizace, která vytvořila bitovou kopii. Příklady: Canonical, MicrosoftWindowsServer
* **Nabídka**: Název skupiny souvisejících obrázků vytvořených vydavatelem. Příklady: UbuntuServer, WindowsServer
* **Skladová položka**: Instance nabídky, například hlavní vydání distribuce. Příklady: 18.04-LTS, 2019-Datacenter
* **Verze**: Číslo verze sku obrázku. 

Chcete-li identifikovat image Marketplace při nasazení virtuálního virtuálního montovana programově, zadávat tyto hodnoty jednotlivě jako parametry. Některé nástroje přijímají obraz *URN*, který kombinuje tyto hodnoty oddělené dvojtečkou (:) charakter: *Vydavatel*:*Nabídka*:*Sku*:*Verze*. V URN můžete nahradit číslo verze "nejnovější", který vybere nejnovější verzi obrázku. 

Pokud vydavatel bitové kopie poskytuje další licenční a nákupní podmínky, musíte tyto podmínky přijmout a povolit programové nasazení. Budete také muset dodat parametry *plánu nákupu* při programovém nasazení virtuálního montu. Viz [Nasazení bitové kopie s podmínkami Marketplace](#deploy-an-image-with-marketplace-terms).

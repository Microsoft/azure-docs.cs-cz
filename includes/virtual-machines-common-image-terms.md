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
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174939"
---
## <a name="terminology"></a>Terminologie

Image na Marketplace v Azure má následující atributy:

* **Vydavatel**: Organizace, která image vytvořila. Příklady: Canonical, MicrosoftWindowsServer
* **Nabídka**: Název skupiny souvisejících imagí vytvořených vydavatelem. Příklady: UbuntuServer, WindowsServer
* **SKU**: Instance nabídky, například hlavní vydaná verze distribuce. Příklady: 18,04 – LTS, 2019 – Datacenter
* **Verze**: Číslo verze SKU image 

Pokud chcete identifikovat image Marketplace při programovém nasazení virtuálního počítače, zadejte tyto hodnoty jednotlivě jako parametry. Některé nástroje přijímají obrázkovou hodnotu *urn*, která kombinuje tyto hodnoty oddělené dvojtečkou (:) optické *Publisher*:*Nabídka*:*SKU*:*verze* V názvu URN můžete nahradit číslo verze "poslední", které vybere nejnovější verzi obrázku. 

Pokud Vydavatel obrázku poskytuje další licenční a nákupní podmínky, musíte tyto podmínky přijmout a povolit programové nasazení. Při programovém nasazování virtuálního počítače budete taky muset zadávat parametry *plánu nákupu* . Viz [nasazení image s podmínkami Marketplace](#deploy-an-image-with-marketplace-terms).

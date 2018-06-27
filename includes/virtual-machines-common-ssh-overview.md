---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 5e199771c39ba2fbdeabbd04ed4081a9cd3ea117
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36964514"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled SSH a klíče

SSH je šifrované připojení protokol, který umožňuje zabezpečené přihlašování přes nezabezpečený připojení. Je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. I když SSH, samotné poskytuje šifrované připojení, pomocí hesla s připojeními SSH stále zranitelný virtuálního počítače vůči hrubou silou útoky, nebo hádání hesel. Bezpečnější a upřednostňované metoda připojení k virtuálnímu počítači pomocí protokolu SSH je pomocí páru veřejného a privátního klíče RSA, také označované jako klíče SSH. 

* *Veřejný klíč* je umístěn na virtuálním počítačům s Linuxem nebo jiné služby, který chcete použít u kryptografie využívající veřejného klíče.

* *Privátní klíč* je co je k dispozici k virtuálním počítačům s Linuxem při provádění připojení SSH, ověřit vaši identitu. Chraňte tento privátní klíč. Nesdílejte ho.

V závislosti na zásady zabezpečení vaší organizace můžete znovu použít jeden pár veřejného a privátního klíče přístup k více službám a virtuálních počítačích Azure. Není nutné samostatné páru klíčů pro každý virtuální počítač nebo službu, kterou chcete získat přístup. 

Veřejný klíč je možné s kýmkoli sdílet. Pouze vy (nebo vaše místní infrastruktura zabezpečení) ale máte privátní klíč.
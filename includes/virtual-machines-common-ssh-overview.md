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
ms.openlocfilehash: 919582745d166cf8b3f3937f9bac4fc0dc1fe64f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "31613402"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled SSH a klíče

SSH je protokol šifrované připojení, která umožňuje zabezpečený přihlášení přes nezabezpečený připojení. Je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. I když SSH, samotné poskytuje šifrované připojení, pomocí hesla s připojeními SSH stále zranitelný virtuálního počítače vůči hrubou silou útoky, nebo hádání hesel. Bezpečnější a upřednostňované metoda připojení k virtuálnímu počítači pomocí protokolu SSH je pomocí páru veřejného a privátního klíče RSA, také označované jako klíče SSH. 

* *Veřejný klíč* je umístěn na virtuálním počítačům s Linuxem nebo jiné služby, který chcete použít u kryptografie využívající veřejného klíče.

* *Privátní klíč* je co je k dispozici k virtuálním počítačům s Linuxem při provádění připojení SSH, ověřit vaši identitu. Chraňte tento privátní klíč. Nesdílejte ho.

V závislosti na zásady zabezpečení vaší organizace můžete znovu použít jeden pár veřejného a privátního klíče přístup k více službám a virtuálních počítačích Azure. Není nutné samostatné páru klíčů pro každý virtuální počítač nebo službu, kterou chcete získat přístup. 

Veřejný klíč je možné s kýmkoli sdílet. Pouze vy (nebo vaše místní infrastruktura zabezpečení) ale máte privátní klíč.
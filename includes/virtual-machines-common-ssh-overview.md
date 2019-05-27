---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bca78e2963f19b60071b1b27c8dc65c76818e10e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66166099"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled Azure a klíčů SSH

[SSH](https://www.ssh.com/ssh/) je šifrované připojení protokol, který umožňuje zabezpečené přihlášení přes zabezpečená připojení. SSH je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. I když SSH samotné poskytuje šifrované připojení, používat hesla s připojeními SSH stále zranitelný virtuálního počítače k útoku hrubou silou útoků nebo hádání hesel. Bezpečnější a upřednostňovaný způsob připojení k virtuálnímu počítači pomocí SSH je pomocí pár veřejného a privátního klíče, označované také jako *klíče SSH*. 

* *Veřejný klíč* je umístěn na virtuální počítač s Linuxem nebo jakoukoli jinou službu, kterou chcete používat s kryptografie využívající veřejný klíč.

* *Privátní klíč* zůstane v místním systému. Chraňte tento privátní klíč. Nesdílejte ho.

Při použití klienta SSH pro připojení k virtuálním počítačům s Linuxem (který obsahuje veřejný klíč), vzdáleném virtuálním počítači ověřuje klienta, abyste měli jistotu, že má privátní klíč. Pokud klient nemá privátní klíč, je udělen přístup k virtuálnímu počítači. 

V závislosti na zásadách zabezpečení vaší organizace můžete využít jeden pár veřejného a privátního klíče pro přístup k více virtuálních počítačů Azure a služeb. Není nutné samostatné páru klíčů pro každý virtuální počítač nebo službu, kterou budete chtít získat přístup. 

Veřejný klíč je sdílet s kýmkoli, ale pouze vy (nebo vaše místní infrastruktura zabezpečení) by měly mít privátní klíč.
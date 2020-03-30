---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 12/21/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3f9b86dd3716a25ab95265cf46b616144f57163b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71168643"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled SSH a klíčů

[SSH](https://www.ssh.com/ssh/) je šifrovaný protokol připojení, který umožňuje zabezpečené přihlášení přes nezabezpečené připojení. SSH je výchozí protokol připojení pro virtuální počítače s Linuxem hostované v Azure. Přestože SSH sám poskytuje šifrované připojení, pomocí hesla s připojením SSH stále ponechává virtuální hod náchylné k útokům hrubou silou nebo hádání hesel. Bezpečnější a upřednostňovanější způsob připojení k virtuálnímu virtuálnímu zařízení pomocí SSH je pomocí páru veřejného a soukromého klíče, označované také jako *klíče SSH*. 

* *Veřejný klíč* se umístí na váš virtuální počítač s Linuxem nebo na jakoukoli jinou službu, kterou chcete použít s kryptografií s veřejným klíčem.

* *Soukromý klíč* zůstane v místním systému. Chraňte tento privátní klíč. Nesdílejte ho.

Když použijete klienta SSH pro připojení k virtuálnímu počítači s Linuxem (který má veřejný klíč), vzdálený virtuální počítač testuje klienta, aby se ujistil, že má soukromý klíč. Pokud má klient soukromý klíč, je mu udělen přístup k virtuálnímu virtuálnímu soudu. 

V závislosti na zásadách zabezpečení vaší organizace můžete znovu použít jeden pár veřejného a soukromého klíče pro přístup k více virtuálním počítačům a službám Azure. Nepotřebujete samostatný pár klíčů pro každý virtuální virtuální ms nebo služby, které chcete získat přístup. 

Veřejný klíč lze sdílet s kýmkoli, ale pouze vy (nebo místní infrastruktura zabezpečení) byste měli mít váš soukromý klíč.
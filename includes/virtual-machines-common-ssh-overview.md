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
ms.openlocfilehash: 0363b719e2587226282257e19e58e3c4e55c2219
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47454492"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled Azure a klíčů SSH

SSH je šifrované připojení protokol, který umožňuje zabezpečené přihlášení přes zabezpečená připojení. SSH je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. I když SSH samotné poskytuje šifrované připojení, používat hesla s připojeními SSH stále zranitelný virtuálního počítače k útoku hrubou silou útoků nebo hádání hesel. Bezpečnější a upřednostňovaný způsob připojení k virtuálnímu počítači pomocí SSH je pomocí pár veřejného a privátního klíče, označované také jako *klíče SSH*. 

* *Veřejný klíč* je umístěn na virtuální počítač s Linuxem nebo jakoukoli jinou službu, kterou chcete používat s kryptografie využívající veřejný klíč.

* *Privátní klíč* je, co se předkládá virtuálního počítače s Linuxem při vytvořte připojení SSH, abychom ověřili vaši identitu. Chraňte tento privátní klíč. Nesdílejte ho.

V závislosti na zásadách zabezpečení vaší organizace můžete využít jeden pár veřejného a privátního klíče pro přístup k více virtuálních počítačů Azure a služeb. Není nutné samostatné páru klíčů pro každý virtuální počítač nebo službu, kterou budete chtít získat přístup. 

Veřejný klíč je sdílet s kýmkoli, ale pouze vy (nebo vaše místní infrastruktura zabezpečení) by měly mít privátní klíč.
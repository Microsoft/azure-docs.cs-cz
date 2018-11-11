---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: cff3d7bfb89d5b03f986da32edc148efcfb7e7bd
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51506291"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled Azure a klíčů SSH

SSH je šifrované připojení protokol, který umožňuje zabezpečené přihlášení přes zabezpečená připojení. SSH je výchozím protokolem připojení pro virtuální počítače Linux hostované v Azure. I když SSH samotné poskytuje šifrované připojení, používat hesla s připojeními SSH stále zranitelný virtuálního počítače k útoku hrubou silou útoků nebo hádání hesel. Bezpečnější a upřednostňovaný způsob připojení k virtuálnímu počítači pomocí SSH je pomocí pár veřejného a privátního klíče, označované také jako *klíče SSH*. 

* *Veřejný klíč* je umístěn na virtuální počítač s Linuxem nebo jakoukoli jinou službu, kterou chcete používat s kryptografie využívající veřejný klíč.

* *Privátní klíč* na vás místní systém používá klienta SSH k ověření vaší identity při připojování k virtuálním počítačům s Linuxem. Chraňte tento privátní klíč. Nesdílejte ho.

V závislosti na zásadách zabezpečení vaší organizace můžete využít jeden pár veřejného a privátního klíče pro přístup k více virtuálních počítačů Azure a služeb. Není nutné samostatné páru klíčů pro každý virtuální počítač nebo službu, kterou budete chtít získat přístup. 

Veřejný klíč je sdílet s kýmkoli, ale pouze vy (nebo vaše místní infrastruktura zabezpečení) by měly mít privátní klíč.
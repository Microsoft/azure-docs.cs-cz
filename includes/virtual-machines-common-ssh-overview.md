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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "71168643"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled SSH a klíčů

[SSH](https://www.ssh.com/ssh/) je zašifrovaný protokol připojení, který umožňuje zabezpečená přihlášení přes nezabezpečená připojení. SSH je výchozí protokol připojení pro virtuální počítače se systémem Linux hostované v Azure. I když SSH sám poskytuje šifrované připojení, použití hesel s připojeními SSH stále opouští virtuální počítač před útoky hrubou silou nebo odhadem hesla. Bezpečnější a upřednostňovanou metodou připojení k virtuálnímu počítači pomocí SSH je použití páru veřejných klíčů, označovaného také jako *klíče SSH*. 

* *Veřejný klíč* je umístěn na virtuálním počítači se systémem Linux nebo v jakékoli jiné službě, kterou chcete používat s kryptografií s veřejným klíčem.

* *Privátní klíč* zůstane v místním systému. Chraňte tento privátní klíč. Nesdílejte ho.

Když použijete klienta SSH pro připojení k VIRTUÁLNÍmu počítači Linux (který má veřejný klíč), vzdálený virtuální počítač otestuje klienta, aby se ujistil, že má privátní klíč. Pokud má klient privátní klíč, získá přístup k virtuálnímu počítači. 

V závislosti na zásadách zabezpečení vaší organizace můžete pro přístup k několika virtuálním počítačům a službám Azure použít jednu dvojici klíčů veřejného a privátního klíče. Pro každý virtuální počítač nebo službu, ke které chcete získat přístup, nepotřebujete samostatné dvojice klíčů. 

Veřejný klíč se dá sdílet s kýmkoli, ale váš privátní klíč by měl mít jenom vy (nebo vaše místní infrastruktura zabezpečení).
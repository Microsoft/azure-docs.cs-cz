---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016355"
---
## <a name="overview-of-ssh-and-keys"></a>Přehled SSH a klíčů

[SSH](https://www.ssh.com/ssh/) je šifrovaný protokol připojení, který poskytuje zabezpečená přihlášení přes nezabezpečená připojení. SSH je výchozí protokol připojení pro virtuální počítače se systémem Linux hostované v Azure. I když SSH poskytuje šifrované připojení, používá hesla s připojením SSH pořád virtuální počítač zranitelný proti útokům hrubou silou. Doporučujeme připojit se k virtuálnímu počítači přes protokol SSH pomocí páru klíčů veřejného a soukromého klíče, označovaného také jako *klíče SSH*. 

- *Veřejný klíč* je umístěný na svém virtuálním počítači se systémem Linux.

- *Privátní klíč* zůstane v místním systému. Chraňte tento privátní klíč. Nesdílejte ho.

Když použijete klienta SSH pro připojení k VIRTUÁLNÍmu počítači Linux (který má veřejný klíč), vzdálený virtuální počítač otestuje klienta, aby se ujistil, že má správný privátní klíč. Pokud má klient privátní klíč, získá přístup k virtuálnímu počítači. 

V závislosti na zásadách zabezpečení vaší organizace můžete pro přístup k několika virtuálním počítačům a službám Azure použít jednu dvojici klíčů veřejného a privátního klíče. Pro každý virtuální počítač nebo službu, ke které chcete získat přístup, nepotřebujete samostatné dvojice klíčů. 

Veřejný klíč se dá sdílet s kýmkoli, ale k privátnímu klíči by měl mít přístup jenom vy (nebo vaše místní infrastruktura zabezpečení).
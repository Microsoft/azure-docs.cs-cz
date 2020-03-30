---
title: Generování a export certifikátů pro připojení VPN uživatelů | Virtuální WAN Azure
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pomocí PowerShellu ve Windows 10 nebo Windows Server 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059909"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Generování a export certifikátů pro připojení VPN uživatelů

Připojení VPN uživatele (point-to-site) používají k ověření certifikáty. Tento článek ukazuje, jak vytvořit kořenový certifikát podepsaný svým držitelem a generovat klientské certifikáty pomocí PowerShellu ve Windows 10 nebo Windows Server 2016.

Kroky uvedené v tomto článku je nutné provést v počítači se systémem Windows 10 nebo Windows Server 2016. Rutiny prostředí PowerShell, které slouží ke generování certifikátů, jsou součástí operačního systému a nefungují v jiných verzích systému Windows. Počítač se systémem Windows 10 nebo Windows Server 2016 je potřeba pouze ke generování certifikátů. Jakmile jsou certifikáty vygenerovány, můžete je nahrát nebo nainstalovat do libovolného podporovaného klientského operačního systému.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Další kroky

Pokračujte [v krocích virtuální sítě WAN pro připojení VPN uživatele](virtual-wan-about.md)

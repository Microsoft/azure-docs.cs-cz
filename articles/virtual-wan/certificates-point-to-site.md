---
title: Generování a export certifikátů pro připojení VPN uživatele | Virtuální síť WAN Azure
description: Vytvořte kořenový certifikát podepsaný svým držitelem, exportujte veřejný klíč a vygenerujte klientské certifikáty pro připojení VPN uživatelů pomocí prostředí PowerShell ve Windows 10 nebo Windows serveru 2016.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 2205f170ee846d4db94db7f524a1c424cfbc8f7b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328034"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>Generování a export certifikátů pro připojení k síti VPN uživatele

Připojení uživatele VPN (Point-to-site) používají k ověřování certifikáty. V tomto článku se dozvíte, jak vytvořit kořenový certifikát podepsaný svým držitelem a jak vygenerovat klientské certifikáty pomocí prostředí PowerShell ve Windows 10 nebo Windows serveru 2016.

Kroky v tomto článku je nutné provést v počítači se systémem Windows 10 nebo Windows Server 2016. Rutiny PowerShellu, které použijete k vygenerování certifikátů, jsou součástí operačního systému a nefungují v ostatních verzích Windows. Počítač s Windows 10 nebo Windows serverem 2016 je potřeba jenom k vygenerování certifikátů. Po vygenerování certifikátů je můžete nahrát nebo nainstalovat na libovolný podporovaný klientský operační systém.

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>Další kroky

Pokračovat s [kroky virtuální sítě WAN pro připojení k síti VPN uživatele](virtual-wan-about.md)

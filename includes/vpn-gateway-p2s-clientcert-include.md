---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 8c55b9b9ce6e98e91e7c6a712e0a9dbca0964512
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985398"
---
Každý klientský počítač, který můžete připojit k virtuální síti pomocí připojení Point-to-Site musí mít nainstalovaný certifikát klienta. Generovat z kořenového certifikátu a nainstalujte ho na každý klientský počítač. Pokud nechcete nainstalovat platný klientský certifikát, ověření se nezdaří, když se klient pokusí připojit k virtuální síti.

Můžete buď vygenerovat jedinečný certifikát pro každého klienta, nebo můžete použít stejný certifikát pro více klientů. Výhodou generování jedinečných certifikátů pro klienty je možnost jednotlivý certifikát odvolat. Jinak pokud více klienti používají stejný klientský certifikát k ověření a jeho odvolání, bude nutné vygenerovat a nainstalovat nové certifikáty pro každého klienta, který používá tento certifikát.

Klientské certifikáty můžete vygenerovat pomocí následujících metod:

- **Podnikový certifikát:**

  - Pokud používáte podnikové certifikační řešení, vygenerujte klientský certifikát s běžným názvem formátu *name@yourdomain.com*. Použijte tento formát místo *domény\uživatelské jméno* formátu.
  - Ujistěte se, že klientský certifikát je založen na šabloně certifikátu uživatele, který má *ověření klienta* uvedená jako první položku v seznamu uživatelů. Zkontrolujte certifikát tak, že na něj kliknuli dvakrát a zobrazení **rozšířené použití klíče** v **podrobnosti** kartu.

- **Certifikát podepsaný svým držitelem:** Postupujte podle pokynů v některém z následujících článků P2S tak, aby klientské certifikáty, které vytvoříte, budou kompatibilní s připojení P2S. Kompatibilní klientský certifikát můžete vytvořit postupu v těchto článcích: 

  * [Pokyny pro Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert): Tyto pokyny vyžadují Windows 10 a PowerShell, čímž vygenerujete certifikáty. Vygenerované certifikáty můžete nainstalovat na libovolný podporovaný klient P2S.
  * [Pokyny pro MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Použijte MakeCert, pokud nemáte přístup k počítači s Windows 10 pro generování certifikátů. I když MakeCert je zastaralý, slouží stále ke generování certifikátů. Vygenerované certifikáty můžete nainstalovat na libovolný podporovaný klient P2S.
  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Pokud generujete klientský certifikát z certifikátu podepsaného svým držitelem, je automaticky nainstalován v počítači, který jste použili k jeho vygenerování. Pokud chcete nainstalovat klientský certifikát na jiný klientský počítač, můžete ho exportujte jako soubor .pfx spolu s celý řetěz certifikátů. Díky tomu se vytvoří soubor .pfx, který obsahuje informace o kořenovém certifikátu potřebné pro klienta k ověření. 

**Export certifikátu**

Postup exportování certifikátu najdete v tématu [generování a export certifikátů pro Point-to-Site pomocí Powershellu](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).

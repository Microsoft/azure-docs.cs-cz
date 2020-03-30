---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 31ccf14c82f6248c74d6af932fe9e338d26d2747
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174869"
---
Každý klientský počítač, který připojíte k virtuální síti s připojením typu Point-to-Site, musí mít nainstalovaný klientský certifikát. Vygenerujete jej z kořenového certifikátu a nainstalujete do každého klientského počítače. Pokud nenainstalujete platný klientský certifikát, ověřování se nezdaří, když se klient pokusí připojit k virtuální síti.

Můžete buď vygenerovat jedinečný certifikát pro každého klienta, nebo můžete použít stejný certifikát pro více klientů. Výhodou generování jedinečných certifikátů pro klienty je možnost jednotlivý certifikát odvolat. V opačném případě, pokud k ověření používá stejný klientský certifikát více klientů a vy jej odvoláte, budete muset generovat a instalovat nové certifikáty pro každého klienta, který tento certifikát používá.

Klientské certifikáty můžete generovat pomocí následujících metod:

- **Podnikový certifikát:**

  - Pokud používáte řešení podnikových certifikátů, vygenerujte klientský certifikát s *názvem\@* ve formátu běžné hodnoty yourdomain.com . Použijte tento formát namísto formátu *název domény\uživatelské jméno.*
  - Ujistěte se, že klientský certifikát je založen na šabloně uživatelského certifikátu, která má *ověřování klienta* uvedené jako první položka v seznamu uživatelů. Zkontrolujte certifikát poklepáním a **zobrazením rozšířeného použití klíče** na kartě **Podrobnosti.**

- **Kořenový certifikát podepsaný svým držitelem:** Postupujte podle pokynů v jednom z následujících článků certifikátu P2S, aby klientské certifikáty, které vytvoříte, byly kompatibilní s vašimi připojeními P2S. Kroky v těchto článcích generují kompatibilní klientský certifikát: 

  * [Pokyny pro Windows 10 PowerShell:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert) Tyto pokyny pro generování certifikátů vyžadují Windows 10 a PowerShell. Generované certifikáty lze nainstalovat na libovolného podporovaného klienta P2S.
  * [MakeCert pokyny](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Použijte MakeCert, pokud nemáte přístup k počítači se systémem Windows 10 pro generování certifikátů. Přestože MakeCert je zastaralé, můžete jej stále použít ke generování certifikátů. Vygenerované certifikáty můžete nainstalovat do libovolného podporovaného klienta P2S.
  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

  Při generování klientského certifikátu z kořenového certifikátu podepsaného svým držitelem se automaticky nainstaluje do počítače, který jste použili ke generování. Chcete-li nainstalovat klientský certifikát do jiného klientského počítače, exportujte jej jako soubor PFX spolu s celým řetězcem certifikátů. Tím vytvoříte soubor .pfx, který obsahuje informace o kořenovém certifikátu potřebné k ověření klienta. 

**Export certifikátu**

Postup exportu certifikátu naleznete v [tématu Generování a export certifikátů pro point-to-site pomocí prostředí PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).

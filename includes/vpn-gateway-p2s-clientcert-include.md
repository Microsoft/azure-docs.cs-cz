---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: 34986ac80a309bcfd495e5782496ba560f84c5f7
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041528"
---
Každý klientský počítač, který se připojujete k virtuální síti s připojením typu Point-to-site, musí mít nainstalovaný klientský certifikát. Vygenerujete ho z kořenového certifikátu a nainstalujete ho na každý klientský počítač. Pokud nenainstalujete platný certifikát klienta, ověřování se nezdaří, pokud se klient pokusí připojit k virtuální síti.

Můžete buď vygenerovat jedinečný certifikát pro každého klienta, nebo můžete použít stejný certifikát pro více klientů. Výhodou generování jedinečných certifikátů pro klienty je možnost jednotlivý certifikát odvolat. V opačném případě, pokud více klientů používá ke ověření stejný klientský certifikát a odvoláte ho, budete muset vygenerovat a nainstalovat nové certifikáty pro každého klienta, který tento certifikát používá.

Klientské certifikáty můžete vygenerovat pomocí následujících metod:

* **Podnikový certifikát:**

  * Pokud používáte podnikové certifikační řešení, vygenerujte klientský certifikát s názvem běžný název formátu Value *\@ yourdomain.com* . Místo formátu *Name\Username domény* použijte tento formát.

  * Ujistěte se, že je klientský certifikát založený na šabloně certifikátu uživatele, která má *ověřování klienta* uvedené jako první položka v seznamu uživatelů. Ověřte certifikát tak, že na něj dvakrát kliknete a zobrazíte **rozšířené použití klíče** na kartě **Podrobnosti** .

* **Kořenový certifikát podepsaný svým držitelem:** Postupujte podle kroků v jednom z následujících článků P2S certifikátů, aby vytvořené klientské certifikáty byly kompatibilní s vašimi připojeními P2S.

  Když vygenerujete klientský certifikát z kořenového certifikátu podepsaného svým držitelem, automaticky se nainstaluje na počítač, který jste použili k jeho vygenerování. Pokud chcete nainstalovat klientský certifikát do jiného klientského počítače, exportujte ho jako soubor. pfx spolu s celým řetězem certifikátů. Tím se vytvoří soubor. pfx, který obsahuje informace o kořenovém certifikátu potřebné k ověření klienta.

  Kroky v těchto článcích generují kompatibilní klientský certifikát, který pak můžete exportovat a distribuovat.

  * [Pokyny pro Windows 10 PowerShell:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert) Tyto pokyny pro generování certifikátů vyžadují Windows 10 a PowerShell. Vygenerované certifikáty lze nainstalovat do libovolného podporovaného klienta P2S.

  * [Pokyny pro Makecert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): použijte Makecert, pokud nemáte přístup k počítači s Windows 10 pro generování certifikátů. I když se MakeCert už nepoužívá, můžete ho i nadále používat ke generování certifikátů. Vygenerované certifikáty můžete nainstalovat do libovolného podporovaného klienta P2S.

  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).
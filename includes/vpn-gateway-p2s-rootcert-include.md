---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174867"
---
Použijte kořenový certifikát, který byl vygenerován pomocí podnikového řešení (doporučeno), nebo vygenerujte certifikát podepsaný svým držitelem. Po vytvoření kořenového certifikátu exportujte data veřejného certifikátu (nikoli soukromý klíč) jako soubor Cer kódovaný base64. Potom nahrajte data veřejného certifikátu na server Azure.

* **Podnikový certifikát:** Pokud používáte podnikové řešení, můžete použít stávající řetěz certifikátů. Získejte soubor CER pro kořenový certifikát, který chcete použít.
* **Kořenový certifikát podepsaný svým držitelem:** Pokud nepoužíváte řešení podnikových certifikátů, vytvořte kořenový certifikát podepsaný svým držitelem. V opačném případě nebudou vytvářené certifikáty kompatibilní s připojeními P2S a klienti při pokusu o připojení obdrží chybu připojení. Můžete použít Azure PowerShell, MakeCert nebo OpenSSL. Kroky v následujících článcích popisují, jak generovat kompatibilní kořenový certifikát podepsaný svým držitelem:

  * [Pokyny pro Windows 10 PowerShell:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) Tyto pokyny pro generování certifikátů vyžadují Windows 10 a PowerShell. Klientské certifikáty vygenerované pomocí kořenového certifikátu můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.
  * [Pokyny pro MakeCert:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) Použijte MakeCert, pokud nemáte přístup k počítači s Windows 10, který byste mohli použít ke generování certifikátů. Přestože MakeCert je zastaralé, můžete jej stále použít ke generování certifikátů. Klientské certifikáty, které generujete z kořenového certifikátu, lze nainstalovat do libovolného podporovaného klienta P2S.
  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

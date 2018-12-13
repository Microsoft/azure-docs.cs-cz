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
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323667"
---
Použít buď kořenový certifikát vygenerovaný pomocí podnikového řešení (doporučeno) nebo vygenerovat certifikát podepsaný svým držitelem. Po vytvoření kořenového certifikátu exportujte data veřejného certifikátu (ne privátní klíč) jako soubor .cer X.509 s kódováním Base64. Nakonec odešlete data veřejného certifikátu na Azure server.

* **Podnikový certifikát:** Pokud používáte podnikové řešení, můžete použít stávající řetěz certifikátů. Získání souboru .cer pro kořenový certifikát, který chcete použít.
* **Certifikát podepsaný svým držitelem:** Pokud nepoužíváte podnikové certifikační řešení, vytvořte certifikát podepsaný svým držitelem. Jinak certifikáty, které vytvoříte, nebudou kompatibilní s vaší připojení typu P2S a klienti budou při pokusu o připojení obdrží chybu připojení. Můžete použít Azure PowerShell, MakeCert nebo OpenSSL. Kroky v následující články popisují, jak vygenerovat certifikát podepsaný svým držitelem kořenové kompatibilní:

  * [Pokyny pro Windows 10 PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Tyto pokyny vyžadují Windows 10 a PowerShell, čímž vygenerujete certifikáty. Klientské certifikáty vygenerované pomocí kořenového certifikátu můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.
  * [Pokyny pro MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Použijte MakeCert, pokud nemáte přístup k počítači s Windows 10 pro generování certifikátů. I když MakeCert je zastaralý, slouží stále ke generování certifikátů. Klientské certifikáty, které generují z kořenového certifikátu můžete nainstalovat na všech podporovaných klientů P2S.
  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

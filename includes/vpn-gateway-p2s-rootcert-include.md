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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174867"
---
Použijte buď kořenový certifikát, který byl vygenerován pomocí podnikového řešení (doporučeno), nebo Vygenerujte certifikát podepsaný svým držitelem. Po vytvoření kořenového certifikátu exportujte data veřejného certifikátu (ne privátní klíč) jako soubor X. 509. cer kódovaný v kódování Base64. Pak nahrajte data veřejného certifikátu na server Azure.

* **Podnikový certifikát:** Pokud používáte podnikové řešení, můžete použít svůj existující řetěz certifikátů. Získejte soubor. cer pro kořenový certifikát, který chcete použít.
* **Kořenový certifikát podepsaný svým držitelem:** Pokud nepoužíváte podnikové certifikační řešení, vytvořte kořenový certifikát podepsaný svým držitelem. V opačném případě certifikáty, které vytvoříte, nebudou kompatibilní s vašimi připojeními P2S a klienti při pokusu o připojení obdrží chybu připojení. Můžete použít Azure PowerShell, MakeCert nebo OpenSSL. Postup v následujících článcích popisuje, jak vygenerovat kompatibilní kořenový certifikát podepsaný svým držitelem:

  * [Pokyny pro Windows 10 PowerShell:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) Tyto pokyny pro generování certifikátů vyžadují Windows 10 a PowerShell. Klientské certifikáty vygenerované pomocí kořenového certifikátu můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.
  * [Pokyny pro MakeCert:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) Použijte MakeCert, pokud nemáte přístup k počítači s Windows 10, který byste mohli použít ke generování certifikátů. I když se MakeCert už nepoužívá, můžete ho i nadále používat ke generování certifikátů. Klientské certifikáty vygenerované z kořenového certifikátu lze nainstalovat do libovolného podporovaného klienta P2S.
  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)

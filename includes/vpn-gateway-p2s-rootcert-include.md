---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 59d18c89a8f66f781f01d1dace9d8968df17a626
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93041526"
---
Získejte soubor. cer pro kořenový certifikát. Můžete použít buď kořenový certifikát, který byl vygenerován pomocí podnikového řešení (doporučeno), nebo vygenerovat certifikát podepsaný svým držitelem. Po vytvoření kořenového certifikátu exportujte data veřejného certifikátu (ne privátní klíč) jako soubor X. 509. cer kódovaný v kódování Base64. Tento soubor nahrajete později do Azure.

* **Podnikový certifikát:** Pokud používáte podnikové řešení, můžete použít svůj existující řetěz certifikátů. Získejte soubor. cer pro kořenový certifikát, který chcete použít.
* **Kořenový certifikát podepsaný svým držitelem:** Pokud nepoužíváte podnikové certifikační řešení, vytvořte kořenový certifikát podepsaný svým držitelem. V opačném případě certifikáty, které vytvoříte, nebudou kompatibilní s vašimi připojeními P2S a klienti při pokusu o připojení obdrží chybu připojení. Můžete použít Azure PowerShell, MakeCert nebo OpenSSL. Postup v následujících článcích popisuje, jak vygenerovat kompatibilní kořenový certifikát podepsaný svým držitelem:

  * [Pokyny pro Windows 10 PowerShell:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) Tyto pokyny pro generování certifikátů vyžadují Windows 10 a PowerShell. Klientské certifikáty vygenerované pomocí kořenového certifikátu můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.
  * [Pokyny pro MakeCert:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) Použijte MakeCert, pokud nemáte přístup k počítači s Windows 10, který byste mohli použít ke generování certifikátů. I když se MakeCert už nepoužívá, můžete ho i nadále používat ke generování certifikátů. Klientské certifikáty vygenerované z kořenového certifikátu lze nainstalovat do libovolného podporovaného klienta P2S.
  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md).
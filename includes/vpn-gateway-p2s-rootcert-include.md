---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afee9450bc1485f781eb0d70b5d4dd2f50424573
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44343205"
---
Můžete použít buď kořenový certifikát vygenerovaný pomocí podnikového řešení (doporučeno), nebo můžete vygenerovat certifikát podepsaný svým držitelem. Po vytvoření kořenového certifikátu vyexportujte data veřejného certifikátu (ne privátní klíč) jako soubor .cer X.509 v kódování Base-64 nahrajte data veřejného certifikátu do Azure.

* **Podnikový certifikát:** Pokud používáte podnikové řešení, můžete použít stávající řetěz certifikátů. Získejte soubor .cer pro kořenový certifikát, který chcete používat.
* **Kořenový certifikát podepsaný svým držitelem:** Pokud nepoužíváte podnikové certifikační řešení, musíte vytvořit kořenový certifikát podepsaný svým držitelem. Je důležité, abyste postupovali podle pokynů v některém z níže uvedených článků věnujících se certifikátům Point-to-Site. Jinak certifikáty, které vytvoříte, nebudou kompatibilní s připojeními typu Point-to-Site a klienti při pokusu o připojení obdrží chybu připojení. Můžete použít Azure PowerShell, MakeCert nebo OpenSSL. Kompatibilní certifikát můžete vytvořit pomocí postupu v uvedených článcích:

  * [Pokyny pro Windows 10 PowerShell:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md) Tyto pokyny pro generování certifikátů vyžadují Windows 10 a PowerShell. Klientské certifikáty vygenerované pomocí kořenového certifikátu můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.
  * [Pokyny pro MakeCert:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) Použijte MakeCert, pokud nemáte přístup k počítači s Windows 10, který byste mohli použít ke generování certifikátů. Nástroj MakeCert je zastaralý, ale stále ho můžete použít ke generování certifikátů. Klientské certifikáty vygenerované pomocí kořenového certifikátu můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.
  * [Pokyny pro Linux](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)

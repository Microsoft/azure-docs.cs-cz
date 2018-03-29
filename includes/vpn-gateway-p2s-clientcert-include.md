---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8a49653b4083cbfd17656d701225dcb14f91f637
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
Každý klientský počítač, který se připojuje k virtuální síti pomocí připojení Point-to-Site, musí mít nainstalovaný klientský certifikát. Klientský certifikát se generuje z kořenového certifikátu a nainstaluje na každý klientský počítač. Pokud není nainstalovaný platný klientský certifikát a klient se pokusí připojit k virtuální síti, ověření se nezdaří.

Můžete buď vygenerovat jedinečný certifikát pro každého klienta, nebo můžete použít stejný certifikát pro více klientů. Výhodou generování jedinečných certifikátů pro klienty je možnost jednotlivý certifikát odvolat. V opačném případě, pokud více klientů používá stejný klientský certifikát a vy ho potřebujete odvolat, bude nutné vygenerovat a nainstalovat nové certifikáty pro všechny klienty, kteří používají tento certifikát k ověření.

Klientské certifikáty můžete vygenerovat následujícími způsoby:

- **Podnikový certifikát:**

  - Pokud používáte podnikové certifikační řešení, vygenerujte klientský certifikát s běžným názvem ve formátu name@yourdomain.com (namísto formátu název_domény\uživatelské_jméno).
  - Ujistěte se, že je certifikát založený na šabloně uživatelského certifikátu, která má jako první položku v seznamu používání Ověření klienta místo Přihlášení pomocí čipové karty atd. Certifikát můžete zkontrolovat dvojím kliknutím na klientský certifikát a zobrazením **Podrobnosti > Použití rozšířeného klíče**.

- **Kořenový certifikát podepsaný svým držitelem:** Je důležité, abyste postupovali podle pokynů v některém z níže uvedených článků věnujících se certifikátům Point-to-Site. Jinak klientské certifikáty, které vytvoříte, nebudou kompatibilní s připojeními typu Point-to-Site a klienti při pokusu o připojení obdrží chybu. Kompatibilní klientský certifikát můžete vytvořit pomocí postupu v jednom z následujících článků: 

  * [Pokyny pro Windows 10 PowerShell:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientcert) Tyto pokyny pro generování certifikátů vyžadují Windows 10 a PowerShell. Vygenerované certifikáty můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.
  * [Pokyny pro MakeCert:](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md) Použijte MakeCert, pokud nemáte přístup k počítači s Windows 10, který byste mohli použít ke generování certifikátů. Nástroj MakeCert je zastaralý, ale stále ho můžete použít ke generování certifikátů. Vygenerované certifikáty můžete nainstalovat na jakémkoli podporovaném klientu Point-to-Site.

  Pokud generujete klientský certifikát pomocí kořenového certifikátu podepsaného svým držitelem podle předchozích pokynů, automaticky se nainstaluje na počítač, který jste použili k jeho vygenerování. Pokud chcete klientský certifikát nainstalovat na jiný klientský počítač, musíte ho vyexportovat jako soubor .pfx spolu s úplným řetězem certifikátů. Tím se vytvoří soubor .pfx, který obsahuje informace o kořenovém certifikátu potřebné pro úspěšné ověření klienta. Postup pro export certifikátu najdete v tématu [Certifikáty – export klientského certifikátu](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport).
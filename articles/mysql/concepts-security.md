---
title: Zabezpečení – databáze Azure pro MySQL
description: Přehled funkcí zabezpečení v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: aac2641913331095550c0e19cc587257a996fcce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537019"
---
# <a name="security-in-azure-database-for-mysql"></a>Zabezpečení ve službě Azure Database for MySQL

Existuje několik vrstev zabezpečení, které jsou k dispozici k ochraně dat v azure databázi pro server MySQL. Tento článek popisuje tyto možnosti zabezpečení.

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="in-transit"></a>V tranzitu
Azure Database for MySQL zabezpečuje vaše data šifrováním dat při přenosu pomocí zabezpečení transportní vrstvy. Šifrování (SSL/TLS) je vynuceno ve výchozím nastavení.

### <a name="at-rest"></a>V klidu
Služba Azure Database for MySQL používá kryptografický modul ověřený fips 140-2 pro šifrování dat v klidovém stavu. Data, včetně záloh, jsou šifrována na disku, s výjimkou dočasných souborů vytvořených při spouštění dotazů. Služba používá 256bitovou šifru AES, která je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.


## <a name="network-security"></a>Zabezpečení sítě
Připojení k azure databázi pro mysql server jsou nejprve směrovány přes místní bránu. Brána má veřejně přístupnou IP adresu, zatímco IP adresy serveru jsou chráněny. Další informace o bráně naleznete v [článku architektury připojení](concepts-connectivity-architecture.md).  

Nově vytvořený Azure Database for MySQL server má bránu firewall, která blokuje všechna externí připojení. Přestože se dostanou k bráně, není jim povoleno připojit se k serveru. 

### <a name="ip-firewall-rules"></a>Pravidla brány firewall IP
Pravidla brány firewall IP udělují přístup k serverům na základě původní adresy IP každého požadavku. Další informace najdete v [přehledu pravidel brány firewall.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě
Koncové body virtuální síťové služby rozšiřují připojení virtuální sítě přes páteřní síť Azure. Pomocí pravidel virtuální sítě můžete povolit Azure Database for MySQL server povolit připojení z vybraných podsítí ve virtuální síti. Další informace naleznete v přehledu [koncového bodu služby virtuální sítě](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Privátní IP adresa
Private Link umožňuje připojení k databázi Azure pro MySQL v Azure prostřednictvím privátního koncového bodu. Azure Private Link v podstatě přináší služby Azure uvnitř vaší privátní virtuální sítě (VNet). K prostředkům PaaS lze přistupovat pomocí privátní IP adresy, stejně jako jakýkoli jiný prostředek ve virtuální síti. Další informace naleznete v přehledu [soukromých odkazů](concepts-data-access-security-private-link.md)

## <a name="access-management"></a>Správa přístupu

Při vytváření databáze Azure pro server MySQL zadáte pověření pro uživatele správce. Tento správce lze použít k vytvoření dalších uživatelů MySQL.


## <a name="threat-protection"></a>Ochrana před hrozbami

Můžete se přihlásit k [rozšířené ochraně před hrozbami,](concepts-data-access-and-security-threat-protection.md) která detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k serverům nebo jejich zneužití.

[Protokolování auditu](concepts-audit-logs.md) je k dispozici ke sledování aktivity ve vašich databázích. 


## <a name="next-steps"></a>Další kroky
- Povolení pravidel brány firewall pro [IP adresy](concepts-firewall-rules.md) nebo [virtuální sítě](concepts-data-access-and-security-vnet.md)
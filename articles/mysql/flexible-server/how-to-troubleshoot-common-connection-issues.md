---
title: Řešení potíží s připojením – Azure Database for MySQL-flexibilní Server
description: Naučte se řešit potíže s připojením pro Azure Database for MySQL flexibilní Server.
keywords: připojení MySQL, připojovací řetězec, problémy s připojením, trvalá chyba, Chyba připojení
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 9/21/2020
ms.openlocfilehash: bdd52ba77fd9a65ce27985ff3c86a93fc887ddf9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109977"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql---flexible-server"></a>Řešení potíží s připojením pro Azure Database for MySQL-flexibilní Server

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Problémy s připojením mohou být způsobeny řadou věcí, včetně:

* Nastavení brány firewall
* Časový limit připojení
* Nesprávné přihlašovací údaje
* Dosáhlo se maximálního limitu u některých Azure Database for MySQL flexibilních prostředků serveru.

V tomto článku se podíváme, jak můžete řešit některé běžné chyby a kroky k vyřešení těchto chyb.

## <a name="troubleshoot-common-errors"></a>Odstraňování běžných chyb

Pokud se aplikace trvale nepřipojí k Azure Database for MySQL flexibilnímu serveru, obvykle se jedná o problém s jedním z následujících způsobů:

* Šifrované připojení pomocí protokolu TLS/SSL: flexibilní Server podporuje pouze šifrovaná připojení pomocí protokolu TLS 1,2 (Transport Layer Security) a všechna **příchozí připojení s TLS 1,0 a tls 1,1 budou odepřena**. Verzi TLS nelze zakázat ani změnit. Přečtěte si další informace o [šifrovaném připojení pomocí protokolu TLS 1,2 (Transport Layer Security) v Azure Database for MySQL-flexibilním serveru](./how-to-connect-tls-ssl.md).
- Flexibilní Server v *privátním přístupu (integrace virtuální sítě)*: Ujistěte se, že se připojujete ze stejné virtuální sítě jako flexibilní Server. Podívejte se na [virtuální síť v Azure Database for MySQL flexibilním serveru]<!--(./concepts-networking-virtual-network.md)-->
- Flexibilní Server s *veřejným přístupem (povolenými IP adresami)* Ujistěte se, že je brána firewall nakonfigurovaná tak, aby povolovala připojení z vašeho klienta. Další informace najdete [v tématu vytváření a Správa flexibilních pravidel brány firewall serveru pomocí Azure Portal](./how-to-manage-firewall-portal.md).
* Konfigurace brány firewall klienta: Brána firewall klienta musí umožňovat připojení k vašemu databázovému serveru. IP adresy a porty serveru, které se nedají povolit, a také názvy aplikací, jako je MySQL v některých branách firewall.
* Chyba uživatele: v připojovacím řetězci možná máte chybné typové parametry připojení, jako je název serveru.

### <a name="resolve-connectivity-issues"></a>Řešení potíží s připojením

* Pokud chcete získat další informace o šifrovaných připojeních, přečtěte si téma [šifrované připojení pomocí protokolu TLS 1,2 (Transport Layer Security) v Azure Database for MySQL-flexibilní Server](./how-to-connect-tls-ssl.md) -->.
* Pokud používáte **veřejný přístup (povolených IP adres)**, pak nastavte [pravidla brány firewall](./how-to-manage-firewall-portal.md) tak, aby povolovala IP adresu klienta. Pro účely dočasného testování nastavte pravidlo brány firewall pomocí direktivy 0.0.0.0 jako počáteční IP adresu a jako koncovou IP adresu použijte 255.255.255.255. Tím se Server otevře na všech IP adresách. Pokud dojde k vyřešení problému s připojením, odeberte toto pravidlo a vytvořte pravidlo brány firewall pro odpovídající omezené IP adresy nebo rozsah adres.
* U všech bran firewall mezi klientem a internetem se ujistěte, že je port 3306 otevřený pro odchozí připojení.
* Ověřte připojovací řetězec a další nastavení připojení. Podívejte se na předdefinované připojovací řetězce na stránce **připojovací řetězce** , které jsou k dispozici pro váš server v Azure Portal pro běžné jazyky.

## <a name="next-steps"></a>Další kroky
- [Pomocí MySQL Workbench se můžete připojit a dotazovat data v Azure Database for MySQL flexibilním serveru](./connect-workbench.md).
- [Php slouží k připojení a dotazování dat v Azure Database for MySQL flexibilním serveru](./connect-php.md).
- [Použijte Python pro připojení a dotazování dat v Azure Database for MySQL flexibilním serveru](./connect-python.md).

---
title: Připojení SSL/TLS – Azure Database for MySQL
description: Informace o konfiguraci Azure Database for MySQL a přidružených aplikací pro správné používání připojení SSL
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 2969c963b491e4b08a0959d548e43ba11276d28a
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126545"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>Připojení SSL/TLS v Azure Database for MySQL

Azure Database for MySQL podporuje připojení vašeho databázového serveru k klientským aplikacím pomocí SSL (Secure Sockets Layer) (SSL). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

> [!NOTE]
> Aktualizace `require_secure_transport` hodnoty parametru serveru nemá vliv na chování služby MySQL. Pomocí funkcí vynucení protokolu SSL a TLS popsaných v tomto článku můžete zabezpečit připojení k vaší databázi.

>[!NOTE]
> Na základě zpětné vazby od zákazníků jsme rozšířili vyřazení kořenových certifikátů pro naši stávající kořenovou certifikační autoritu Baltimore do 15. února 2021 (02/15/2021).

> [!IMPORTANT] 
> Platnost kořenového certifikátu SSL je nastavená na vypršení platnosti od 15. února 2021 (02/15/2021). Aktualizujte prosím svoji aplikaci, aby používala [nový certifikát](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Další informace najdete v tématu [plánované aktualizace certifikátů](concepts-certificate-rotation.md) .

## <a name="ssl-default-settings"></a>Výchozí nastavení SSL

Ve výchozím nastavení by databázová služba měla být nakonfigurovaná tak, aby při připojování k MySQL vyžadovala připojení SSL.  Doporučujeme, abyste se vyhnuli vypnutí možnosti SSL, kdykoli to bude možné.

Při zřizování nového serveru Azure Database for MySQL prostřednictvím Azure Portal a CLI je vynucování připojení SSL ve výchozím nastavení povolené. 

V Azure Portal se zobrazují připojovací řetězce pro různé programovací jazyky. Tyto připojovací řetězce zahrnují požadované parametry SSL pro připojení k vaší databázi. V Azure Portal vyberte svůj server. V záhlaví **Nastavení** vyberte **připojovací řetězce**. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

V některých případech aplikace vyžadují k zabezpečenému připojení soubor místního certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (CA). V současné době můžou zákazníci **použít jenom** předdefinovaný certifikát pro připojení k serveru Azure Database for MySQL, který se nachází na adrese https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Podobně následující odkazy odkazují na certifikáty pro servery v cloudech svrchovaného serveru: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Čína](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)a [Azure Německo](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Informace o tom, jak povolit nebo zakázat připojení SSL při vývoji aplikace, najdete v tématu [Postup konfigurace protokolu SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>Vynucení protokolu TLS v Azure Database for MySQL

Azure Database for MySQL podporuje šifrování pro klienty připojující se k databázovému serveru pomocí protokolu TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje Zabezpečená síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

### <a name="tls-settings"></a>Nastavení TLS

Azure Database for MySQL poskytuje možnost vyhovět verzi TLS pro připojení klienta. Pokud chcete vynutilit verzi TLS, použijte nastavení možnosti **Minimální verze protokolu TLS** . Pro toto nastavení možností jsou povoleny následující hodnoty:

|  Minimální nastavení TLS             | Podporovaná verze TLS klienta                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (výchozí) | Není vyžadován protokol TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 a vyšší           |
| TLS1_1                           | TLS 1,1, TLS 1,2 a vyšší                   |
| TLS1_2                           | TLS verze 1,2 a vyšší                     |


Například nastavení hodnoty minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Další možností je nastavení na 1,2 znamená, že povolíte připojení jenom z klientů pomocí protokolu TLS 1.2 + a všechna připojení k TLS 1,0 a TLS 1,1 budou odmítnutá.

> [!Note] 
> Ve výchozím nastavení Azure Database for MySQL nevynutila minimální verzi TLS (nastavení `TLSEnforcementDisabled` ).
>
> Jakmile vynucujete minimální verzi protokolu TLS, nemůžete později zakázat vynucení minimální verze.

Informace o nastavení nastavení TLS pro Azure Database for MySQL najdete v tématu [Konfigurace nastavení TLS](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-mysql-single-server"></a>Podpora šifry pomocí Azure Database for MySQL jednoho serveru

V rámci komunikace SSL/TLS se šifrovací sady ověřují a podporují jenom šifrovací obleky, které můžou komunikovat s Serer databáze. Ověřování šifrovací sady se řídí ve [vrstvě brány](concepts-connectivity-architecture.md#connectivity-architecture) a ne explicitně na samotném uzlu. Pokud šifrovací sady neodpovídají jedné z níže uvedených sad, připojení příchozích klientů se odmítnou.

### <a name="cipher-suite-supported"></a>Podporovaná šifrovací sada

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Další kroky

- [Knihovny připojení pro Azure Database for MySQL](concepts-connection-libraries.md)
- Informace o tom, jak [nakonfigurovat SSL](howto-configure-ssl.md)
- Informace o tom, jak [nakonfigurovat TLS](howto-tls-configurations.md)

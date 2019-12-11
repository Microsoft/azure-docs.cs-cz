---
title: SSL – Citus (-Scale) – Azure Database for PostgreSQL
description: Pokyny a informace pro konfiguraci Azure Database for PostgreSQL – Citus () a přidružených aplikací pro správné používání připojení SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973981"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurace SSL v Azure Database for PostgreSQL – Citus (škálování)
Připojení klientských aplikací k uzlu koordinátora Citus () vyžaduje SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředníkem" tím, že šifruje datový proud mezi serverem a vaší aplikací.

## <a name="enforcing-ssl-connections"></a>Vynucování připojení SSL
Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal je vynucování připojení SSL ve výchozím nastavení povolené. 

Připojovací řetězce, které jsou předem definované v nastavení "připojovací řetězce" na serveru v Azure Portal obsahují také požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení SSL.
Některé aplikační architektury, které používají PostgreSQL pro své databázové služby, ve výchozím nastavení při instalaci nepovolují protokol SSL. Pokud server PostgreSQL vynutil připojení SSL, ale aplikace není nakonfigurovaná pro SSL, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení SSL, najdete v dokumentaci k vaší aplikaci.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení SSL
V některých případech aplikace vyžadují místní soubor certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (. cer) pro zabezpečené připojení. Certifikát pro připojení k Azure Database for PostgreSQL – Citus (škálovatelný) se nachází na https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Stáhněte si soubor certifikátu a uložte ho do svého upřednostňovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql
Následující příklad ukazuje, jak se připojit k uzlu koordinátoru Citus (psql) pomocí nástroje příkazového řádku. Použijte nastavení připojovacího řetězce `sslmode=verify-full` k vymáhání ověření certifikátu SSL. Předat cestu k místnímu souboru certifikátu k parametru `sslrootcert`.

Níže je uveden příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Potvrďte, že hodnota předaná `sslrootcert` odpovídá cestě k souboru pro certifikát, který jste uložili.

## <a name="next-steps"></a>Další kroky
Zvyšte zabezpečení pomocí [pravidel brány firewall v Azure Database for PostgreSQL – Citus (škálování)](concepts-hyperscale-firewall-rules.md).

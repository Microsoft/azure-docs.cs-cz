---
title: Konfigurace SSL v Azure Database for PostgreSQL – Citus (škálování)
description: Pokyny a informace pro konfiguraci Azure Database for PostgreSQL – Citus () a přidružených aplikací pro správné používání připojení SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: be02c9114f30a63fe710cfd2fc56347bfc68fab5
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273718"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurace SSL v Azure Database for PostgreSQL – Citus (škálování)
Připojení klientských aplikací k uzlu koordinátora Citus () vyžaduje SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředníkem" tím, že šifruje datový proud mezi serverem a vaší aplikací.

## <a name="enforcing-ssl-connections"></a>Vynucování připojení SSL
Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal je vynucování připojení SSL ve výchozím nastavení povolené. 

Připojovací řetězce, které jsou předem definované v nastavení "připojovací řetězce" na serveru v Azure Portal obsahují také požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení SSL.
Některé aplikační architektury, které používají PostgreSQL pro své databázové služby, ve výchozím nastavení při instalaci nepovolují protokol SSL. Pokud server PostgreSQL vynutil připojení SSL, ale aplikace není nakonfigurovaná pro SSL, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení SSL, najdete v dokumentaci k vaší aplikaci.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení SSL
V některých případech aplikace vyžadují místní soubor certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (. cer) pro zabezpečené připojení. Certifikát pro připojení k Azure Database for PostgreSQL – Citus (škálování) se nachází na adrese https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Stáhněte si soubor certifikátu a uložte ho do svého upřednostňovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql
Následující příklad ukazuje, jak se připojit k uzlu koordinátoru Citus (psql) pomocí nástroje příkazového řádku. Použijte nastavení `sslmode=verify-full` připojovacího řetězce pro vymáhání ověření certifikátu SSL. Předat do `sslrootcert` parametru cestu k místnímu souboru certifikátu.

Níže je uveden příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Potvrďte, že hodnota předaná, aby `sslrootcert` odpovídala cestě k souboru pro certifikát, který jste uložili.

## <a name="next-steps"></a>Další kroky
Zvyšte zabezpečení pomocí [pravidel brány firewall v Azure Database for PostgreSQL – Citus (škálování)](concepts-hyperscale-firewall-rules.md).

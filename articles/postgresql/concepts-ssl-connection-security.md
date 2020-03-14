---
title: SSL-Azure Database for PostgreSQL – jeden server
description: Pokyny a informace o tom, jak nakonfigurovat připojení SSL pro Azure Database for PostgreSQL pro jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c235562834ae78a12b690fcd1b96d6a3640e0c66
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371660"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurace připojení SSL v Azure Database for PostgreSQL – jeden server

Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředníkem" tím, že šifruje datový proud mezi serverem a vaší aplikací.

Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurovaná tak, aby vyžadovala připojení SSL. Pokud klientská aplikace nepodporuje připojení SSL, můžete zakázat vyžadování protokolu SSL.

## <a name="enforcing-ssl-connections"></a>Vynucování připojení SSL

Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal a CLI je vynucování připojení SSL ve výchozím nastavení povolené. 

Připojovací řetězce, které jsou předem definované v nastavení "připojovací řetězce" na serveru v Azure Portal obsahují také požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

## <a name="configure-enforcement-of-ssl"></a>Konfigurace vynucení SSL

Volitelně můžete zakázat vynucování připojení SSL. Microsoft Azure doporučuje vždycky povolit nastavení **připojení SSL vynutilo** pro zvýšené zabezpečení.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Přejděte na server Azure Database for PostgreSQL a klikněte na **zabezpečení připojení**. Pomocí přepínacího tlačítka povolíte nebo zakážete nastavení **Vynutilí připojení SSL** . Pak klikněte na **Uložit**.

![Zabezpečení připojení – zakázat vymáhání SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Nastavení můžete potvrdit zobrazením stránky **Přehled** , kde se zobrazí indikátor **stavu vystavení SSL** .

### <a name="using-azure-cli"></a>Použití Azure CLI

Můžete povolit nebo zakázat parametr **SSL-Enforcement** pomocí `Enabled` nebo `Disabled`ch hodnot v rozhraní příkazového řádku Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení SSL.

Některé aplikační architektury, které používají PostgreSQL pro své databázové služby, ve výchozím nastavení při instalaci nepovolují protokol SSL. Pokud server PostgreSQL vynutil připojení SSL, ale aplikace není nakonfigurovaná pro SSL, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení SSL, najdete v dokumentaci k vaší aplikaci.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení SSL

V některých případech aplikace vyžadují místní soubor certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (. cer) pro zabezpečené připojení. Certifikát pro připojení k serveru Azure Database for PostgreSQL se nachází na adrese https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Stáhněte si soubor certifikátu a uložte ho do svého upřednostňovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql

Následující příklad ukazuje, jak se připojit k serveru PostgreSQL pomocí nástroje příkazového řádku psql. Použijte nastavení připojovacího řetězce `sslmode=verify-full` k vymáhání ověření certifikátu SSL. Předat cestu k místnímu souboru certifikátu k parametru `sslrootcert`.

Následující příkaz je příkladem připojovacího řetězce psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Potvrďte, že hodnota předaná `sslrootcert` odpovídá cestě k souboru pro certifikát, který jste uložili.

## <a name="tls-connectivity-in-azure-database-for-postgresql-single-server"></a>Připojení TLS na Azure Database for PostgreSQL jednom serveru

Azure Database for PostgreSQL – jeden server podporuje šifrování pro klienty připojující se k databázovému serveru pomocí protokolu TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje Zabezpečená síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

### <a name="tls-settings"></a>Nastavení TLS

Zákazníci teď mají možnost vyhovět verzi TLS pro klienta připojující se k jejich Azure Database for PostgreSQLmu jedinému serveru. Chcete-li použít možnost TLS, použijte nastavení možnosti **Minimální verze protokolu TLS** . Pro toto nastavení možností jsou povoleny následující hodnoty:

|  Minimální nastavení TLS             | Podporovaná verze TLS                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (výchozí) | Není vyžadován protokol TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 a vyšší |
| TLS1_1                           | TLS 1,1, TLS 1,2 a vyšší          |
| TLS1_2                           | TLS verze 1,2 a vyšší           |


Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Případně můžete tuto hodnotu nastavit na 1,2 znamená, že povolíte připojení pouze z klientů pomocí protokolu TLS 1,2 a všechna připojení pomocí protokolu TLS 1,0 a TLS 1,1 budou odmítnuty.

> [!Note] 
> Pro všechny nové servery je standardně zakázaný protokol TLS pro všechny nové servery Azure Database for PostgreSQL pro jeden server.
>
> V současné době jsou verze TLS podporované byAzure databází pro PostgreSQL jsou TLS 1,0, 1,1 a 1,2.

Informace o tom, jak nastavit nastavení TLS pro váš Azure Database for PostgreSQL samostatný server, najdete v článku [jak nakonfigurovat nastavení TLS](howto-tls-configurations.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si různé možnosti připojení aplikací v [knihovně připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).

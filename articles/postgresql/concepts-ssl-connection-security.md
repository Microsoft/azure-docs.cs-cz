---
title: SSL/TLS-Azure Database for PostgreSQL – jeden server
description: Pokyny a informace o tom, jak nakonfigurovat připojení TLS pro Azure Database for PostgreSQL pro jeden server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 2785f79d327402a40be0a905877b5113b3f751b7
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710443"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurace připojení TLS v Azure Database for PostgreSQL – jeden server

Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). Vynucování připojení TLS mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředník-in-the-middle" tím, že šifruje datový proud mezi serverem a vaší aplikací.

Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurovaná tak, aby vyžadovala připojení TLS. Pokud klientská aplikace nepodporuje připojení TLS, můžete zakázat vyžadování TLS.

## <a name="enforcing-tls-connections"></a>Vynucování připojení TLS

Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal a CLI je vynucování připojení TLS ve výchozím nastavení povolené. 

Připojovací řetězce, které jsou předem definované v nastavení "připojovací řetězce" na serveru v Azure Portal obsahují také požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu TLS. Parametr TLS se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

## <a name="configure-enforcement-of-tls"></a>Konfigurace vynucení TLS

Volitelně můžete zakázat vynucování připojení TLS. Microsoft Azure doporučuje vždycky povolit nastavení **připojení SSL vynutilo** pro zvýšené zabezpečení.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Přejděte na server Azure Database for PostgreSQL a klikněte na **zabezpečení připojení**. Pomocí přepínacího tlačítka povolíte nebo zakážete nastavení **Vynutilí připojení SSL** . Pak klikněte na **Uložit**.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Zabezpečení připojení – zakázat vymáhání TLS/SSL":::

Nastavení můžete potvrdit zobrazením stránky **Přehled** , kde se zobrazí indikátor **stavu vystavení SSL** .

### <a name="using-azure-cli"></a>Použití Azure CLI

Můžete povolit nebo zakázat parametr **SSL-Enforcement** pomocí `Enabled` hodnot nebo v rozhraní příkazového `Disabled` řádku Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení TLS.

Některé aplikační architektury, které používají PostgreSQL pro své databázové služby, během instalace standardně nepovolují protokol TLS. Pokud váš server PostgreSQL vynutil připojení TLS, ale aplikace není nakonfigurovaná pro TLS, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení TLS, najdete v dokumentaci k vaší aplikaci.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení TLS

V některých případech aplikace vyžadují k zabezpečenému připojení soubor místního certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (CA). Certifikát pro připojení k serveru Azure Database for PostgreSQL se nachází na adrese https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Stáhněte si soubor certifikátu a uložte ho do svého upřednostňovaného umístění. 

Podívejte se na následující odkazy pro certifikáty pro servery v cloudech v rámci svrchovaného cloudu: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure Čína](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)a [Azure Německo](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

### <a name="connect-using-psql"></a>Připojení pomocí psql

Následující příklad ukazuje, jak se připojit k serveru PostgreSQL pomocí nástroje příkazového řádku psql. Použijte `sslmode=verify-full` nastavení připojovacího řetězce pro vymáhání ověření certifikátu TLS/SSL. Předat do parametru cestu k místnímu souboru certifikátu `sslrootcert` .

Následující příkaz je příkladem připojovacího řetězce psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Potvrďte, že hodnota předaná, aby `sslrootcert` odpovídala cestě k souboru pro certifikát, který jste uložili.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Vynucení protokolu TLS na Azure Database for PostgreSQL jednom serveru

Azure Database for PostgreSQL – jeden server podporuje šifrování pro klienty připojující se k databázovému serveru pomocí protokolu TLS (Transport Layer Security). TLS je průmyslový standardní protokol, který zajišťuje Zabezpečená síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

### <a name="tls-settings"></a>Nastavení TLS

Azure Database for PostgreSQL jeden server poskytuje možnost vyhovět verzi TLS pro připojení klienta. Pokud chcete vynutilit verzi TLS, použijte nastavení možnosti **Minimální verze protokolu TLS** . Pro toto nastavení možností jsou povoleny následující hodnoty:

|  Minimální nastavení TLS             | Podporovaná verze TLS klienta                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (výchozí) | Není vyžadován protokol TLS                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 a vyšší |
| TLS1_1                           | TLS 1,1, TLS 1,2 a vyšší          |
| TLS1_2                           | TLS verze 1,2 a vyšší           |


Například nastavení minimální verze nastavení TLS na TLS 1,0 znamená, že váš server umožní připojení z klientů pomocí protokolu TLS 1,0, 1,1 a 1,2 +. Další možností je nastavení na 1,2 znamená, že povolíte připojení jenom z klientů pomocí protokolu TLS 1.2 + a všechna připojení k TLS 1,0 a TLS 1,1 budou odmítnutá.

> [!Note] 
> Ve výchozím nastavení Azure Database for PostgreSQL nevynutila minimální verzi TLS (nastavení `TLSEnforcementDisabled` ).
>
> Jakmile vynucujete minimální verzi protokolu TLS, nemůžete později zakázat vynucení minimální verze.

Informace o tom, jak nastavit nastavení TLS pro váš Azure Database for PostgreSQL samostatný server, najdete v článku [jak nakonfigurovat nastavení TLS](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Podpora šifry pomocí Azure Database for PostgreSQL jednoho serveru

V rámci komunikace SSL/TLS se šifrovací sady ověřují a podporují jenom šifrovací obleky, které můžou komunikovat s Serer databáze. Ověřování šifrovací sady se řídí ve [vrstvě brány](concepts-connectivity-architecture.md#connectivity-architecture) a ne explicitně na samotném uzlu. Pokud šifrovací sady neodpovídají jedné z níže uvedených sad, připojení příchozích klientů se odmítnou.

### <a name="cipher-suite-supported"></a>Podporovaná šifrovací sada

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Další kroky

Přečtěte si různé možnosti připojení aplikací v [knihovně připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).

- Informace o tom, jak [nakonfigurovat TLS](howto-tls-configurations.md)
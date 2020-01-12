---
title: SSL-Azure Database for PostgreSQL – jeden server
description: Pokyny a informace o tom, jak nakonfigurovat připojení SSL pro Azure Database for PostgreSQL pro jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4f7fd3ea1c83e1bf5183aedf4fe894809884414c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903510"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurace připojení SSL v Azure Database for PostgreSQL – jeden server
Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí SSL (Secure Sockets Layer) (SSL). Vynucování připojení SSL mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředníkem" tím, že šifruje datový proud mezi serverem a vaší aplikací.

Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurovaná tak, aby vyžadovala připojení SSL. Pokud klientská aplikace nepodporuje připojení SSL, můžete zakázat vyžadování protokolu SSL. 

## <a name="enforcing-ssl-connections"></a>Vynucování připojení SSL
Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal a CLI je vynucování připojení SSL ve výchozím nastavení povolené. 

Připojovací řetězce, které jsou předem definované v nastavení "připojovací řetězce" na serveru v Azure Portal obsahují také požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

## <a name="configure-enforcement-of-ssl"></a>Konfigurace vynucení SSL
Volitelně můžete zakázat vynucování připojení SSL. Microsoft Azure doporučuje vždycky povolit nastavení **připojení SSL vynutilo** pro zvýšené zabezpečení.

> [!NOTE]
> V současné době je verze TLS podporovaná pro Azure Database for PostgreSQL TLS 1,0, TLS 1,1, TLS 1,2.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Přejděte na server Azure Database for PostgreSQL a klikněte na **zabezpečení připojení**. Pomocí přepínacího tlačítka povolíte nebo zakážete nastavení **Vynutilí připojení SSL** . Potom klikněte na **Uložit**. 

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

Níže je uveden příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Potvrďte, že hodnota předaná `sslrootcert` odpovídá cestě k souboru pro certifikát, který jste uložili.


## <a name="next-steps"></a>Další kroky
Přečtěte si různé možnosti připojení aplikací v [knihovně připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).

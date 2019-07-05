---
title: Konfigurace připojení SSL ve službě Azure Database for PostgreSQL – jeden Server
description: Pokyny a informace o konfiguraci – Azure Database for PostgreSQL – jeden Server a přidružené aplikace pro správné použití připojení SSL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461843"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurace připojení SSL ve službě Azure Database for PostgreSQL – jeden Server
Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací do služby PostgreSQL pomocí vrstvy SSL (Secure Sockets). Vynucení připojení SSL mezi databázovým serverem a klientských aplikací pomáhá chránit před útoky "man-in-the-middle" tím, že šifruje datový proud mezi serverem a vaší aplikace.

Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurované vyžadování připojení SSL. Můžete zakázat, pokud klientská aplikace nepodporuje připojení SSL, které vyžadují protokol SSL. 

## <a name="enforcing-ssl-connections"></a>Vynucení připojení SSL
Pro všechny – Azure Database for PostgreSQL servery poskytované prostřednictvím webu Azure portal a rozhraní příkazového řádku je standardně povolená vynucení připojení SSL. 

Připojovací řetězce, které jsou předem definované v nastavení "Připojovací řetězce" v rámci vašeho serveru na webu Azure Portal, zahrnout požadované parametry pro běžných jazyků pro připojení k vašemu databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, třeba "ssl = true" nebo "sslmode = vyžadují" nebo "sslmode = požadované" a další varianty konfigurací.

## <a name="configure-enforcement-of-ssl"></a>Nakonfigurovat vynucení SSL
Volitelně můžete zakázat vynucení připojení SSL. Microsoft Azure doporučuje vždy povolit **vynucování SSL připojení** nastavení pro zvýšení zabezpečení.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Navštivte web vašeho serveru Azure Database for PostgreSQL a klikněte na tlačítko **zabezpečení připojení**. Povolit nebo zakázat pomocí přepínacího tlačítka **vynucování SSL připojení** nastavení. Potom klikněte na **Uložit**. 

![Zabezpečení připojení – zakázat vynucování SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Toto nastavení můžete ověřit zobrazením **přehled** stránku a podívejte se **stav vynucení SSL** indikátoru.

### <a name="using-azure-cli"></a>Použití Azure CLI
Můžete povolit nebo zakázat **vynucení ssl** pomocí parametru `Enabled` nebo `Disabled` hodnoty v uvedeném pořadí v Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Zkontrolujte vaše aplikace nebo rozhraní framework podporuje připojení SSL
Některé aplikačních architektur, které používají PostgreSQL pro databázi služby není ve výchozím nastavení Povolit protokol SSL během instalace. Pokud váš server PostgreSQL vynucuje připojení SSL, ale aplikace není nakonfigurována pro protokol SSL, aplikace nemusí podařit připojit ke svému databázovému serveru. Další informace o povolení připojení SSL v dokumentaci vaší aplikace.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátů pro připojení SSL
V některých případech může aplikace vyžadují soubor místní certifikát vygenerovaný z důvěryhodné certifikační autority (CA) soubor certifikátu (.cer) se navázat zabezpečené připojení. Certifikát pro připojení k databázi Azure PostgreSQL server je umístěn v https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Stáhněte si soubor certifikátu a uložte ho do upřednostňované umístění. 

### <a name="connect-using-psql"></a>Připojte se pomocí nástroje psql
Následující příklad ukazuje, jak se připojit k serveru PostgreSQL pomocí nástroje příkazového řádku psql. Použití `sslmode=verify-full` nastavení připojovacího řetězce k vynucení ověřování certifikátů SSL. Předání certifikátu místní cesta k souboru `sslrootcert` parametru.

Níže je příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Potvrďte, že hodnota předaná `sslrootcert` odpovídá cesta k souboru certifikátu, který jste uložili.


## <a name="next-steps"></a>Další postup
Projděte si různé možnosti připojení k aplikaci v [připojení knihoven pro službu Azure Database for PostgreSQL](concepts-connection-libraries.md).

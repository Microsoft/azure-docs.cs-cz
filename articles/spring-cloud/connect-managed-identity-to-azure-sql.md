---
title: Použití spravované identity k připojení Azure SQL k Azure jaře cloudové aplikaci
description: Nastavte spravovanou identitu pro připojení Azure SQL k aplikaci pro jarní cloudovou službu Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 3f1cde0bf233c67d02b9b7266ce3b3c8a3696db8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123345"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Použití spravované identity pro připojení Azure SQL Database k aplikaci pro jarní cloudy Azure

**Tento článek se týká:** ✔️ Java

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro cloudovou aplikaci Azure pro jaře a použít ji pro přístup k Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) je inteligentní, škálovatelná služba relační databáze vytvořená pro Cloud. Je vždycky aktuální, s využitím AI a automatizovanými funkcemi, které optimalizují výkon a odolnost. Možnosti služby COMPUTE a škálování na úrovni serveru automaticky škálují prostředky na vyžádání, takže se můžete soustředit na vytváření nových aplikací, aniž byste se museli starat o velikost úložiště nebo správu prostředků.

## <a name="prerequisites"></a>Požadavky
V tomto příkladu se používají následující prostředky.
* Postupujte podle [kurzu JPAu jarních dat](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) , abyste zřídili Azure SQL Database a mohli pracovat s aplikací Java místně.
* Využijte [kurz Azure jarní cloudové služby s přiřazenou správou identit](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) , která vám umožní zřídit cloudovou aplikaci Azure s POVOLENým mi.

## <a name="grant-permission-to-the-managed-identity"></a>Udělení oprávnění spravované identitě
Připojte se k SQL serveru a spusťte následující dotaz SQL:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

<MIName>Postupuje podle pravidla: `<service instance name>/apps/<app name>` , například myspringcloud/Apps/sqldemo. Můžete také zadat dotaz na MIName pomocí Azure CLI:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Konfigurace aplikace Java pro použití spravované identity
Otevřete `src/main/resources/application.properties` soubor a přidejte `Authentication=ActiveDirectoryMSI;` na konec následujícího řádku. Nezapomeňte použít správnou hodnotu proměnné $AZ _DATABASE_NAME.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Sestavení a nasazení aplikace do jarního cloudu Azure
Znovu sestavte aplikaci a nasaďte ji do aplikace pro jarní cloudy Azure zřízené v druhém bodě odrážek v části požadavky. Teď máte aplikaci pro spouštění pružinou, kterou ověřuje spravovaná identita, která používá JPA k ukládání a načítání dat z Azure SQL Database v jarním cloudu Azure.

## <a name="next-steps"></a>Další kroky

* [Přístup k objektu BLOB úložiště se spravovanou identitou v Azure jaře cloudu](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Postup povolení spravované identity přiřazené systémem pro aplikaci Azure jaře Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Další informace o spravovaných identitách pro prostředky Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Ověřování Azure jaře cloudu s Key Vault v akcích GitHubu](./spring-cloud-github-actions-key-vault.md)
---
title: Exportu certifikátů emulátoru DB Cosmos Azure | Microsoft Docs
description: Při vývoji v jazycích a moduly runtime, která nepoužívají Windows Store certifikát budete muset exportovat a spravovat certifikáty SSL. Tento příspěvek poskytuje podrobné pokyny.
services: cosmos-db
documentationcenter: ''
keywords: Emulátor Azure Cosmos DB
author: voellm
manager: kfile
editor: ''
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: tvoellm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 87d453cd544b3e913209f50e4e08b77282efab39
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exportu certifikátů emulátoru DB Cosmos Azure pro použití s Java, Python a Node.js

[**Stáhněte si v emulátoru**](https://aka.ms/cosmosdb-emulator)

Emulátor DB Cosmos Azure poskytuje místní prostředí, které emuluje služby Azure Cosmos DB pro účely vývoje, včetně jeho použití připojení SSL. Tento příspěvek ukazuje, jak k exportu certifikátů SSL pro použití v jazycích a moduly runtime, který nelze integrovat do úložiště certifikátů systému Windows, například Java, která používá vlastní [úložiště certifikátů](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) a Python, který používá [soketu obálky](https://docs.python.org/2/library/ssl.html) a Node.js, která používá [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Další informace o emulátoru v [použití emulátoru DB Cosmos Azure pro vývoj a testování](./local-emulator.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otáčení certifikáty
> * Export certifikátu protokolu SSL
> * Informace o způsobu použití certifikátu v jazyce Java, Python a Node.js

## <a name="certification-rotation"></a>Otočení certifikační

Certifikáty v emulátoru místního DB Cosmos Azure jsou generovány při prvním spuštění emulátor. Existují dva certifikáty. Jeden použité pro připojování k místní emulátoru a jeden pro správu tajných klíčů v emulátoru. Certifikát, který chcete exportovat, je připojení certifikát s popisným názvem "DocumentDBEmulatorCertificate".

Oba certifikáty je možné znovu generovat kliknutím **obnovit Data** jak je uvedeno níže z emulátoru DB Cosmos Azure je spuštěná na hlavním panelu Windows. Máte-li znovu vygenerovat certifikáty a byly nainstalovány do úložiště certifikátů Java nebo je použili jinde je nutné je aktualizovat, jinak aplikace se už připojí k místní emulátor.

![Azure Cosmos DB místní emulátoru obnovení dat](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Tom, jak exportovat certifikát Azure Cosmos DB SSL

1. Spusťte správce certifikátů systému Windows ve spuštění certlm.msc a přejděte do složky-> osobní certifikáty a otevřete certifikát s popisným názvem **DocumentDbEmulatorCertificate**.

    ![Azure Cosmos DB místní emulátoru exportovat krok 1](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Klikněte na **podrobnosti** pak **OK**.

    ![Azure Cosmos DB místní emulátoru exportovat krok 2](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Klikněte na tlačítko **kopírovat do souboru...** .

    ![Azure Cosmos DB místní emulátoru exportovat krok 3](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Klikněte na **Další**.

    ![Azure Cosmos DB místní emulátoru exportovat krok 4](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Klikněte na tlačítko **Ne, neexportovat privátní klíč**, pak klikněte na tlačítko **Další**.

    ![Azure Cosmos DB místní emulátoru exportovat krok 5](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Klikněte na **X.509 s kódováním Base-64 (. CER)** a potom **Další**.

    ![Azure Cosmos DB místní emulátoru exportovat krok 6](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Zadejte název certifikátu. V takovém případě **documentdbemulatorcert** a pak klikněte na **Další**.

    ![Azure Cosmos DB místní emulátoru exportovat krok 7](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Klikněte na **Dokončit**.

    ![Krok 8 exportovat Azure Cosmos DB místní emulátoru](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Postup použití certifikátu v jazyce Java

Při spuštění aplikací v jazyce Java nebo MongoDB aplikací, které používají klienta Java je snazší nainstalujte certifikát do úložiště certifikátů výchozí Java než předávání "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"příznaky. Například zahrnuty [Java ukázkové aplikace](https://localhost:8081/_explorer/index.html) závisí na výchozím úložišti certifikátů.

Postupujte podle pokynů [přidání certifikátu do úložiště certifikátů certifikační Autority Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) pro import certifikátu X.509 do výchozího úložiště certifikátů Java. Uvědomte si, že pracovat v adresáři % JAVA_HOME % při spuštění příkazu keytool.

Jednou "CosmosDBEmulatorCertificate" SSL je nainstalován certifikát pro aplikaci by mohli připojit a používat místní emulátoru DB Cosmos Azure. Pokud budete pokračovat, potíže se můžete použít postup popsaný [ladění připojení protokolem SSL/TLS](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) článku. Je velmi pravděpodobné že certifikát není nainstalovaný do %JAVA_HOME%/jre/lib/security/cacerts úložiště. Pro příklad, pokud máte víc nainstalovaných verzí aplikace Java může pomocí různých cacerts úložiště než ten, který jste aktualizovali.

## <a name="how-to-use-the-certificate-in-python"></a>Postup použití certifikátu v Pythonu

Ve výchozím nastavení [Python SDK(version 2.0.0 or higher)](sql-api-sdk-python.md) pro rozhraní SQL API nebude zkuste a použít certifikát SSL při připojování k místní emulátor. Pokud ale chcete použít ověřování SSL můžete podle příklady v [Python soketu obálky](https://docs.python.org/2/library/ssl.html) dokumentaci.

## <a name="how-to-use-the-certificate-in-nodejs"></a>Použití certifikátu v Node.js

Ve výchozím nastavení [Node.js SDK(version 1.10.1 or higher)](sql-api-sdk-node.md) pro rozhraní SQL API nebude zkuste a použít certifikát SSL při připojování k místní emulátor. Pokud ale chcete použít ověřování SSL můžete podle příklady v [Node.js dokumentaci](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Otočený certifikáty
> * Exportovat certifikát SSL
> * Zjistili, jak pro použití certifikátu v jazyce Java, Python a Node.js

Nyní můžete přejít k vytvořit aktivační událost INSTEAD HTTP funkce Azure s kurzu vstupní vazby Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Vytvoření funkce Azure se vstupem z Azure Cosmos DB](tutorial-functions-http-trigger.md) 

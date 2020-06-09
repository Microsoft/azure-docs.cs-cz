---
title: Export certifikátů emulátoru služby Azure Cosmos DB
description: Při vývoji v jazycích a modulech runtime, které nepoužívají úložiště certifikátů Windows, budete muset exportovat a spravovat certifikáty TLS/SSL. Tento příspěvek obsahuje podrobné pokyny.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.custom: tracking-python
ms.openlocfilehash: 89d91a3c5c8a4209489ed427067a90e7e2d0b880
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561166"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Export certifikátů emulátoru služby Azure Cosmos DB pro použití s Javou, Pythonem a Node.js

[**Stáhnout emulátor**](https://aka.ms/cosmosdb-emulator)

Emulátor Azure Cosmos DB poskytuje místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje, včetně použití připojení TLS. Tento příspěvek ukazuje, jak exportovat certifikáty TLS/SSL pro použití v jazycích a modulech runtime, které neintegrují úložiště certifikátů Windows, jako je Java, která používá vlastní [úložiště certifikátů](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) a Python, které využívá [obálky soketů](https://docs.python.org/2/library/ssl.html) a Node. js, které používá [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Další informace o emulátoru najdete v tématu [Použití emulátoru služby Azure Cosmos DB pro účely vývoje a testování](./local-emulator.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Obměna certifikátů
> * Exportuje se certifikát TLS/SSL.
> * Seznámení s používáním certifikátů v Javě, Pythonu a Node.js

## <a name="certification-rotation"></a>Obměna certifikátů

Certifikáty v místním emulátoru služby Azure Cosmos DB se generují při prvním spuštění emulátoru. Existují dva certifikáty. Jeden slouží k připojování k místnímu emulátoru a jeden ke správě tajných klíčů v rámci emulátoru. Certifikát, který chcete exportovat, je certifikát připojení s popisným názvem DocumentDBEmulatorCertificate.

Oba certifikáty je možné znovu vygenerovat kliknutím na **Resetovat data** v emulátoru služby Azure Cosmos DB spuštěném na hlavním panelu Windows, jak je znázorněno níže. Pokud certifikáty vygenerujete znovu a už dříve jste je nainstalovali do úložiště certifikátů Javy nebo použili jinde, budete je muset aktualizovat, jinak vaše aplikace ztratí připojení k místnímu emulátoru.

![Resetování dat místního emulátoru služby Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Postup exportování Azure Cosmos DB certifikátu TLS/SSL

1. Spuštěním certlm.msc spusťte Správce certifikátů systému Windows, přejděte do složky Osobní > Certifikáty a otevřete certifikát s popisným názvem **DocumentDbEmulatorCertificate**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 1](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Klikněte na **Podrobnosti** a pak na **OK**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 2](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Klikněte na **Kopírovat do souboru**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 3](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Klikněte na **Další**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 4](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Klikněte na **Ne, neexportovat privátní klíč** a pak klikněte na **Další**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 5](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Klikněte na **X.509, kódování Base-64 (CER)** a pak na **Další**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 6](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Zadejte název certifikátu. V tomto případě zadejte **documentdbemulatorcert** a pak klikněte na **Další**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 7](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Klikněte na **Finish** (Dokončit).

    ![Místní emulátor služby Azure Cosmos DB – export, krok 8](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Použití certifikátu v Javě

Při spouštění aplikací v jazyce Java nebo aplikací MongoDB, které používají klienta Java, je snazší nainstalovat certifikát do výchozího úložiště certifikátů Java, než aby bylo možné tyto `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` příznaky předat. Například zahrnutá ukázková aplikace Java ( `https://localhost:8081/_explorer/index.html` ) závisí na výchozím úložišti certifikátů.

Podle pokynů v tématu o [přidání certifikátu do úložiště certifikátů certifikační autority Javy](https://docs.microsoft.com/azure/java-add-certificate-ca-store) importujte certifikát X.509 do výchozího úložiště certifikátů Javy. Mějte na paměti, že při používání nástroje keytool budete pracovat v adresáři %JAVA_HOME%.

Po instalaci certifikátu TLS/SSL CosmosDBEmulatorCertificate by aplikace měla být schopná připojit se k místnímu emulátoru Azure Cosmos DB a používat ho. Pokud se budou i nadále objevovat potíže, měli byste si přečíst článek o [ladění připojení SSL a TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Je velmi pravděpodobné, že certifikát není nainstalovaný v úložišti %JAVA_HOME%/jre/lib/security/cacerts. Například pokud máte více nainstalovaných verzí Javy, vaše aplikace může používat jiné úložiště cacerts, než které jste aktualizovali.

## <a name="how-to-use-the-certificate-in-python"></a>Použití certifikátu v Pythonu

Ve výchozím nastavení se [sada Python SDK (verze 2.0.0 nebo novější)](sql-api-sdk-python.md) pro rozhraní API SQL nepokusí použít certifikát TLS/SSL při připojování k místnímu emulátoru. Pokud ale chcete ověřování pomocí protokolu TLS použít, můžete postupovat podle příkladů v dokumentaci k [obálkám soketu Pythonu](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Použití certifikátu v Node.js

Ve výchozím nastavení se [sada Node. js SDK (verze 1.10.1 nebo vyšší)](sql-api-sdk-node.md) pro rozhraní API SQL nepokusí a při připojování k místnímu emulátoru nepoužije certifikát TLS/SSL. Pokud ale chcete ověřování pomocí protokolu TLS použít, můžete postupovat podle příkladů v [dokumentaci k Node. js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Obměna certifikátů
> * Vyexportoval se certifikát TLS/SSL.
> * Seznámili jste se s používáním certifikátů v Javě, Pythonu a Node.js.

Teď můžete přejít k části Koncepty, která obsahuje další informace o službě Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)

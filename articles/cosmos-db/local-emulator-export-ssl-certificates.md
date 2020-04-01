---
title: Export certifikátů emulátoru služby Azure Cosmos DB
description: Při vývoji v jazycích a runčasech, které nepoužívají úložiště certifikátů windows, budete muset exportovat a spravovat certifikáty TLS/SSL. Tento příspěvek obsahuje podrobné pokyny.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: b4283ea7d500ca038d9f1cade89c772880ece199
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409066"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Export certifikátů emulátoru služby Azure Cosmos DB pro použití s Javou, Pythonem a Node.js

[**Stáhnout emulátor**](https://aka.ms/cosmosdb-emulator)

Emulátor Azure Cosmos DB poskytuje místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje, včetně použití připojení TLS. Tento příspěvek ukazuje, jak exportovat certifikáty TLS/SSL pro použití v jazycích a runčasech, které se neintegrují s úložištěm certifikátů Windows, jako je Java, který používá vlastní [úložiště certifikátů](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) a Python, který používá [socket wrappers](https://docs.python.org/2/library/ssl.html) a Node.js, který používá [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Další informace o emulátoru najdete v tématu [Použití emulátoru služby Azure Cosmos DB pro účely vývoje a testování](./local-emulator.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Obměna certifikátů
> * Export certifikátu TLS/SSL
> * Seznámení s používáním certifikátů v Javě, Pythonu a Node.js

## <a name="certification-rotation"></a>Obměna certifikátů

Certifikáty v místním emulátoru služby Azure Cosmos DB se generují při prvním spuštění emulátoru. Existují dva certifikáty. Jeden slouží k připojování k místnímu emulátoru a jeden ke správě tajných klíčů v rámci emulátoru. Certifikát, který chcete exportovat, je certifikát připojení s popisným názvem DocumentDBEmulatorCertificate.

Oba certifikáty je možné znovu vygenerovat kliknutím na **Resetovat data** v emulátoru služby Azure Cosmos DB spuštěném na hlavním panelu Windows, jak je znázorněno níže. Pokud certifikáty vygenerujete znovu a už dříve jste je nainstalovali do úložiště certifikátů Javy nebo použili jinde, budete je muset aktualizovat, jinak vaše aplikace ztratí připojení k místnímu emulátoru.

![Resetování dat místního emulátoru služby Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Export certifikátu TLS/SSL Azure Cosmos DB

1. Spuštěním certlm.msc spusťte Správce certifikátů systému Windows, přejděte do složky Osobní > Certifikáty a otevřete certifikát s popisným názvem **DocumentDbEmulatorCertificate**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 1](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Klikněte na **Podrobnosti** a pak na **OK**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 2](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Klikněte na **Kopírovat do souboru**.

    ![Místní emulátor služby Azure Cosmos DB – export, krok 3](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Klikněte na **Další**.

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

Při spouštění aplikací java nebo aplikací MongoDB, které používají klienta Java, je `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` jednodušší nainstalovat certifikát do výchozího úložiště certifikátů Java než předávání příznaků. Například zahrnutá [ukázková aplikace Java](https://localhost:8081/_explorer/index.html) závisí na výchozím úložišti certifikátů.

Podle pokynů v tématu o [přidání certifikátu do úložiště certifikátů certifikační autority Javy](https://docs.microsoft.com/azure/java-add-certificate-ca-store) importujte certifikát X.509 do výchozího úložiště certifikátů Javy. Mějte na paměti, že při používání nástroje keytool budete pracovat v adresáři %JAVA_HOME%.

Po instalaci certifikátu TLS/SSL "CosmosDBEmulatorCertificate" by vaše aplikace měla být schopna připojit a používat místní emulátor Azure Cosmos DB. Pokud se budou i nadále objevovat potíže, měli byste si přečíst článek o [ladění připojení SSL a TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). Je velmi pravděpodobné, že certifikát není nainstalovaný v úložišti %JAVA_HOME%/jre/lib/security/cacerts. Například pokud máte více nainstalovaných verzí Javy, vaše aplikace může používat jiné úložiště cacerts, než které jste aktualizovali.

## <a name="how-to-use-the-certificate-in-python"></a>Použití certifikátu v Pythonu

Ve výchozím nastavení [python SDK (verze 2.0.0 nebo vyšší)](sql-api-sdk-python.md) pro SQL API se nepokusí použít certifikát TLS/SSL při připojování k místnímu emulátoru. Pokud však chcete použít ověření TLS, můžete postupovat podle příkladů v dokumentaci [obálky socketu Pythonu.](https://docs.python.org/2/library/ssl.html)

## <a name="how-to-use-the-certificate-in-nodejs"></a>Použití certifikátu v Node.js

Ve výchozím nastavení [node.js SDK (verze 1.10.1 nebo vyšší)](sql-api-sdk-node.md) pro sql api se nepokusí použít certifikát TLS/SSL při připojování k místnímu emulátoru. Pokud však chcete použít ověření TLS, můžete postupovat podle příkladů v [dokumentaci Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste provedli následující:

> [!div class="checklist"]
> * Obměna certifikátů
> * Export certifikátu TLS/SSL
> * Seznámili jste se s používáním certifikátů v Javě, Pythonu a Node.js.

Teď můžete přejít k části Koncepty, která obsahuje další informace o službě Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Nastavitelné úrovně konzistence dat v Azure Cosmos DB](../cosmos-db/consistency-levels.md)

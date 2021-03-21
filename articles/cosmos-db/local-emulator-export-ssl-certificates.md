---
title: Export certifikátů emulátoru služby Azure Cosmos DB
description: Naučte se exportovat certifikát emulátoru Azure Cosmos DB pro použití s aplikacemi Java, Python a Node.js. Certifikáty by se měly exportovat a používat pro jazyky a běhová prostředí, která nepoužívají úložiště certifikátů Windows.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperf-fy21q1
ms.openlocfilehash: 952be09662c2c74f883d63de72bba2b9cb58d0e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553999"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Export certifikátů emulátoru Azure Cosmos DB pro použití s aplikacemi Java, Python a Node.js
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Emulátor služby Azure Cosmos DB zajistí místní prostředí, které emuluje službu Azure Cosmos DB pro účely vývoje. Emulátor Azure Cosmos DB podporuje jenom zabezpečenou komunikaci prostřednictvím připojení TLS.

Certifikáty v Azure Cosmos DB místním emulátoru se generují při prvním spuštění emulátoru. Existují dva certifikáty. Jedna z nich se používá pro připojení k místnímu emulátoru a druhá se používá ke správě výchozího šifrování dat emulátoru v rámci emulátoru. Certifikát, který chcete exportovat, je certifikát připojení s popisným názvem DocumentDBEmulatorCertificate.

Když použijete emulátor pro vývoj aplikací v různých jazycích, jako je Java, Python nebo Node.js, musíte exportovat certifikát emulátoru a naimportovat ho do požadovaného úložiště certifikátů.

Jazyk .NET a modul runtime používají úložiště certifikátů Windows k bezpečnému připojení k Azure Cosmos DB místním emulátoru, když je aplikace spuštěná na hostiteli s operačním systémem Windows. Další jazyky mají vlastní metody správy a použití certifikátů. Java například používá vlastní [úložiště certifikátů](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), Python používá [obálky soketů](https://docs.python.org/2/library/ssl.html)a Node.js používá [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

Tento článek ukazuje, jak exportovat certifikáty TLS/SSL pro použití v různých jazycích a běhová prostředí, která se neintegrují do úložiště certifikátů Windows. Další informace o emulátoru najdete v tématu [Použití emulátoru služby Azure Cosmos DB pro účely vývoje a testování](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Export certifikátu Azure Cosmos DB TLS/SSL

Je potřeba exportovat certifikát emulátoru a úspěšně použít koncový bod emulátoru z jazyků a běhových prostředí, která se neintegrují do úložiště certifikátů Windows. Certifikát můžete exportovat pomocí Správce certifikátů Windows. Pomocí následujících podrobných pokynů exportujte certifikát "DocumentDBEmulatorCertificate" jako soubor X. 509 (. cer) s kódováním BASE-64:

1. Spuštěním certlm.msc spusťte Správce certifikátů systému Windows, přejděte do složky Osobní > Certifikáty a otevřete certifikát s popisným názvem **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 1":::

1. Klikněte na **Podrobnosti** a pak na **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 2":::

1. Klikněte na **Kopírovat do souboru**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 3":::

1. Klikněte na **Next** (Další).

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 4":::

1. Klikněte na **Ne, neexportovat privátní klíč** a pak klikněte na **Další**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 5":::

1. Klikněte na **X.509, kódování Base-64 (CER)** a pak na **Další**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 6":::

1. Zadejte název certifikátu. V tomto případě zadejte **documentdbemulatorcert** a pak klikněte na **Další**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 7":::

1. Klikněte na **Finish** (Dokončit).

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Místní emulátor služby Azure Cosmos DB – export, krok 8":::

## <a name="use-the-certificate-with-java-apps"></a>Použití certifikátu s aplikacemi Java

Při spouštění aplikací Java nebo aplikací MongoDB, které používají klienta založeného na jazyce Java, je snazší nainstalovat certifikát do výchozího úložiště certifikátů Java, než aby se tyto příznaky prošly `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` . Například zahrnutá ukázková aplikace Java ( `https://localhost:8081/_explorer/index.html` ) závisí na výchozím úložišti certifikátů.

Podle pokynů v části [Přidání certifikátu do úložiště certifikátů Java](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm) importujte certifikát X. 509 do výchozího úložiště certifikátů Java. Mějte na paměti, že při spuštění nástroje webtool budete pracovat v adresáři *% JAVA_HOME%* . Po importu certifikátu do úložiště certifikátů se klienti pro SQL a Azure Cosmos DB API pro MongoDB budou moci připojit k emulátoru Azure Cosmos DB.

Případně můžete pomocí následujícího skriptu bash importovat certifikát:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Po instalaci certifikátu TLS/SSL CosmosDBEmulatorCertificate by vaše aplikace měla být schopná připojit se k místnímu emulátoru Azure Cosmos DB a používat ho. Pokud máte nějaké problémy, můžete postupovat podle článku [ladění připojení SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) . Ve většině případů se certifikát nemusí nainstalovat do úložiště *% JAVA_HOME%/JRE/lib/security/cacerts%* . Například pokud máte více nainstalovaných verzí Java, může být v aplikaci použito jiné úložiště cacerts než ta, kterou jste aktualizovali.

## <a name="use-the-certificate-with-python-apps"></a>Použití certifikátu s aplikacemi v Pythonu

Při připojování k emulátoru z aplikací Python je ověřování TLS zakázané. Ve výchozím nastavení se [sada Python SDK (verze 2.0.0 nebo novější)](sql-api-sdk-python.md) pro rozhraní SQL API nepokouší použít certifikát TLS/SSL při připojování k místnímu emulátoru. Pokud ale chcete ověřování pomocí protokolu TLS použít, můžete postupovat podle příkladů v dokumentaci k [obálkám soketu Pythonu](https://docs.python.org/2/library/ssl.html) .

## <a name="how-to-use-the-certificate-in-nodejs"></a>Použití certifikátu v Node.js

Při připojování k emulátoru z Node.js SDK je ověřování TLS zakázané. Ve výchozím nastavení se [ sadaNode.js SDK (verze 1.10.1 nebo novější)](sql-api-sdk-node.md) pro rozhraní SQL API nepokouší použít certifikát TLS/SSL při připojování k místnímu emulátoru. Pokud ale chcete ověřování pomocí protokolu TLS použít, můžete postupovat podle příkladů v [ dokumentaci kNode.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Otočení certifikátů emulátoru

Můžete vynutit opětovné vygenerování certifikátů emulátoru výběrem možnosti **resetovat data** z emulátoru Azure Cosmos DB spuštěného v oznamovací oblasti systému Windows. Všimněte si, že tato akce také vymaže všechna data uložená v místním emulátoru.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Resetování dat místního emulátoru služby Azure Cosmos DB":::

Pokud jste certifikát nainstalovali do úložiště certifikátů Java nebo je používali jinde, budete ho muset znovu naimportovat pomocí aktuálních certifikátů. Vaše aplikace se nemůže připojit k místnímu emulátoru, dokud neaktualizujete certifikáty.

## <a name="next-steps"></a>Další kroky

* [Použití parametrů příkazového řádku a příkazů prostředí PowerShell k řízení emulátoru](emulator-command-line-parameters.md)
* [Ladění problémů s emulátorem](troubleshoot-local-emulator.md)

---
title: Vývoj zabezpečené webové aplikace | Dokumenty společnosti Microsoft
description: Tato jednoduchá ukázková aplikace implementuje osvědčené postupy zabezpečení, které vylepšují vaši aplikaci a stav zabezpečení vaší organizace při vývoji v Azure.
keywords: není k dispozici
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.openlocfilehash: 730e478622da8cd90af1c559e4d0c6fd04151cca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686795"
---
# <a name="develop-a-secure-web-app"></a>Vývoj zabezpečené webové aplikace

Tato ukázka je jednoduchá aplikace Pythonu, která zobrazuje webovou stránku obsahující odkazy na prostředky zabezpečení pro vývoj aplikací v Azure. Aplikace implementuje osvědčené postupy zabezpečení, které vám pomůžou zlepšit vaši aplikaci a stav zabezpečení vaší organizace při vývoji aplikací v Azure.

Měli byste postupovat podle kroků popsaných v tomto článku postupně, abyste zajistili, že součásti aplikace jsou správně nakonfigurovány. Databáze, služba Azure App Service, instance Azure Key Vault a instance Azure Application Gateway závisí na sobě navzájem.

Skripty nasazení nastavují infrastrukturu. Po spuštění skriptů nasazení budete muset provést nějakou ruční konfiguraci na webu Azure Portal, abyste propojili součásti a služby dohromady.

Ukázková aplikace je zaměřena na začátečníky vyvíjející aplikace v Azure, kteří chtějí implementovat bezpečnostní opatření ve svých aplikacích.

Při vývoji a nasazování této aplikace se dozvíte, jak:

- Vytvořte instanci Azure Key Vault, uložte a načtěte z ní tajné klíče.
- Nasaďte Azure Database pro PostgreSQL, nastavte zabezpečená hesla a autorizujte k nim přístup.
- Spusťte kontejner Alpine Linuxu v Azure Web Apps pro Linux a povolte spravované identity pro prostředky Azure.
- Vytvořte a nakonfigurujte instanci Aplikační brány Azure pomocí brány firewall, která používá [sadu pravidel OWASP Top 10](https://coreruleset.org/).
- Povolte šifrování dat při přenosu a v klidovém stavu pomocí služeb Azure.

Po vývoji a nasazení této aplikace budete mít nastavena následující ukázková webová aplikace spolu s konfigurací a bezpečnostními opatřeními, která jsou popsána.

![Ukázková webová aplikace](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architektura

Aplikace je typická n-vrstvá aplikace se třemi vrstvami. Front-end, back-end a databázová vrstva s integrovanými komponentami monitorování a správy tajných klíčů jsou zobrazeny zde:

![Architektura aplikací](./media/secure-web-app/architecture.png)

Architektura se skládá z těchto komponent:

- [Brána aplikací Azure](../../application-gateway/index.yml). Poskytuje bránu a bránu firewall pro naši aplikační architekturu.
- [Azure Web Apps na Linuxu](../../app-service/containers/app-service-linux-intro.md). Poskytuje modul runtime kontejneru pro spuštění aplikace Python v prostředí Linuxu.
- [Azure Key Vault](../../key-vault/index.yml). Ukládá a šifruje tajné kódy naší aplikace a spravuje vytváření zásad přístupu kolem nich.
- [Databáze Azure pro PostgreSQL](https://azure.microsoft.com/services/postgresql/). Bezpečně ukládá data naší aplikace.
- [Azure Security Center](../../security-center/index.yml) a [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Poskytuje monitorování a upozornění na provoz naší aplikace.

## <a name="threat-model"></a>Model hrozby

Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a zajištění, že je zaveden správný plán zmírnění.

Tato ukázka používá [Microsoft Threat Modeling Tool](threat-modeling-tool.md) k implementaci modelování hrozeb pro zabezpečenou ukázkovou aplikaci. Pomocí diagramu komponent a toků dat můžete identifikovat problémy a hrozby v rané fázi procesu vývoje. To šetří čas a peníze později.

Toto je model ohrožení pro ukázkovou aplikaci:

![Model hrozby](./media/secure-web-app/threat-model.png)

Některé ukázkové hrozby a potenciální chyby zabezpečení, které generuje nástroj pro modelování hrozeb, jsou zobrazeny na následujícím snímku obrazovky. Model hrozeb poskytuje přehled o vystaveném povrchu útoku a vyzve vývojáře, aby přemýšleli o tom, jak zmírnit problémy.

![Výstup modelu hrozby](./media/secure-web-app/threat-model-output.png)

Například vkládání SQL v předchozím výstupu modelu hrozeb je zmírněno dezinfekcí uživatelských vstupů a použitím uložených funkcí v Azure Database pro PostgreSQL. Toto zmírnění zabraňuje svévolné provádění dotazů během čtení a zápisy dat.

Vývojáři zlepšují celkové zabezpečení systému tím, že zmírňují každou hrozbu ve výstupu modelu hrozeb.

## <a name="deployment"></a>Nasazení

Následující možnosti umožňují spouštět Linux ve službě Azure App Service:

- Vyberte kontejner ze seznamu předem sestavených kontejnerů Microsoftu v Azure, které byly vytvořeny s podpůrnými technologiemi (Python, Ruby, PHP, Java, Node.js, .NET Core).
- Použijte kontejner na zakázku. Vyberte vlastní registry kontejnerů jako zdroj bitové kopie a nastavujte na mnoha dostupných technologiích, které podporují protokol HTTP.

V tomto příkladu spustíte skript nasazení, který nasadí webovou aplikaci do služby App Service a vytvoří prostředky.

Aplikace může používat různé modely nasazení uvedené níže:

![Diagram toku dat nasazení](./media/secure-web-app/deployment.png)

Existuje mnoho způsobů nasazení aplikací v Azure, včetně:

- Šablony Azure Resource Manageru
- PowerShell
- Azure CLI
- portál Azure
- Azure DevOps

Tato aplikace používá:

- [Docker](https://docs.docker.com/) k vytvoření a sestavení iimages kontejneru.
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pro nasazení.
- [Docker Hub](https://hub.docker.com/) jako registr kontejnerů.

## <a name="security-considerations"></a>Důležité informace o zabezpečení

### <a name="network"></a>Síť

Ukázková aplikace používá end-to-end TLS/SSL šifrování pro příchozí data proudící do a ze sítě. Brána je konfigurována s certifikátem podepsaným svým držitelem.
> [!IMPORTANT]
> V této demonstraci se používá certifikát podepsaný svým držitelem. V provozním prostředí byste měli získat certifikáty od ověřené certifikační autority (CA).

Brána firewall aplikace také kontroluje příchozí provoz a upozorní správce, když je zjištěn škodlivý provoz v síťovém provozu.
Application Gateway zmírňuje možnost hrozby vkládání DDoS a SQL zjištěné v modelu hrozeb.

### <a name="identity"></a>Identita

Pro přihlášení k portálu, ukázkové aplikace používá vícefaktorové ověřování pro Azure Active Directory (Azure AD) správci, kteří jsou přiřazen přístup k prostředkům.
Ukázková aplikace používá spravované identity získat oprávnění ke čtení a načtení tajných klíčů z Azure Key Vault, zajištění aplikace není nutné pevný kód přihlašovací údaje a tokeny číst tajné klíče. Azure AD automaticky vytvoří instanční objekty, které aplikace potřebuje ke čtení a upravuje tajné klíče při použití spravovaných identit.

Spravované identity pro prostředky Azure a MFA ztěžují protivníkům získat oprávnění a eskalovat jejich oprávnění v systému. Tato hrozba byla zdůrazněna v modelu hrozby.
Aplikace používá OAuth, který umožňuje uživatelům registrovaným v aplikaci OAuth přihlásit se k aplikaci.

### <a name="storage"></a>Storage

Data v databázi PostgreSQL se automaticky šifrují v klidovém stavu službou Azure Database for PostgreSQL. Databáze autorizuje IP adresy služby App Service, takže k prostředkům databáze má přístup ke správným ověřovacím přihlašovacím údajům je pouze nasazená webová aplikace App Service.

### <a name="logging-and-auditing"></a>Protokolování a auditování

Aplikace implementuje protokolování pomocí Application Insights ke sledování metrik, protokolů a výjimek, ke kterým dochází. Toto protokolování poskytuje dostatek metadat aplikace informovat vývojáře a členy provozního týmu o stavu aplikace. Poskytuje také dostatek dat k zpětnému odkupu v případě bezpečnostních incidentů.

## <a name="cost-considerations"></a>Důležité informace o nákladech

Pokud ještě nemáte účet Azure, můžete si vytvořit bezplatný účet. Na [stránce bezplatného účtu](https://azure.microsoft.com/free/) můžete začít, zjistit, co můžete dělat s bezplatným účtem Azure, a zjistit, které produkty jsou zdarma po dobu 12 měsíců.

Chcete-li nasadit prostředky v ukázkové aplikaci s funkcemi zabezpečení, musíte zaplatit za některé prémiové funkce. Vzhledem k tomu, že aplikace se škáluje a bezplatné úrovně a zkušební verze nabízené Azure je třeba upgradovat tak, aby splňovaly požadavky aplikací, vaše náklady se mohou zvýšit. K odhadu nákladů použijte [cenovou kalkulačku](https://azure.microsoft.com/pricing/calculator/) Azure.

## <a name="deploy-the-solution"></a>Nasazení řešení

### <a name="prerequisites"></a>Požadavky

Chcete-li aplikaci zprovoznit, je třeba nainstalovat tyto nástroje:

- Editor kódu pro úpravu a zobrazení kódu aplikace. [Visual Studio Code](https://code.visualstudio.com/) je open source možnost.
- [Azure CLI](/cli/azure/install-azure-cli) ve vývojovém počítači.
- [Git](https://git-scm.com/) ve vašem systému. Git se používá k místnímu klonování zdrojového kódu.
- [jq](https://stedolan.github.io/jq/), unixový nástroj pro dotazování JSON uživatelsky přívětivým způsobem.

K nasazení prostředků ukázkové aplikace potřebujete předplatné Azure. Pokud nemáte předplatné Azure, můžete [si vytvořit bezplatný účet](https://azure.microsoft.com/free/) pro testování ukázkové aplikace.

Po instalaci těchto nástrojů jste připraveni nasadit aplikaci v Azure.

### <a name="environment-setup"></a>Nastavení prostředí

Spusťte skripty nasazení pro nastavení prostředí a předplatného:

1. Chcete-li klonovat úložiště zdrojového kódu, použijte tento příkaz Git:

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. Chcete-li se přesunout do adresáře, použijte tento příkaz:

   ```shell
   cd tutorial-project/scripts
   ```

3. Ve složce skriptů jsou soubory, které jsou specifické pro platformu, kterou používáte (Windows nebo Linux). Při instalaci azure cli už bylo nainstalované, přihlaste se k účtu Azure na příkazovém řádku spuštěním tohoto příkazu Azure CLI:

   ```azurecli-interactive
   az login
   ```

Prohlížeč se otevře, přihlaste se pomocí svých přihlašovacích údajů. Po přihlášení můžete začít nasazovat prostředky z příkazového řádku.

Skripty `deploy-powershell.ps1` nasazení `deploy-bash.sh` a obsahují kód, který nasazuje celou aplikaci.
Nasazení řešení:

1. Pokud jste v PowerShellu, `deploy-powershell.ps1` spusťte soubor zadáním `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` nahrazení názvu oblasti a skupiny prostředků vhodnými oblastmi Azure a názvem pro skupinu prostředků
2. Pokud jste na Linuxu `deploy-bash.sh` spustit `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`soubor zadáním , budete muset vytvořit soubor spustitelný zadáním`chmod +x deploy-bash.sh`

Následující příklady představují úryvky klíčových součástí. Příklady můžete nasadit jednotlivě nebo se zbývajícími součástmi spuštěním nasadit soubory.

### <a name="implementation-guidance"></a>Prováděcí pokyny

Skript nasazení je jeden skript, který lze rozdělit do čtyř fází. Každá fáze nasazuje a konfiguruje prostředek Azure, který je v [diagramu architektury](#architecture).

Čtyři fáze jsou:

- Nasazení trezoru klíčů Azure.
- Nasazení databáze Azure pro PostgreSQL.
- Nasaďte Azure Web Apps na Linuxu.
- Nasazení brány aplikace pomocí brány firewall webových aplikací

Každá fáze vychází z předchozí ho pomocí konfigurace z dříve nasazených prostředků.

Chcete-li dokončit kroky implementace, ujistěte se, že jste nainstalovali nástroje uvedené v části [Požadavky](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Nasazení trezoru klíčů Azure

V této části vytvoříte a nasadíte instanci Azure Key Vault, která se používá k ukládání tajných kódů a certifikátů.

Po dokončení nasazení máte instanci Azure Key Vault nasazenou v Azure.

Nasazení úložiště klíčů Azure pomocí azure cli:

1. Deklarujte proměnné pro Azure Key Vault.
2. Zaregistrujte zprostředkovatele Azure Key Vault.
3. Vytvořte skupinu prostředků pro instanci.
4. Vytvořte instanci Azure Key Vault ve skupině prostředků vytvořené v kroku 3.

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

Je osvědčeným postupem používat spravované identity pro prostředky Azure v aplikacích, které používají Key Vault pro přístup k prostředkům. Stav zabezpečení se zvyšuje, když přístupové klíče k trezoru klíčů nejsou uloženy v kódu nebo v konfiguraci.

#### <a name="deploy-azure-database-for-postgresql"></a>Nasazení databáze Azure pro PostgreSQL

Azure Database for PostgreSQL funguje následujícím způsobem, nejprve vytvořte databázový server a pak vytvořte databázi, na které chcete uložit schéma a data.

Po dokončení nasazení máte v Azure spuštěný postgresql server a databázi.

Nasazení Azure Database pro PostgreSQL pomocí Azure CLI:

1. Otevřete terminál pomocí azure CLI a nastavení předplatného Azure.
2. Vygenerujte kombinaci zabezpečeného uživatelského jména a hesla, která se používá pro přístup k databázi. (Ty by měly být uloženy v Azure Key Vault pro aplikace, které je používají.)
3. Vytvořte instanci serveru PostgreSQL.
4. Vytvořte databázi na instanci serveru, kterou jste vytvořili v kroku 3.
5. Spusťte skripty PostgreSQL na instanci PostgreSQL.

Níže uvedený kód závisí na PGUSERNAME a PGPASSWORD tajemství uložené v Azure KeyVault z nasazení KeyVault výše.

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

Po nasazení databáze je potřeba uložit jeho přihlašovací údaje a připojovací řetězec v Azure Key Vault.
Ve složce skripty je `functions.sql` soubor, který obsahuje kód PL/pgSQL, který při spuštění vytváří uložené funkce. Spuštění tohoto souboru parametrizuje vstupy k omezení vkládání SQL.

PostgreSQL je dodáván s `psql` nástrojem s názvem, který se používá pro připojení k databázi. Chcete-li spustit `functions.sql`, musíte se připojit k azure databáze pro postgreSQL instance z místního počítače a spustit ji odtud. Instalace psql nástroje je součástí výchozí instalace postgreSQL na každém operačním systému.
Další informace naleznete v [dokumentaci k psql](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell `psql` také obsahuje nástroj. Cloud Shell můžete použít přímo z portálu Azure výběrem ikony cloudového prostředí.

Chcete-li povolit vzdálený přístup k instanci PostgreSQL, musíte autorizovat IP adresu v PostgreSQL.
Tento přístup povolíte tak, že přejdete na kartu **Zabezpečení připojení,** vyberete **možnost Přidat IP klienta**a uložíte nové nastavení.

![Autorizovat IP adresu klienta](./media/secure-web-app/add-client-ip-postgres.png)

Pokud používáte Cloud Shell místo místního nástroje pSQL, vyberte **Povolit přístup ke službám Azure** a změňte jeho hodnotu **na ZAPNUTO,** abyste mohli mít přístup ke cloudovému prostředí.

Pak se připojte k instanci spuštěním příkazu níže psql s parametry připojovacího řetězce na kartě **Připojovací řetězce** instance PostgreSQL na webu Azure Portal.
Nahraďte prázdné závorky parametry z okna připojovacího řetězce databáze a heslo s heslem z trezoru klíčů Azure.

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Spusťte následující PL/pgSQL skript poté, co se ujistěte, že jste připojeni k databázi. Skript vytvoří uložené funkce používané k vložení dat do databáze.

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

Další informace o nastavení ověření TLS a Certifikační autority (CA) pro PostgreSQL najdete [v tématu Konfigurace připojení TLS v Azure Database for PostgreSQL](/azure/postgresql/concepts-ssl-connection-security).

Kořenový certifikát je součástí kontejneru. K získání certifikátu jsou podniknuty tyto kroky:

1. Stáhněte soubor certifikátu z [certifikační autority](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Stáhněte a nainstalujte OpenSSL do počítače](/azure/postgresql/concepts-ssl-connection-security).
3. Dekódujte soubor certifikátu:

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Přečtěte si více o tom, jak nakonfigurovat zabezpečení TLS pro PostgreSQL zde [Konfigurace zabezpečení připojení TLS](/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Nasazení Webových aplikací Azure na Linuxu

Služby Linuxu můžete snadno vytvářet nad Azure App Service, protože Azure poskytuje sadu předem vytvořených kontejnerů a ibi pro široce používané jazyky, jako je Python, Ruby, C# a Java. Azure také podporuje vlastní kontejnery, které můžou prakticky všechny programovací jazyky spouštět na platformě Azure App Service.

Naváděná aplikace je jednoduchá aplikace Pythonu, která běží na nejnovější distribuci Ubuntu Linux. Připojuje se k instancím Azure Key Vault a PostgreSQL, které byly vytvořeny v předchozích částech pro správu přihlašovacích údajů a ukládání dat.

Následující soubor Dockeru je k dispozici v kořenové složce aplikace:

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

Výše uvedený soubor Dockerfile se používá k vytvoření kontejneru, `mcr.microsoft.com/samples/basic-linux-app`který je hostovaný v registru kontejnerů Azure na adrese .

Kód níže:

1. Deklaruje proměnné a názvy pro instanci služby App Service.
2. Vytvoří skupinu prostředků pro plán služby App Service.
3. Zřídí azure web apps na linuxové kontejnery instance.
4. Umožňuje protokolování pro kontejner webové aplikace.
5. Nastaví některé konfigurace aplikací v nastavení aplikace kontejneru.

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

Tento skript vytvoří přiřazenou identitu pro instanci služby App Service, kterou lze použít s MSI k interakci s trezorem klíčů Azure bez tajných kódů pevného kódování v kódu nebo konfiguraci.

Přejděte na instanci Azure Key Vault na portálu a autorizujte přiřazenou identitu na kartě zásad přístupu. **Add new access policy** V **části Vybrat hlavní objekt**vyhledejte název aplikace, který je podobný názvu vytvořené instance služby App Service.
Instanční objekt připojený k aplikaci by měl být viditelný. Vyberte ji a uložte stránku zásad přístupu, jak je znázorněno na následujícím snímku obrazovky.

Vzhledem k tomu, že aplikace potřebuje pouze načíst klíče, vyberte **získat** oprávnění v možnostech tajných klíčů, povolení přístupu při současném snížení udělených oprávnění.

![Zásady přístupu k trezoru klíčů](./media/secure-web-app/kv-access-policy.png)

*Vytvoření zásad přístupu trezoru klíčů*

Uložte zásady přístupu a pak uložte novou změnu na kartě **Zásady přístupu** a aktualizujte zásady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Nasazení aplikační brány s povolenou bránou firewall webových aplikací

Ve webových aplikacích se nedoporučuje vystavovat služby přímo vnějšímu světu na internetu.
Pravidla vyrovnávání zatížení a brány firewall poskytují větší zabezpečení a kontrolu nad příchozím provozem a pomáhají vám je spravovat.

Nasazení instance aplikační brány:

1. Vytvořte skupinu prostředků pro použití aplikační brány.
2. Zřízení virtuální sítě připojit k bráně.
3. Vytvořte podsíť pro bránu ve virtuální síti.
4. Zřídit veřejnou IP adresu.
5. Zřízení brány aplikace.
6. Povolte bránu firewall webových aplikací na bráně.

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

Předchozí skript:

1. Vytvoří nový certifikát podepsaný svým držitelem v Azure.
2. Stáhne certifikát podepsaný svým držitelem jako soubor s kódováním base64.
3. Vygeneruje heslo pro certifikát podepsaný svým držitelem.
4. Exportuje certifikát jako soubor PFX podepsaný heslem.
5. Ukládá heslo certifikátu v Azure Key Vault.

Tato část nasazuje aplikační bránu:

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

Po dokončení nasazení máte aplikační bránu s povolenou bránou firewall webové aplikace.

Instance brány zpřístupňuje port 443 pro protokol HTTPS. Tato konfigurace zajišťuje, že naše aplikace je přístupná pouze na portu 443 prostřednictvím protokolu HTTPS.

Blokování nepoužívaných portů a omezení expozice povrchu útoku je osvědčeným postupem zabezpečení.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Přidání skupin zabezpečení sítě do instance služby App Service

Instance služby App Service lze integrovat s virtuálními sítěmi. Tato integrace umožňuje jejich konfiguraci pomocí zásad skupiny zabezpečení sítě, které spravují příchozí a odchozí provoz aplikace.

1. Chcete-li tuto funkci povolit, vyberte v okně instance služby Azure App v části **Nastavení** **položku Networking**. V pravém podokně v části **Integrace virtuální sítě**vyberte Klepnutím **sem nakonfigurujete**.

   ![Nová integrace virtuální sítě](./media/secure-web-app/app-vnet-menu.png)

    *Nová integrace virtuální sítě pro službu App Service*

1. Na další stránce vyberte **Přidat virtuální síť (náhled).**

1. V další nabídce vyberte virtuální síť vytvořenou `hello-vnet`v nasazení, které začíná na . Můžete buď vytvořit novou podsíť, nebo vybrat existující síť.
   V takovém případě vytvořte novou podsíť. Nastavte **rozsah Adresa** na **10.0.3.0/24** a pojmenujte **podsíť podsítě podsítě**.

   ![Konfigurace virtuální sítě služby App Service](./media/secure-web-app/app-vnet-config.png)

    *Konfigurace virtuální sítě pro službu App Service*

Teď, když jste povolili integraci virtuální sítě, můžete do naší aplikace přidat skupiny zabezpečení sítě.

1. Použijte vyhledávací pole, vyhledejte **skupiny zabezpečení sítě**. Ve výsledcích vyberte **skupiny zabezpečení sítě.**

    ![Hledání skupin zabezpečení sítě](./media/secure-web-app/nsg-search-menu.png)

    *Hledání skupin zabezpečení sítě*

2. V další nabídce vyberte **Přidat**. Zadejte **název** skupiny nsg a **skupiny prostředků,** ve které by měl být umístěn. Tento soubor nsg bude použit v podsíti aplikační brány.

    ![Vytvoření nSG](./media/secure-web-app/nsg-create-new.png)

    *Vytvoření nSG*

3. Po vytvoření souboru nsg vyberte jej. V okně vyberte v části **Nastavení** **pravidla příchozího zabezpečení**. Nakonfigurujte tato nastavení tak, aby umožňovala připojení přicházející do brány aplikace přes port 443.

   ![Konfigurace sítě zabezpečení sítě](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurace sítě zabezpečení sítě*

4. V odchozích pravidlech pro službu nsg brány přidejte pravidlo, které umožňuje odchozí připojení k `AppService`instanci služby App Service vytvořením pravidla, které cílí na značku služby :

   ![Přidání odchozích pravidel pro zákon o neplnění pravidel silničního provozu](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Přidání odchozích pravidel pro zákon o neplnění pravidel silničního provozu*

    Přidejte další odchozí pravidlo, které povoluje bráně odesílat odchozí pravidla do virtuální sítě.

   ![Přidání dalšího odchozího pravidla](./media/secure-web-app/nsg-outbound-vnet.png)

    *Přidání dalšího odchozího pravidla*

5. V okně podsítí skupiny nsg vyberte **Přidružit**, vyberte virtuální síť vytvořenou v nasazení a vyberte podsíť brány s názvem **gw-podsíť**. Skupina nsg se použije v podsíti.

6. Vytvořte jiný soubor služeb nsg jako v předchozím kroku, tentokrát pro instanci služby App Service. Dejte mu jméno. Přidejte příchozí pravidlo pro port 443 stejně jako pro soubor NSG aplikační brány.

   Pokud máte instanci služby App Service nasazenou v instanci prostředí služby App Service, což není případ této aplikace, můžete přidat příchozí pravidla, která povolí sondy Azure Service Health otevřením portů 454-455 na příchozích skupinách zabezpečení vašeho nsg služby App Service. Zde je konfigurace:

   ![Přidání pravidel pro sondy stavu služby Azure](./media/secure-web-app/nsg-create-healthprobes.png)

    *Přidání pravidel pro sondy stavu služby Azure (jenom prostředí služby App Service)*

7. V odchozích bezpečnostních pravidlech vytvořte nové pravidlo odchozího zabezpečení, které umožňuje instanci služby App Service komunikovat s databází PostgreSQL. Nakonfigurujte ji takto:

   ![Pravidlo pro povolení odchozích připojení PostgreSQL](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Přidání pravidla pro povolení odchozích připojení PostgreSQL*

Chcete-li omezit prostor pro útok, upravte nastavení sítě služby App Service tak, aby přístup k aplikaci umožňovala pouze aplikační bráně.
Můžete to provést tak, že přejdete na kartu síť služby App Service, vyberete kartu **Omezení IP** a vytvoříte pravidlo povolit, které umožňuje přímo přistupovat ke službě pouze ip adresy brány aplikace.

IP adresu brány můžete načíst z její stránky s přehledem. Na kartě **CIDR IP adresy** zadejte ADRESU `<GATEWAY_IP_ADDRESS>/32`IP v tomto formátu: .

![Povolit pouze bránu](./media/secure-web-app/app-allow-gw-only.png)

*Povolit přístup ke službě App Service pouze IP bráně*

#### <a name="implement-azure-active-directory-oauth"></a>Implementace služby Azure Active Directory OAuth

Dokumenty Azure distribuované na stránce ukázkové webové aplikace jsou prostředky v naší aplikaci, které mohou potřebovat ochranu. Azure Active Directory (Azure AD) můžete použít k implementaci ověřování pro webové, desktopové a mobilní aplikace pomocí různých toků ověřování.
Aplikace používá **přihlášení s Microsoft**, který umožňuje aplikaci číst profily uživatelů, kteří byli přidáni do našeho seznamu uživatelů Azure AD s jedním tenantem.

Na webu Azure Portal nakonfigurujte aplikaci tak, aby používala požadovaná pověření:

1. Vyberte **Službu Azure Active Directory**nebo ji vyhledejte pomocí vyhledávacího pole.

2. Vyberte **novou registraci**:

   ![Vytvoření registrace](./media/secure-web-app/ad-auth-create.png)

   *Vytvoření registrace aplikace Azure AD*

3. Na další stránce zadejte název aplikace. V části **Podporované typy účtů**vyberte možnost Účty pouze v tomto **organizačním adresáři**.
    V části **Identifikátor URI přesměrování**zadejte základní doménu, na které bude aplikace spuštěna, plus doménu s koncovým bodem tokenu. Například: *GATEWAY_HASH*.cloudapp.net/token.

   ![Konfigurace registrace aplikace Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Konfigurace registrace aplikace Azure AD*

4. Zobrazí se obrazovka s registrovanou aplikací a jejími informacemi. Tyto informace je potřeba přidat do instance Azure Key Vault.
   1. Zkopírujte ID aplikace (klienta) a `CLIENTID`uložte ji do trezoru klíčů jako .
   2. Zkopírujte identifikátor URI přesměrování, který jste zadali `REDIRECTURI`v předchozím kroku, a uložte jej jako .
   3. Zkopírujte název výchozího adresáře Azure AD, který má *název*formátu `TENANT`.microsoftonline.com, a uložte jej do trezoru klíčů jako .
   4. Přejděte na kartu **Certifikáty & tajných kódů** aplikace Azure AD, kterou jste vytvořili dříve, a vyberte **Nový tajný klíč klienta**, jak je znázorněno na následujícím snímku obrazovky. Nastavte datum vypršení platnosti a zkopírujte vygenerovanou hodnotu a uložte ji do trezoru klíčů jako `CLIENTSECRET`.

      ![Tajný klíč autorizace Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Tajný klíč autorizace Azure AD*

   5. Vygenerujte zabezpečený náhodný tajný klíč pomocí libovolného nástroje příkazového řádku/online. Uložte jej do `FLASKSECRETKEY`trezoru klíčů jako . Aplikační rámec používá tento klíč k vytvoření relací.
        Informace o generování tajného klíče naleznete v tématu [Flask Sessions](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Po konfiguraci přihlášení, budete muset přidat uživatele do propojení Azure AD, aby jim umožnilo přihlásit se k prostředku. Pokud je chcete přidat, přejděte na kartu **Uživatelé** ve službě Azure AD, vyberte **Všichni uživatelé**a pak vyberte **Nový uživatel** nebo Nový uživatel **typu Host**. Pro testování můžete přidat uživatele typu Host a pozvat uživatele do adresáře. Nebo můžete přidat nového uživatele, pokud byla ověřena doména, ve které aplikace běží. V tomto příkladu lze zaregistrovat pouze uživatele registrované v tenantovi Azure AD pro přístup. Informace o přístupu k víceklientské přihlášení naleznete v dokumentaci.

   ![Přidání uživatelů do výchozí domény](./media/secure-web-app/ad-auth-add-user.png)

   *Přidání uživatelů do výchozí domény Služby Azure Active Directory*

Po přidání konfigurace Azure AD a tajných kódů do trezoru klíčů, uživatelé mohou být ověřeny do aplikace pomocí azure oauth ověřování.
V kódu aplikace to zpracovává Azure Active Directory Authentication Library (ADAL).

Poté, co jsou tajné klíče v trezoru klíčů a aplikace má přístup k tajným kódům a\/databázi, aplikační služba může být dosaženo prostřednictvím adresy URL aplikace brány (https: /GATEWAY_HASH.cloudapp.net), které můžete získat z jeho blade.

Pokud při přihlášení k Azure AD, zobrazí se chyba, která říká, že "Uživatel není registrován v adresáři, který se pokoušíte přihlásit do" je třeba přidat uživatele. Chcete-li přidat uživatele, přejděte na kartu **Uživatelé** ve službě Azure AD a přidejte uživatele ručně zadáním jeho podrobností nebo pozváním uživatele zadáním jeho e-mailové adresy jako uživatele typu Host do služby Azure AD v okně **Pozvat hosta.**

#### <a name="deploy-application-insights"></a>Nasazení služby Application Insights
Teď, když je aplikace nasazená a funkční, musíte zpracovat chyby, ke kterým dochází v rámci aplikace spolu s protokolováním a shromažďováním dat trasování.
Shromažďování dat protokolování a trasování poskytuje zobrazení událostí auditu, ke kterým dochází v aplikaci.

Application Insights je služba, která shromažďuje protokoly, které mohou být generovány uživateli nebo systémem.

Vytvoření instance Application Insights:

1. Vyhledejte **přehledy aplikací** pomocí vyhledávacího pole na webu Azure Portal.
2. Vyberte **Application Insights**. Zadejte podrobnosti zde uvedené k vytvoření instance.

   ![Vytvoření instance Application Insights](./media/secure-web-app/app-insights-data.png)

Po dokončení nasazení máte instanci Application Insights.

Po vytvoření instance Applications Insights, musíte aplikaci seznámit s instrumentace klíč, který umožňuje odesílat protokoly do cloudu. To provést načtením application insights klíč a jeho použití v rámci knihovny aplikací, které Azure poskytuje pro Application Insights. Osvědčeným postupem je ukládání klíčů a tajných klíčů v azure key vault uchovávat je v bezpečí.

Pro základní ukázkové aplikace, po vytvoření aplikace Insights instance, je třeba, aby aplikace vědomi instrumentace klíč, který umožňuje odesílat protokoly do cloudu.
V trezoru `APPINSIGHTSKEY` klíčů nastavte tajný klíč a nastavte jeho hodnotu jako klíč instrumentace. To umožňuje aplikaci odesílat protokoly a metriky do Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementace vícefaktorového ověřování pro Azure Active Directory

Správci musí zajistit, aby byly chráněny účty předplatného na portálu. Předplatné je zranitelné vůči útokům, protože spravuje prostředky, které jste vytvořili. Chcete-li předplatné chránit, povolte vícefaktorové ověřování na kartě **Azure Active Directory** předplatného.

Azure AD funguje na základě zásad, které se používají pro uživatele nebo skupiny uživatelů, které odpovídají určitým kritériím.
Azure vytvoří výchozí zásadu určující, že správci potřebují dvoufaktorové ověřování pro přihlášení k portálu.
Po povolení této zásady se můžete zobrazit výzva k odhlášení a opětovnému přihlášení k portálu Azure.

Povolení vícefaktorové registrace pro přihlášení správce:

1. Přejděte na kartu **Azure Active Directory** na webu Azure Portal
2. V kategorii zabezpečení vyberte podmíněný přístup. Zobrazí se tato obrazovka:

   ![Podmíněný přístup – zásady](./media/secure-web-app/ad-mfa-conditional-add.png)

Pokud nemůžete vytvořit novou zásadu:

1. Přejděte na kartu **Vícefaktorové.**
2. Vyberte bezplatný **zkušební** odkaz Azure AD Premium a přihlaste se k odběru bezplatné zkušební verze.

   ![Bezplatná zkušební verze Azure AD Premium](./media/secure-web-app/ad-trial-premium.png)

Vraťte se na obrazovku podmíněného přístupu.

1. Vyberte novou kartu zásad.
2. Zadejte název zásady.
3. Vyberte uživatele nebo skupiny, pro které chcete povolit vícefaktorové povolení.
4. V části **Access controls**vyberte kartu **Grant** a pak vyberte **Vyžadovat vícefaktorové ověřování** (a další nastavení, pokud chcete).

   ![Vyžadování MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Zásadu můžete povolit zaškrtnutím políčka v horní části obrazovky nebo na kartě **Podmíněný přístup.** Pokud je zásada povolena, uživatelé potřebují vícefaktorové povolení k přihlášení k portálu.

Existuje zásada směrného plánu, která vyžaduje vícefaktorové zabezpečení pro všechny správce Azure. Můžete ji okamžitě povolit na portálu. Povolení této zásady může zneplatnit aktuální relaci a přinutit vás znovu přihlásit.

Pokud zásady směrného plánu nejsou povoleny:

1. Vyberte **vyžadovat vícefaktorové finanční správy pro správce**.
2. Vyberte **možnost Použít zásady okamžitě**.

   ![Vyberte okamžitě použít zásady](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Monitorování aplikací a prostředků pomocí Azure Sentinelu

Jak aplikace roste, je obtížné agregovat všechny signály zabezpečení a metriky přijaté z prostředků a učinit je užitečné způsobem orientovaným na akci.

Azure Sentinel je navržený tak, aby shromažďoval data, zjišťoval možné typy hrozeb a poskytoval přehled o incidentech zabezpečení.
Zatímco čeká na ruční zásah, Azure Sentinel se může spolehnout na předem napsané playbooky k zahájení výstrah a procesů správy incidentů.

Ukázková aplikace se skládá z několika prostředků, které azure sentinel můžete sledovat.
Chcete-li nastavit Azure Sentinel, musíte nejprve vytvořit pracovní prostor Analýzy protokolů, který ukládá všechna data shromážděná z různých prostředků.

Vytvoření tohoto pracovního prostoru:

1. Ve vyhledávacím poli na webu Azure Portal vyhledejte **službu Log Analytics**. Vyberte **pracovní prostory Analýzy protokolů**.

   ![Hledání pracovních prostorů Analýzy protokolů](./media/secure-web-app/sentinel-log-analytics.png)

    *Hledání pracovních prostorů Analýzy protokolů*

2. Na další stránce vyberte **Přidat** a zadejte název, skupinu prostředků a umístění pracovního prostoru.
   ![Vytvoření pracovního prostoru služby Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Vytvoření pracovního prostoru služby Log Analytics*

3. Pomocí vyhledávacího pole vyhledejte **Azure Sentinel**.

   ![Vyhledání textu Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *Vyhledání textu Azure Sentinel*

4. Vyberte **Přidat** a pak vyberte pracovní prostor Analýzy protokolů, který jste vytvořili dříve.

   ![Přidání pracovního prostoru Analýzy protokolů](./media/secure-web-app/sentinel-workspace-add.png)

    *Přidání pracovního prostoru Analýzy protokolů*

5. Na stránce **Azure Sentinel – datové konektory** v části **Konfigurace**vyberte **Datové konektory**. Zobrazí se pole služeb Azure, které můžete propojit s instancí úložiště Log Analytics pro analýzu v Azure Sentinelu.

   ![Datové konektory Log Analytics](./media/secure-web-app/sentinel-connectors.png)

    *Přidání datového konektoru do Azure Sentinelu*

   Chcete-li například připojit aplikační bránu, postupujte takto:

   1. Otevřete okno instance Aplikační brány Azure.
   2. V části **Monitorování** vyberte **Nastavení diagnostiky**.
   3. Vyberte **Přidat diagnostické nastavení**.

      ![Přidat diagnostiku aplikační brány](./media/secure-web-app/sentinel-gateway-connector.png)

      *Přidat diagnostiku aplikační brány*

   4. Na stránce **Nastavení diagnostiky** vyberte pracovní prostor Log Analytics, který jste vytvořili, a pak vyberte všechny metriky, které chcete shromáždit a odeslat do Azure Sentinelu. Vyberte **Uložit**.

        ![Nastavení konektoru Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Nastavení konektoru Azure Sentinel*

  Metriky z prostředku jsou v Azure Sentinelu, kde můžete dotazovat a zkoumat je.

   Přidejte stejné metriky v diagnostických nastaveních pro Azure Key Vault, veřejnou IP adresu, Azure Database for PostgreSQL a všechny služby, které podporují diagnostické protokoly ve vašem účtu.

Po nastavení metriky Azure Sentinel má data k analýze.

## <a name="evaluate-and-verify"></a>Vyhodnocení a ověření

Po vývoji a nasazení architektury je třeba zajistit, aby kód a nasazené služby splňovaly standardy zabezpečení. Toto jsou některé kroky, které můžete provést k ověření softwaru:

- Analýza statického kódu
- Prohledávání chyb zabezpečení
- Hledání a oprava chyb zabezpečení v závislostech aplikací

Jedná se o základní stavební kameny pro osvědčené postupy v bezpečném vývoji.

### <a name="static-code-analysis"></a>Analýza statického kódu

U ukázkové aplikace ověřování pomocí nástrojů pro statickou analýzu zahrnuje hledání chyb zabezpečení v kódu aplikace pomocí technik, jako je kontrola pošcení a analýza toku dat. Nástroje pro statickou analýzu pythonu vám dávají větší jistotu, že vaše aplikace je bezpečná.

**Analyzování kódu**

PyFlakes, knihovna v Pythonu, vám pomůže odstranit mrtvý kód a nepoužívané funkce z aplikací, jak je znázorněno zde:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Linting poskytuje rady a možné změny, které mohou váš kód čistší a méně náchylné k chybám za běhu.

**PyLint**

PyLint poskytl největší hodnotu pro tento projekt. Provádí kontroly standardu kódu, kontrolu chyb a refaktoring tipy k zajištění, že kód spuštěný na serveru je bezpečný. Pomocí PyLint aktualizovat kód, můžete odstranit chyby a zlepšit hodnocení PyLint, jak ukazují následující obrázky.

![Před PyLint](./media/secure-web-app/before-pylint.png)

*Před PyLint*

Po opravě některých chyb kódu nalezených pomocí nástrojů linting máte větší jistotu, že kód není náchylný k chybám. Oprava chyb výrazně snižuje bezpečnostní rizika, ke kterým může dojít při nasazení kódu do produkčního prostředí.

![Po Pylintovi](./media/secure-web-app/after-pylint.png)

*Po PyLint*

### <a name="vulnerability-scanning"></a>Prohledávání chyb zabezpečení

[Nástroj ZAP společnosti OWASP](https://www.zaproxy.org/) je open-source skener zranitelnosti webových aplikací, který můžete použít ke kontrole chyb zabezpečení ukázkové aplikace. Spuštění nástroje v ukázkové aplikaci odhalí některé možné chyby a vektory útoku.

![NÁSTROJ ZAP](./media/secure-web-app/zap-tool.png)

*NÁSTROJ ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Hledání a oprava slabých míst v závislostech aplikací

Chcete-li najít a opravit závislosti aplikací, můžete použít [kontrolu závislostí oWASP](https://owasp.org/www-project-dependency-check/).

Bezpečnost je podobná aplikace, která kontroluje závislosti. Najdete ji na [GitHubu](https://github.com/pyupio/safety). Bezpečnostní kontrola chyb zabezpečení nalezených ve známých databázích zranitelnosti.

![Bezpečnost](./media/secure-web-app/pysafety.png)

*Bezpečnost*

## <a name="next-steps"></a>Další kroky

Následující články vám mohou pomoci navrhovat, vyvíjet a nasazovat zabezpečené aplikace.

- [Návrh](secure-design.md)
- [Vývoj](secure-develop.md)
- [Nasadit](secure-deploy.md)

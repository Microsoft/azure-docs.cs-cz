---
title: Vývoj zabezpečené webové aplikace | Microsoft Docs
description: Tato jednoduchá ukázková aplikace implementuje osvědčené postupy zabezpečení, které zlepšují vaši aplikaci a zabezpečení stav vaší organizace při vývoji v Azure.
keywords: ná
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
ms.openlocfilehash: 640900458eccc36afe58cb148ffd7b94b43be879
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934918"
---
# <a name="develop-a-secure-web-app"></a>Vývoj zabezpečené webové aplikace

Tato ukázka je jednoduchá aplikace v Pythonu, která zobrazuje webovou stránku obsahující odkazy na prostředky zabezpečení pro vývoj aplikací v Azure. Aplikace implementuje osvědčené postupy zabezpečení, které vám při vývoji aplikací v Azure můžou zlepšit zabezpečení vaší aplikace a stav zabezpečení vaší organizace.

Postupujte podle kroků popsaných v tomto článku postupně a ujistěte se, že součásti aplikace jsou správně nakonfigurovány. Databáze, Azure App Service, instance Azure Key Vault a instance služby Azure Application Gateway, jsou na sobě navzájem závislé.

Skripty nasazení nastavily infrastrukturu. Po spuštění skriptů nasazení budete muset v Azure Portal provést několik ručních konfigurací, aby se komponenty a služby vzájemně procházely.

Ukázková aplikace je zaměřená na začátečníky vyvíjet aplikace v Azure, které chtějí implementovat bezpečnostní opatření ve svých aplikacích.

Při vývoji a nasazení této aplikace se naučíte:

- Vytvořte instanci Azure Key Vault, uložte si z ní a načtěte tajné kódy.
- Nasaďte Azure Database for PostgreSQL, nastavte bezpečná hesla a udělte k ní přístup.
- Spusťte kontejner Alpine Linux v Azure Web Apps pro Linux a povolte spravované identity pro prostředky Azure.
- Vytvořte a nakonfigurujte instanci Azure Application Gateway s bránou firewall, která používá [OWASP prvních 10 RuleSet](https://coreruleset.org/).
- Povolí šifrování dat při přenosu a v klidovém provozu pomocí služeb Azure.

Po vývoji a nasazení této aplikace budete mít k dispozici následující ukázkovou webovou aplikaci spolu s popsanými opatřeními konfigurace a zabezpečení.

![Ukázková webová aplikace](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>Architektura
Aplikace je typickou n-vrstvou aplikací se třemi úrovněmi. Rozhraní front-end, back-end a databázová vrstva s integrovanými součástmi pro monitorování a správu tajného kódu se zobrazují tady:

![Architektura aplikací](./media/secure-web-app/architecture.png)

Tato architektura se skládá z těchto součástí:

- [Application Gateway Azure](../../application-gateway/index.yml). Poskytuje bránu a bránu firewall pro naši aplikační architekturu.
- [Azure Web Apps v systému Linux](../../app-service/containers/app-service-linux-intro.md). Poskytuje modul runtime kontejneru pro spuštění aplikace v Pythonu v prostředí Linux.
- [Azure Key Vault](../../key-vault/index.yml). Ukládá a šifruje tajné kódy naší aplikace a spravuje vytváření zásad přístupu.
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/). Bezpečně ukládá data naší aplikace.
- [Azure Security Center](../../security-center/index.yml) a [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Poskytuje monitorování a výstrahy týkající se provozu naší aplikace.

## <a name="threat-model"></a>Model hrozeb
Modelování hrozeb je proces identifikace potenciálních bezpečnostních hrozeb pro vaši firmu a aplikaci a následnému zajištění správného plánu zmírnění.

Tato ukázka používá [Microsoft Threat Modeling Tool](threat-modeling-tool.md) k implementaci modelování hrozeb pro zabezpečenou ukázkovou aplikaci. Díky vytváření diagramů komponent a toků dat můžete identifikovat problémy a hrozby včas v procesu vývoje. Tím ušetříte čas i peníze později.

Toto je model hrozeb pro ukázkovou aplikaci:

![Model hrozeb](./media/secure-web-app/threat-model.png)

Některé ukázkové hrozby a potenciální ohrožení zabezpečení, které nástroj pro modelování hrozeb generuje, se zobrazují na následujícím snímku obrazovky. Model hrozeb poskytuje přehled o zpřístupnění plochy pro útok a vyzývá vývojáře, aby si myslí, jak tyto problémy zmírnit.

![Výstup modelu hrozeb](./media/secure-web-app/threat-model-output.png)

Například injektáže SQL na předchozím výstupu modelu hrozeb se omezuje úpravou uživatelských vstupů a pomocí uložených funkcí v Azure Database for PostgreSQL. Toto zmírnění brání libovolnému spuštění dotazů během čtení a zápisu dat.

Vývojáři zlepšují celkové zabezpečení systému tím, že zmírnit jednotlivé hrozby ve výstupu modelu hrozeb.

## <a name="deployment"></a>Nasazení
Následující možnosti umožňují spustit Linux na Azure App Service:

- Vyberte kontejner ze seznamu předem vytvořených kontejnerů Microsoft v Azure, které byly vytvořeny s podpůrnými technologiemi (Python, Ruby, PHP, Java, Node. js, .NET Core).
- Použijte vlastní kontejner. Jako zdroj bitové kopie vyberte vlastní Registry kontejnerů a sestavte je na mnoha dostupných technologiích podporujících protokol HTTP.

V tomto příkladu spustíte skript nasazení, který nasadí WebApp do App Service a vytvoří prostředky.

Aplikace může používat různé modely nasazení, které jsou uvedené níže:

![Diagram toku dat nasazení](./media/secure-web-app/deployment.png)

Existuje mnoho způsobů, jak nasadit aplikace v Azure, včetně těchto:

- Šablony Azure Resource Manageru
- PowerShell
- Azure CLI
- portál Azure
- Azure DevOps

Tato aplikace se používá:

- [Docker](https://docs.docker.com/) pro vytvoření a sestavení imagí kontejneru.
- Rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pro nasazení
- [Docker Hub](https://hub.docker.com/) jako registr kontejneru.

## <a name="security-considerations"></a>Aspekty zabezpečení

### <a name="network"></a>Síť
Ukázková aplikace používá pro přenos dat přenášených do sítě a z provozu koncová šifrování SSL. Brána je nakonfigurována s certifikátem podepsaným svým držitelem.
> [!IMPORTANT]
> V této ukázce se používá certifikát podepsaný svým držitelem. V produkčním prostředí byste měli získat certifikáty od ověřené certifikační autority (CA).

Brána firewall aplikace také kontroluje příchozí provoz a správce výstrah, když se v síťovém provozu zjistí škodlivý provoz.
Application Gateway zmírnit možnost DDoS a hrozby injektáže SQL zjištěné v modelu hrozeb.

### <a name="identity"></a>Identita
Ukázková aplikace pro přihlášení k portálu používá službu Multi-Factor Authentication pro správce služby Azure Active Directory (Azure AD), kteří mají přiřazený přístup k prostředkům.
Ukázková aplikace používá spravované identity k získání oprávnění ke čtení a načítání tajných kódů z Azure Key Vault, takže aplikace nepotřebuje při čtení tajných kódů zadat přihlašovací údaje a tokeny. Služba Azure AD automaticky vytvoří instanční objekty, které aplikace potřebuje pro čtení a změny tajných kódů při použití spravovaných identit.

Spravované identity pro prostředky Azure a MFA usnadňují nežádoucí osoby získání oprávnění a eskalaci jejich oprávnění v systému. Tato hrozba byla poukázána v modelu hrozeb.
Aplikace používá OAuth, který umožňuje uživatelům registrovaným v aplikaci OAuth přihlásit se k aplikaci.

### <a name="storage"></a>Storage
Data v databázi PostgreSQL se automaticky zašifrují, Azure Database for PostgreSQL. Databáze autorizuje App Service IP adresy, takže přístup k prostředkům databáze pomocí správných přihlašovacích údajů může pouze nasazená webová aplikace App Service.

### <a name="logging-and-auditing"></a>Protokolování a auditování
Aplikace implementuje protokolování pomocí Application Insights ke sledování metrik, protokolů a výjimek, ke kterým dojde. Toto protokolování poskytuje dostatek metadat aplikace pro informování vývojářů a členů provozního týmu ve stavu aplikace. Poskytuje také dostatek dat pro obvracení v případě incidentů zabezpečení.

## <a name="cost-considerations"></a>Důležité informace o nákladech
Pokud ještě nemáte účet Azure, můžete si vytvořit nějaký bezplatný. Přejděte na [stránku bezplatný účet](https://azure.microsoft.com/free/) , abyste mohli začít, podívejte se, co můžete dělat s bezplatným účtem Azure, a zjistěte, které produkty jsou zdarma po dobu 12 měsíců.

Pokud chcete nasadit prostředky v ukázkové aplikaci s funkcemi zabezpečení, musíte platit za některé prémiové funkce. Vzhledem k tomu, že se aplikace škáluje a úrovně Free a zkušební verze, které nabízí Azure, je potřeba upgradovat, aby splňovaly požadavky na aplikace, může dojít ke zvýšení nákladů. Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) Azure můžete odhadnout náklady.

## <a name="deploy-the-solution"></a>Nasazení řešení
### <a name="prerequisites"></a>Požadavky
Chcete-li aplikaci nainstalovat a spustit, je nutné nainstalovat tyto nástroje:

- Editor kódu pro úpravu a zobrazení kódu aplikace. [Visual Studio Code](https://code.visualstudio.com/) je možnost Open Source.
- Rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest,) ve vývojovém počítači.
- V systému [Git](https://git-scm.com/) . Git se používá ke klonování zdrojového kódu místně.
- [JQ](https://stedolan.github.io/jq/), nástroj pro systém UNIX pro dotazování formátu JSON uživatelsky přívětivým způsobem.

K nasazení prostředků ukázkové aplikace potřebujete předplatné Azure. Pokud nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/) pro otestování ukázkové aplikace.

Po instalaci těchto nástrojů jste připraveni aplikaci nasadit do Azure.

### <a name="environment-setup"></a>Nastavení prostředí
Spusťte skripty nasazení a nastavte prostředí a předplatné:

1. Pokud chcete klonovat úložiště zdrojového kódu, použijte tento příkaz git:

   ``` git
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```
2. Pokud se chcete přesunout do adresáře, použijte tento příkaz:

   ```
   cd tutorial-project/scripts
   ```

3. Ve složce Scripts jsou soubory, které jsou specifické pro platformu, kterou používáte (Windows nebo Linux). Protože rozhraní Azure CLI už je nainstalované, přihlaste se k účtu Azure na příkazovém řádku spuštěním tohoto příkazu CLI:

   ``` azurecli
   az login
   ```

Prohlížeč se otevře, přihlaste se pomocí svých přihlašovacích údajů. Po přihlášení můžete začít nasazovat prostředky z příkazového řádku.

Skripty `deploy-powershell.ps1` nasazení a `deploy-bash.sh` obsahují kód, který nasadí celou aplikaci.
Nasazení řešení:

1. Pokud jste v prostředí PowerShell, spusťte `deploy-powershell.ps1` soubor zadáním `./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME` nahrazení oblasti a názvu skupiny prostředků vhodnými oblastmi Azure a názvem pro skupinu prostředků.
2. Pokud jste v systému Linux spustili `deploy-bash.sh` soubor `/deploy-bash.sh REGION RESOURCE_GROUP_NAME`zadáním, bude pravděpodobně nutné nastavit spustitelný soubor souboru zadáním`chmod +x deploy-bash.sh`

Následující příklady prezentují fragmenty klíčových komponent. Můžete nasadit příklady jednotlivě nebo se zbytkem součástí spuštěním souborů nasazení.

### <a name="implementation-guidance"></a>Pokyny k implementaci
Skript nasazení je jeden skript, který může být rozdělen do čtyř fází. Každá fáze nasadí a nakonfiguruje prostředek Azure, který je v [diagramu architektury](#architecture).

Čtyři fáze:

- Nasazení Azure Key Vault.
- Nasazení Azure Database for PostgreSQL.
- Nasazení Azure Web Apps v systému Linux.
- Nasaďte Application Gateway s bránou firewall webových aplikací.

Každá fáze sestaví na předchozí straně pomocí konfigurace z dříve nasazených prostředků.

K dokončení kroků implementace se ujistěte, že jste nainstalovali nástroje uvedené v části [požadavky](#prerequisites).

#### <a name="deploy-azure-key-vault"></a>Nasazení Azure Key Vault
V této části vytvoříte a nasadíte instanci Azure Key Vault, která se používá k ukládání tajných klíčů a certifikátů.

Po dokončení nasazení budete mít na Azure nasazenou instanci Azure Key Vault.

Nasazení Azure Key Vault pomocí rozhraní příkazového řádku Azure:

1. Deklarujte proměnné pro Azure Key Vault.
2. Zaregistrujte poskytovatele Azure Key Vault.
3. Vytvořte skupinu prostředků pro instanci.
4. Vytvořte instanci Azure Key Vault ve skupině prostředků vytvořené v kroku 3.

   ``` azurecli

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
Osvědčeným postupem je použití spravovaných identit pro prostředky Azure v aplikacích, které používají Key Vault k přístupu k prostředkům. Stav zabezpečení se zvyšuje, když se přístupové klíče Key Vault neukládají v kódu nebo v konfiguraci.

#### <a name="deploy-azure-database-for-postgresql"></a>Nasazení Azure Database for PostgreSQL
Azure Database for PostgreSQL funguje následujícím způsobem, nejprve vytvořte databázový server a pak vytvořte databázi, do které chcete uložit schéma a data.

Po dokončení nasazení budete mít PostgreSQL Server a databázi běžící v Azure.

Nasazení Azure Database for PostgreSQL pomocí rozhraní příkazového řádku Azure:

1. Otevřete terminál pomocí Azure CLI a nastavení předplatného Azure.
2. Vygeneruje zabezpečenou kombinaci uživatelského jména a hesla, která se používá pro přístup k databázi. (Ty by měly být uložené v Azure Key Vault pro aplikace, které je používají.)
3. Vytvořte instanci serveru PostgreSQL.
4. Vytvořte databázi na instanci serveru, kterou jste vytvořili v kroku 3.
5. Spusťte skripty PostgreSQL v instanci PostgreSQL.

Následující kód závisí na tajných klíčích PGUSERNAME a PGPASSWORD uložených ve službě Azure klíčů v kroku nasazení trezoru klíčů výše.

   ``` azurecli
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

Po nasazení databáze je třeba uložit své přihlašovací údaje a připojovací řetězec do Azure Key Vault.
Ve složce Scripts je `functions.sql` soubor, který obsahuje kód pl/PGSQL, který při spuštění vytvoří uložené funkce. Po spuštění tohoto souboru se parameterizes vstupy a omezí se vkládání SQL.

PostgreSQL je součástí balíčku s názvem `psql` , který se používá pro připojení k databázi nástroje. Aby bylo `functions.sql`možné spustit, musíte se z místního počítače připojit k instanci Azure Database for PostgreSQL a spustit ji. Instalace nástroje psql je součástí výchozí instalace pro PostgreSQL v každém operačním systému.
Další informace najdete v [dokumentaci k psql](https://www.postgresql.org/docs/9.3/app-psql.html).

Azure Cloud Shell také obsahuje `psql` nástroj. Cloud Shell můžete použít přímo z Azure Portal tak, že vyberete ikonu Cloud Shell.

Pokud chcete povolit vzdálený přístup k instanci PostgreSQL, musíte autorizovat IP adresu v PostgreSQL.
Tento přístup povolíte tak, že na kartě **zabezpečení připojení** vyberete **Přidat IP adresu klienta**a uložíte nová nastavení.

![Autorizovat IP adresu klienta](./media/secure-web-app/add-client-ip-postgres.png)

Pokud místo místního nástroje psql používáte Cloud Shell, vyberte možnost **Povolení přístupu ke službám Azure** a změňte její hodnotu na zapnuto, aby byl přístup k Cloud Shell povolen.

Pak se připojte k instanci spuštěním níže uvedeného příkazu psql s parametry připojovacího řetězce na kartě **připojovací řetězce** instance PostgreSQL na Azure Portal.
Nahraďte prázdné složené závorky parametry z okna připojovací řetězec databáze a heslo heslem z Azure Key Vault.

```sql
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

Po ujištění, že jste připojení k databázi, spusťte následující skript PL/pgSQL. Skript vytvoří uložené funkce, které slouží k vložení dat do databáze.

```sql
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


Další informace o tom, jak nastavit ověřování SSL a certifikační autority (CA) pro PostgreSQL, najdete v tématu [Konfigurace připojení SSL v Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

V kontejneru je zahrnutý kořenový certifikát. Postup pro získání certifikátu:

1. Stáhněte si soubor certifikátu od [certifikační autority](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).
2. [Stáhněte a nainstalujte si OpenSSL na svém počítači](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).
3. Dekódování souboru certifikátu:

   ```powershell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

Další informace o tom, jak nakonfigurovat zabezpečení SSL pro PostgreSQL, najdete v tématu [Konfigurace zabezpečení připojení SSL](https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security).

#### <a name="deploy-azure-web-apps-on-linux"></a>Nasazení Azure Web Apps v systému Linux
Služby pro Linux můžete snadno sestavit nad Azure App Service, protože Azure poskytuje sadu předem připravených kontejnerů a imagí pro široce používané jazyky, jako je Python, Ruby C#, a Java. Azure podporuje také vlastní kontejnery, které umožňují spouštět prakticky všechny programovací jazyky na platformě Azure App Service.

Nasazená aplikace je jednoduchá aplikace v Pythonu, která běží na nejnovější distribuci Ubuntu Linux. Připojuje se k instancím Azure Key Vault a PostgreSQL, které byly vytvořeny v předchozích částech pro správu přihlašovacích údajů a úložiště dat.

V kořenové složce aplikace je k dispozici následující soubor Docker:

``` docker
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

Souboru Dockerfile výše se používá k sestavení kontejneru hostovaného na Azure Container Registry `mcr.microsoft.com/samples/basic-linux-app`.

Následující kód:

1. Deklaruje proměnné a názvy pro instanci App Service.
2. Vytvoří skupinu prostředků pro plán App Service.
3. Zřídí instanci Azure Web Apps v kontejnerech platformy Linux.
4. Povolí protokolování pro kontejner webové aplikace.
5. Nastaví některé konfigurace aplikací v nastavení aplikace kontejneru.

   ```
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

Tento skript vytvoří přiřazenou identitu pro instanci App Service, kterou lze použít s balíčkem MSI k interakci s Azure Key Vault bez tajných tajných kódů v kódu nebo konfiguraci.

Na portálu přejděte na instanci Azure Key Vault a autorizujte přiřazenou identitu na kartě zásady přístupu. Vyberte **Přidat nové zásady přístupu**. V části **Vybrat objekt zabezpečení**vyhledejte název aplikace, který se podobá názvu vytvořené instance App Service.
Objekt služby připojený k aplikaci by měl být viditelný. Vyberte stránku a uložte si zásadu přístupu, jak je znázorněno na následujícím snímku obrazovky.

Vzhledem k tomu, že aplikace potřebuje jenom načíst klíče, vyberte v možnostech tajných kódů oprávnění **získat** a umožněte přístup při omezení udělených oprávnění.

![Zásada přístupu Key Vault](./media/secure-web-app/kv-access-policy.png)

*Vytvoření zásady přístupu Key Vault*

Uložte zásady přístupu a pak uložte novou změnu na kartě **zásady přístupu** a aktualizujte zásady.

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>Nasazení Application Gateway s povoleným firewallem webových aplikací
Ve službě Web Apps nedoporučujeme vystavovat služby přímo na světovém světě na internetu.
Vyrovnávání zatížení a pravidla brány firewall poskytují lepší zabezpečení a kontrolu nad příchozím provozem a umožňují vám ho spravovat.

Nasazení instance Application Gateway:

1. Vytvořte skupinu prostředků, která bude obsahovat Aplikační bránu.
2. Zajistěte, aby se virtuální síť připojila k bráně.
3. Vytvořte podsíť pro bránu ve virtuální síti.
4. Zřízení veřejné IP adresy.
5. Zřídit Aplikační bránu.
6. Povolte v bráně firewall webových aplikací.

   ``` azurecli
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

Tato část nasazuje Aplikační bránu:

```powershell
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

Po dokončení nasazení máte bránu Application Gateway s povoleným firewallem webových aplikací.

Instance brány zveřejňuje port 443 pro protokol HTTPS. Tato konfigurace zajišťuje, že naše aplikace je přístupná jenom na portu 443 přes protokol HTTPS.

Osvědčeným postupem zabezpečení blokování nepoužívaných portů a omezení expozice prostoru pro útok.

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>Přidání skupin zabezpečení sítě do instance App Service

Instance App Service lze integrovat s virtuálními sítěmi. Tato integrace umožňuje nakonfigurovat zásady skupiny zabezpečení sítě, které spravují příchozí a odchozí provoz aplikace.

1. Pokud chcete tuto funkci povolit, v okně instance služby Azure App Service v části **Nastavení**vyberte **sítě**. V pravém podokně v části **Integrace virtuální**sítě vyberte **kliknutím sem proveďte konfiguraci**.

   ![Nová integrace virtuální sítě](./media/secure-web-app/app-vnet-menu.png)

    *Nová integrace virtuální sítě pro App Service*
1. Na další stránce vyberte **Přidat virtuální síť (Preview)** .

1. V další nabídce vyberte virtuální síť vytvořenou v nasazení, které začíná `hello-vnet`na. Můžete buď vytvořit novou podsíť, nebo vybrat některou z existujících.
   V takovém případě vytvořte novou podsíť. Nastavte **Rozsah adres** na **10.0.3.0/24** a pojmenujte podsíť **AppDomain App-Subnet**.

   ![Konfigurace App Service virtuální sítě](./media/secure-web-app/app-vnet-config.png)

    *Konfigurace virtuální sítě pro App Service*

Teď, když jste povolili integraci virtuální sítě, můžete do naší aplikace přidat skupiny zabezpečení sítě.

1. Pomocí vyhledávacího pole vyhledejte **skupiny zabezpečení sítě**. Ve výsledcích vyberte **skupiny zabezpečení sítě** .

    ![Vyhledat skupiny zabezpečení sítě](./media/secure-web-app/nsg-search-menu.png)

    *Vyhledat skupiny zabezpečení sítě*

2. V další nabídce vyberte **Přidat**. Zadejte **název** NSG a **skupinu prostředků** , ve které se má umístit. Tento NSG se použije pro podsíť služby Application Gateway.

    ![Vytvoření NSG](./media/secure-web-app/nsg-create-new.png)

    *Vytvoření NSG*

3. Po vytvoření NSG ho vyberte. V okně v části **Nastavení**vyberte **příchozí pravidla zabezpečení**. Nakonfigurujte tato nastavení tak, aby umožňovala připojení přicházející do aplikační brány přes port 443.

   ![Konfigurace NSG](./media/secure-web-app/nsg-gateway-config.png)

   *Konfigurace NSG*

4. V odchozích pravidlech pro bránu NSG přidejte pravidlo, které umožňuje odchozí připojení k instanci App Service vytvořením pravidla, které cílí na tag `AppService`služby:

   ![Přidat odchozí pravidla pro NSG](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *Přidat odchozí pravidla pro NSG*

    Přidejte další odchozí pravidlo, které povolí bráně odesílat odchozí pravidla do virtuální sítě.

   ![Přidat další odchozí pravidlo](./media/secure-web-app/nsg-outbound-vnet.png)

    *Přidat další odchozí pravidlo*

5. V okně podsítě v NSG vyberte přidružit, vybertevirtuální síť vytvořenou v nasazení a vyberte podsíť brány s názvem **GS-Subnet**. NSG se aplikuje na podsíť.

6. Vytvořte další NSG jako v předchozím kroku, tentokrát pro instanci App Service. Zadejte název. Přidejte příchozí pravidlo pro port 443 jako u služby Application Gateway NSG.

   Pokud máte nasazenou instanci App Service v instanci služby App Service Environment, která není pro tuto aplikaci případ, můžete přidat příchozí pravidla a povolit Azure Service Health sondy otevřením portů 454-455 v příchozích skupinách zabezpečení App Service NSG. Tady je konfigurace:

   ![Přidat pravidla pro Azure Service Health sondy](./media/secure-web-app/nsg-create-healthprobes.png)

    *Přidat pravidla pro Azure Service Health sondy (jenom App Service Environment)*

7. V odchozích pravidlech zabezpečení vytvořte nové odchozí pravidlo zabezpečení, které umožňuje instanci App Service komunikovat s databází PostgreSQL. Nakonfigurujte ho takto:

   ![Pravidlo pro povolení odchozích připojení PostgreSQL](./media/secure-web-app/nsg-outbound-postgresql.png)

   *Přidání pravidla pro povolení odchozích připojení PostgreSQL*

Chcete-li omezit plochu pro útok, upravte nastavení App Service sítě tak, aby umožňovalo přístup k aplikaci pouze bráně aplikace.
Provedete to tak, že na kartě App Service síť vyberete kartu **omezení IP adres** a vytvoříte pravidlo Povolit, které umožňuje, aby se k této službě mohl přímo přistupovat jenom IP adresa služby Application Gateway.

IP adresu brány můžete načíst ze své stránky s přehledem. Na kartě **IP adresa CIDR** zadejte IP adresu v tomto formátu: `<GATEWAY_IP_ADDRESS>/32`.

![Povolí jenom bránu.](./media/secure-web-app/app-allow-gw-only.png)

*Pro přístup k App Service Povolte jenom IP adresu brány.*


#### <a name="implement-azure-active-directory-oauth"></a>Implementovat Azure Active Directory OAuth

Dokumenty Azure distribuované na stránce Ukázková webová aplikace jsou prostředky v naší aplikaci, které by mohly potřebovat ochranu. Azure Active Directory (Azure AD) můžete použít k implementaci ověřování pro webové, desktopové a mobilní aplikace pomocí různých toků ověřování.
Aplikace používá **přihlášení s Microsoftem**, které umožňuje aplikaci číst profily uživatelů, kteří byli přidáni do našeho seznamu uživatelů Azure AD pro jednoho tenanta.

V Azure Portal nakonfigurujte aplikaci tak, aby používala požadované přihlašovací údaje:

1. Vyberte **Azure Active Directory**nebo ho vyhledejte pomocí vyhledávacího pole.

2. Vyberte **Nová registrace**:

   ![Vytvořit registraci](./media/secure-web-app/ad-auth-create.png)

   *Vytvoření registrace aplikace Azure AD*

3. Na další stránce zadejte název aplikace. V části **podporované typy účtů**vyberte **účty jenom v tomto organizačním adresáři**.
    V části **identifikátor URI přesměrování**zadejte základní doménu, na které bude aplikace spuštěná, a jednu s koncovým bodem tokenu. Příklad: *GATEWAY_HASH*. cloudapp.NET/token.

   ![Konfigurace registrace aplikace v Azure AD](./media/secure-web-app/ad-auth-type.png)

   *Konfigurace registrace aplikace v Azure AD*

4. Zobrazí se obrazovka, která zobrazuje registrovanou aplikaci a její informace. Tyto informace je nutné přidat do instance Azure Key Vault.
   1. Zkopírujte ID aplikace (klienta) a uložte ho v Key Vault jako `CLIENTID`.
   2. Zkopírujte identifikátor URI přesměrování, který jste zadali v předchozím kroku, a uložte ho `REDIRECTURI`jako.
   3. Zkopírujte název výchozího adresáře služby Azure AD s *názvem*format. microsoftonline.com a uložte ho do Key Vault jako `TENANT`.
   4. Na kartě **certifikáty & tajné klíče** aplikace Azure AD, kterou jste vytvořili dříve, vyberte **nový tajný klíč klienta**, jak je znázorněno na následujícím snímku obrazovky. Nastavte datum vypršení platnosti a potom zkopírujte vygenerovanou hodnotu a uložte ji v Key Vault jako `CLIENTSECRET`.

      ![Autorizační klíč autorizace Azure AD](./media/secure-web-app/ad-auth-secrets.png)

      *Autorizační klíč autorizace Azure AD*

   5. Vygenerujte zabezpečený náhodný tajný klíč pomocí libovolného nástroje příkazového řádku/online. Uložte ho do Key Vault jako `FLASKSECRETKEY`. Aplikační rozhraní používá tento klíč k vytváření relací.
        Informace o tom, jak vygenerovat tajný klíč, najdete v tématu věnovaném relacím na [baňce](http://flask.pocoo.org/docs/1.0/quickstart/#sessions).

5. Po nakonfigurování přihlášení budete muset přidat uživatele do odkazu Azure AD, aby se mohly přihlašovat k prostředku. Pokud je chcete přidat, klikněte na kartu **Uživatelé** v Azure AD, vyberte **Všichni uživatelé**a pak vyberte **Nový uživatel** nebo **Nový uživatel typu Host**. Pro účely testování můžete přidat uživatele typu Host a pozvat uživatele do adresáře. Nebo můžete přidat nového uživatele, pokud je ověřená doména, ve které je aplikace spuštěná. V tomto příkladu je možné zaregistrovat přístup jenom uživatelům registrovaným v tenantovi Azure AD. Informace o přístupu pro více tenantů naleznete v dokumentaci.

   ![Přidat uživatele do výchozí domény](./media/secure-web-app/ad-auth-add-user.png)

   *Přidat uživatele do výchozí domény Azure Active Directory*

Po přidání konfigurace a tajných kódů Azure AD do Key Vault se uživatelé můžou do aplikace ověřit pomocí ověřování Azure OAuth.
V kódu aplikace to zpracovává knihovna Azure Active Directory Authentication Library (ADAL).

Po použití tajných kódů v Key Vault a aplikace má přístup k tajným klíčům a databázi, může být Aplikační služba dostupná prostřednictvím adresy URL aplikace brány (https://GATEWAY_HASH.cloudapp.net), kterou můžete získat z svého okna.

Pokud se při přihlášení ke službě Azure AD zobrazí chybová zpráva "uživatel není zaregistrován v adresáři, ke kterému se pokoušíte přihlašovat", je nutné přidat uživatele. Pokud chcete uživatele přidat, klikněte na kartu **Uživatelé** služby Azure AD a přidejte uživatele ručně zadáním jejich podrobností nebo pozváním uživatele do služby Azure AD zadáním e-mailové adresy jako uživatel typu Host v okně **pozvat Host** .

#### <a name="deploy-application-insights"></a>Nasazení Application Insights
Teď, když je aplikace nasazená a funkční, je potřeba zpracovat chyby, ke kterým dochází v rámci aplikace společně s shromažďováním dat protokolování a trasování.
Shromažďování dat protokolování a trasování poskytuje zobrazení událostí auditu, ke kterým dochází v aplikaci.

Application Insights je služba, která shromažďuje protokoly, které mohou být generovány uživateli nebo systémem.

Vytvoření instance Application Insights:

1. Vyhledejte **Application Insights** pomocí vyhledávacího pole v Azure Portal.
2. Vyberte **Application Insights**. Zadáním níže uvedených podrobností vytvořte instanci.

   ![Vytvoření instance Application Insights](./media/secure-web-app/app-insights-data.png)

Po dokončení nasazení budete mít instanci Application Insights.

Po vytvoření instance Application Insights je potřeba, aby aplikace měla na paměti klíč instrumentace, která umožňuje odesílání protokolů do cloudu. Provedete to tak, že načtete Application Insights klíč a použijete ho v knihovnách aplikací, které Azure poskytuje pro Application Insights. Osvědčeným postupem je ukládání klíčů a tajných kódů v Azure Key Vault pro zajištění zabezpečení.

Pro základní ukázkovou aplikaci je po vytvoření instance Application Insights nutné, aby aplikace měla na paměti klíč instrumentace, která umožňuje odesílání protokolů do cloudu.
V Key Vault nastavte `APPINSIGHTSKEY` tajný klíč a nastavte jeho hodnotu jako klíč instrumentace. Tím umožníte, aby aplikace odesílala protokoly a metriky do Application Insights.

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>Implementace služby Multi-Factor Authentication pro Azure Active Directory
Správci musí zajistit, aby byly účty předplatného na portálu chráněné. Předplatné je zranitelné vůči útokům, protože spravuje prostředky, které jste vytvořili. Pokud chcete předplatné chránit, povolte vícefaktorové ověřování na kartě **Azure Active Directory** předplatného.

Azure AD funguje na základě zásad, které se aplikují na uživatele nebo skupiny uživatelů, kteří odpovídají určitým kritériím.
Azure vytvoří výchozí zásadu, která určuje, že správci pro přihlášení k portálu potřebují ověřování pomocí dvou faktorů.
Po povolení této zásady se může zobrazit výzva, abyste se odhlásili a znovu přihlásili do Azure Portal.

Povolení MFA pro přihlášení správce:

1. Přejít na kartu **Azure Active Directory** v Azure Portal
2. V kategorii zabezpečení vyberte podmíněný přístup. Zobrazí se tato obrazovka:

   ![Podmíněný přístup – zásady](./media/secure-web-app/ad-mfa-conditional-add.png)

Pokud nemůžete vytvořit novou zásadu:

1. Přejít na kartu **MFA**
2. Vyberte odkaz Azure AD Premium **bezplatnou zkušební verzi** , abyste se přihlásili k odběru bezplatné zkušební verze.

   ![Azure AD Premium bezplatnou zkušební verzi](./media/secure-web-app/ad-trial-premium.png)

Vraťte se na obrazovku podmíněného přístupu.

1. Vyberte kartu nová zásada.
2. Zadejte název zásady.
3. Vyberte uživatele nebo skupiny, pro které chcete povolit MFA.
4. V části **řízení přístupu**vyberte kartu **udělení** a potom v případě potřeby vyberte **vyžadovat vícefaktorové ověřování** (a další nastavení).

   ![Vyžadování MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

Zásadu můžete povolit zaškrtnutím políčka v horní části obrazovky nebo na kartě **podmíněný přístup** . Pokud je tato zásada povolená, uživatelé se k portálu budou potřebovat MFA pro přihlášení k portálu.

Existují základní zásady, které vyžadují MFA pro všechny správce Azure. Můžete ji povolit hned na portálu. Když se tyto zásady povolí, může se zrušit platnost aktuální relace a znovu se přihlašujete.

Pokud není zásada standardních hodnot povolená:
1.  Vyberte **vyžadovat MFA pro správce**.
2.  Vyberte možnost **použít zásadu hned**.

   ![Vybrat použít zásadu hned](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>Použití služby Azure Sentinel k monitorování aplikací a prostředků

Vzhledem k tomu, že aplikace roste, je obtížné agregovat všechny signály zabezpečení a metriky obdržené z prostředků a učinit je užitečné v cestě orientované na akce.

Sentinel Azure je navržená tak, aby shromáždila data, zjistila možné typy hrozeb a poskytovala přehled o incidentech zabezpečení.
I když čeká na ruční zásah, může Azure Sentinel spoléhat na předem zapsané playbooky, aby se aktivovaly výstrahy a procesy správy incidentů.

Ukázková aplikace se skládá z několika prostředků, které může sledovat Azure Sentinel.
Pokud chcete nastavit službu Azure Sentinel, musíte nejdřív vytvořit Log Analytics pracovní prostor, ve kterém se budou ukládat všechna data shromážděná z různých prostředků.

Postup vytvoření tohoto pracovního prostoru:

1. Do vyhledávacího pole v Azure Portal vyhledejte **Log Analytics**. Vyberte **Log Analytics pracovní prostory**.

   ![Hledání Log Analytics pracovních prostorů](./media/secure-web-app/sentinel-log-analytics.png)

    *Hledání Log Analytics pracovních prostorů*

2. Na další stránce vyberte **Přidat** a potom zadejte název, skupinu prostředků a umístění pro pracovní prostor.
   ![Vytvoření pracovního prostoru Log Analytics](./media/secure-web-app/sentinel-log-analytics-create.png)

   *Vytvoření pracovního prostoru Log Analytics*

3. Pomocí vyhledávacího pole vyhledejte službu **Azure Sentinel**.

   ![Vyhledat Sentinel Azure](./media/secure-web-app/sentinel-add.png)

    *Vyhledat Sentinel Azure*

4. Vyberte **Přidat** a potom vyberte pracovní prostor Log Analytics, který jste vytvořili dříve.

   ![Přidání pracovního prostoru Log Analytics](./media/secure-web-app/sentinel-workspace-add.png)

    *Přidání pracovního prostoru Log Analytics*

5. Na stránce **datové konektory Azure Sentinel** v části **Konfigurace**vyberte **datové konektory**. Zobrazí se pole služeb Azure, které můžete propojit s instancí úložiště Log Analytics pro analýzu ve službě Azure Sentinel.

   ![Log Analytics datových konektorů](./media/secure-web-app/sentinel-connectors.png)

    *Přidání datového konektoru do Azure Sentinel*

   Chcete-li například připojit Aplikační bránu, proveďte tyto kroky:

   1. Otevřete okno instance služby Azure Application Gateway.
   2. V části **monitorování**vyberte **nastavení diagnostiky**.
   3. Vyberte **Přidat nastavení diagnostiky**.

      ![Přidat diagnostiku Application Gateway](./media/secure-web-app/sentinel-gateway-connector.png)

      *Přidat diagnostiku Application Gateway*

   4. Na stránce **nastavení diagnostiky** vyberte pracovní prostor Log Analytics, který jste vytvořili, a pak vyberte všechny metriky, které chcete shromáždit a odeslat do Azure Sentinel. Vyberte **Uložit**.

        ![Nastavení konektoru služby Azure Sentinel](./media/secure-web-app/sentinel-connector-settings.png)

        *Nastavení konektoru služby Azure Sentinel*

  Metriky z prostředku jsou ve službě Azure Sentinel, kde se můžete dotazovat a prozkoumat.

   Přidejte stejné metriky do nastavení diagnostiky pro Azure Key Vault, veřejnou IP adresu, Azure Database for PostgreSQL a všechny služby podporující diagnostické protokoly ve vašem účtu.

Po nastavení metrik Azure Sentinel obsahuje data, která se mají analyzovat.

## <a name="evaluate-and-verify"></a>Vyhodnotit a ověřit
Po vývoji a nasazení architektury je potřeba zajistit, aby kód a nasazené služby splňovaly bezpečnostní standardy. Toto jsou některé kroky, které můžete provést k ověření softwaru:

- Analýza statického kódu
- Kontrola ohrožení zabezpečení
- Hledání a oprava ohrožení zabezpečení v závislostech aplikace

Toto jsou základní stavební bloky pro osvědčené postupy při bezpečném vývoji.

### <a name="static-code-analysis"></a>Analýza statického kódu
U ukázkové aplikace ověřování pomocí nástrojů statické analýzy zahrnuje hledání ohrožení zabezpečení v kódu aplikace pomocí technik, jako je kontrola chuti a analýza toku dat. Nástroje pro statické analýzy Pythonu vám poskytnou větší jistotu, že je vaše aplikace zabezpečená.

**Linting**

PyFlakes, knihovna Python linting Library vám pomůže odebrat mrtvý kód a nepoužívané funkce z aplikací, jak je znázorněno zde:

![PyFlakes](./media/secure-web-app/pyflakes.png)

Linting poskytuje tipy a možné změny, které mohou být v průběhu běhu náchylné k vyčištění kódu a méně náchylné k chybám.

**PyLint**

PyLint poskytl nejvíc hodnotu pro tento projekt. Provádí kontroly kódu – standardní, kontrolu chyb a refaktoring, aby bylo zajištěno, že je kód spuštěný na serveru bezpečný. Když použijete PyLint k aktualizaci kódu, můžete eliminovat chyby a zlepšit hodnocení PyLint, jak ukazuje následující obrázek.

![Před PyLint](./media/secure-web-app/before-pylint.png)

*Před PyLint*

Po opravě některých chyb kódu zjištěných nástroji linting máte větší jistotu, že kód není náchylný k chybám. Oprava chyb významně snižuje bezpečnostní rizika, ke kterým může dojít při nasazení kódu do produkčních prostředí.

![Po Pylint](./media/secure-web-app/after-pylint.png)

*Po PyLint*

### <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení
[OWASP](https://www.zaproxy.org/) je open source nástroj pro ohrožení zabezpečení webových aplikací, který můžete použít ke kontrole ohrožení zabezpečení v ukázkové aplikaci. Spuštění nástroje na ukázkové aplikaci odhalí některé možné chyby a vektory útoku.

![Nástroj ZAP](./media/secure-web-app/zap-tool.png)

*Nástroj ZAP*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>Vyhledání a oprava chyb zabezpečení v závislostech aplikací
Chcete-li vyhledat a opravit závislosti aplikace, můžete použít [kontrolu závislostí OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check).

Bezpečnost je podobná aplikace, která kontroluje závislosti. Můžete ji najít na [GitHubu](https://github.com/pyupio/safety). Bezpečnostní kontroly chyb zabezpečení nalezené v dobře známých databázích ohrožení zabezpečení.

![Bezpečnostních](./media/secure-web-app/pysafety.png)

*Bezpečnostních*

## <a name="next-steps"></a>Další kroky
Následující články vám pomůžou při návrhu, vývoji a nasazení zabezpečených aplikací.

- [Vytvořit](secure-design.md)
- [Vývoj](secure-develop.md)
- [Nasazení](secure-deploy.md)

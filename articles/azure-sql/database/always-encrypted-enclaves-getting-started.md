---
title: 'Kurz: Začínáme s Always Encrypted s využitím zabezpečených enclaves v Azure SQL Database'
description: V tomto kurzu se naučíte, jak vytvořit základní prostředí pro Always Encrypted s využitím zabezpečených enclaves v Azure SQL Database a jak šifrovat data na místě a vydávat bohatě důvěrné dotazy proti šifrovaným sloupcům pomocí SQL Server Management Studio (SSMS).
keywords: šifrování dat, šifrování SQL, šifrování databáze, citlivá data, Always Encrypted, zabezpečení enclaves, SGX, ověřování identity
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: tutorial
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: 809ac72977b670faff984ad39effb1c70767e141
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120942"
---
# <a name="tutorial-getting-started-with-always-encrypted-with-secure-enclaves-in-azure-sql-database"></a>Kurz: Začínáme s Always Encrypted s využitím zabezpečených enclaves v Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted se zabezpečeným enclaves pro Azure SQL Database je aktuálně ve **verzi Public Preview**.

V tomto kurzu se naučíte, jak začít s [Always Encrypted s využitím zabezpečených enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) v Azure SQL Database. Zobrazí se vám následující:

> [!div class="checklist"]
> - Postup vytvoření prostředí pro testování a vyhodnocení Always Encrypted pomocí zabezpečeného enclavesu.
> - Jak šifrovat data na místě a vydávat bohatě důvěrné dotazy proti šifrovaným sloupcům pomocí SQL Server Management Studio (SSMS).

## <a name="prerequisites"></a>Předpoklady

Tento kurz vyžaduje Azure PowerShell a [SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="powershell-requirements"></a>Požadavky na PowerShell

Informace o tom, jak nainstalovat a spustit Azure PowerShell, najdete v tématu [přehled Azure PowerShell](/powershell/azure) . 

Minimální verze AZ modules Required pro podporu operací ověření identity:

- AZ 4.5.0
- AZ. Accounts 1.9.2
- AZ. Attestation 0.1.8

Spuštěním následujícího příkazu ověřte nainstalovanou verzi všech modulů AZ:

```powershell
Get-InstalledModule
```

Pokud se verze neshodují s minimálním požadavkem, spusťte `Update-Module` příkaz.

Galerie prostředí PowerShell má zastaralé verze protokolu TLS (Transport Layer Security) 1,0 a 1,1. Doporučuje se protokol TLS 1,2 nebo novější verze. Pokud používáte nižší verzi protokolu TLS než 1,2, může docházet k následujícím chybám:

- `WARNING: Unable to resolve package source 'https://www.powershellgallery.com/api/v2'`
- `PackageManagement\Install-Package: No match was found for the specified search criteria and module name.`

Pokud chcete pokračovat v interakci s Galerie prostředí PowerShell, spusťte před příkazy Install-Module tento příkaz.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
```

### <a name="ssms-requirements"></a>Požadavky na SSMS

Informace o tom, jak stáhnout SSMS, najdete v tématu [stažení SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) .

Požadovaná minimální verze SSMS je 18,8.


## <a name="step-1-create-and-configure-a-server-and-a-dc-series-database"></a>Krok 1: vytvoření a konfigurace serveru a databáze DC-Series

V tomto kroku vytvoříte nový Azure SQL Database logický Server a novou databázi pomocí generování hardwaru DC-Series, které vyžaduje Always Encrypted s zabezpečeným enclaves. Další informace najdete v tématu [DC-Series](service-tiers-vcore.md#dc-series).

1. Otevřete konzolu PowerShellu a naimportujte požadovanou verzi AZ.

  ```PowerShell
  Import-Module "Az" -MinimumVersion "4.5.0"
  ```
  
2. Přihlaste se k Azure. V případě potřeby [přepněte na předplatné](/powershell/azure/manage-subscriptions-azureps) , které používáte pro tento kurz.

  ```PowerShell
  Connect-AzAccount
  $subscriptionId = "<your subscription ID>"
  Set-AzContext -Subscription $subscriptionId
  ```

3. Vytvoříte novou skupinu prostředků. 

  > [!IMPORTANT]
  > Je potřeba vytvořit skupinu prostředků v oblasti (umístění), která podporuje generování hardwaru DC-Series a Microsoft Azure ověření identity. Seznam oblastí, které podporují DC-Series, najdete v tématu [dostupnost řadiče domény](service-tiers-vcore.md#dc-series-1). [Toto](https://azure.microsoft.com/global-infrastructure/services/?products=azure-attestation) je místní dostupnost Microsoft Azure ověření identity.

  ```powershell
  $resourceGroupName = "<your new resource group name>"
  $location = "<Azure region supporting DC-series and Microsoft Azure Attestation>"
  New-AzResourceGroup -Name $resourceGroupName -Location $location
  ```

4. Vytvořte logický Server Azure SQL. Po zobrazení výzvy zadejte jméno správce serveru a heslo. Nezapomeňte si pamatovat jméno správce a heslo – budete ho potřebovat později, abyste se mohli připojit k serveru.

  ```powershell
  $serverName = "<your server name>" 
  New-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -Location $location 
  ```

5. Vytvořte pravidlo brány firewall serveru, které umožňuje přístup ze zadaného rozsahu IP adres.
  
  ```powershell
  $startIp = "<start of IP range>"
  $endIp = "<end of IP range>"
  $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
  ```

6. Přiřaďte k serveru identitu spravovaného systému. 

  ```PowerShell
  $server = Set-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName -AssignIdentity
  $serverObjectId = $server.Identity.PrincipalId
  ```

7. Vytvořte databázi DC-Series.

  ```powershell
  $databaseName = "ContosoHR"
  $edition = "GeneralPurpose"
  $vCore = 2
  $generation = "DC"
  New-AzSqlDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -DatabaseName $databaseName `
    -Edition $edition `
    -Vcore $vCore `
    -ComputeGeneration $generation
  ```

8. Načtěte a uložte informace o vašem serveru a databázi. Tyto informace budete potřebovat spolu s názvem správce a heslem z kroku 4 v této části v dalších částech.

  ```powershell
  Write-Host 
  Write-Host "Fully qualified server name: $($server.FullyQualifiedDomainName)" 
  Write-Host "Server Object Id: $serverObjectId"
  Write-Host "Database name: $databaseName"
  ```
  
## <a name="step-2-configure-an-attestation-provider"></a>Krok 2: Konfigurace poskytovatele ověření identity 

V tomto kroku vytvoříte a nakonfigurujete poskytovatele ověření identity v Microsoft Azure ověření identity. To je potřeba k ověření, že zabezpečené enklávy vaše databáze používá.

1. Zkopírujte níže zásadu ověření identity a zásadu uložte v textovém souboru (txt). Informace o následujících zásadách najdete v tématu [Vytvoření a konfigurace poskytovatele ověření identity](always-encrypted-enclaves-configure-attestation.md#create-and-configure-an-attestation-provider).

  ```output
  version= 1.0;
  authorizationrules 
  {
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
  };
  ```

2. Importujte požadovanou verzi nástroje `Az.Attestation` .  

  ```powershell
  Import-Module "Az.Attestation" -MinimumVersion "0.1.8"
  ```
  
3. Vytvořte poskytovatele ověření identity. 

  ```powershell
  $attestationProviderName = "<your attestation provider name>" 
  New-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName -Location $location
  ```

4. Nakonfigurujte zásady ověřování identity.
  
  ```powershell
  $policyFile = "<the pathname of the file from step 1 in this section>"
  $teeType = "SgxEnclave"
  $policyFormat = "Text"
  $policy=Get-Content -path $policyFile -Raw
  Set-AzAttestationPolicy -Name $attestationProviderName `
    -ResourceGroupName $resourceGroupName `
    -Tee $teeType `
    -Policy $policy `
    -PolicyFormat  $policyFormat
  ```

5. Udělte vašemu poskytovateli ověřování přístup k logickému serveru Azure SQL. V tomto kroku použijete ID objektu identity spravované služby, který jste k serveru přiřadili dřív.

  ```powershell
  New-AzRoleAssignment -ObjectId $serverObjectId `
    -RoleDefinitionName "Attestation Reader" `
    -ResourceName $attestationProviderName `
    -ResourceType "Microsoft.Attestation/attestationProviders" `
    -ResourceGroupName $resourceGroupName  
  ```

6. Načtěte adresu URL ověření identity, která odkazuje na zásadu ověření identity, kterou jste nakonfigurovali pro enklávy SGX. Uložte adresu URL, jak ji budete potřebovat později.

  ```powershell
  $attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $resourceGroupName 
  $attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
  Write-Host
  Write-Host "Your attestation URL is: $attestationUrl"
  ```
  
  Adresa URL ověření identity by měla vypadat takto: `https://contososqlattestation.uks.attest.azure.net/attest/SgxEnclave`

## <a name="step-3-populate-your-database"></a>Krok 3: naplnění databáze

V tomto kroku vytvoříte tabulku a naplníte ji daty, která budete později šifrovat a dotazovat.

1. Otevřete SSMS a připojte se k databázi **ContosoHR** na logickém serveru Azure SQL, který jste vytvořili, **aniž by** v připojení k databázi Always Encrypted povoleno.
    1. V dialogovém okně **připojit k serveru** zadejte plně kvalifikovaný název serveru (například *myserver123.Database.Windows.NET*) a zadejte uživatelské jméno správce a heslo, které jste zadali při vytváření serveru.
    2. Klikněte na **možnosti >>** a vyberte kartu **Vlastnosti připojení** . Ujistěte se, že jste vybrali databázi **ContosoHR** (nikoli výchozí hlavní databázi). 
    3. Vyberte kartu **Always Encrypted** .
    4. Ujistěte **se, že není zaškrtnuté** políčko **Povolit Always Encrypted (šifrování sloupce)** .

        ![Připojit bez Always Encrypted](media/always-encrypted-enclaves/connect-without-always-encrypted-ssms.png)

    5. Klikněte na **Připojit**.

2. Vytvoří novou tabulku s názvem **zaměstnanci**.

    ```sql
    CREATE SCHEMA [HR];
    GO

    CREATE TABLE [HR].[Employees]
    (
        [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
        [SSN] [char](11) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [Salary] [money] NOT NULL
    ) ON [PRIMARY];
    GO
    ```

3. Přidejte do tabulky **Employees** několik záznamů zaměstnanců.

    ```sql
    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('795-73-9838'
            , N'Catherine'
            , N'Abel'
            , $31692);

    INSERT INTO [HR].[Employees]
            ([SSN]
            ,[FirstName]
            ,[LastName]
            ,[Salary])
        VALUES
            ('990-00-6818'
            , N'Kim'
            , N'Abercrombie'
            , $55415);
    ```


## <a name="step-4-provision-enclave-enabled-keys"></a>Krok 4: zřízení klíčů s povoleným enklávy

V tomto kroku vytvoříte hlavní klíč sloupce a šifrovací klíč sloupce, který umožní enklávy výpočty.

1. Pomocí instance SSMS z předchozího kroku v **Průzkumník objektů** rozbalte svou databázi a přejděte na   >  **klíče Always Encrypted** zabezpečení.
1. Zřídit nový hlavní klíč sloupce s povolenou enklávy:
    1. Klikněte pravým tlačítkem na **Always Encrypted klíče** a vyberte **Nový hlavní klíč sloupce...**
    2. Vyberte název hlavního klíče sloupce: **CMK1**.
    3. Ujistěte se, že jste vybrali možnost **úložiště certifikátů systému Windows (aktuální uživatel nebo místní počítač)** nebo **Azure Key Vault**.
    4. Vyberte možnost **Povolení výpočtů enklávy**.
    5. Pokud jste vybrali Azure Key Vault, přihlaste se k Azure a vyberte svůj Trezor klíčů. Další informace o tom, jak vytvořit Trezor klíčů pro Always Encrypted, najdete v tématu [Správa trezorů klíčů z Azure Portal](/archive/blogs/kv/manage-your-key-vaults-from-new-azure-portal).
    6. Vyberte svůj certifikát nebo klíč hodnoty klíče Azure, pokud už existuje, nebo klikněte na tlačítko **vygenerovat certifikát** a vytvořte nový.
    7. Vyberte **OK**.

        ![Povolení výpočtů enklávy](media/always-encrypted-enclaves/allow-enclave-computations.png)

1. Vytvořit nový šifrovací klíč sloupce s povolenou enklávy:

    1. Klikněte pravým tlačítkem na **Always Encrypted klíče** a vyberte **nový šifrovací klíč sloupce**.
    2. Zadejte název nového šifrovacího klíče sloupce: **CEK1**.
    3. V rozevíracím seznamu **hlavní klíč sloupce** vyberte hlavní klíč sloupce, který jste vytvořili v předchozích krocích.
    4. Vyberte **OK**.

## <a name="step-5-encrypt-some-columns-in-place"></a>Krok 5: šifrování některých sloupců na místě

V tomto kroku zašifrujete data uložená ve sloupcích **SSN** a **platy** uvnitř enklávy na straně serveru a pak otestujete dotaz SELECT na data.

1. Otevřete novou instanci SSMS a připojte se k databázi **pomocí** Always Encrypted povolenou pro připojení k databázi.
    1. Spustí novou instanci SSMS.
    2. V dialogovém okně **připojit k serveru** zadejte plně kvalifikovaný název serveru (například *myserver123.Database.Windows.NET*) a zadejte uživatelské jméno správce a heslo, které jste zadali při vytváření serveru.
    3. Klikněte na **možnosti >>** a vyberte kartu **Vlastnosti připojení** . Ujistěte se, že jste vybrali databázi **ContosoHR** (nikoli výchozí hlavní databázi). 
    4. Vyberte kartu **Always Encrypted** .
    5. Ujistěte se, že je zaškrtnuté políčko **povolit Always Encrypted (šifrování sloupce)** .
    6. Podle kroků v části [Krok 2: Konfigurace poskytovatele ověření identity](#step-2-configure-an-attestation-provider)zadejte adresu URL enklávy ověření identity, kterou jste získali. Podívejte se na následující snímek obrazovky.

        ![Připojit s ověřením identity](media/always-encrypted-enclaves/connect-to-server-configure-attestation.png)

    7. Vyberte **Connect** (Připojit).
    8. Pokud budete vyzváni k povolení parametrizace pro dotazy Always Encrypted, vyberte **Povolit**.



1. Použijte stejnou instanci SSMS (s povolenou Always Encrypted), otevřete nové okno dotazu a Zašifrujte sloupce **SSN** a **mzdu** spuštěním níže uvedených příkazů.

    ```sql
    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [SSN] [char] (11) COLLATE Latin1_General_BIN2
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER TABLE [HR].[Employees]
    ALTER COLUMN [Salary] [money]
    ENCRYPTED WITH (COLUMN_ENCRYPTION_KEY = [CEK1], ENCRYPTION_TYPE = Randomized, ALGORITHM = 'AEAD_AES_256_CBC_HMAC_SHA_256') NOT NULL
    WITH
    (ONLINE = ON);

    ALTER DATABASE SCOPED CONFIGURATION CLEAR PROCEDURE_CACHE;
    ```

    > [!NOTE]
    > Všimněte si, že příkaz ALTER DATABASE SCOPEed CONFIGURATION CLEAR PROCEDURE_CACHE k vymazání mezipaměti plánu dotazů pro databázi ve výše uvedeném skriptu. Po změně tabulky je nutné vymazat plány všech dávek a uložených procedur, které přistupují k tabulce a aktualizovat informace o šifrování parametrů. 

1. Chcete-li ověřit, zda jsou sloupce **SSN** a **platy** nyní zašifrovány, otevřete nové okno dotazu v instanci SSMS **bez** Always Encrypted povoleného pro připojení k databázi a spusťte následující příkaz. Okno dotazu by mělo vracet šifrované hodnoty ve sloupcích **SSN** a **plat** . Pokud spustíte stejný dotaz pomocí instance SSMS s povoleným Always Encrypted, měla by se zobrazit dešifrovaná data.

    ```sql
    SELECT * FROM [HR].[Employees];
    ```

## <a name="step-6-run-rich-queries-against-encrypted-columns"></a>Krok 6: spuštění bohatých dotazů proti šifrovaným sloupcům

Můžete spustit bohatou dotaz na šifrované sloupce. Některé zpracování dotazů se provede v enklávy na straně serveru. 

1. V instanci SSMS **s** povoleným Always Encrypted Ujistěte se, že je také povoleno parametrizace pro Always Encrypted.
    1. V hlavní nabídce SSMS vyberte **nástroje** .
    2. Vybrat **Možnosti...**.
    3. Přejděte k **provedení dotazu**  >  **SQL Server**  >  **Upřesnit**.
    4. Ujistěte se, že je zaškrtnuté políčko **Povolit parametrizace pro Always Encrypted** .
    5. Vyberte **OK**.
2. Otevřete nové okno dotazu, vložte následující dotaz a spusťte příkaz. Dotaz by měl vracet hodnoty a řádky nešifrovaného textu splňující zadaná kritéria hledání.

    ```sql
    DECLARE @SSNPattern [char](11) = '%6818';
    DECLARE @MinSalary [money] = $1000;
    SELECT * FROM [HR].[Employees]
    WHERE SSN LIKE @SSNPattern AND [Salary] >= @MinSalary;
    ```

3. Zkuste stejný dotaz znovu v instanci SSMS, která nemá povolenou Always Encrypted. Dojde k selhání.
 
## <a name="next-steps"></a>Další kroky

Po dokončení tohoto kurzu můžete přejít k jednomu z následujících kurzů:
- [Kurz: vývoj aplikace .NET pomocí Always Encrypted se zabezpečeným enclaves](/sql/connect/ado-net/sql/tutorial-always-encrypted-enclaves-develop-net-apps)
- [Kurz: vývoj aplikace .NET Framework pomocí Always Encrypted se zabezpečeným enclaves](/sql/relational-databases/security/tutorial-always-encrypted-enclaves-develop-net-framework-apps)
- [Kurz: vytváření a používání indexů ve sloupcích s povoleným enklávy pomocí náhodného šifrování](/sql/relational-databases/security/tutorial-creating-using-indexes-on-enclave-enabled-columns-using-randomized-encryption)

## <a name="see-also"></a>Viz také

- [Konfigurace a použití Always Encrypted se zabezpečeným enclaves](/sql/relational-databases/security/encryption/configure-always-encrypted-enclaves)

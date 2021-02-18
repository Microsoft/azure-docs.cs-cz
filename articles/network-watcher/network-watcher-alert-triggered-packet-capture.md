---
title: Použití zachycení paketů k proaktivnímu monitorování sítě pomocí výstrah – Azure Functions
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak vytvořit výstrahu zachytávání paketů aktivované pomocí Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 8e2af73be0fc887b132f523133159472ce1d1f98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570987"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Použití zachytávání paketů pro proaktivní monitorování sítě s výstrahami a Azure Functions

Network Watcher Capture paketů vytváří relace zachycení pro sledování provozu na virtuálních počítačích a z nich. Zachytávací soubor může mít filtr, který je definován tak, aby sledoval pouze provoz, který chcete monitorovat. Tato data se pak ukládají do objektu BLOB úložiště nebo lokálně v hostovaném počítači.

Tuto schopnost lze spustit vzdáleně z jiných scénářů automatizace, jako je například Azure Functions. Zachycení paketů poskytuje možnost spouštět proaktivní zachycení na základě definovaných anomálií sítě. Mezi další použití patří shromažďování statistik sítě, získávání informací o neoprávněných vniknutích k síti, ladění komunikace mezi klientem a serverem a další.

Prostředky, které jsou nasazené v Azure Run 24/7. Vy a vaši pracovníci nemůžete aktivně monitorovat stav všech prostředků 24/7. Například co se stane, když vznikne problém 2?

Pomocí Network Watcher, upozorňování a funkcí v rámci ekosystému Azure můžete aktivně reagovat s daty a nástroji pro řešení problémů ve vaší síti.

![Diagram zobrazuje Network Watcher rozšíření na virtuálním počítači, který přechází do segmentů T C P odeslaných > Network Watcher Network Watcher Azure Functions 100.][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Azure PowerShell](/powershell/azure/install-Az-ps).
* Existující instance Network Watcher. Pokud ho ještě nemáte, [vytvořte instanci Network Watcher](network-watcher-create.md).
* Existující virtuální počítač ve stejné oblasti jako Network Watcher s rozšířením [virtuálního počítače](../virtual-machines/extensions/network-watcher-linux.md)se [systémem Windows](../virtual-machines/extensions/network-watcher-windows.md) nebo Linux.

## <a name="scenario"></a>Scenario

V tomto příkladu váš virtuální počítač posílá více segmentů TCP, než je obvyklé, a chcete být upozorněni. Segmenty TCP se tady používají jako příklad, ale můžete použít libovolnou podmínku výstrahy.

Když se zobrazí výstraha, chcete získat data na úrovni paketů a pochopit, proč se komunikace zvýšila. Pak můžete provést kroky, které virtuální počítač vrátí do běžné komunikace.

V tomto scénáři se předpokládá, že máte existující instanci Network Watcher a skupinu prostředků s platným virtuálním počítačem.

V následujícím seznamu je přehled pracovního postupu, který se provádí:

1. Na vašem VIRTUÁLNÍm počítači se aktivuje výstraha.
1. Výstraha volá službu Azure Function prostřednictvím Webhooku.
1. Vaše funkce Azure vaši výstrahu zpracuje a spustí relaci zachytávání paketů Network Watcher.
1. Zachytávání paketů běží na virtuálním počítači a shromažďuje provoz.
1. Soubor zachytávání paketů se nahraje do účtu úložiště pro kontrolu a diagnostiku.

K automatizaci tohoto procesu vytvoříme a připojíme upozornění na našem virtuálním počítači, které se aktivuje, když dojde k incidentu. Vytvoříme také funkci, která bude volat Network Watcher.

Tento scénář provádí následující akce:

* Vytvoří funkci Azure, která spustí zachytávání paketů.
* Vytvoří pravidlo výstrahy na virtuálním počítači a nakonfiguruje pravidlo upozornění pro volání funkce Azure Functions.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

Prvním krokem je vytvoření funkce Azure pro zpracování výstrahy a vytvoření zachytávání paketů.

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**  >    >  **Function App** Compute.

    ![Vytvoření aplikace funkcí][1-1]

2. V okně **Function App** zadejte následující hodnoty a pak vyberte **OK** a vytvořte aplikaci:

    |**Nastavení** | **Hodnota** | **Podrobnosti** |
    |---|---|---|
    |**Název aplikace**|PacketCaptureExample|Název aplikace Function App|
    |**Předplatné**|[Vaše předplatné] Předplatné, pro které chcete vytvořit aplikaci Function App.||
    |**Skupina prostředků**|PacketCaptureRG|Skupina prostředků, která obsahuje aplikaci Function App|
    |**Plán hostování**|Plán Consumption| Typ plánování použití aplikace Function App Možnosti jsou spotřeba nebo plán Azure App Service. |
    |**Umístění**|USA – střed| Oblast, ve které se má vytvořit aplikace Function App|
    |**Účet úložiště**|automaticky generované| Účet úložiště, který Azure Functions potřeby pro účely obecného úložiště.|

3. V okně **aplikací funkcí PacketCaptureExample** vyberte **funkce**  >  **vlastní funkce**  > **+** .

4. Vyberte **HttpTrigger-PowerShell** a potom zadejte zbývající informace. Nakonec, pokud chcete vytvořit funkci, vyberte **vytvořit**.

    |**Nastavení** | **Hodnota** | **Podrobnosti** |
    |---|---|---|
    |**Scénář**|Experimentální|Typ scénáře|
    |**Pojmenování funkce**|AlertPacketCapturePowerShell|Název funkce|
    |**Úroveň autorizace**|Funkce|Úroveň autorizace pro funkci|

![Příklad funkcí][functions1]

> [!NOTE]
> Šablona prostředí PowerShell je experimentální a nemá plnou podporu.

V tomto příkladu jsou vyžadovány vlastní nastavení a jsou vysvětleny v následujících krocích.

### <a name="add-modules"></a>Přidat moduly

Pokud chcete použít rutiny Network Watcher PowerShellu, nahrajte do aplikace Function App nejnovější modul PowerShellu.

1. Na svém místním počítači s nainstalovanými nejnovějšími moduly Azure PowerShell spusťte následující příkaz PowerShellu:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Tento příklad poskytuje místní cestu Azure PowerShellch modulů. Tyto složky jsou používány v pozdějším kroku. Moduly používané v tomto scénáři jsou:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Složky PowerShellu][functions5]

1. Vyberte možnost **nastavení aplikace Function App**  >  **Přejít na Editor služby App Service**.

    ![Nastavení aplikace funkcí][functions2]

1. Klikněte pravým tlačítkem na složku **AlertPacketCapturePowershell** a pak vytvořte složku s názvem **azuremodules**. 

4. Vytvořte podsložku pro každý modul, který potřebujete.

    ![Složka a podsložky][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Klikněte pravým tlačítkem myši na podsložku **AZ. Network** a pak vyberte **nahrát soubory**. 

6. Přejít do modulů Azure. V místní složce **AZ. Network** vyberte všechny soubory ve složce. Pak vyberte **OK**. 

7. Opakujte tyto kroky pro **AZ. Accounts** a **AZ. Resources**.

    ![Nahrání souborů][functions6]

1. Po dokončení by měly všechny složky obsahovat soubory modulu PowerShell z místního počítače.

    ![Soubory PowerShellu][functions7]

### <a name="authentication"></a>Authentication

Chcete-li použít rutiny prostředí PowerShell, je nutné provést ověření. Ověřování nakonfigurujete v aplikaci Function App. Pokud chcete nakonfigurovat ověřování, musíte nakonfigurovat proměnné prostředí a nahrát do aplikace Function app soubor se zašifrovaným klíčem.

> [!NOTE]
> Tento scénář poskytuje pouze jeden příklad implementace ověřování pomocí Azure Functions. To lze provést ještě jiným způsobem.

#### <a name="encrypted-credentials"></a>Šifrovaná pověření

Následující skript prostředí PowerShell vytvoří soubor klíče s názvem **PassEncryptKey. Key**. Poskytuje také šifrovanou verzi hesla, která je zadána. Toto heslo je stejné heslo, které je definováno pro Azure Active Directory aplikaci, která se používá pro ověřování.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

V Editor služby App Service aplikace Function App vytvořte ve složce **AlertPacketCapturePowerShell** složku s názvem **Keys** . Pak nahrajte soubor **PassEncryptKey. Key** , který jste vytvořili v předchozí ukázce prostředí PowerShell.

![Klíč funkcí][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Načíst hodnoty pro proměnné prostředí

Posledním požadavkem je nastavení proměnných prostředí, které jsou nezbytné pro přístup k hodnotám pro ověřování. V následujícím seznamu jsou uvedeny proměnné prostředí, které jsou vytvořeny:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

ID klienta je ID aplikace aplikace v Azure Active Directory.

1. Pokud ještě nemáte aplikaci, která se má použít, spusťte následující příklad k vytvoření aplikace.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Heslo, které použijete při vytváření aplikace, by mělo být stejné jako heslo, které jste vytvořili dříve při ukládání souboru klíče.

1. V Azure Portal vyberte **předplatná**. Vyberte předplatné, které chcete použít, a pak vyberte **řízení přístupu (IAM)**.

    ![Funkce IAM][functions9]

1. Zvolte účet, který chcete použít, a pak vyberte **vlastnosti**. Zkopírujte ID aplikace.

    ![ID aplikace Functions][functions10]

#### <a name="azuretenant"></a>AzureTenant

Získejte ID tenanta spuštěním následující ukázky prostředí PowerShell:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Hodnotou proměnné prostředí AzureCredPassword je hodnota, kterou získáte spuštěním následující ukázky prostředí PowerShell. Tento příklad je stejný, který je zobrazený v předchozím **šifrovaném oddílu přihlašovací údaje** . Hodnota, která je potřebná, je výstupem `$Encryptedpassword` proměnné.  Toto je heslo instančního objektu, které jste zašifroval pomocí skriptu PowerShellu.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Uložení proměnných prostředí

1. Přejít do aplikace Function App. Pak vyberte **funkce nastavení aplikace**  >  **Konfigurovat nastavení aplikace**.

    ![Konfigurace nastavení aplikace][functions11]

1. Přidejte proměnné prostředí a jejich hodnoty do nastavení aplikace a pak vyberte **Uložit**.

    ![Nastavení aplikace][functions12]

### <a name="add-powershell-to-the-function"></a>Přidání PowerShellu do funkce

Nyní je čas provést volání do Network Watcher v rámci funkce Azure Functions. V závislosti na požadavcích se implementace této funkce může lišit. Obecný tok kódu je však následující:

1. Vstupní parametry procesu.
2. Dotazování existujících zachytávání paketů za účelem ověření omezení a vyřešení konfliktů názvů.
3. Vytvořte zachytávání paketů s příslušnými parametry.
4. Pravidelné dotazování na sběr paketů, dokud není dokončeno.
5. Upozorněte uživatele, že relace zachycení paketů je dokončená.

V následujícím příkladu je kód prostředí PowerShell, který lze použít ve funkci. Existují hodnoty, které je třeba nahradit pro **SubscriptionId**, **resourceGroupName** a **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Načtení adresy URL funkce 
1. Po vytvoření funkce nakonfigurujte upozornění tak, aby volalo adresu URL, která je přidružená k funkci. Tuto hodnotu získáte zkopírováním adresy URL funkce z aplikace Function App.

    ![Hledání adresy URL funkce][functions13]

2. Zkopírujte adresu URL funkce aplikace Function App.

    ![Kopírování adresy URL funkce][2]

Pokud požadujete vlastní vlastnosti v datové části požadavku POST Webhooku, přečtěte si téma [Konfigurace Webhooku na upozornění metriky Azure](../azure-monitor/alerts/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurace upozornění na virtuálním počítači

Výstrahy je možné nakonfigurovat tak, aby upozornily na uživatele, když konkrétní metrika přechází prahová hodnota, která je jí přiřazena. V tomto příkladu je výstraha v odeslaných segmentech TCP, ale výstraha může být aktivována pro mnoho dalších metrik. V tomto příkladu je výstraha nakonfigurována pro volání Webhooku pro volání funkce.

### <a name="create-the-alert-rule"></a>Vytvoření pravidla výstrahy

Přejít na existující virtuální počítač a pak přidat pravidlo výstrahy. Podrobnější dokumentaci týkající se konfigurace výstrah najdete v popisu [Vytvoření upozornění v Azure monitor pro služby Azure – Azure Portal](../azure-monitor/alerts/alerts-classic-portal.md). V okně **pravidlo výstrahy** zadejte následující hodnoty a pak vyberte **OK**.

  |**Nastavení** | **Hodnota** | **Podrobnosti** |
  |---|---|---|
  |**Název**|TCP_Segments_Sent_Exceeded|Název pravidla výstrahy.|
  |**Popis**|Počet odeslaných segmentů TCP překročení prahové hodnoty|Popis pravidla výstrahy.|
  |**Metrika**|Odeslané segmenty TCP| Metrika, která se má použít k aktivaci výstrahy. |
  |**Condition** (Podmínka)|Větší než| Podmínka, která se má použít při vyhodnocování metriky.|
  |**Prahová hodnota**|100| Hodnota metriky, která aktivuje výstrahu. Tato hodnota by měla být nastavená na platnou hodnotu pro vaše prostředí.|
  |**Hodin**|Za posledních pět minut| Určuje období, ve kterém se má hledat prahová hodnota metriky.|
  |**Webhook**|[adresa URL Webhooku z aplikace Function app]| Adresa URL Webhooku z aplikace Function App, která byla vytvořena v předchozích krocích.|

> [!NOTE]
> Metrika segmentů TCP není ve výchozím nastavení povolena. Další informace o tom, jak povolit další metriky, najdete v tématu [povolení monitorování a diagnostiky](../azure-monitor/overview.md).

## <a name="review-the-results"></a>Kontrola výsledků

Po kritériích pro aktivační události výstrahy se vytvoří zachycení paketů. Přejít na Network Watcher a pak vyberte **zachytávání paketů**. Na této stránce můžete vybrat odkaz soubor pro zachytávání paketů a stáhnout zachytávání paketů.

![Zobrazit zachytávání paketů][functions14]

Pokud je zachytávací soubor uložený místně, můžete ho načíst tak, že se přihlásíte k virtuálnímu počítači.

Pokyny ke stahování souborů z účtů Azure Storage najdete v tématu Začínáme [s úložištěm objektů BLOB v Azure pomocí .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Další nástroj, který můžete použít, je [Průzkumník služby Storage](https://storageexplorer.com/).

Po stažení je vaše zachycení možné zobrazit pomocí libovolného nástroje, který může číst soubor **. Cap** . Následují odkazy na dva z těchto nástrojů:

- [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide)
- [Nástroj](https://www.wireshark.org/)

## <a name="next-steps"></a>Další kroky

Naučte se, jak zobrazit zachycení paketů pomocí [analýzy zachycení paketů pomocí programu Wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
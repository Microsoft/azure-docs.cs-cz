---
title: Použití sběru paketů k proaktivnímu monitorování sítě pomocí výstrah – Funkce Azure
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak vytvořit výstrahu aktivované zachycení paketů s Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: ea506e137d71fc3124a4f93f1e97750a08dd4284
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842933"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Použití sběru paketů pro proaktivní monitorování sítě pomocí výstrah a funkcí Azure

Zachycení paketu Sledování sítě vytvoří zachytávací relace pro sledování provozu ve virtuálních počítačích a z virtuálních počítačů. Sběrný soubor může mít filtr, který je definován ke sledování pouze přenosů, které chcete sledovat. Tato data se pak ukládají do objektu blob úložiště nebo místně v počítači hosta.

Tuto funkci lze spustit vzdáleně z jiných scénářů automatizace, jako jsou funkce Azure. Zachytávání paketů umožňuje spouštět proaktivní sběr dat na základě definovaných síťových anomálií. Mezi další použití patří shromažďování síťových statistik, získávání informací o vniknutích do sítě, ladění komunikace mezi klientem a serverem a další.

Prostředky, které se nasazují v Azure, běží 24 hodin denně, 7 dní v hod. Vy a vaši zaměstnanci nemůžete aktivně sledovat stav všech zdrojů 24 hodin denně, 7 dní v ruce. Co se například stane, když dojde k problému ve 2:00?

Pomocí sledování sítě, upozorňování a funkcí z ekosystému Azure můžete proaktivně reagovat pomocí dat a nástrojů k řešení problémů ve vaší síti.

![Scénář][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

* Nejnovější verze [Azure PowerShellu](/powershell/azure/install-Az-ps).
* Existující instance sledování sítě. Pokud ji ještě nemáte, [vytvořte instanci sledovacího programu sítě](network-watcher-create.md).
* Existující virtuální počítač ve stejné oblasti jako Sledovací modul sítě s [rozšířením Windows](../virtual-machines/windows/extensions-nwa.md) nebo [linuxovým virtuálním počítačem](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scénář

V tomto příkladu váš virtuální počítač odesílá více segmentů TCP než obvykle a chcete být upozorněni. Segmenty TCP se zde používají jako příklad, ale můžete použít libovolnou podmínku výstrahy.

Když budete upozorněni, chcete přijímat data na úrovni paketů, abyste pochopili, proč se komunikace zvýšila. Pak můžete podniknout kroky k návratu virtuálního počítače k běžné komunikaci.

Tento scénář předpokládá, že máte existující instanci sledovacího programu sítě a skupinu prostředků s platným virtuálním počítačem.

Následující seznam obsahuje přehled pracovního postupu, který probíhá:

1. Na vašem virtuálním počítači se aktivuje výstraha.
1. Výstraha volá vaši funkci Azure přes webhooku.
1. Vaše funkce Azure zpracuje výstrahu a spustí relaci zachycení paketu Sledování sítě.
1. Zachycení paketů běží na virtuálním počítači a shromažďuje přenosy.
1. Soubor pro sběr paketů se nahraje do účtu úložiště pro kontrolu a diagnostiku.

Chcete-li tento proces automatizovat, vytvoříme a připojíme výstrahu na našem virtuálním počítači, aby se aktivovala, když dojde k incidentu. Také vytvoříme funkci pro volání do sledování sítě.

Tento scénář provádí následující akce:

* Vytvoří funkci Azure, která spustí sběr paketů.
* Vytvoří pravidlo výstrahy na virtuálním počítači a nakonfiguruje pravidlo výstrahy pro volání funkce Azure.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

Prvním krokem je vytvoření funkce Azure pro zpracování výstrahy a vytvoření zachycení paketů.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit aplikaci** > **výpočetní** > **funkce prostředků**.

    ![Vytvoření aplikace funkcí][1-1]

2. V okně **Aplikace funkce** zadejte následující hodnoty a pak vyberte **OK,** chcete-li aplikaci vytvořit:

    |**Nastavení** | **Hodnotu** | **Podrobnosti** |
    |---|---|---|
    |**Název aplikace**|Příklad packetcapture|Název aplikace funkce.|
    |**Předplatné**|[Vaše předplatné] Předplatné, pro které chcete vytvořit aplikaci funkce.||
    |**Skupina prostředků**|PacketCaptureRG|Skupina prostředků, která obsahuje aplikaci funkce.|
    |**Plán hostování**|Plán Consumption| Typ plánu, který aplikace funkce používá. Možnosti jsou spotřeba nebo plán služby Azure App Service. |
    |**Umístění**|USA – střed| Oblast, ve které chcete vytvořit aplikaci funkce.|
    |**Účet úložiště**|{automaticky vygenerováno}| Účet úložiště, který Azure Functions potřebuje pro úložiště pro obecné účely.|

3. V okně **PacketCaptureExample Function Apps** vyberte**funkce** >**+** **Functions** > Custom .

4. Vyberte **httptrigger-powershell**a zadejte zbývající informace. Chcete-li funkci vytvořit, vyberte **příkaz Vytvořit**.

    |**Nastavení** | **Hodnotu** | **Podrobnosti** |
    |---|---|---|
    |**Scénář**|Experimentální|Typ scénáře|
    |**Pojmenování funkce**|Prostředí AlertPacketCapturePowerShell|Název funkce|
    |**Úroveň autorizace**|Funkce|Úroveň autorizace funkce|

![Příklad funkcí][functions1]

> [!NOTE]
> Šablona prostředí PowerShell je experimentální a nemá plnou podporu.

V tomto příkladu jsou vyžadována vlastní nastavení a jsou vysvětlena v následujících krocích.

### <a name="add-modules"></a>Přidat moduly

Chcete-li použít rutiny PowerShell u network watcheru, nahrajte nejnovější modul Prostředí PowerShell do aplikace funkce.

1. V místním počítači s nainstalovanými nejnovějšími moduly Azure PowerShell upusťte následující příkaz PowerShellu:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    Tento příklad poskytuje místní cestu modulů Azure PowerShell. Tyto složky se používají v pozdějším kroku. Moduly, které se používají v tomto scénáři jsou:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Složky powershellu][functions5]

1. Vyberte **Nastavení** > aplikace Funkce**Přejděte na Editor služeb aplikací**.

    ![Nastavení aplikace funkcí][functions2]

1. Klepněte pravým tlačítkem myši na složku **AlertPacketCapturePowershell** a vytvořte složku nazvanou **azuremodules**. 

4. Vytvořte podsložku pro každý modul, který potřebujete.

    ![Složky a podsložky][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Klepněte pravým tlačítkem myši na podsložku **Az.Network** a vyberte příkaz **Odeslat soubory**. 

6. Přejděte do modulů Azure. V místní složce **Az.Network** vyberte všechny soubory ve složce. Pak vyberte **OK**. 

7. Opakujte tento postup pro **Az.Accounts** a **Az.Resources**.

    ![Nahrání souborů][functions6]

1. Po dokončení by každá složka měla mít soubory modulu PowerShell z místního počítače.

    ![Soubory prostředí PowerShell][functions7]

### <a name="authentication"></a>Ověřování

Chcete-li použít rutiny prostředí PowerShell, je nutné ověřit. Ověřování nakonfigurujete v aplikaci funkce. Chcete-li konfigurovat ověřování, je nutné nakonfigurovat proměnné prostředí a nahrát šifrovaný soubor klíče do aplikace funkce.

> [!NOTE]
> Tento scénář poskytuje pouze jeden příklad, jak implementovat ověřování pomocí funkce Azure. Existují i jiné způsoby, jak to udělat.

#### <a name="encrypted-credentials"></a>Šifrovaná pověření

Následující skript prostředí PowerShell vytvoří soubor klíče s názvem **PassEncryptKey.key**. Poskytuje také šifrovanou verzi hesla, které je součástí dodávky. Toto heslo je stejné heslo, které je definováno pro aplikaci Azure Active Directory, která se používá pro ověřování.

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

V Editoru služby App Service aplikace funkce vytvořte složku s názvem **klíče** v části **AlertPacketCapturePowerShell**. Potom nahrajte soubor **PassEncryptKey.key,** který jste vytvořili v předchozí ukázce prostředí PowerShell.

![Funkční klávesa][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Načtení hodnot pro proměnné prostředí

Konečným požadavkem je nastavení proměnných prostředí, které jsou nezbytné pro přístup k hodnotám pro ověřování. V následujícím seznamu jsou uvedeny vytvořené proměnné prostředí:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

ID klienta je ID aplikace ve službě Azure Active Directory.

1. Pokud ještě nemáte aplikaci k použití, spusťte následující příklad k vytvoření aplikace.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Heslo, které používáte při vytváření aplikace, by mělo být stejné heslo, které jste vytvořili dříve při ukládání souboru klíče.

1. Na webu Azure Portal vyberte **Předplatná**. Vyberte předplatné, které chcete použít, a pak vyberte **řízení přístupu (IAM)**.

    ![Funkce IAM][functions9]

1. Vyberte účet, který chcete použít, a pak vyberte **Vlastnosti**. Zkopírujte ID aplikace.

    ![ID aplikace funkcí][functions10]

#### <a name="azuretenant"></a>AzureTenant

Získání ID klienta spuštěním následující ukázky Prostředí PowerShell:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Hodnota proměnné prostředí AzureCredPassword je hodnota, kterou získáte spuštěním následující ukázky prostředí PowerShell. Tento příklad je stejný, který je uveden v předchozí části **Šifrované přihlašovací údaje.** Hodnota, která je potřeba, je `$Encryptedpassword` výstup proměnné.  Toto je heslo instančního objektu, které jste zašifrovali pomocí skriptu prostředí PowerShell.

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

1. Přejděte do aplikace funkce. Pak vyberte **Nastavení** > aplikace Funkce**Konfigurace nastavení aplikace**.

    ![Konfigurace nastavení aplikace][functions11]

1. Přidejte proměnné prostředí a jejich hodnoty do nastavení aplikace a pak vyberte **Uložit**.

    ![Nastavení aplikace][functions12]

### <a name="add-powershell-to-the-function"></a>Přidání PowerShellu do funkce

Teď je čas volat do Sledování sítě z funkce Azure. V závislosti na požadavcích se může implementace této funkce lišit. Obecný tok kódu je však následující:

1. Zpracovat vstupní parametry.
2. Dotaz na existující zachycení paketů za účelem ověření omezení a vyřešení konfliktů názvů.
3. Vytvořte sběr paketů s příslušnými parametry.
4. Dotazování paketů pravidelně, dokud není dokončena.
5. Upozorněte uživatele, že relace zachycení paketu je dokončena.

Následující příklad je kód prostředí PowerShell, který lze použít ve funkci. Existují hodnoty, které je třeba nahradit pro **subscriptionId**, **resourceGroupName**a **storageAccountName**.

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
1. Po vytvoření funkce nakonfigurujte výstrahu tak, aby volala adresu URL, která je přidružená k této funkci. Chcete-li získat tuto hodnotu, zkopírujte adresu URL funkce z aplikace funkce.

    ![Hledání adresy URL funkce][functions13]

2. Zkopírujte adresu URL funkce aplikace pro funkci.

    ![Kopírování adresy URL funkce][2]

Pokud požadujete vlastní vlastnosti v datové části požadavku webhooku POST, přečtěte si [stránku Konfigurace webhooku na upozornění metriky Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurace výstrahy na virtuálním počítači

Výstrahy lze nakonfigurovat tak, aby upozorňovaly jednotlivce, když určitá metrika překročí prahovou hodnotu, která je k ní přiřazena. V tomto příkladu je výstraha na segmenty TCP, které jsou odeslány, ale výstraha může být spuštěna pro mnoho dalších metrik. V tomto příkladu je výstraha nakonfigurována tak, aby volala webhookpro volání funkce.

### <a name="create-the-alert-rule"></a>Vytvoření pravidla výstrahy

Přejděte na existující virtuální počítač a přidejte pravidlo výstrahy. Podrobnější dokumentaci ke konfiguraci výstrah najdete na [webu Vytvoření výstrah ve službě Azure Monitor pro služby Azure – portál Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Do okna Pravidlo **výstrahy** zadejte následující hodnoty a pak vyberte **OK**.

  |**Nastavení** | **Hodnotu** | **Podrobnosti** |
  |---|---|---|
  |**Název**|TCP_Segments_Sent_Exceeded|Název pravidla výstrahy.|
  |**Popis**|Odeslané segmenty TCP překročily prahovou hodnotu|Popis pravidla výstrahy.|
  |**Metrika**|Odeslané segmenty Protokolu TCP| Metrika, která se má použít k aktivaci výstrahy. |
  |**Podmínka**|Větší než| Podmínka, která má být používána při vyhodnocování metriky.|
  |**Práh**|100| Hodnota metriky, která aktivuje výstrahu. Tato hodnota by měla být nastavena na platnou hodnotu pro vaše prostředí.|
  |**Období**|Za posledních pět minut| Určuje období, ve kterém chcete vyhledat prahovou hodnotu v metrice.|
  |**Webový hák**|[webhookurl z aplikace funkce]| Adresa URL webhooku z aplikace funkce, která byla vytvořena v předchozích krocích.|

> [!NOTE]
> Metrika segmentů TCP není ve výchozím nastavení povolena. Další informace o povolení dalších metrik najdete v části [Povolit monitorování a diagnostiku](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Kontrola výsledků

Po aktivaci kritérií pro výstrahu je vytvořeno zachycení paketu. Přejděte na sledovací modul sítě a vyberte **možnost Zachytávání paketů**. Na této stránce můžete vybrat odkaz na soubor sběru paketů a stáhnout jej.

![Zobrazit sběr paketů][functions14]

Pokud je sběrný soubor uložen místně, můžete jej načíst přihlášením k virtuálnímu počítači.

Pokyny ke stahování souborů z účtů úložiště Azure najdete v tématu [Začínáme s úložištěm objektů Blob Azure pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, který můžete [použít,](https://storageexplorer.com/)je Průzkumník úložiště .

Po stažení sběru jej můžete zobrazit pomocí libovolného nástroje, který dokáže číst soubor **CAP.** Následují odkazy na dva z těchto nástrojů:

- [Analyzátor zpráv společnosti Microsoft](https://technet.microsoft.com/library/jj649776.aspx)
- [Wireshark](https://www.wireshark.org/)

## <a name="next-steps"></a>Další kroky

Naučte se, jak zobrazit zachytávání paketů na stránce [Analýza zachytávání paketů pomocí aplikace Wireshark](network-watcher-deep-packet-inspection.md).


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

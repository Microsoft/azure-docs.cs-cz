---
title: Použití zachytávání paketů k proaktivnímu monitorování sítě pomocí výstrah a Azure Functions | Dokumentace Microsoftu
description: Tento článek popisuje, jak vytvořit zachytávání paketů upozornění aktivovaných pomocí služby Azure Network Watcher
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fd7e7151d8ec676239ed810fb700149aab0fe0fa
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427398"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Použití zachytávání paketů pro Proaktivní monitorování sítě pomocí výstrah a Azure Functions

Zachytávání paketů sledovací proces sítě vytvoří relace zachycení sledujte provoz do a z virtuálních počítačů. Zachytávací soubor může mít filtr, který je definován sledovat pouze provoz, který chcete monitorovat. Tato data se pak ukládá v objektu blob, úložiště nebo místně na počítači hosta.

Tuto funkci můžete spustit vzdáleně z jiných automatizace scénářů, jako je Azure Functions. Zachytávání paketů dává možnost spouštět proaktivní zachycení založené na definované sítě anomálie. Mezi další použití patří shromažďování statistik sítě, získávání informací o síti vniknutí, ladění komunikaci klienta se serverem a další.

Prostředky, které jsou nasazené v Azure spouštět 24 hodin denně 7. Vás a vašich zaměstnanců nelze sledovat aktivně stavu všech prostředků 24 hodin denně 7. Co se například stane, když dojde k problému na 2: 00?

Pomocí Network Watcher, upozorňování a funkce z v rámci ekosystému Azure můžete proaktivně reagovat s daty a nástroje pro řešení problémů ve vaší síti.

![Scénář][scenario]

## <a name="prerequisites"></a>Požadavky

* Nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
* Stávající instance Network Watcheru. Pokud ho ještě nemáte, [vytvoření instance služby Network Watcher](network-watcher-create.md).
* Existující virtuální počítač ve stejné oblasti jako sledovací proces sítě se [rozšíření Windows](../virtual-machines/windows/extensions-nwa.md) nebo [rozšíření virtuálního počítače Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scénář

V tomto příkladu váš virtuální počítač odesílá víc segmentů TCP než obvykle a chcete být upozorněni. Segmentů TCP slouží jako příklad je zde však můžete použít jakoukoli podmínku upozornění.

Když se zobrazí výstraha, chcete dostávat data na úrovni paketů pochopit, proč bylo zvýšeno komunikace. Potom můžete provést kroky a vraťte se na regulární komunikaci virtuálního počítače.

Tento scénář předpokládá, že máte stávající instance Network Watcheru a skupinu prostředků s platným virtuálním počítačem.

V následujícím seznamu je přehled toho, které u něho pracovního postupu:

1. Na svém virtuálním počítači se aktivuje upozornění.
1. Upozornění volá funkci Azure pomocí webhooku.
1. Funkce Azure zpracovávat upozornění a spuštění relace zachytávání paketů Network Watcher.
1. Zachytávání paketů běží na virtuálním počítači a shromažďuje provoz.
1. Soubor zachycení paketu je nahráli do účtu úložiště pro kontrolu a diagnostiku.

K automatizaci tohoto procesu, můžeme vytvořit a připojit upozornění na naše virtuálním počítači aktivovat, když dojde k incidentu. Můžeme také vytvořit funkci, která volá do Network Watcher.

Tento scénář provede následující akce:

* Vytvoří funkci Azure, který se spustí zachytávání paketů.
* Vytvoří pravidlo upozornění na virtuálním počítači a nakonfiguruje pravidlo výstrahy pro volání funkce Azure functions.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

Prvním krokem je vytvoření funkce Azure ke zpracování upozornění a vytvořit zachytávání paketů.

1. V [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek** > **Compute** > **aplikace Function App**.

    ![Vytvoření aplikace function app][1-1]

2. Na **aplikace Function App** okně zadejte následující hodnoty a pak vyberte **OK** vytvořte aplikaci:

    |**Nastavení** | **Hodnota** | **Podrobnosti** |
    |---|---|---|
    |**Název aplikace**|PacketCaptureExample|Název aplikace function app.|
    |**Předplatné**|[Vaše předplatné] Předplatné, pro který chcete vytvořit aplikaci function app.||
    |**Skupina prostředků**|PacketCaptureRG|Skupinu prostředků k obsáhnutí aplikace function app.|
    |**Plán hostování**|Plán Consumption| Typ plánu vaše aplikace používá funkce. Možnosti jsou spotřeby nebo plán služby App Service. |
    |**Umístění**|USA – střed| Oblast, ve kterém chcete vytvořit aplikaci function app.|
    |**Účet úložiště**|{automaticky generované}| Účet úložiště, Azure Functions potřebuje pro úložiště pro obecné účely.|

3. Na **aplikace Function App PacketCaptureExample** okně vyberte **funkce** > **vlastní funkce**  >  **+**.

4. Vyberte **HttpTrigger – Powershell**a pak zadejte zbývající informace. Nakonec vytvořte novou funkci, vyberte **vytvořit**.

    |**Nastavení** | **Hodnota** | **Podrobnosti** |
    |---|---|---|
    |**Scénář**|Experimentální|Typ scénáře|
    |**Pojmenujte svoji funkci**|AlertPacketCapturePowerShell|Název funkce|
    |**Úroveň autorizace**|Funkce|Úroveň autorizace pro funkci|

![Příklad funkce][functions1]

> [!NOTE]
> Prostředí PowerShell šablona je experimentální a nemá plnou podporu.

Vlastní nastavení se vyžadují pro účely tohoto příkladu a jsou vysvětlené v následujících krocích.

### <a name="add-modules"></a>Přidat moduly

Pokud chcete používat rutiny Powershellu sledovací proces sítě, nahrajte do aplikace function app nejnovější modul Powershellu.

1. Na místním počítači s nejnovější moduly Azure Powershellu, které jsou nainstalovány spusťte následující příkaz Powershellu:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    V tomto příkladu obsahuje místní cestu modulů Azure Powershellu. Tyto složky se používají v pozdějším kroku. Moduly, které se používají v tomto scénáři jsou:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![Složky prostředí PowerShell][functions5]

1. Vyberte **fungovat nastavení aplikace** > **přejít do App Service Editor**.

    ![Nastavení aplikace funkcí][functions2]

1. Klikněte pravým tlačítkem myši **AlertPacketCapturePowershell** složky a pak vytvořte složku s názvem **azuremodules**. 

4. Vytvořte podsložku pro každý modul, který potřebujete.

    ![Složky a podsložky][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Klikněte pravým tlačítkem myši **AzureRM.Network** podsložky a pak vyberte **nahrát soubory**. 

6. Přejdete na moduly Azure. Místní **AzureRM.Network** složky, vyberte všechny soubory ve složce. Pak vyberte **OK**. 

7. Opakujte tyto kroky pro **AzureRM.Profile** a **azurerm.resources zavedla**.

    ![Nahrání souborů][functions6]

1. Jakmile dokončíte, každá složka obsahuje soubory modulu prostředí PowerShell z místního počítače.

    ![Soubory prostředí PowerShell][functions7]

### <a name="authentication"></a>Authentication

Pokud chcete používat rutiny prostředí PowerShell, je třeba ověřit. Konfigurace ověřování do aplikace function App. Postup konfigurace ověřování, musíte nakonfigurovat proměnné prostředí a nahrání šifrovaného klíče souboru do aplikace function app.

> [!NOTE]
> Tento scénář obsahuje pouze jeden příklad implementace ověřování s využitím Azure Functions. Existují jiné způsoby, jak to provést.

#### <a name="encrypted-credentials"></a>Zašifrované přihlašovací údaje

Následující skript Powershellu vytvoří soubor klíče s názvem **PassEncryptKey.key**. Poskytuje také zašifrovaná verze heslo, které pochází. Toto heslo je stejné heslo, které je definováno pro aplikace Azure Active Directory, která se používá k ověřování.

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

V App Service editoru z aplikace function app, vytvořte složku s názvem **klíče** pod **AlertPacketCapturePowerShell**. Nahrajte **PassEncryptKey.key** soubor, který jste vytvořili v předchozí ukázce Powershellu.

![Klíč funkce][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Načíst hodnoty pro proměnné prostředí

Poslední požadavek je nastavit proměnné prostředí, které jsou potřebné pro přístup k hodnotám pro ověřování. Následující seznam obsahuje proměnné prostředí, které jsou vytvořeny:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

ID klienta je ID aplikace pro aplikaci v Azure Active Directory.

1. Pokud ještě nemáte aplikaci pro použití, spusťte následující příklad k vytvoření aplikace.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Heslo, které používáte při vytváření aplikace by měly být stejné heslo, které jste předtím vytvořili při ukládání souboru klíče.

1. Na webu Azure Portal, vyberte **předplatná**. Vyberte předplatné, které chcete použít a potom vyberte **řízení přístupu (IAM)**.

    ![Funkce IAM][functions9]

1. Vyberte účet, který chcete použít a potom vyberte **vlastnosti**. Zkopírujte ID aplikace.

    ![ID aplikace funkcí][functions10]

#### <a name="azuretenant"></a>AzureTenant

Získejte ID tenanta spuštěním následující ukázku prostředí PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Hodnota proměnné prostředí AzureCredPassword je hodnota, kterou můžete získat ve spuštění následující ukázku prostředí PowerShell. Tento příklad je stejný jako ten, který je zobrazen v předchozím **zašifrované přihlašovací údaje** oddílu. Hodnota, která je potřeba je výstup `$Encryptedpassword` proměnné.  Toto je heslo instančního objektu služby, který jste zašifrovali pomocí skriptu prostředí PowerShell.

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

### <a name="store-the-environment-variables"></a>Store proměnné prostředí

1. Přejděte do aplikace function app. Potom vyberte **fungovat nastavení aplikace** > **nakonfigurovat nastavení aplikace**.

    ![Konfigurace nastavení aplikace][functions11]

1. Přidání proměnné prostředí a jejich hodnoty nastavení aplikace a pak vyberte **Uložit**.

    ![Nastavení aplikací][functions12]

### <a name="add-powershell-to-the-function"></a>Přidání prostředí PowerShell do funkce

Nyní je čas pro volání do Network Watcher z v rámci funkce Azure functions. V závislosti na požadavcích implementaci této funkce se můžou lišit. Obecný tok kódu je však následujícím způsobem:

1. Vstupní parametry procesu.
2. Paket existující dotaz zaznamená ověření omezení a vyřešte konflikty názvů.
3. Vytvoření zachytávání paketů s příslušnými parametry.
4. Zachytávání paketů dotazování pravidelně, dokud neskončí.
5. Upozorněte uživatele, že relace zachytávání paketů je dokončen.

V následujícím příkladu je kódu Powershellu, který lze použít ve funkci. Existují hodnoty, které je potřeba nahradit pro **subscriptionId**, **resourceGroupName**, a **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Profile\AzureRM.Profile.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Network\AzureRM.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\AzureRM.Resources\AzureRM.Resources.psd1" -Global

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
            Connect-AzureRmAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}
                $networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName

                #Get existing packetCaptures
                $packetCaptures = Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzureRmNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Načíst adresu URL funkce 
1. Po vytvoření funkce, nastavte upozornění pro adresu URL, která je přidružená k funkci volání. Pokud chcete získat tuto hodnotu, zkopírujte adresu URL funkce z vaší aplikace function app.

    ![Jak najít adresu URL funkce][functions13]

2. Zkopírujte adresu URL funkce pro vaši aplikaci function app.

    ![Zkopírujete adresu URL funkce][2]

Pokud budete potřebovat vlastní vlastnosti v datové části požadavku POST webhooku, podívejte se na [konfigurace webhooku v upozornění Azure metriky](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurace výstrahy na virtuálním počítači

Výstrahy můžete nakonfigurovat konkrétní metrika překročí mezní hodnotu, která je přiřazena upozornit jednotlivce. V tomto příkladu je výstraha na segmentů TCP, které jsou odeslány, ale může být výstraha pro mnoho dalších metrik. V tomto příkladu je nakonfigurované upozornění tak k zavolání webhooku pro volání funkce.

### <a name="create-the-alert-rule"></a>Vytvořit pravidlo upozornění

Přejít na existující virtuální počítač a pak přidejte pravidlo upozornění. Podrobnější dokumentaci týkající se konfigurace výstrahy najdete v [vytvoření výstrah ve službě Azure Monitor pro Azure services – Azure portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Zadejte následující hodnoty **pravidlo upozornění** okna a pak vyberte **OK**.

  |**Nastavení** | **Hodnota** | **Podrobnosti** |
  |---|---|---|
  |**Název**|TCP_Segments_Sent_Exceeded|Název pravidla výstrahy.|
  |**Popis**|Byla překročena prahová hodnota odeslaných segmentů TCP|Popis pro pravidlo upozornění.||
  |**Metrika**|Odeslání segmentů TCP| Metrika používat pro aktivaci upozornění. |
  |**Podmínka**|Větší než| Podmínka má použít při vyhodnocování metriku.|
  |**Prahová hodnota**|100| Hodnota, která aktivuje upozornění metriky. Tato hodnota by měla nastavena na platnou hodnotu pro vaše prostředí.|
  |**Období**|Za posledních pět minut| Určuje dobu, ve kterém se hledá prahovou hodnotu na metriku.|
  |**Webhook**|[URL webhooku z aplikace function app]| Adresa URL webhooku z aplikace function app, který byl vytvořen v předchozích krocích.|

> [!NOTE]
> Metrika segmentů TCP není standardně povolená. Další informace o tom, jak povolit další metriky návštěvou [povolení monitorování a diagnostiky](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Kontrola výsledků

Za kritéria pro výstrahy aktivační události se vytvoří zachytávání paketů. Přejděte na Network Watcher a potom vyberte **zachytávání paketů**. Na této stránce můžete vybrat odkaz paketů zachycení souboru ke stažení zachytávání paketů.

![Zobrazení zachytávání paketů][functions14]

Pokud zachytávací soubor je uložený místně, můžete je načíst po přihlášení k virtuálnímu počítači.

Pokyny týkající se stahování souborů z účtů služby Azure storage najdete v tématu [Začínáme s Azure Blob storage pomocí .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Dalším nástrojem, můžete použít, je [Průzkumníka služby Storage](http://storageexplorer.com/).

Po stažení vaše zachycení, je můžete zobrazit pomocí libovolného nástroje, který může číst **CAP** souboru. Tady jsou odkazy na dvě z těchto nástrojů:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Další postup

Zjistěte, jak zobrazit vaši zachytávání paketů návštěvou [analýza zachytávání paketů s Wireshark](network-watcher-deep-packet-inspection.md).


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

---
title: Pomocí zachytáváním paketů provádět monitorování proaktivní sítě pomocí výstrah a Azure Functions | Microsoft Docs
description: Tento článek popisuje, jak vytvořit zaznamenání výstrahy spouštěná paketů s sledovací proces sítě Azure
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
ms.openlocfilehash: 4c96ca70b9b6a82dcccec443ac0b1e06f96a2396
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31597407"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Použít zachytáváním paketů pro monitorování proaktivní sítě pomocí výstrah a Azure Functions

Zachycení dat ze sítě sledovacích procesů paketu vytvoří zaznamenání relace ke sledování provozu do a z virtuálních počítačů. Zachycení soubor může mít filtr, který je definován sledovat pouze provoz, který chcete monitorovat. Tato data jsou pak uloženy v storage blob nebo místně na počítači hosta.

Tato funkce můžete spustit vzdáleně z jiných automatizace scénáře, jako je Azure Functions. Zachytáváním paketů vám dává možnost spustit proaktivní zachycení založené na definované sítě anomálií. Mezi další použití patří shromažďování statistiku sítě, získání informací o síti vniknutí, ladění komunikaci klienta se serverem a další.

Prostředky, které jsou nasazené v Azure spustit 24 hodin denně 7. Vás a vašich zaměstnanců nelze monitorovat aktivně stavu všech prostředků, 24 nebo 7. Co se například stane, pokud se vyskytne problém na 2: 00?

Pomocí sledovací proces sítě, výstrahy a funkce z v ekosystému Azure můžete proaktivně reagovat s daty a nástroje k řešení problémů ve vaší síti.

![Scénář][scenario]

## <a name="prerequisites"></a>Požadavky

* Nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Existující instanci sledovací proces sítě. Pokud jste ještě nemáte, [vytvořit instanci sledovací proces sítě](network-watcher-create.md).
* Existující virtuální počítač ve stejné oblasti jako sledovací proces sítě s [rozšíření Windows](../virtual-machines/windows/extensions-nwa.md) nebo [rozšíření virtuálního počítače Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="scenario"></a>Scénář

V tomto příkladu váš virtuální počítač odesílá víc segmentů TCP než obvykle a chcete být upozorněni. Segmentů TCP slouží jako příklad zde, ale žádné výstrahy podmínku můžete použít.

Když se zobrazí výstraha, budete chtít přijímat data na úrovni paketů pochopit, proč má vyšší komunikace. Potom můžete provést kroky se vraťte do regulární komunikace virtuálního počítače.

Tento scénář předpokládá, že máte existující instanci sledovací proces sítě a skupinu prostředků s platným virtuálním počítačem.

V následujícím seznamu je přehled pracovního postupu, který probíhá:

1. Výstrahy na vašem virtuálním počítači.
1. Výstraha volání funkce Azure prostřednictvím webhook, jehož.
1. Funkce Azure zpracovává výstrahy a spustí relaci zachytávání paketů sledovací proces sítě.
1. Zachytávání paketů běží ve virtuálním počítači a shromažďuje provoz.
1. Odeslání souboru zachytávání paketů do účtu úložiště ke kontrole a diagnostiku.

K automatizaci tohoto procesu, můžeme vytvořit a připojit výstrahu na našem virtuálních počítačů k aktivaci dojde-li k incidentu. Také vytvoříme funkce k volání do sledovací proces sítě.

Tento scénář provede následující akce:

* Vytvoří Azure funkci, která se spouští zachytáváním paketů.
* Vytvoří pravidlo výstrahy na virtuálním počítači a nakonfiguruje pravidlo výstrahy pro volání funkce Azure.

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

Prvním krokem je vytvoření funkce Azure ke zpracování upozornění a vytvořit zachytáváním paketů.

1. V [portál Azure](https://portal.azure.com), vyberte **vytvořit prostředek** > **výpočetní** > **aplikaci funkce**.

    ![Vytvoření aplikace – funkce][1-1]

2. Na **aplikaci funkce** okno, zadejte následující hodnoty a potom vyberte **OK** k vytvoření aplikace:

    |**Nastavení** | **Hodnota** | **Podrobnosti** |
    |---|---|---|
    |**Název aplikace**|PacketCaptureExample|Název funkce aplikace.|
    |**Předplatné**|[Předplatné] Předplatné, pro který chcete vytvořit aplikaci funkce.||
    |**Skupina prostředků**|PacketCaptureRG|Skupina prostředků tak, aby obsahovala aplikaci funkce.|
    |**Plán hostování**|Plán Consumption| Typ naplánujte vaše aplikace používá funkce. Možnosti jsou spotřeby nebo plán služby Azure App Service. |
    |**Umístění**|Střed USA| Oblast, ve které chcete vytvořit aplikaci funkce.|
    |**Účet úložiště**|{automaticky vygenerovanou}| Účet úložiště, který potřebuje funkce Azure pro úložiště pro obecné účely.|

3. Na **PacketCaptureExample funkce aplikace** vyberte **funkce** > **vlastní funkce**  >  **+**.

4. Vyberte **HttpTrigger-Powershell**a pak zadejte zbývající informace. Nakonec vytvořit funkce, vyberte **vytvořit**.

    |**Nastavení** | **Hodnota** | **Podrobnosti** |
    |---|---|---|
    |**Scénář**|Experimentální|Typ scénáře|
    |**Pojmenujte svoji funkci**|AlertPacketCapturePowerShell|Název funkce|
    |**Úroveň oprávnění**|Funkce|Úroveň oprávnění pro funkci|

![Příklad funkce][functions1]

> [!NOTE]
> Šablona prostředí PowerShell je experimentální a nemá plnou podporu.

Úpravy jsou požadovány pro tento příklad a jsou vysvětlené v následujících krocích.

### <a name="add-modules"></a>Přidání modulů

Použití rutin prostředí PowerShell sledovací proces sítě, nahrajte do aplikaci funkce nejnovější modul prostředí PowerShell.

1. Na místním počítači s nejnovější nainstalované moduly prostředí Azure PowerShell spusťte následující příkaz prostředí PowerShell:

    ```powershell
    (Get-Module AzureRM.Network).Path
    ```

    Tento příklad poskytuje místní cesty moduly prostředí Azure PowerShell. Tyto složky se používají v pozdější fázi. Moduly, které se používají v tomto scénáři jsou:

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

    ![Složky prostředí PowerShell][functions5]

1. Vyberte **funkce nastavení aplikace** > **přejděte do editoru služby aplikace**.

    ![Nastavení Function App][functions2]

1. Klikněte pravým tlačítkem myši **AlertPacketCapturePowershell** složku a pak vytvořte složku s názvem **azuremodules**. 

4. Vytvořte podsložku pro každý modul, který potřebujete.

    ![Složky a podsložky][functions3]

    * AzureRM.Network

    * AzureRM.Profile

    * AzureRM.Resources

1. Klikněte pravým tlačítkem myši **AzureRM.Network** podsložku a potom vyberte **nahrání souborů**. 

6. Přejděte na Azure moduly. Místní **AzureRM.Network** složky, vyberte všechny soubory ve složce. Pak vyberte **OK**. 

7. Opakujte tyto kroky pro **AzureRM.Profile** a **AzureRM.Resources**.

    ![Nahrání souborů][functions6]

1. Po dokončení, každé složky by měly mít soubory modulu prostředí PowerShell z místního počítače.

    ![Soubory prostředí PowerShell][functions7]

### <a name="authentication"></a>Authentication

Pokud chcete používat rutiny prostředí PowerShell, je třeba ověřit. Nakonfigurujte ověřování v aplikaci funkce. Postup konfigurace ověřování, musíte nakonfigurovat proměnné prostředí a nahrát soubor šifrované klíče do aplikaci funkce.

> [!NOTE]
> Tento scénář obsahuje pouze jeden z příkladů jak implementovat ověřování s Azure Functions. Uděláte to tak i jinými způsoby.

#### <a name="encrypted-credentials"></a>Zašifrované přihlašovací údaje

Následující skript prostředí PowerShell vytvoří soubor klíče s názvem **PassEncryptKey.key**. Nabízí taky zašifrovaná verze hesla, která se dodává. Toto heslo je stejné heslo, které je definována pro aplikaci Azure Active Directory, která se používá k ověřování.

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

V aplikaci služby editoru funkce aplikace, vytvořte složku s názvem **klíče** pod **AlertPacketCapturePowerShell**. Nahrajte **PassEncryptKey.key** soubor, který jste vytvořili v předchozí ukázce prostředí PowerShell.

![Funkce klíč][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Načtení hodnoty pro proměnné prostředí

Požadavek na konečné je nastavení proměnných prostředí, které jsou potřebné pro přístup k hodnotám pro ověřování. V následujícím seznamu jsou proměnné prostředí, které jsou vytvořené:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

ID klienta je ID aplikace aplikace v Azure Active Directory.

1. Pokud ještě nemáte aplikace pro použití, spusťte následující příklad k vytvoření aplikace.

    ```powershell
    $app = New-AzureRmADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Heslo, které použijete při vytváření aplikace musí být stejné heslo, které jste předtím vytvořili při ukládání souboru klíče.

1. Na portálu Azure vyberte **odběry**. Vyberte předplatné, které chcete použít a pak vyberte **přístup k ovládacímu prvku (IAM)**.

    ![Funkce IAM][functions9]

1. Vyberte účet, který chcete použít a potom vyberte **vlastnosti**. Zkopírujte ID aplikace.

    ![ID aplikace funkce][functions10]

#### <a name="azuretenant"></a>AzureTenant

Získejte ID klienta tak, že spustíte následující ukázku v prostředí PowerShell:

```powershell
(Get-AzureRmSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Hodnota proměnné prostředí AzureCredPassword je hodnota, kterou můžete získat z systémem následující ukázku v prostředí PowerShell. Tento příklad je stejný jako ten, který je zobrazen v předchozím **šifrovat přihlašovací údaje** části. Hodnota, která je potřeba je výstup `$Encryptedpassword` proměnné.  Toto je hlavní heslo služby, který jste zašifrovali pomocí skriptu prostředí PowerShell.

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

### <a name="store-the-environment-variables"></a>Uložení proměnné prostředí

1. Přejděte do aplikaci funkce. Potom vyberte **funkce nastavení aplikace** > **konfigurovat nastavení aplikace**.

    ![Konfigurace nastavení aplikace][functions11]

1. Přidání proměnné prostředí a jejich hodnoty nastavení aplikace a pak vyberte **Uložit**.

    ![Nastavení aplikací][functions12]

### <a name="add-powershell-to-the-function"></a>Přidání funkce prostředí PowerShell

Nyní je čas provádět volání do sledovací proces sítě z v rámci Azure funkce. V závislosti na požadavcích implementace této funkce se může lišit. Obecný tok kódu však vypadá takto:

1. Proces vstupní parametry.
2. Ověřte omezení a vyřešit konflikty v názvech zaznamená paketu existující dotaz.
3. Vytvořte zachytáváním paketů s příslušnými parametry.
4. Dotazování paketu zachytit pravidelně, dokud nebude dokončeno.
5. Upozorněte uživatele, relace zachytávání paketů je dokončena.

V následujícím příkladu je prostředí PowerShell kód, který můžete použít ve funkci. Existují hodnoty, které je nutné nahradit pro **subscriptionId**, **resourceGroupName**, a **storageAccountName**.

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
#### <a name="retrieve-the-function-url"></a>Načíst URL pro funkce 
1. Po vytvoření funkce, nakonfigurujte upozornění k volání adresu URL, který je spojen s funkcí. Chcete-li získat tuto hodnotu, zkopírujte adresu URL funkce z vaší aplikace. funkce.

    ![Adresa URL funkce hledání][functions13]

2. Zkopírujte adresu URL funkce pro vaši aplikaci funkce.

    ![Kopírování adresu – funkce][2]

Pokud budete potřebovat vlastní vlastnosti v datové části požadavek POST webhooku, podívejte se na [konfigurace webhook, jehož na výstrahu Azure metriky](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurace výstrahy na virtuálním počítači

Výstrahy můžete nakonfigurovat pro jednotlivce upozornění, když konkrétní metrika překračuje prahovou hodnotu, která je k němu přiřazen. V tomto příkladu je výstraha na segmentů TCP, které se odesílají, ale může být výstraha pro mnoho jiné metriky. V tomto příkladu je nakonfigurované výstrahu volat webhook pro volání funkce.

### <a name="create-the-alert-rule"></a>Vytvořit pravidlo výstrahy

Přejít na existující virtuální počítač a poté přidejte pravidlo výstrahy. Podrobnější dokumentaci týkající se konfigurace výstrahy naleznete na [vytvoření výstrahy v Azure monitorování pro služby Azure – portál Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Zadejte následující hodnoty v **pravidlo výstrahy** a pak vyberte **OK**.

  |**Nastavení** | **Hodnota** | **Podrobnosti** |
  |---|---|---|
  |**Název**|TCP_Segments_Sent_Exceeded|Název pravidla výstrahy.|
  |**Popis**|Segmentů TCP odeslané překročil prahovou hodnotu|Popis pro pravidlo výstrahy.||
  |**Metrika**|Odeslané segmenty TCP| Metrika sloužící k aktivaci upozornění. |
  |**Podmínka**|Více než| Podmínku, která má použít při vyhodnocení metriku.|
  |**Prahová hodnota**|100| Hodnota metriku, která aktivuje výstrahu. Tato hodnota by měla nastavit na platnou hodnotu pro vaše prostředí.|
  |**Období**|Za posledních pět minut| Určuje dobu, ve kterém má být vyhledán prahovou hodnotu na metriky.|
  |**Webhook**|[URL webhooku se nenačetla z funkce aplikace]| Adresa URL webhooku z funkce aplikace, který byl vytvořen v předchozích krocích.|

> [!NOTE]
> Metrika segmentů TCP není povoleno ve výchozím nastavení. Další informace o tom, jak povolit další metriky navštivte stránky [zapínání monitorování a Diagnostika](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Kontrola výsledků

Po kritéria pro výstrahy aktivačních událostí vytvoří se zachytáváním paketů. Přejděte do sledovací proces sítě a potom vyberte **zachytáváním paketů**. Na této stránce můžete vybrat odkaz souboru zachytávání paketů stáhnout zachytáváním paketů.

![Zobrazení zachytávání paketů][functions14]

Pokud soubor snímek je uložený místně, můžete ji načíst po přihlášení k virtuálnímu počítači.

Pokyny týkající se stahování souborů z účtů úložiště Azure najdete v tématu [Začínáme s Azure Blob storage pomocí rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Jiný nástroj, můžete je [Storage Explorer](http://storageexplorer.com/).

Po stažení vaší zachycení, můžete ji zobrazit pomocí libovolného nástroje, který může číst **CAP** souboru. Tady jsou odkazy na dva z těchto nástrojů:

- [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Další postup

Zjistěte, jak zobrazit vaše paketu zachycení tak, že navštívíte [analýza zachytávání paketů s Wireshark](network-watcher-deep-packet-inspection.md).


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

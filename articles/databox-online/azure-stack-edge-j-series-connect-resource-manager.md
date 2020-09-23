---
title: Připojení k Azure Resource Manager na zařízení GPU Azure Stack Edge pro
description: Popisuje, jak se připojit k Azure Resource Manager běžícímu na grafickém procesoru Azure Stack Edge pro pomocí Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5cf406dc0577f477858dd8a6570f7975747112e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891240"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Připojení k Azure Resource Manager na zařízení Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure Resource Manager poskytuje vrstvu pro správu, která umožňuje vytvářet, aktualizovat a odstraňovat prostředky v předplatném Azure. Zařízení Azure Stack Edge pro podporuje stejná Azure Resource Manager rozhraní API k vytváření, aktualizaci a odstraňování virtuálních počítačů v místním předplatném. Tato podpora vám umožní spravovat zařízení způsobem konzistentním s cloudem. 

V tomto kurzu se dozvíte, jak se připojit k místním rozhraním API na zařízení Azure Stack Edge pro prostřednictvím Azure Resource Manager pomocí Azure PowerShell.

## <a name="about-azure-resource-manager"></a>Informace o Azure Resource Manageru

Azure Resource Manager poskytuje jednotnou vrstvu správy pro volání rozhraní API pro zařízení Azure Stack Edge pro a provádění operací, jako je vytváření, aktualizace a odstraňování virtuálních počítačů. Architektura Azure Resource Manager je podrobně popsána v následujícím diagramu.

![Diagram pro Azure Resource Manager](media/azure-stack-edge-j-series-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Koncové body na zařízení Azure Stack Edge pro

Následující tabulka shrnuje různé koncové body, které jsou vystavené na vašem zařízení, podporované protokoly a porty pro přístup k těmto koncovým bodům. V celém článku najdete odkazy na tyto koncové body.

| # | Koncový bod | Podporované protokoly | Použitý port | Použití |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | HTTPS | 443 | Připojení k Azure Resource Manager pro automatizaci |
| 2. | Služba tokenů zabezpečení | HTTPS | 443 | Ověření prostřednictvím přístupu a aktualizací tokenů |
| 3. | Blob | HTTPS | 443 | Připojení k úložišti objektů BLOB přes REST |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Připojení k pracovnímu postupu Azure Resource Manager

Proces připojení k místním rozhraním API zařízení pomocí Azure Resource Manager vyžaduje následující kroky:

| Krok # | Provedete tento krok... | .. v tomto umístění. |
| --- | --- | --- |
| 1. | [Konfigurace zařízení Azure Stack Edge pro](#step-1-configure-azure-stack-edge-pro-device) | Místní webové uživatelské rozhraní |
| 2. | [Vytvoření a instalace certifikátů](#step-2-create-and-install-certificates) | Klient systému Windows/místní webové uživatelské rozhraní |
| 3. | [Kontrola a konfigurace požadovaných součástí](#step-3-install-powershell-on-the-client) | Klient Windows |
| 4. | [Nastavení Azure PowerShell na klientovi](#step-4-set-up-azure-powershell-on-the-client) | Klient Windows |
| 5. | [Upravit soubor hostitele pro překlad názvů koncových bodů](#step-5-modify-host-file-for-endpoint-name-resolution) | Klient systému Windows nebo server DNS |
| 6. | [Ověřte, že je název koncového bodu vyřešený.](#step-6-verify-endpoint-name-resolution-on-the-client) | Klient Windows |
| 7. | [Použití rutin Azure PowerShell k ověření připojení k Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Klient Windows |

V následujících částech najdete podrobné informace o každém z výše uvedených kroků v části připojení k Azure Resource Manager.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že klient, který se používá pro připojení k zařízení přes Azure Resource Manager, používá protokol TLS 1,2. Další informace najdete [v konfiguraci TLS 1,2 na klientském počítači se systémem Windows přístup k zařízení Azure Stack Edge pro](azure-stack-edge-j-series-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>Krok 1: konfigurace zařízení Azure Stack Edge pro 

V místním webovém uživatelském rozhraní zařízení Azure Stack Edge pro proveďte následující kroky.

1. Dokončete nastavení sítě pro zařízení Azure Stack Edge pro. 

    ![Stránka místní webové uživatelské rozhraní "nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Poznamenejte si IP adresu zařízení. Tuto IP adresu budete používat později.

2. Na stránce **zařízení** nakonfigurujte název zařízení a doménu DNS. Poznamenejte si název zařízení a doménu DNS, abyste je mohli později použít.

    ![Stránka místního webového uživatelského rozhraní (zařízení)](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > Název zařízení, doména DNS, bude použit k vytvoření koncových bodů, které jsou zpřístupněny.
    > Použijte Azure Resource Manager a koncové body objektů BLOB ze stránky **zařízení** v místním webovém uživatelském rozhraní.


## <a name="step-2-create-and-install-certificates"></a>Krok 2: vytvoření a instalace certifikátů

Certifikáty zajišťují, že komunikace je důvěryhodná. Na zařízení Azure Stack Edge pro se automaticky vygenerují zařízení podepsané svým držitelem, objekty BLOB a certifikáty Azure Resource Manager. Volitelně můžete také přenést vlastní podepsané objekty BLOB a certifikáty Azure Resource Manager.

Když přiřadíte podepsanému certifikátu vlastní, budete také potřebovat odpovídající podpisový řetězec certifikátu. Pro podpisový řetěz, Azure Resource Manager a certifikáty objektů BLOB v zařízení budete potřebovat odpovídající certifikáty na klientském počítači, aby bylo možné ověřit a komunikovat se zařízením.

Pokud se chcete připojit k Azure Resource Manager, budete muset vytvořit nebo získat podpisový řetěz a certifikáty koncového bodu, naimportovat tyto certifikáty na klienta Windows a nakonec tyto certifikáty nahrát do zařízení.

### <a name="create-certificates-optional"></a>Vytvořit certifikáty (volitelné)

Pouze pro testovací a vývojové použití můžete pomocí prostředí Windows PowerShell vytvořit certifikáty v místním systému. Při vytváření certifikátů pro klienta postupujte podle těchto pokynů:

1. Nejprve musíte vytvořit kořenový certifikát pro podpisový řetězec. Další informace najdete v tématu Postup [Vytvoření certifikátů podpisového řetězce](azure-stack-edge-j-series-manage-certificates.md#create-signing-chain-certificate).

2. Můžete dále vytvořit certifikáty koncového bodu pro objekt BLOB a Azure Resource Manager. Tyto koncové body můžete získat ze stránky **zařízení** v místním webovém uživatelském rozhraní. Podívejte se na postup [Vytvoření certifikátů koncového bodu](azure-stack-edge-j-series-manage-certificates.md#create-signed-endpoint-certificates).

3. U všech těchto certifikátů se ujistěte, že název subjektu a alternativní název subjektu splňují následující pokyny:

    |Typ |Název subjektu (SN)  |Alternativní název subjektu (SAN)  |Příklad názvu subjektu |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Jeden certifikát pro více sítí SAN pro oba koncové body|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Další informace o certifikátech najdete v tématu [Správa certifikátů](azure-stack-edge-j-series-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Nahrávání certifikátů na zařízení

Certifikáty, které jste vytvořili v předchozím kroku, se budou nacházet v osobním úložišti na vašem klientovi. Tyto certifikáty je nutné exportovat v klientovi do odpovídajících souborů formátu, které je možné odeslat do zařízení.

1. Kořenový certifikát musí být exportován jako soubor formátu DER s příponou souboru *. cer* . Podrobný postup najdete v tématu [export certifikátů jako soubor ve formátu. cer](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-der-format).

2. Certifikáty koncového bodu musí být exportovány jako soubory *. pfx* s privátními klíči. Podrobný postup najdete v tématu [export certifikátů jako soubor. pfx s privátními klíči](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. Kořenové a koncové certifikáty se pak nahrají na zařízení pomocí možnosti **+ Přidat certifikát** na stránce **certifikáty** v místním webovém uživatelském rozhraní. Pokud chcete nahrát certifikáty, postupujte podle kroků v části [nahrávání certifikátů](azure-stack-edge-j-series-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Import certifikátů na klienta se spuštěným Azure PowerShell

Klient systému Windows, ve kterém budete vyvolávat rozhraní API pro Azure Resource Manager, musí vytvořit vztah důvěryhodnosti se zařízením. Z tohoto důvodu musí být certifikáty, které jste vytvořili v předchozím kroku, naimportovány do příslušného úložiště certifikátů na vašem klientovi Windows.

1. Kořenový certifikát, který jste exportovali jako Formát DER s příponou *. cer* , by se teď měl naimportovat do důvěryhodných kořenových certifikačních autorit v klientském počítači. Podrobný postup najdete v tématu [Import certifikátů do úložiště Důvěryhodné kořenové certifikační autority.](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format)

2. Certifikáty koncového bodu, které jste exportovali jako soubor *. pfx* , se musí exportovat jako *. cer*. Tento *CER* se pak naimportuje do **osobního** úložiště certifikátů ve vašem systému. Podrobný postup najdete v tématu [Import certifikátů do osobního úložiště](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Krok 3: instalace PowerShellu na klienta 

Váš klient Windows musí splňovat následující požadavky:

1. Spusťte PowerShell verze 5,0. Musíte mít PowerShell verze 5,0 nebo vyšší. Pokud chcete zjistit verzi PowerShellu v systému, spusťte následující rutinu:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Porovnejte **Hlavní** verzi a ujistěte se, že je 5,0 nebo novější.

    Pokud máte zastaralou verzi, přečtěte si [Upgrade existujícího Windows PowerShellu](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

    Pokud nemáte \' prostředí PowerShell 5,0, postupujte podle pokynů k [instalaci prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6).

    Ukázkový výstup najdete níž.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Můžete získat přístup k Galerie prostředí PowerShell.

    Spusťte PowerShell jako správce. Ověřte, zda je PSGallery registrován jako úložiště.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Ukázkový výstup najdete níž.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Pokud vaše úložiště není důvěryhodné nebo potřebujete další informace, přečtěte si téma [ověření dostupnosti Galerie prostředí PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?view=azs-1908#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Krok 4: nastavení Azure PowerShell na klientovi 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Do svého klienta nainstalujete Azure PowerShell moduly, které budou fungovat s vaším zařízením.

    a. Spusťte PowerShell jako správce. Potřebujete přístup k galerii prostředí PowerShell. 


    b. Chcete-li nainstalovat požadované moduly Azure PowerShell z Galerie prostředí PowerShell spusťte následující příkaz:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Ujistěte se, že je na konci instalace spuštěná verze modulu Azure-RM 2.5.0. 
    Pokud máte existující verzi modulu Azure-RM, která se neshoduje s požadovanou verzí, odinstalujte ji pomocí tohoto příkazu:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Nyní bude nutné nainstalovat požadovanou verzi znovu.
   

Níže je uvedený ukázkový výstup, který indikuje, že moduly 2.5.0 verze AzureRM byly úspěšně nainstalovány.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Krok 5: Úprava souboru hostitele pro překlad názvů koncových bodů 

Nyní přidáte virtuální IP adresu konzistentní s Azure, kterou jste definovali v místním webovém uživatelském rozhraní zařízení, na:

- Hostitelský soubor na klientovi, nebo,
- Konfigurace serveru DNS

> [!IMPORTANT]
> Doporučujeme, abyste změnili konfiguraci serveru DNS pro překlad názvů koncových bodů.

V klientovi se systémem Windows, který používáte pro připojení k zařízení, proveďte následující kroky:

1. Spusťte **Poznámkový blok** jako správce a pak otevřete soubor **hosts** umístěný na adrese C:\WINDOWS\SYSTEM32\DRIVERS\ETC.

    ![Soubor hostitelů Průzkumníka Windows](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)

2. Do souboru **hostitelů** přidejte následující položky, které nahradíte odpovídajícími hodnotami pro vaše zařízení: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > Položka v souboru Hosts by měla přesně odpovídat, která je k dispozici pro připojení k Azure Resource Manager v pozdějším kroku. Zajistěte, aby položka domény DNS byla v malých písmenech.

    IP adresu zařízení jste v předchozím kroku uložili z místního webového uživatelského rozhraní.

    Přihlašovací jméno. \<appliance name\> ..\<DNS domain\> položka je koncový bod služby tokenů zabezpečení (STS). Služba STS zodpovídá za vytváření, ověřování, obnovení a zrušení tokenů zabezpečení. Služba tokenů zabezpečení se používá k vytvoření přístupového tokenu a k aktualizaci tokenu, který se používá pro průběžnou komunikaci mezi zařízením a klientem.

3. Pro referenci použijte následující obrázek. Uložte soubor **hostitelů** .

    ![soubor hostitelů v programu Poznámkový blok](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Krok 6: ověření překladu názvů koncových bodů na klientovi

Ověřte, jestli je název koncového bodu vyřešený na klientovi, který používáte pro připojení k virtuální IP adrese konzistentní s Azure.

1. K ověření, že je název koncového bodu vyřešený, můžete použít nástroj příkazového řádku ping.exe. Po zadání IP adresy vrátí příkaz příkazu k zadání názvu hostitele TCP/IP počítače, který chcete \' znovu sledovat.

    Přidejte `-a` přepínač do příkazového řádku, jak je znázorněno v následujícím příkladu. Pokud je název hostitele možné vrátit, vrátí tyto potenciálně cenné informace v odpovědi.

    ![Příkaz testovat v příkazovém řádku](media/azure-stack-edge-j-series-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Krok 7: nastavení Azure Resource Manager prostředí

Nastavte prostředí Azure Resource Manager a ověřte, že zařízení komunikuje s klientem prostřednictvím Azure Resource Manager pracuje správně. Pro toto ověření proveďte následující kroky:


1. Pomocí `Add-AzureRmEnvironment` rutiny zajistěte, aby komunikace prostřednictvím Azure Resource Manager fungovala správně a aby volání rozhraní API procházela prostřednictvím portu vyhrazeného pro Azure Resource Manager-443.

    `Add-AzureRmEnvironment`Rutina přidá koncové body a metadata, aby bylo možné Azure Resource Manager rutiny připojit s novou instancí Azure Resource Manager. 


    > [!IMPORTANT]
    > Adresa URL koncového bodu Azure Resource Manager, kterou zadáte v následující rutině, rozlišuje velká a malá písmena. Ujistěte se, že adresa URL koncového bodu je malá a velká písmena a odpovídá, co jste zadali v souboru Hosts. Pokud se neshodují malá a velká písmena, zobrazí se chyba.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Ukázkový výstup je uveden níže:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Nastavte prostředí jako Azure Stack Edge pro a port, který se má použít pro Azure Resource Manager volání jako 443. Prostředí definujete dvěma způsoby:

    - Nastavte prostředí. Zadejte následující příkaz:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Další informace najdete na webu [set-AzureRMEnvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0).

    - Definujte prostředí vložené pro každou rutinu, kterou spustíte. Tím zajistíte, že všechna volání rozhraní API procházejí přes správné prostředí. Ve výchozím nastavení by volání procházela přes Azure Public, ale chcete, aby procházela s prostředím, které jste nastavili pro Azure Stack hraniční zařízení pro.

    - Přečtěte si další informace o [tom, jak přepnout prostředí AzureRM](#switch-environments).

2. Voláním rozhraní API místních zařízení ověřte připojení k Azure Resource Manager. 

    1. Tyto přihlašovací údaje jsou určené pro účet místního počítače a používají se výhradně pro přístup přes rozhraní API.

    2. Můžete se připojit prostřednictvím `login-AzureRMAccount` příkazu nebo pomocí `Connect-AzureRMAccount` příkazu. 

        1. Pokud se chcete přihlásit, zadejte následující příkaz. ID tenanta v této instanci je pevně kódované – c0257de7-538f-415c-993A-1b87a031879d. Použijte následující uživatelské jméno a heslo.

            - **Uživatelské jméno**  -  *EdgeArmUser*

            - **Heslo**  -  [Nastavte heslo pro Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md) a použijte toto heslo pro přihlášení. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Alternativním způsobem, jak se přihlásit, je použití `login-AzureRmAccount` rutiny. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` – TenantId c0257de7-538f-415c-993A-1b87a031879d 

            Zde je ukázkový výstup příkazu. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> Platnost připojení k Azure Resource Manager vyprší každých 1,5 hodin nebo pokud se vaše zařízení Azure Stack Edge pro restartuje. Pokud k tomu dojde, všechny rutiny, které spustíte, vrátí chybové zprávy, které už nejsou připojené k Azure. Budete se muset znovu přihlásit.

## <a name="switch-environments"></a>Přepínání prostředí

Spusťte `Disconnect-AzureRmAccount` příkaz pro přepnutí na jiný `AzureRmEnvironment` . 

Pokud používáte `Set-AzureRmEnvironment` a `Login-AzureRmAccount` bez použití `Disconnect-AzureRmAccount` , prostředí se ve skutečnosti nepřepne.  

Následující příklady ukazují, jak přepínat mezi dvěma prostředími `AzDBE1` a `AzDBE2` .

Nejprve vypíšete všechna existující prostředí v klientovi.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

V dalším kroku získáte přístup k aktuálně připojenému prostředí prostřednictvím Azure Resource Manager.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Před přepnutím do jiného prostředí byste se teď měli odpojit od aktuálního prostředí.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Přihlaste se k druhému prostředí. Ukázkový výstup je uvedený níže.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Spuštěním této rutiny potvrďte, ke kterému prostředí jste se připojili.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Nyní jste přešli na zamýšlené prostředí.

## <a name="next-steps"></a>Další kroky

[Nasaďte virtuální počítače na zařízení Azure Stack Edge pro](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).

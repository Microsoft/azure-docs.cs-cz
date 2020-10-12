---
title: Nasazení virtuálních počítačů na GPU zařízení Azure Stack Edge pro pomocí Azure CLI a Pythonu
description: Popisuje, jak vytvořit a spravovat virtuální počítače na zařízení GPU Azure Stack Edge pro pomocí Azure CLI a Pythonu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: c27f6ef47b8e4db83ceb63e308e318803800f8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890721"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-using-azure-cli-and-python"></a>Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge pro pomocí Azure CLI a Pythonu

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]

V tomto kurzu se dozvíte, jak vytvořit a spravovat virtuální počítač na zařízení Azure Stack Edge pro s využitím rozhraní příkazového řádku (CLI) Azure a Pythonu.

## <a name="vm-deployment-workflow"></a>Pracovní postup nasazení virtuálních počítačů

Pracovní postup nasazení je znázorněný v následujícím diagramu.

![Pracovní postup nasazení virtuálních počítačů](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

Shrnutí nejvyšší úrovně pracovního postupu nasazení je následující:

1. Připojení k Azure Resource Manager
2. Vytvoření skupiny prostředků
3. vytvořit účet úložiště
4. Přidat do souboru hostitelů identifikátor URI objektu BLOB
5. Instalace certifikátů
6. Nahrání virtuálního pevného disku
7. Vytvoření spravovaných disků z VHD
8. Vytvoření image virtuálního počítače z disku spravovaného imagí
9. Vytvoření virtuálního počítače s dříve vytvořenými prostředky
10. Vytvoření virtuální sítě
11. Vytvoření VNIC pomocí ID podsítě virtuální sítě

Podrobné vysvětlení diagramu pracovního postupu najdete v tématu [nasazení virtuálních počítačů na zařízení Azure Stack Edge pro pomocí Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md). Informace o tom, jak se připojit k Azure Resource Manager, najdete v tématu [připojení k Azure Resource Manager pomocí Azure PowerShell](azure-stack-edge-j-series-connect-resource-manager.md).

## <a name="prerequisites"></a>Požadavky

Než začnete vytvářet a spravovat virtuální počítač na zařízení Azure Stack Edge pro pomocí Azure CLI a Pythonu, musíte se ujistit, že jste dokončili požadavky uvedené v následujících krocích:

1. V zařízení Azure Stack Edge pro jste dokončili nastavení sítě, jak je popsáno v části [Krok 1: konfigurace zařízení Azure Stack Edge pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Bylo povoleno síťové rozhraní pro výpočetní výkon. Tato IP adresa síťového rozhraní se používá k vytvoření virtuálního přepínače pro nasazení virtuálního počítače. Následující kroky vás provedou procesem:

    1. Přejít na **výpočetní**výkon. Vyberte síťové rozhraní, které budete používat k vytvoření virtuálního přepínače.

        > [!IMPORTANT] 
        > Pro výpočetní výkon můžete nakonfigurovat jenom jeden port.

    2. Povolte výpočetní prostředky v síťovém rozhraní. Azure Stack Edge pro vytvoří a spravuje virtuální přepínač odpovídající tomuto síťovému rozhraní.

    <!--If you decide to use another network interface for compute, make sure that you:

    - Delete all the VMs that you have deployed using Azure Resource Manager.

    - Delete all virtual network interfaces and the virtual network associated with this network interface.

    - You can now enable another network interface for compute.-->

3. Vytvořili jste a nainstalovali jste všechny certifikáty na zařízení Azure Stack Edge pro a v důvěryhodném úložišti klienta. Postupujte podle postupu popsaného v části [Krok 2: vytvoření a instalace certifikátů](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

4. Vytvořili jste certifikát *. cer* s kódováním Base-64 (formát PEM) pro zařízení Azure Stack Edge pro. Tato služba je už na zařízení nahraná jako podpisový řetěz a nainstalovala se do důvěryhodného kořenového úložiště na vašem klientovi. Tento certifikát je také vyžadován ve formátu *PEM* , aby Python mohl pracovat na tomto klientovi.

    Tento certifikát převeďte do formátu PEM pomocí `certutil` příkazu. Tento příkaz musíte spustit v adresáři, který obsahuje váš certifikát.

    ```powershell
    certutil.exe <SourceCertificateName.cer> <DestinationCertificateName.pem>
    ```
    Následující příklad ukazuje použití příkazu:

    ```powershell
    PS C:\Certificates> certutil.exe -encode aze-root.cer aze-root.pem
    Input Length = 2150
    Output Length = 3014
    CertUtil: -encode command completed successfully.
    PS C:\Certificates>
    ```    
    Tento PEM můžete také přidat do úložiště Python později.

5. IP adresu zařízení jste přiřadili na stránce **síť** v místním webovém uživatelském rozhraní zařízení. Tuto IP adresu musíte přidat do:

    - Hostitelský soubor na klientovi, nebo,
    - Konfigurace serveru DNS
    
    > [!IMPORTANT]
    > Doporučujeme, abyste změnili konfiguraci serveru DNS pro překlad názvů koncových bodů.

    1. Spusťte **Poznámkový blok** jako správce (k uložení souboru se vyžaduje oprávnění správce) a pak otevřete soubor **hosts** v umístění `C:\Windows\System32\Drivers\etc` .
    
        ![Soubor hostitelů Průzkumníka Windows](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Do souboru **hostitelů** přidejte následující položky, které nahradíte odpovídajícími hodnotami pro vaše zařízení:
    
        ```
        <Device IP> login.<appliance name>.<DNS domain>
        <Device IP> management.<appliance name>.<DNS domain>
        <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
    3. Pro referenci použijte následující obrázek. Uložte soubor **hostitelů** .

        ![soubor hostitelů v programu Poznámkový blok](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

6. [Stáhněte si skript Pythonu](https://aka.ms/ase-vm-python) použitý v tomto postupu.

## <a name="step-1-set-up-azure-clipython-on-the-client"></a>Krok 1: nastavení rozhraní příkazového řádku Azure CLI/Python na klientovi

### <a name="verify-profile-and-install-azure-cli"></a>Ověřit profil a nainstalovat rozhraní příkazového řádku Azure

<!--1. Verify the API profile of the client and identify which version of the modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Nainstalujte rozhraní příkazového řádku Azure CLI do svého klienta. V tomto příkladu se nainstalovalo rozhraní příkazového řádku Azure CLI 2.0.80. Pokud chcete ověřit verzi rozhraní příkazového řádku Azure CLI, spusťte `az --version` příkaz.

    Následuje ukázkový výstup výše uvedeného příkazu:

    ```powershell
    PS C:\windows\system32> az --version
    azure-cli                         2.0.80
    
    command-modules-nspkg              2.0.3
    core                              2.0.80
    nspkg                              3.0.4
    telemetry                          1.0.4
    Extensions:
    azure-cli-iot-ext                  0.7.1
    
    Python location 'C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe'
    Extensions directory 'C:\.azure\cliextensions'
    
    Python (Windows) 3.6.6 (v3.6.6:4cf1f54eb7, Jun 27 2018, 02:47:15) [MSC v.1900 32 bit (Intel)]
    
    Legal docs and information: aka.ms/AzureCliLegal
    
    Your CLI is up-to-date.
    
    Please let us know how we are doing: https://aka.ms/clihats
    PS C:\windows\system32>
    ```

    Pokud nemáte rozhraní příkazového řádku Azure CLI, Stáhněte a nainstalujte rozhraní příkazového [řádku Azure CLI ve Windows](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest). Azure CLI můžete spustit pomocí příkazového řádku Windows nebo pomocí Windows PowerShellu.

2. Poznamenejte si umístění Pythonu pro rozhraní příkazového řádku. To je potřeba k určení umístění důvěryhodného kořenového úložiště certifikátů pro Azure CLI.

3. Pro spuštění ukázkového skriptu používaného v tomto článku budete potřebovat následující verze knihovny Pythonu:

    ```powershell
    azure-common==1.1.23
    azure-mgmt-resource==2.1.0
    azure-mgmt-network==2.7.0
    azure-mgmt-compute==5.0.0
    azure-mgmt-storage==1.5.0
    azure-storage-blob==1.2.0rc1
    haikunator
    msrestazure==0.6.2
    ```
    Chcete-li nainstalovat verze, spusťte následující příkaz:

    ```powershell
    .\python.exe -m pip install haikunator
    ```

    Následující vzorový výstup zobrazuje instalaci Haikunator:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install haikunator

    Collecting haikunator
      Downloading https://files.pythonhosted.org/packages/43/fa/130968f1a1bb1461c287b9ff35c630460801783243acda2cbf3a4c5964a5/haikunator-2.1.0-py2.py3-none-any.whl
    
    Installing collected packages: haikunator
    Successfully installed haikunator-2.1.0
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> 
    ```

    Následující vzorový výstup ukazuje instalaci PIP pro `msrestazure` : 
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe -m pip install msrestazure==0.6.2
    Requirement already satisfied: msrestazure==0.6.2 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (0.6.2)
    Requirement already satisfied: msrest<2.0.0,>=0.6.0 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from msrestazure==0.6.2) (0.6.10)
    === CUT ===========================  CUT ==================================
    Requirement already satisfied: cffi!=1.11.3,>=1.8 in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (1.13.2)
    Requirement already satisfied: pycparser in c:\program files (x86)\microsoft sdks\azure\cli2\lib\site-packages (from cffi!=1.11.3,>=1.8->cryptography>=1.1.0->adal<2.0.0,>=0.6.0->msrestazure==0.6.2) (2.18)
    You are using pip version 10.0.1, however version 20.0.1 is available.
    You should consider upgrading using the 'python -m pip install --upgrade pip' command.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

### <a name="trust-the-azure-stack-edge-pro-ca-root-certificate"></a>Důvěřovat kořenovému certifikátu certifikační autority Azure Stack Edge pro

1. Najděte umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kam jste nainstalovali `az cli` . Spusťte prostředí Windows PowerShell jako správce. Přepněte na cestu, kde je `az cli` nainstalovaná Python: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\python.exe` .

    Chcete-li získat umístění certifikátu, zadejte následující příkaz:

    ```powershell
    .\python -c "import certifi; print(certifi.where())"
    ```
    
    Rutina vrátí umístění certifikátu, jak je vidět níže:  
        
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python -c "import certifi; print(certifi.where())"
    C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```
      
    Poznamenejte si toto umístění, protože ho budete později používat – `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`

2. Pokud chcete důvěřovat kořenovému certifikátu certifikační autority pro Azure Stack Edge pro, připojovat se k existujícímu certifikátu Pythonu. Zadejte cestu, kam jste dříve uložili certifikát PEM.

    ```powershell
    $pemFile = "<Path to the pem format certificate>"
    ```
    Příklad cesty by byl "C:\VM-scripts\rootteam3device.pem".
    
    Pak do Windows PowerShellu zadejte následující řady příkazů:

    ```powershell
    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
        $root.Import($pemFile)
        
    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry= [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")
    
    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText
    
    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry
    
    Write-Host "Python Cert store was updated to allow the Azure Stack Edge Pro CA root certificate"
    ```
    
### <a name="connect-to-azure-stack-edge-pro"></a>Připojení k Azure Stack Edge pro

1. Pomocí příkazu zaregistrujte své prostředí Azure Stack Edge pro `az cloud register` .

    V některých scénářích je přímé odchozí připojení k Internetu směrováno prostřednictvím proxy serveru nebo brány firewall, která vynutila zachycení SSL. V těchto případech může příkaz AZ Cloud Register selhat s chybou, jako je například \" neúspěšné získání koncových bodů z cloudu. \" Tuto chybu můžete obejít tak, že ve Windows PowerShellu nastavíte následující proměnné prostředí:

    ```powershell
    $ENV:AZURE_CLI_DISABLE_CONNECTION_VERIFICATION = 1 
    $ENV:ADAL_PYTHON_SSL_NO_VERIFY = 1
    ```

2. Nastavte proměnné prostředí pro skript pro Azure Resource Manager koncový bod, umístění, kde se prostředky vytvářejí, a cestu k umístění zdrojového virtuálního pevného disku. Umístění prostředků je pevně na všech zařízeních Azure Stack Edge pro a je nastavené na `dbelocal` . Musíte také zadat předpony adres a privátní IP adresu. Všechny následující proměnné prostředí jsou hodnoty založené na hodnotách s výjimkou `AZURE_RESOURCE_LOCATION` , která by měla být pevně zakódované na `"dbelocal"` .

    ```powershell
    $ENV:ARM_ENDPOINT = "https://management.team3device.teatraining1.com"
    $ENV:AZURE_RESOURCE_LOCATION = "dbelocal"
    $ENV:VHD_FILE_PATH = "C:\Downloads\Ubuntu1604\Ubuntu13.vhd"
    $ENV:ADDRESS_PREFIXES = "5.5.0.0/16"
    $ENV:PRIVATE_IP_ADDRESS = "5.5.174.126"
    ```

3. Zaregistrujte své prostředí. Při spuštění AZ Cloud Register použijte následující parametry:

    | Hodnota | Popis | Příklad |
    | --- | --- | --- |
    | Název prostředí | Název prostředí, ke kterému se pokoušíte připojit | Zadejte název, například `aze-environ` |
    | Správce prostředků koncový bod | Tato adresa URL je `https://Management.<appliancename><dnsdomain>` . <br> Tuto adresu URL získáte tak, že přejdete na stránku **zařízení** v místním webovém uživatelském rozhraní vašeho zařízení. |Například, `https://management.team3device.teatraining1.com`.  |
    
    ```powershell
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.<appliance name>.<DNS domain>"
    ```
    Následující příklad ukazuje použití výše uvedeného příkazu:
    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud register -n az-new-env --endpoint-resource-manager "https://management.team3device.teatraining1.com"
    ```
    
    
4. Aktivní prostředí nastavíte pomocí následujících příkazů:

    ```powershell
    az cloud set -n <EnvironmentName>
    ```
    Následující příklad ukazuje použití výše uvedeného příkazu:

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud set -n az-new-env
    Switched active cloud to 'az-new-env'.
    Use 'az login' to log in to this cloud.
    Use 'az account set' to set the active subscription.
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

4. Přihlaste se k prostředí Azure Stack Edge pro pomocí `az login` příkazu. K prostředí Azure Stack Edge pro se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

   Pomocí těchto kroků se přihlaste jako *uživatel*:

   Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolený službu Multi-Factor Authentication, musíte to udělat.

   Následující příklad ukazuje použití ukázky `az login` :
    
    ```powershell
    PS C:\Certificates> az login -u EdgeARMuser
    ```
   Po použití příkazu pro přihlášení se zobrazí výzva k zadání hesla. Zadejte Azure Resource Manager heslo.

   V následujícím příkladu je uveden vzorový výstup pro úspěšné přihlášení po zadání hesla:  
   
   ```powershell
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az login -u EdgeARMuser
   Password:
   [
        {
            "cloudName": "az-new-env",
            "id": "A4257FDE-B946-4E01-ADE7-674760B8D1A3",
            "isDefault": true,
            "name": "Default Provider Subscription",
            "state": "Enabled",
            "tenantId": "c0257de7-538f-415c-993a-1b87a031879d",
            "user": {
                "name": "EdgeArmUser@localhost",
                "type": "user"
            }
        }
   ]
   PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
   ```
   Poznamenejte si `id` `tenantId` hodnoty a tak, aby odpovídaly vašemu ID předplatného Azure Resource Manager a Azure Resource Manager ID tenanta a budou se používat v pozdějším kroku.
       
   Následující proměnné prostředí je třeba nastavit tak, aby fungovaly jako *instanční objekt*:

   ```
   $ENV:ARM_TENANT_ID = "c0257de7-538f-415c-993a-1b87a031879d"
   $ENV:ARM_CLIENT_ID = "cbd868c5-7207-431f-8d16-1cb144b50971"
   $ENV:ARM_CLIENT_SECRET - "<Your Azure Resource Manager password>"
   $ENV:ARM_SUBSCRIPTION_ID = "A4257FDE-B946-4E01-ADE7-674760B8D1A3"
   ```

   Vaše ID klienta Azure Resource Manager je pevně zakódováno. Ve výstupu `az login` příkazu, který jste provedli dříve, se nachází vaše Azure Resource Manager ID tenanta a ID Předplatného Azure Resource Manager. Azure Resource Manager tajný klíč klienta je Azure Resource Manager heslo, které jste nastavili.

   Další informace najdete v tématu [Azure Resource Manager heslo](azure-stack-edge-j-series-set-azure-resource-manager-password.md).

5. Změňte profil na verzi 2019-03-01 – Hybrid. Chcete-li změnit verzi profilu, spusťte následující příkaz:

    ```powershell
    az cloud update --profile 2019-03-01-hybrid
    ```

    Následující příklad ukazuje použití ukázky `az cloud update` :

    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> az cloud update --profile 2019-03-01-hybrid
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ```

## <a name="step-2-create-a-vm"></a>Krok 2: Vytvoření virtuálního počítače

K vytvoření virtuálního počítače se vám poskytne skript Pythonu. V závislosti na tom, jestli jste přihlášeni jako uživatel nebo nastavili jako instanční objekt, skript provede odpovídající vstup a vytvoří virtuální počítač.

1. Spusťte skript Pythonu ze stejného adresáře, ve kterém je nainstalovaný Python.

    `.\python.exe example_dbe_arguments_name_https.py cli`

2. Po spuštění skriptu trvá odeslání virtuálního pevného disku 20-30 minut. Chcete-li zobrazit průběh operace odeslání, můžete použít Průzkumník služby Azure Storage nebo AzCopy.

    Tady je ukázkový výstup úspěšného spuštění skriptu. Skript vytvoří všechny prostředky v rámci skupiny prostředků, použije tyto prostředky k vytvoření virtuálního počítače a nakonec odstraní skupinu prostředků včetně všech prostředků, které vytvořil.

    
    ```powershell
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2> .\python.exe example_dbe_arguments_name_https.py cli
    
    Create Resource Group
    Create a storage account
    Uploading to Azure Stack Storage as blob:
            ubuntu13.vhd
    
    Listing blobs...
            ubuntu13.vhd
    
    VM image resource id:
                /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/azure-sample-group-virtual-machines118/providers/Microsoft.Compute/images/UbuntuImage
    
    Create Vnet
    Create Subnet
    Create NIC
    Creating Linux Virtual Machine
    Tag Virtual Machine
    Create (empty) managed Data Disk
    Get Virtual Machine by Name
    Attach Data Disk
    Detach Data Disk
    Deallocating the VM (to prepare for a disk resize)
    Update OS disk size
    Start VM
    Restart VM
    Stop VM
    
    List VMs in subscription
            VM: VmName118
    
    List VMs in resource group
            VM: VmName118
    
    Delete VM
    All example operations completed successfully!
    
    Delete Resource Group
    Deleted: azure-sample-group-virtual-machines118
    PS C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2>
    ``` 


## <a name="next-steps"></a>Další kroky

[Běžné AZ CLI Commands pro virtuální počítače se systémem Linux](../virtual-machines/linux/cli-manage.md)
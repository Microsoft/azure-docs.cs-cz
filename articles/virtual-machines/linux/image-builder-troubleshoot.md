---
title: Řešení potíží se službou Azure image Builder
description: Řešení běžných problémů a chyb při použití služby Azure VM Image Builder Service
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 52801d0d7b02bb3637b5edb03072bde04a023de9
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881784"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Řešení potíží se službou Azure image Builder

Tento článek vám pomůže vyřešit běžné problémy, se kterými se můžete setkat při používání služby Azure image Builder.

K AIB selhání může dojít ve dvou oblastech:
- Odeslání šablony obrázku
- Sestavení image

## <a name="troubleshoot-image-template-submission-errors"></a>Řešení chyb při odesílání šablony imagí

Chyby odeslání šablony obrázku jsou vráceny pouze při odeslání. Není k dispozici protokol chyb pro chyby odeslání šablony obrázku. Pokud při odeslání došlo k chybě, můžete vrátit chybu tak, že zkontrolujete stav šablony, konkrétně zkontrolujete `ProvisioningState` a `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> V případě PowerShellu budete muset nainstalovat [moduly PowerShellu pro sestavovatele imagí Azure](../windows/image-builder-powershell.md#prerequisites).

Následující části obsahují pokyny k řešení problémů s běžnými chybami při odesílání šablon imagí.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Aktualizace nebo upgrade šablon imagí se v tuto chvíli nepodporuje.

#### <a name="error"></a>Chyba

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Příčina

Šablona již existuje.

#### <a name="solution"></a>Řešení

Pokud odešlete šablonu konfigurace obrázku a odeslání selžou, neúspěšný artefakt šablony stále existuje. Odstraňte neúspěšnou šablonu.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>Operace prostředku se dokončila se stavem zřizování terminálu selhalo.

#### <a name="error"></a>Chyba

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Příčina

Ve většině případů dojde k chybě selhání nasazení prostředků z důvodu chybějících oprávnění.

#### <a name="solution"></a>Řešení

V závislosti na vašem scénáři může Azure image Builder potřebovat oprávnění k těmto akcím:
- Zdrojová Image nebo skupina prostředků Galerie sdílených imagí
- Prostředek pro distribuci distribuce nebo sdílenou image v galerii imagí
- Účet úložiště, kontejner nebo objekt blob, ke kterému má přidaný soubor přístup. 

Další informace o konfiguraci oprávnění najdete v tématu [Konfigurace oprávnění služby Azure image Builder pomocí Azure CLI](image-builder-permissions-cli.md) nebo [Konfigurace oprávnění služby Azure image Builder pomocí PowerShellu](image-builder-permissions-powershell.md) .

### <a name="error-getting-managed-image"></a>Chyba při získávání spravované image

#### <a name="error"></a>Chyba

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Příčina

Chybí oprávnění.

#### <a name="solution"></a>Řešení

V závislosti na vašem scénáři může Azure image Builder potřebovat oprávnění k těmto akcím:
* Zdrojová Image nebo skupina prostředků Galerie sdílených imagí
* Prostředek pro distribuci distribuce nebo sdílenou image v galerii imagí
* Účet úložiště, kontejner nebo objekt blob, ke kterému má přidaný soubor přístup. 

Další informace o konfiguraci oprávnění najdete v tématu [Konfigurace oprávnění služby Azure image Builder pomocí Azure CLI](image-builder-permissions-cli.md) nebo [Konfigurace oprávnění služby Azure image Builder pomocí PowerShellu](image-builder-permissions-powershell.md) .

### <a name="build--step-failed-for-image-version"></a>Nepovedlo se vytvořit krok sestavení pro verzi image.

#### <a name="error"></a>Chyba
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Příčina

Azure image Builder nemůže najít zdrojovou image.

#### <a name="solution"></a>Řešení

Ujistěte se, že je zdrojová image správná a existuje v umístění služby Azure image Builder.

### <a name="downloading-external-file-to-local-file"></a>Stahuje se externí soubor do místního souboru.

#### <a name="error"></a>Chyba

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Příčina

Název souboru nebo umístění nejsou správné, nebo je umístění nedosažitelné.

#### <a name="solution"></a>Řešení

Zajistěte, aby byl soubor dostupný. Ověřte, zda je název a umístění správné.

## <a name="troubleshoot-build-failures"></a>Řešení potíží s chybami sestavení

V případě selhání sestavení imagí můžete získat chybu z rozhraní `lastrunstatus` a pak zkontrolovat podrobnosti v části přizpůsobení. log.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Protokol přizpůsobení

Když je sestavení image spuštěné, vytvoří se protokoly a uloží se do účtu úložiště. Azure image Builder vytvoří účet úložiště v dočasné skupině prostředků při vytváření artefaktu šablony obrázku.

Název účtu úložiště používá následující vzor: **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Například *IT_aibmdi_helloImageTemplateLinux01*.

Vlastní nastavení. účet úložiště můžete zobrazit ve skupině prostředků tak, že vyberete objekty blob **účtu úložiště**  >    >  `packerlogs` .  Pak vyberte **adresář > přizpůsobení. log**.


### <a name="understanding-the-customization-log"></a>Princip protokolu přizpůsobení

Protokol je podrobnější. Zahrnuje sestavení imagí včetně všech problémů s distribucí imagí, jako je například replikace Galerie sdílených imagí. Tyto chyby jsou uvedené v chybové zprávě stavu šablony obrázku.

Přizpůsobení. log zahrnuje tyto fáze:

1. Nasaďte virtuální počítač sestavení a závislosti pomocí šablon ARM do fáze IT_ pracovní skupiny prostředků. Tato fáze zahrnuje několik příspěvků do poskytovatele prostředků Azure image Builder:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Stav fáze nasazení. Tato fáze zahrnuje stav nasazení každého prostředku:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Připojte se ke fázi sestavení virtuálního počítače.

    Pokud je Windows, služba Azure image Builder se připojuje pomocí WinRM:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    V případě systému Linux se služba Azure image Builder připojí pomocí SSH:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Spusťte fázi přizpůsobení. Když se vlastní nastavení spustí, můžete je identifikovat kontrolou v části přizpůsobení. log. Hledání *(telemetrie)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Fáze zrušení zřízení. Azure image Builder přidá skrytého úprav. Tento krok zrušení zřízení zodpovídá za přípravu virtuálního počítače na zrušení zřízení. Spustí nástroj Windows Sysprep (pomocí c:\DeprovisioningScript.ps1) nebo se v systému Linux waagent dezřizování (pomocí/tmp/DeprovisioningScript.sh). 

    Příklad:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Vyčistit fázi. Po dokončení sestavení se prostředky Azure image Builder odstraní.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Tipy pro řešení potíží se skriptem/vloženým přizpůsobením
- Před odesláním do Tvůrce imagí otestujte kód
- Ujistěte se, že Azure Policy a brány firewall umožňují připojení ke vzdáleným prostředkům.
- Komentáře k výstupu do konzoly, jako je například použití `Write-Host` nebo `echo` , vám umožní vyhledat v části přizpůsobení. log.

## <a name="troubleshoot-common-build-errors"></a>Řešení běžných chyb sestavení

### <a name="packer-build-command-failure"></a>Chyba příkazu pro sestavení balíčku

#### <a name="error"></a>Chyba

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Příčina

Přizpůsobení se nezdařilo.

#### <a name="solution"></a>Řešení

Přečtěte si protokol a vyhledejte chyby úprav. Hledání *(telemetrie)*. 

Příklad:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Časový limit překročen

#### <a name="error"></a>Chyba

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Příčina

Sestavení překročilo časový limit sestavení. Tato chyba se zobrazuje v ' lastrunstatus '.

#### <a name="solution"></a>Řešení

1. Zkontrolujte přizpůsobení. log. Identifikujte poslední úpravou, která se má spustit. Vyhledat `(telemetry)` od konce protokolu. 

2. Podívejte se na přizpůsobení skriptů. Vlastní nastavení nemůžou potlačit interakci uživatele u příkazů, jako jsou například `quiet` Možnosti. Například způsobí, `apt-get install -y` že provádění skriptu čeká na interakci uživatele.

3. Pokud používáte `File` úpravou úprav ke stahování artefaktů větších než 20 MB, přejděte k části alternativní řešení.

4. Zkontrolujte chyby a závislosti ve skriptu, které by mohly způsobit čekání skriptu.

5. Pokud očekáváte, že přizpůsobení potřebuje víc času, zvyšte [buildTimeoutInMinutes](image-builder-json.md). Výchozí hodnota je čtyři hodiny.

6. Pokud máte akce náročné na prostředky, jako je například stahování gigabajtů souborů, zvažte základní velikost virtuálního počítače sestavení. Služba používá virtuální počítač Standard_D1_v2. Virtuální počítač má, 1 vCPU a 3,5 GB paměti. Pokud stahujete 50 GB, bude to pravděpodobně vyčerpat prostředky virtuálních počítačů a způsobit selhání komunikace mezi službou Azure image Builder a sestavením virtuálního počítače. Opakujte sestavení s větší pamětí virtuálního počítače nastavením [VM_Size](image-builder-json.md#vmprofile).

### <a name="long-file-download-time"></a>Doba stahování dlouhého souboru

#### <a name="error"></a>Chyba
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Příčina 

Úpravou souboru se stahuje velký soubor.

#### <a name="solution"></a>Řešení

Soubor úprav souborů je vhodný jenom pro stahování malých souborů, které je menší než 20 MB. U větších souborů ke stažení použijte skript nebo vložený příkaz. Například v systému Linux můžete použít `wget` nebo `curl` . Ve Windows můžete použít `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Chyba při čekání na galerii sdílených imagí

#### <a name="error"></a>Chyba

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Příčina

Při čekání na přidání a replikaci image do galerie sdílených imagí (SIG) vypršel časový limit tvůrce imagí. Pokud se bitová kopie vloží do SIG, je možné předpokládat, že sestavení obrázku bylo úspěšné. Celkový proces se ale nezdařil, protože tvůrce imagí čekal na galerii sdílených imagí, aby dokončil replikaci. I když se sestavení nezdařilo, replikace pokračuje. Vlastnosti verze image můžete získat tak, že zkontrolujete *runOutput* distribuce.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Řešení

Zvyšte **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Události s informacemi o nedostatku prostředků Windows

#### <a name="error"></a>Chyba

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Příčina

Vyčerpání prostředků K tomuto problému obvykle dochází web Windows Update, že se používá výchozí velikost virtuálního počítače buildu D1_V2.

#### <a name="solution"></a>Řešení

Zvětšete velikost virtuálního počítače sestavení.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Sestavení se dokončila, ale nevytvořily se žádné artefakty.

#### <a name="error"></a>Chyba

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Příčina

Vypršel časový limit vyvolaný čekáním na vytvoření požadovaných prostředků Azure.

#### <a name="solution"></a>Řešení

Spusťte znovu sestavení a zkuste to znovu.

### <a name="resource-not-found"></a>Prostředek se nenašel.

#### <a name="error"></a>Chyba

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Příčina

Chybí oprávnění.

#### <a name="solution"></a>Řešení

Opakovaný kontroler Azure image Builder má všechna oprávnění, která vyžaduje. 

Další informace o konfiguraci oprávnění najdete v tématu [Konfigurace oprávnění služby Azure image Builder pomocí Azure CLI](image-builder-permissions-cli.md) nebo [Konfigurace oprávnění služby Azure image Builder pomocí PowerShellu](image-builder-permissions-powershell.md) .

### <a name="sysprep-timing"></a>Časování nástroje Sysprep

#### <a name="error"></a>Chyba

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Příčina

Příčinou může být problém s časováním z důvodu velikosti virtuálního počítače D1_V2. Pokud jsou vlastní nastavení omezené a provádějí se za méně než tři sekundy, příkazy Sysprep spouští Azure image Builder k dezřizování. V případě, že je v nástroji Azure image Builder de-ustanovení, příkaz Sysprep kontroluje *WindowsAzureGuestAgent*, které se nemusí plně instalovat, což způsobuje problém s časováním. 

#### <a name="solution"></a>Řešení

Zvětšete velikost virtuálního počítače. Nebo můžete přidat 60 s PowerShellovým přizpůsobením v režimu spánku, abyste se vyhnuli problémům s časováním.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Zrušení tvůrce po zrušení kontextu kontextu zrušení

#### <a name="error"></a>Chyba
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Příčina
Služba image Builder používá port 22 (Linux) nebo 5986 (Windows) pro připojení k virtuálnímu počítači sestavení, k tomu dochází, když je služba odpojená od virtuálního počítače sestavení během sestavení image. Důvody odpojení se můžou lišit, ale když povolíte nebo nakonfigurujete brány firewall ve skriptu, můžou se výše uvedené porty blokovat.

#### <a name="solution"></a>Řešení
Projděte si skripty pro změny nebo povolení brány firewall nebo změny v SSH nebo WinRM a zajistěte, aby všechny změny umožňovaly stálé připojení mezi službou a sestavení virtuálního počítače na výše uvedených portech. Další informace o sítích s tvůrcem imagí najdete v [požadavcích](./image-builder-networking.md).

## <a name="devops-task"></a>Úloha DevOps 

### <a name="troubleshooting-the-task"></a>Řešení potíží s úlohou
Úloha bude neúspěšná, jenom když dojde k chybě během přizpůsobení. když se to stane, úloha nahlásí selhání a opustí pracovní skupinu prostředků s protokoly, abyste mohli problém identifikovat. 

Pokud chcete najít protokol, musíte znát název šablony, přejít do kanálu > neúspěšné sestavení > Projděte si podrobnosti o úloze DevOps pro AIB, potom se zobrazí protokol a název šablony:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Přejít na portál, vyhledejte název šablony ve skupině prostředků a vyhledejte skupinu prostředků s IT_ *.
Přejít na účet úložiště > objekty blob > kontejnery > protokoly.

### <a name="troubleshooting-successful-builds"></a>Řešení potíží s úspěšným sestavením
Může se jednat o některé případy, kdy potřebujete prozkoumat úspěšná sestavení a chcete si prohlédnout protokol. Jak už bylo zmíněno, pokud je sestavení image úspěšné, pracovní skupina prostředků, která obsahuje protokoly, se odstraní v rámci vyčištění. To, co všechno můžete dělat, je ale po vloženém příkazu po vložení přejít do režimu spánku a pak získá protokoly jako pozastavené sestavení. Použijte následující postup:
 
1. Aktualizujte vložený příkaz a přidejte: Write-Host/echo "spánek" – to vám umožní vyhledávat v protokolu.
2. Přidejte režim spánku aspoň na 10mins, můžete použít příkaz [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep)nebo `Sleep` Linux.
3. K identifikaci umístění protokolu použijte výše uvedenou metodu a pak si nechte protokol stáhnout nebo zkontrolovat, dokud se nedostane do režimu spánku.


### <a name="operation-was-canceled"></a>Operace se zrušila.

#### <a name="error"></a>Chyba

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Příčina

Pokud uživatel sestavení nezrušil, byl zrušen uživatelským agentem služby Azure DevOps. V důsledku možností Azure DevOps došlo k nejpravděpodobnějšímu vypršení platnosti 1 hodiny. Pokud používáte privátní projekt a agenta, dostanete 60 minut času sestavení. Pokud sestavení překračuje časový limit, DevOps zruší spuštěnou úlohu.

Další informace o možnostech a omezeních Azure DevOps najdete v tématu [agenti hostované v Microsoftu](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations) .
 
#### <a name="solution"></a>Řešení

Můžete hostovat vlastní agenty DevOps nebo se podívat, jak zkrátit čas svého sestavení. Pokud například distribuujete do galerie sdílených imagí, replikujte do jedné oblasti. Pokud chcete replikovat asynchronně. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Pomalé přihlášení Windows: Počkejte prosím, než se instalační program pro moduly Windows nainstaluje.

#### <a name="error"></a>Chyba
Po vytvoření bitové kopie s Windows 10 pomocí nástroje image Builder vytvořte virtuální počítač z image, budete mít protokol RDP a budete muset počkat minuty při prvním přihlášení, na které vidíte modrou obrazovku s touto zprávou:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Řešení
Nejprve v sestavení bitové kopie ověřte, že neexistují žádná nevyřízená restartování, a to tak, že jako poslední přizpůsobení přidáte restart systému Windows a všechny instalace softwaru budou dokončeny. Nakonec přidejte možnost [/Mode: VM](/windows-hardware/manufacture/desktop/sysprep-command-line-options) do výchozího nástroje Sysprep, který AIB používá. Další informace najdete v části virtuální počítače vytvořené z imagí AIB se úspěšně nevytvořily > přepsání příkazů.  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>Virtuální počítače vytvořené z imagí AIB se nevytvoří úspěšně.

Ve výchozím *nastavení spouští Azure* image Builder na konci každé fáze přizpůsobení image kód pro stanovení *generalizace* . Generalizace je proces, při kterém je image nastavená tak, aby se znovu použila k vytvoření více virtuálních počítačů, a můžete předat nastavení virtuálního počítače, jako je název hostitele, uživatelské jméno atd. V případě systému Windows spustí Azure image Builder *Nástroj Sysprep* a spustí se pro Linux Azure image Builder `waagent -deprovision` . 

Pro Windows používá Azure image Builder obecný příkaz Sysprep. To však nemusí být vhodné pro každé úspěšné generalizaci Windows. Azure image Builder umožňuje přizpůsobit příkaz Sysprep. Poznámka: Azure image Builder je nástroj pro automatizaci obrazu. Je zodpovědný za úspěšné spuštění příkazu Sysprep. K obnovení obrazu ale můžete potřebovat jiné příkazy nástroje Sysprep. Pro Linux používá Azure image Builder obecný `waagent -deprovision+user` příkaz. Další informace najdete v [dokumentaci k agentovi Microsoft Azure Linux](https://github.com/Azure/WALinuxAgent#command-line-options).

Pokud migrujete existující přizpůsobení a používáte jiné příkazy Sysprep/waagent, můžete vyzkoušet obecné příkazy pro tvůrce imagí. Pokud se virtuální počítač nepovede vytvořit, použijte předchozí příkazy Sysprep/waagent.

> [!NOTE]
> Pokud AIB vytvoří vlastní image Windows úspěšně a z ní vytvoříte virtuální počítač, pak se virtuální počítač úspěšně nevytvoří (například příkaz pro vytvoření virtuálního počítače není úplný nebo s vypršenou prodlevou), budete muset zkontrolovat dokumentaci k systému Windows Server Sysprep. Nebo můžete vytvořit žádost o podporu pro tým podpory Windows Server Sysprep Customer Services, který může řešit problémy a poradit se správným příkazem Sysprep.

### <a name="command-locations-and-filenames"></a>Umístění příkazů a názvy souborů

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Příkaz nástroje Sysprep: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Zrušit zřízení příkazu: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Přepsání příkazů

Pokud chcete příkazy přepsat, použijte modul pro vytváření skriptů PowerShellu nebo prostředí k vytvoření souborů příkazů s přesným názvem souboru a umístěte je do dříve uvedených adresářů. Azure image Builder tyto příkazy přečte a výstup se zapíše do části *přizpůsobení. log*.

## <a name="getting-support"></a>Získání podpory
Pokud jste odkazovali na doprovodné materiály a stále nemůžete vyřešit problém, můžete otevřít případ podpory. Když to uděláte, vyberte prosím správné téma produkt a podpora. tím se spustí tým podpory pro tvůrce imagí Azure VM.

Výběr produktu případu:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Přehled nástroje Azure image Builder](../image-builder-overview.md).

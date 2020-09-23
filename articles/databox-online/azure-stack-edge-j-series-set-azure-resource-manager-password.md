---
title: Nastavení Azure Resource Manager hesla na zařízení GPU pro Azure Stack Edge pro
description: Popisuje, jak se připojit k Azure Resource Manager běžícímu na grafickém procesoru Azure Stack Edge pro pomocí Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: df5fea8101834dae089ab97354c438363321a707
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904484"
---
# <a name="set-azure-resource-manager-password-on-azure-stack-edge-pro-gpu-device"></a>Nastavení Azure Resource Manager hesla na zařízení s grafickým procesorem Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak nastavit heslo pro Azure Resource Manager. Toto heslo musíte nastavit při připojování k místním rozhraním API zařízení prostřednictvím Azure Resource Manager.

Postup nastavení hesla se může lišit v závislosti na tom, jestli používáte Azure Portal nebo rutiny PowerShellu. Jednotlivé postupy jsou popsány v následujících částech.


## <a name="reset-password-via-the-azure-portal"></a>Resetování hesla pomocí Azure Portal

1. V Azure Portal pro správu zařízení přejít na prostředek Azure Stack Edge, který jste vytvořili. Začněte tím, že přejdete na **Edge compute >**.

2. V pravém podokně na panelu příkazů vyberte **resetovat heslo nástroje Edge ARM**. 

    ![Resetování hesla uživatele EdgeARM 1](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-1.png)

3. V okně **resetovat heslo uživatele EdgeArm** zadejte heslo pro připojení k místním rozhraním API zařízení prostřednictvím Azure Resource Manager. Potvrďte heslo a vyberte **resetovat**.

    ![Resetování hesla uživatele EdgeARM 2](media/azure-stack-edge-j-series-set-azure-resource-manager-password/set-edgearm-password-2.png)



## <a name="reset-password-via-powershell"></a>Resetování hesla prostřednictvím PowerShellu

1. Na webu Azure Portal přejdete na prostředek Azure Stack Edge, který jste vytvořili pro správu zařízení. Poznamenejte si následující parametry na stránce **Přehled** .

    - Název prostředku Azure Stack Edge
    - ID předplatného

2. Přejít na **nastavení > vlastnosti**. Na stránce **vlastnosti** si poznamenejte následující parametry.

    - Skupina prostředků
    - CIK šifrovací klíč: Vyberte zobrazení a pak zkopírujte **šifrovací klíč**.

    ![Získat šifrovací klíč CIK](media/azure-stack-edge-j-series-set-azure-resource-manager-password/get-cik-portal.png)
 
3. Identifikujte heslo, které budete používat pro připojení k Azure Resource Manager.

4. Spusťte Cloud Shell. Vyberte ikonu v pravém horním rohu:

    ![Spustit Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/start-cloud-shell.png) 

    Po spuštění Cloud shellu budete možná muset přepnout do PowerShellu.

    ![Cloud Shell](media/azure-stack-edge-j-series-set-azure-resource-manager-password/cloud-shell.png)   


5. Nastavte kontext. Zadejte:

    `Set-AzContext -SubscriptionId <Subscription ID>`

    Zde je ukázkový výstup:

    
    ```azurepowershell
    PS Azure:\> Set-AzContext -SubscriptionId 8eb87630-972c-4c36-a270-f330e6c063df
    
        Name        Account   SubscriptionName   Environment  TenantId
        ----       -------    ----------------   -----------  --------
        DataBox_Edge_Test (8eb87630-972c-4c36-a… MSI@50342 DataBox_Edge_Tes AzureCloud           72f988bf-86f1-41af-91ab-2d7…
    
        PS Azure:/
    ```
    
5.  Pokud máte nějaké staré moduly PS, musíte je nainstalovat.

    `Remove-Module  Az.DataBoxEdge -force`

    Zde je ukázkový výstup. V tomto příkladu nebyly nainstalovány žádné staré moduly.

    
    ```azurepowershell
        PS Azure:\> Remove-Module  Az.DataBoxEdge -force
        Remove-Module : No modules were removed. Verify that the specification of modules to remove is correct and those modules exist in the runspace.
        At line:1 char:1
        + Remove-Module  Az.DataBoxEdge -force
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ResourceUnavailable: (:) [Remove-Module], InvalidOperationException
        + FullyQualifiedErrorId : Modules_NoModulesRemoved,Microsoft.PowerShell.Commands.RemoveModuleCommand
    
        PS Azure:\
    ```

6. Další sada příkazů se stáhne a spustí skript pro instalaci modulů PowerShellu.
    
    ```azurepowershell
        cd ~/clouddrive
        wget https://aka.ms/dbe-cmdlet-beta -O Az.DataBoxEdge.zip
        unzip ./Az.DataBoxEdge.zip
        Import-Module ~/clouddrive/Az.DataBoxEdge/Az.DataBoxEdge.psd1 -Force
    ```

7. V další sadě příkazů budete muset zadat název prostředku, název skupiny prostředků, šifrovací klíč a heslo, které jste identifikovali v předchozím kroku.

    ```azurepowershell
    $devicename = "<Azure Stack Edge resource name>"
    $resourceGroup = "<Resource group name>"
    $cik = "<Encryption key>"
    $password = "<Password>"
    ```
    Parametry hesla a šifrovacího klíče musí být předány jako zabezpečené řetězce. K převodu hesla a šifrovacího klíče na zabezpečené řetězce použijte následující rutiny.

    ```azurepowershell
    $pass = ConvertTo-SecureString $password -AsPlainText -Force
    $key = ConvertTo-SecureString $cik -AsPlainText -Force
    ```
    Použijte výše generované zabezpečené řetězce jako parametry v rutině Set-AzDataBoxEdgeUser pro resetování hesla. Použijte stejnou skupinu prostředků, kterou jste použili při vytváření prostředku Azure Stack Edge pro/Data Box Gateway.

    ```azurepowershell
    Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    ```
    Zde je ukázkový výstup.
    
    ```azurepowershell
    PS /home/aseuser/clouddrive> $devicename = "myaseresource"
    PS /home/aseuser/clouddrive> $resourceGroup = "myaserg"
    PS /home/aseuser/clouddrive> $cik = "54a7450fd7b3c506e10efea4e0c88a9390f37e299fbf43e01fb5dfe483ac036b6d0f85a6246e1926e297f98c0ff84c20a57348c689eff179ce31571fc787ac0a"
    PS /home/aseuser/clouddrive> $password = "Password2"
    PS /home/aseuser/clouddrive> $pass = ConvertTo-SecureString $password -AsPlainText -Force
    PS /home/aseuser/clouddrive> $key = ConvertTo-SecureString $cik -AsPlainText -Force
    PS /home/aseuser/clouddrive> Set-AzDataBoxEdgeUser -ResourceGroupName $resourceGroup -DeviceName $devicename -Name EdgeARMUser  -Password $pass -EncryptionKey $key
    
        User name   Type ResourceGroupName DeviceName
    ---------   ---- ----------------- ----------
        EdgeARMUser ARM  myaserg        myaseresource
    
        PS /home/aseuser/clouddrive>
    ```
Pomocí nového hesla se připojte k Azure Resource Manager.

## <a name="next-steps"></a>Další kroky

[Připojení k Azure Resource Manager](azure-stack-edge-j-series-connect-resource-manager.md)

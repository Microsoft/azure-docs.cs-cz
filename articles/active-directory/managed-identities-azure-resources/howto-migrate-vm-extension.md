---
title: Ukončení používání rozšíření virtuálního počítače spravované identity – Azure AD
description: Krok za krokem přestat používat rozšíření virtuálního počítače a začít používat službu metadat instance Azure (IMDS) pro ověřování.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049071"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Jak přestat používat rozšíření spravovaných identit virtuálního počítače a začít používat službu metadat instance Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Rozšíření virtuálního počítače pro spravované identity

Rozšíření virtuálního počítače pro spravované identity se používá k vyžádání tokenů pro spravovanou identitu v rámci virtuálního počítače. Pracovní postup se skládá z následujících kroků:

1. Nejprve zatížení v rámci prostředku volá `http://localhost/oauth2/token` místní koncový bod požádat o přístupový token.
2. Rozšíření virtuálního počítače pak používá přihlašovací údaje pro spravovanou identitu, chcete-li požádat o přístupový token z Azure AD.. 
3. Přístupový token se vrátí volajícímu a lze jej použít k ověření služeb, které podporují ověřování Azure AD, jako je Azure Key Vault nebo Azure Storage.

Vzhledem k několika omezením popsaným v další části se rozšíření virtuálního počítače spravované identity zastaralo ve prospěch použití ekvivalentního koncového bodu ve službě Metadatazure instance (IMDS).

### <a name="provision-the-extension"></a>Ustanovení o prodloužení 

Když nakonfigurujete škálovací sadu virtuálního počítače nebo virtuálního počítače tak, aby měla spravovanou `-Type` identitu, můžete volitelně zvolit zřídit spravované identity pro rozšíření virtuálního počítače prostředků Azure pomocí parametru na rutině [Set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Můžete předat `ManagedIdentityExtensionForWindows` buď `ManagedIdentityExtensionForLinux`nebo , v závislosti na typu virtuálního počítače a pojmenujte jej pomocí parametru. `-Name` Parametr `-Settings` určuje port používaný koncovým bodem tokenu OAuth pro získání tokenu:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Šablonu nasazení Azure Resource Manageru můžete taky použít ke zřízení rozšíření virtuálního `resources` počítače přidáním `ManagedIdentityExtensionForLinux` následujícího jsondo do části do šablony (použijte pro název a typ elementy pro verzi Linuxu).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Pokud pracujete s škálovacími sadami virtuálních strojů, můžete také zřídit spravované identity pro rozšíření škálovací sady virtuálních strojů Azure pomocí rutiny [Add-AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) Můžete předat `ManagedIdentityExtensionForWindows` buď `ManagedIdentityExtensionForLinux`nebo , v závislosti na typu škálovací `-Name` sady virtuálního počítače a pojmenujte ji pomocí parametru. Parametr `-Settings` určuje port používaný koncovým bodem tokenu OAuth pro získání tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Chcete-li zřídit rozšíření škálovací sady virtuálních strojů pomocí šablony nasazení `extensionpProfile` Azure Resource Manager, přidejte do části do šablony následující JSON (použijte `ManagedIdentityExtensionForLinux` pro prvky názvu a typu pro verzi Linuxu).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

Zřizování rozšíření virtuálního počítače může selhat z důvodu selhání vyhledávání DNS. Pokud k tomu dojde, restartujte virtuální počítač a akci opakujte. 

### <a name="remove-the-extension"></a>Odebrání rozšíření 
Pokud chcete rozšíření `-n ManagedIdentityExtensionForWindows` odebrat, použijte `-n ManagedIdentityExtensionForLinux` nebo přepněte (v závislosti na typu virtuálního počítače) s [odstraněním rozšíření AZ VM](https://docs.microsoft.com/cli/azure/vm/)nebo [odstraněním rozšíření AZ VMSS](https://docs.microsoft.com/cli/azure/vmss) pro škálovací sady virtuálních strojů pomocí azure cli nebo `Remove-AzVMExtension` pro Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Získání tokenu pomocí rozšíření virtuálního počítače

Následuje ukázkový požadavek pomocí spravovaných identit pro koncový bod rozšíření virtuálního počítače Azure:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Sloveso HTTP označující, že chcete načíst data z koncového bodu. V tomto případě přístupový token OAuth. | 
| `http://localhost:50342/oauth2/token` | Spravované identity pro koncový bod prostředků Azure, kde 50342 je výchozí port a je konfigurovatelný. |
| `resource` | Parametr řetězce dotazu označující identifikátor URI ID aplikace cílového prostředku. Zobrazí se také `aud` v deklaraci (publika) vydaného tokenu. Tento příklad požaduje token pro přístup ke Správci prostředků Azure, který má identifikátor URI ID aplikace `https://management.azure.com/`. |
| `Metadata` | Pole hlavičky požadavku HTTP, které vyžadují spravované identity pro prostředky Azure jako zmírnění útoku na vyžádání na straně serveru (SSRF). Tato hodnota musí být nastavena na "true", ve všech malá písmena.|
| `object_id` | (Nepovinné) Parametr řetězce dotazu označující object_id spravované identity, pro kterou chcete token. Povinné, pokud váš virtuální počítač má více uživatelem přiřazené spravované identity.|
| `client_id` | (Nepovinné) Parametr řetězce dotazu označující client_id spravované identity, pro kterou chcete token. Povinné, pokud váš virtuální počítač má více uživatelem přiřazené spravované identity.|


Odpověď vzorku:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Popis |
| ------- | ----------- |
| `access_token` | Požadovaný přístupový token. Při volání zabezpečené rozhraní REST API je `Authorization` token vložen do pole hlavičky požadavku jako token "nosiče", což umožňuje rozhraní API k ověření volajícího. | 
| `refresh_token` | Nepoužívají spravované identity pro prostředky Azure. |
| `expires_in` | Počet sekund, po které je přístupový token i nadále platný před vypršením platnosti, od doby vydání. Čas vydání lze nalézt v `iat` deklaraci tokenu. |
| `expires_on` | Časový rozsah při vypršení platnosti přístupového tokenu. Datum je reprezentováno jako počet sekund od "1970-01-01T0:0:0Z UTC" `exp` (odpovídá deklaraci tokenu). |
| `not_before` | Časový rozsah, kdy se projeví přístupový token a může být přijat. Datum je reprezentováno jako počet sekund od "1970-01-01T0:0:0Z UTC" `nbf` (odpovídá deklaraci tokenu). |
| `resource` | Prostředek, pro který byl požadován přístupový token, který odpovídá parametru `resource` řetězce dotazu požadavku. |
| `token_type` | Typ tokenu, což je přístupový token "Nosič", což znamená, že prostředek může poskytnout přístup k nositeli tohoto tokenu. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Poradce při potížích s rozšířením virtuálního počítače 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Restartování rozšíření virtuálního počítače po selhání

V systému Windows a některých verzích Linuxu, pokud se rozšíření zastaví, může být k ručnímu restartování použita následující rutina:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název a typ rozšíření systému Windows je:`ManagedIdentityExtensionForWindows`
- Název a typ rozšíření pro Linux je:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automatizační skript" se nezdaří při pokusu o export schématu pro spravované identity pro rozšíření prostředků Azure

Když jsou spravované identity pro prostředky Azure povolené ve virtuálním počítači, zobrazí se při pokusu o použití funkce "Automatizační skript" pro virtuální počítač nebo jeho skupinu prostředků následující chyba:

![Chyby exportu skriptu pro spravované identity pro automatizační skripty azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Spravované identity pro rozšíření virtuálního počítače prostředků Azure aktuálně nepodporují možnost exportu jeho schématu do šablony skupiny prostředků. V důsledku toho vygenerovaná šablona nezobrazuje parametry konfigurace, které by umožnily spravované identity pro prostředky Azure v prostředku. Tyto oddíly lze přidat ručně podle příkladů v [konfigurovat spravované identity pro prostředky Azure na virtuálním počítači Azure pomocí šablon](qs-configure-template-windows-vm.md).

Když bude funkce exportu schématu dostupná pro spravované identity pro rozšíření virtuálního počítače prostředků Azure (plánované na vyřazení v lednu 2019), bude uvedená v [exportu skupin prostředků, které obsahují rozšíření virtuálních aplikací](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Omezení rozšíření virtuálního počítače 

Existuje několik hlavních omezení použití rozšíření virtuálního počítače. 

 * Nejzávažnější omezení je skutečnost, že pověření použitá k vyžádání tokenů jsou uloženy ve virtuálním počítači. Útočník, který úspěšně poruší virtuální počítač, může pověření exfiltrovat. 
 * Rozšíření virtuálního počítače navíc stále není podporováno několika distribucemi Linuxu, s obrovskými náklady na vývoj, které upravují, vytvářejí a testují rozšíření na každé z těchto distribucí. V současné době jsou podporovány pouze následující distribuce Linuxu: 
    * CoreOS stabilní
    * Centos 7,1 
    * Červený klobouk 7,2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Nasazení virtuálních počítačů se spravovanými identitami má dopad na výkon, protože rozšíření virtuálního počítače musí být také zřízené. 
 * Nakonec rozšíření virtuálního počítače může podporovat pouze 32 uživatelem přiřazených spravovaných identit na virtuální počítač. 

## <a name="azure-instance-metadata-service"></a>Služba metadat instance Azure

[Služba metadat instance Azure (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) je koncový bod REST, který poskytuje informace o spuštěných instancích virtuálních počítačů, které se dá použít ke správě a konfiguraci vašich virtuálních počítačů. Koncový bod je k dispozici na známé nesměrovatelné`169.254.169.254`IP adrese ( ), ke které lze přistupovat pouze z virtuálního počítače.

Existuje několik výhod použití Azure IMDS požadovat tokeny. 

1. Služba je externí než virtuální počítač, proto přihlašovací údaje používané spravované identity již nejsou k dispozici na virtuálním počítači. Místo toho jsou hostované a zabezpečené na hostitelském počítači virtuálního počítače Azure.   
2. Všechny operační systémy Windows a Linux podporované v Azure IaaS můžou používat spravované identity.
3. Nasazení je rychlejší a jednodušší, protože rozšíření virtuálního počítače už není potřeba zřídit.
4. S koncovým bodem IMDS lze jednomu virtuálnímu počítači přiřadit až 1000 spravovaných identit přiřazených uživatelem.
5. Neexistuje žádná významná změna požadavků pomocí IMDS na rozdíl od těch, kteří používají rozšíření virtuálního počítače, proto je poměrně jednoduché portovat přes stávající nasazení, která aktuálně používají rozšíření virtuálního počítače.

Z těchto důvodů bude služba Azure IMDS defacto způsob, jak požádat o tokeny, jakmile se rozšíření virtuálního počítače zastaralo. 


## <a name="next-steps"></a>Další kroky

* [Jak používat spravované identity pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu](how-to-use-vm-token.md)
* [Služba metadat instance Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

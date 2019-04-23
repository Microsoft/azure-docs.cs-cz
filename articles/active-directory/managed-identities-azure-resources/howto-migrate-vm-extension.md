---
title: Pomocí rozšíření virtuálních počítačů spravovanou identitu a spouštění pomocí koncového bodu služby Azure Instance Metadata
description: Projděte pokyny ke přestat používat rozšíření virtuálního počítače a začít používat služba metadat Instance Azure (IMDS) pro ověřování.
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
ms.openlocfilehash: 5b3c6c99b05320ee53c3ff49f5c299650c32e939
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440815"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Postup zastavení virtuálního počítače pomocí spravované identity rozšíření a začít používat službu Azure Instance Metadata

## <a name="virtual-machine-extension-for-managed-identities"></a>Rozšíření virtuálního počítače pro spravované identity

Rozšíření virtuálního počítače pro spravovaným identitám umožňuje požádat o tokeny pro spravovanou identitu v rámci virtuálního počítače. Pracovní postup se skládá z následujících kroků:

1. Nejprve zatížení v rámci prostředku volá místním koncovým bodem `http://localhost/oauth2/token` k vyžádání tokenu přístupu.
2. Rozšíření virtuálního počítače k vyžádání tokenu přístupu z Azure AD použije přihlašovací údaje pro spravovanou identitu. 
3. Přístupový token je vrátit zpět volajícímu a slouží k ověření služby, které podporují ověřování Azure AD, jako je Azure Key Vault nebo Azure Storage.

Z důvodu několik omezení uvedených v následující části se už nepoužívá spravovanou identitu rozšíření virtuálního počítače používat ekvivalentní koncový bod v Azure Instance Metadata služby (IMDS)

### <a name="provision-the-extension"></a>Zřizování rozšíření 

Při konfiguraci virtuálního počítače nebo virtuálního počítače škálovací sady má spravovanou identitu se může volitelné rozhodnout, může volitelně ke zřízení spravovaných identit pro prostředky Azure, virtuálního počítače pomocí rozšíření `-Type` parametru u [ Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) rutiny. Můžete předat buď `ManagedIdentityExtensionForWindows` nebo `ManagedIdentityExtensionForLinux`, v závislosti na typu virtuálního počítače a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Nasazení šablony Azure Resource Manageru můžete také použít ke zřízení rozšíření virtuálního počítače tak, že přidáte následující kód JSON do `resources` části šablony (použijte `ManagedIdentityExtensionForLinux` název a typ elementů pro Linux verze).

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
    
    
Pokud pracujete se škálovacími sadami virtuálních počítačů, můžete také vytvářet spravované identity pro prostředky Azure škálovací sady virtuálních počítačů pomocí rozšíření [přidat AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) rutiny. Můžete předat buď `ManagedIdentityExtensionForWindows` nebo `ManagedIdentityExtensionForLinux`, v závislosti na typu škálování virtuálního počítače nastavte a pojmenujte ho pomocí `-Name` parametru. `-Settings` Parametr určuje port používaný programem koncový bod tokenu OAuth pro získání tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Ke zřízení virtuálního počítače škálovací nastavit rozšíření se šablonou nasazení Azure Resource Manageru, přidejte následující kód JSON do `extensionpProfile` části šablony (použijte `ManagedIdentityExtensionForLinux` název a typ elementů pro Linux verze).

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

Zřizování rozšíření virtuálního počítače může selhat z důvodu chyby vyhledávání DNS. Pokud k tomu dojde, restartujte virtuální počítač a zkuste to znovu. 

### <a name="remove-the-extension"></a>Odebrání rozšíření 
Chcete-li odebrat rozšíření, použijte `-n ManagedIdentityExtensionForWindows` nebo `-n ManagedIdentityExtensionForLinux` přepnout (v závislosti na typu virtuálních počítačů) s [odstranění rozšíření az vm](https://docs.microsoft.com/cli/azure/vm/), nebo [az vmss extension delete](https://docs.microsoft.com/cli/azure/vmss) pro škálování virtuálních počítačů Nastaví pomocí Azure CLI nebo `Remove-AzVMExtension` pro prostředí Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Získání tokenu pomocí rozšíření virtuálního počítače.

Následuje ukázkový požadavek pomocí spravované identity pro prostředky Azure rozšíření koncového bodu virtuálního počítače:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, což znamená, že má k načtení dat z koncového bodu. V takovém případě OAuth přístupový token. | 
| `http://localhost:50342/oauth2/token` | Spravované identity pro koncový bod prostředků Azure, ve kterém 50342 je výchozím portem a je možné konfigurovat. |
| `resource` | Parametr řetězce dotazu, určující identifikátor URI ID aplikace z cílového prostředku. Zobrazí se také v `aud` deklarace identity (cílová skupina) vydaného tokenu. V tomto příkladu žádá token pro přístup k Azure Resource Manageru, který má být identifikátor URI ID aplikace z https://management.azure.com/. |
| `Metadata` | Pole hlavičky požadavku HTTP, vyžaduje spravované identity pro prostředky Azure jako zmírnění proti útoku Server na straně požadavek proti padělání (SSRF). Tato hodnota musí být nastavená na "true", malými písmeny.|
| `object_id` | (Volitelné) Parametr řetězce dotazu, která object_id spravovanou identitu, kterou byste chtěli token pro. Povinné, pokud se váš virtuální počítač má několik spravovaných uživatelsky přiřazené identity.|
| `client_id` | (Volitelné) Parametr řetězce dotazu, která client_id spravovanou identitu, kterou byste chtěli token. Povinné, pokud se váš virtuální počítač má několik spravovaných uživatelsky přiřazené identity.|


Ukázkové odpovědi:

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
| `access_token` | Požadovaný přístupový token. Při volání rozhraní REST API zabezpečeným token, který je součástí `Authorization` pole hlavičky požadavku jako token "nosiče", umožňuje rozhraní API za účelem ověření volající. | 
| `refresh_token` | Není možné použít pomocí spravované identity u prostředků Azure. |
| `expires_in` | Počet sekund, po které přístupový token je stále platné, před vypršením platnosti v době vydání. Čas vystavení lze nalézt v tokenu `iat` deklarací identity. |
| `expires_on` | Interval timespan, když vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od "1970-01-01T0:0:0Z UTC" (odpovídá tokenu `exp` deklarace identity). |
| `not_before` | Interval timespan, když přístupový token se projeví a jdou přijmout. Datum je vyjádřena jako počet sekund od "1970-01-01T0:0:0Z UTC" (odpovídá tokenu `nbf` deklarace identity). |
| `resource` | Přístupový token se požádalo pro odpovídající prostředek `resource` požadavku parametr řetězce dotazu. |
| `token_type` | Zadejte token, který je přístupový token "Nosiče", což znamená, že prostředek může poskytnout přístup k nosný token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Řešení potíží s rozšířením virtuálního počítače 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Restartování se rozšíření virtuálního počítače po selhání

V některých verzích systému Linux a Windows Pokud rozšíření zastaví, následující rutiny můžou sloužit k ho restartovat ručně:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název rozšíření a typ pro Windows je: `ManagedIdentityExtensionForWindows`
- Název rozšíření a typ pro Linux je: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automatizační skript" selhání při pokusu o export schématu pro spravované identity pro rozšíření prostředků Azure

Pokud spravovaných identit pro prostředky Azure je povoleno na virtuálním počítači, se zobrazí následující chyba při pokusu o použití funkce "Skript automatizace" pro virtuální počítač nebo jeho skupina prostředků:

![Chyba při exportování spravovaných identit pro prostředky Azure automatizační skript](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Spravované identity pro rozšíření virtuálního počítače prostředků Azure v současné době nepodporuje možnost exportu schématem pro šablonu skupiny prostředků. Kvůli tomu že vygenerovaná šablona nezobrazuje parametry konfigurace, umožňuje spravovaným identitám pro prostředky Azure pro prostředek. Tyto části můžete přidat ručně podle příkladů v [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablony](qs-configure-template-windows-vm.md).

Když funkce exportu schématu je k dispozici pro spravované identity pro rozšíření virtuálního počítače prostředky Azure (plánovaná k převedení na zastaralého v lednu 2019), objeví se v [export skupiny prostředků, obsahující rozšíření virtuálních počítačů ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Omezení rozšíření virtuálního počítače. 

Existuje několik hlavních omezení pomocí rozšíření virtuálního počítače. 

 * Nejvážnější omezením je skutečnost, že pověření používaná pro požádání o tokeny jsou uložené na virtuálním počítači. Útočník, který úspěšně poruší virtuálního počítače může stáhnout přihlašovací údaje. 
 * Kromě toho se rozšíření virtuálního počítače stále nepodporuje několika Linuxových distribucí, s velkých vývojových nákladů k úpravám, sestavení a testování rozšíření na každém z těchto distribucí. V současné době jsou podporovány pouze následující distribucí systému Linux: 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7.2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Jako rozšíření virtuálního počítače se taky musí zřídit je dopad na výkon až po nasazení virtuálních počítačů pomocí spravované identity. 
 * A konečně rozšíření virtuálního počítače podporují jenom s 32 uživatelsky přiřazené spravované identity na virtuální počítač. 

## <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

[Azure Instance Metadata služby (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) je koncový bod REST, který poskytuje informace o spuštěných instancí virtuálních počítačů, které lze použít ke správě nebo konfiguraci virtuálních počítačů. Koncový bod je k dispozici na dobře známé nesměrovatelných adres IP (`169.254.169.254`), který je přístupný pouze z v rámci virtuálního počítače.

Existuje několik výhod Azure IMDS pomocí žádosti o tokeny. 

1. Služba je mimo virtuální počítač, proto přihlašovací údaje používané spravovaným identitám už nejsou k dispozici na virtuálním počítači. Místo toho jsou hostované a zabezpečená na hostitelském počítači virtuální počítač Azure.   
2. Spravované identity můžete použít všechny Windows a Linux podporované operační systémy na Azure IaaS.
3. Nasazení je rychlejší a jednodušší, protože rozšíření virtuálního počítače už musí být zřízená.
4. Koncový bod, až 1000, uživatelsky přiřazené identity spravované IMDS můžete přidělit jeden virtuální počítač.
5. Neexistuje žádné významné změny požadavků pomocí IMDS oproti těm, kteří používají rozšíření virtuálního počítače, a proto je poměrně jednoduchý na port přes stávající nasazení, které používají rozšíření virtuálního počítače.

Z těchto důvodů bude služba Azure IMDS zjevnou způsob, jak žádosti o tokeny po rozšíření virtuálního počítače je zastaralý. 


## <a name="next-steps"></a>Další kroky

* [Jak získat přístupový token pomocí spravované identity pro prostředky Azure na virtuálním počítači Azure](how-to-use-vm-token.md)
* [Azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

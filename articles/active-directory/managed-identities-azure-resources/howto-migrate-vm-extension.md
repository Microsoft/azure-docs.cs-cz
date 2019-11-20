---
title: Ukončení používání rozšíření spravovaného virtuálního počítače identity – Azure AD
description: Podrobné pokyny k zastavení používání rozšíření virtuálních počítačů a zahájení používání Azure Instance Metadata Service (IMDS) pro ověřování.
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
ms.openlocfilehash: 3440713c287967655678e1cde2c000a6ed28b900
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183955"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Jak ukončit používání rozšíření spravované identity virtuálních počítačů a začít používat Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Rozšíření virtuálního počítače pro spravované identity

Rozšíření virtuálního počítače pro spravované identity se používá k vyžádání tokenů pro spravovanou identitu v rámci virtuálního počítače. Pracovní postup se skládá z následujících kroků:

1. Nejprve zatížení v rámci prostředku zavolá `http://localhost/oauth2/token` místního koncového bodu, aby vyžádala přístupový token.
2. Rozšíření virtuálního počítače pak pomocí přihlašovacích údajů pro spravovanou identitu vyžádá přístupový token ze služby Azure AD. 
3. Přístupový token se vrátí volajícímu a dá se použít k ověření služeb, které podporují ověřování Azure AD, jako je Azure Key Vault nebo Azure Storage.

Kvůli několika omezením, které jsou uvedené v následující části, se rozšíření virtuálního počítače spravované identity už nepoužívá v rámci používání ekvivalentního koncového bodu v Azure Instance Metadata Service (IMDS).

### <a name="provision-the-extension"></a>Zřídit rozšíření 

Když nakonfigurujete virtuální počítač nebo sadu škálování virtuálního počítače tak, aby měly spravovanou identitu, můžete se případně rozhodnout zřídit rozšíření spravované identity pro prostředky Azure Resources pomocí parametru `-Type` v rutině [set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) . V závislosti na typu virtuálního počítače můžete předat buď `ManagedIdentityExtensionForWindows`, nebo `ManagedIdentityExtensionForLinux`a pojmenovat ho pomocí parametru `-Name`. Parametr `-Settings` Určuje port používaný koncovým bodem tokenu OAuth pro získání tokenu:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Pomocí šablony nasazení Azure Resource Manager můžete taky zřídit rozšíření virtuálního počítače tak, že do šablony přidáte následující JSON do části `resources` (použijte `ManagedIdentityExtensionForLinux` pro název a elementy typu pro verzi pro Linux).

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
    
    
Pokud pracujete se sadami škálování virtuálních počítačů, můžete taky zřídit spravované identity pro rozšíření Azure Resource Scale set pomocí rutiny [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) . V závislosti na typu sady škálování virtuálního počítače můžete předat buď `ManagedIdentityExtensionForWindows` nebo `ManagedIdentityExtensionForLinux`, a pojmenovat ho pomocí parametru `-Name`. Parametr `-Settings` Určuje port používaný koncovým bodem tokenu OAuth pro získání tokenu:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Pokud chcete pomocí šablony nasazení Azure Resource Manager zřídit rozšíření pro sadu škálování virtuálního počítače, přidejte následující JSON do části `extensionpProfile` do šablony (použijte `ManagedIdentityExtensionForLinux` pro název a prvky typu pro verzi pro Linux).

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

Zřizování rozšíření virtuálního počítače může selhat z důvodu selhání vyhledávání DNS. Pokud k tomu dojde, restartujte virtuální počítač a zkuste to znovu. 

### <a name="remove-the-extension"></a>Odebrání rozšíření 
Pokud chcete rozšíření odebrat, použijte `-n ManagedIdentityExtensionForWindows` nebo `-n ManagedIdentityExtensionForLinux` přepínač (v závislosti na typu virtuálního počítače) pomocí [AZ VM Extension Delete](https://docs.microsoft.com/cli/azure/vm/)nebo [AZ VMSS Extension Delete](https://docs.microsoft.com/cli/azure/vmss) pro Virtual Machine Scale Sets pomocí Azure CLI nebo `Remove-AzVMExtension` pro PowerShell:

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

Následuje ukázkový požadavek pomocí spravované identity pro koncový bod rozšíření virtuálních počítačů Azure Resources:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Prvek | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, který indikuje, že chcete načíst data z koncového bodu. V tomto případě se jedná o přístupový token OAuth. | 
| `http://localhost:50342/oauth2/token` | Spravované identity pro koncové body prostředků Azure, kde 50342 je výchozí port a dá se konfigurovat. |
| `resource` | Parametr řetězce dotazu, který označuje identifikátor URI ID aplikace cílového prostředku. Také se zobrazí v deklaraci identity `aud` (cílová skupina) vydaného tokenu. Tento příklad vyžaduje token pro přístup k Azure Resource Manager, která má identifikátor URI ID aplikace https://management.azure.com/. |
| `Metadata` | Pole hlavičky požadavku HTTP, které vyžadují spravované identity prostředků Azure jako zmírnění útoků na straně serveru (SSRF). Tato hodnota musí být nastavená na "true", a to u všech malých písmen.|
| `object_id` | Volitelné Parametr řetězce dotazu, který označuje object_id spravované identity, pro kterou chcete vytvořit token. Vyžaduje se, pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem.|
| `client_id` | Volitelné Parametr řetězce dotazu, který označuje client_id spravované identity, pro kterou chcete vytvořit token. Vyžaduje se, pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem.|


Ukázková odpověď:

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

| Prvek | Popis |
| ------- | ----------- |
| `access_token` | Požadovaný přístupový token Při volání zabezpečeného REST API se token vloží do pole hlavička žádosti `Authorization` jako "nosič", což umožňuje rozhraní API ověřit volajícího. | 
| `refresh_token` | Nepoužívá se spravovanými identitami pro prostředky Azure. |
| `expires_in` | Doba v sekundách, po kterou je přístupový token nadále platný, před vypršením platnosti, od doby vystavení. Čas vystavení najdete v deklaraci identity `iat` tokenu. |
| `expires_on` | Časový interval pro přístup k vypršení platnosti přístupového tokenu Datum se reprezentuje jako počet sekund od "1970-01-01T0:0: 0Z UTC" (odpovídá deklaraci identity `exp` tokenu). |
| `not_before` | Časové rozpětí, kdy se přístupový token projeví a lze jej přijmout. Datum se reprezentuje jako počet sekund od "1970-01-01T0:0: 0Z UTC" (odpovídá deklaraci identity `nbf` tokenu). |
| `resource` | Prostředek, pro který byl požadován přístupový token, který se shoduje s parametrem řetězce dotazu `resource` požadavku. |
| `token_type` | Typ tokenu, což je přístupový token "nosiče", což znamená, že prostředek může udělit přístup k nosiči tohoto tokenu. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Řešení potíží s rozšířením virtuálního počítače 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Po selhání restartovat rozšíření virtuálního počítače

Pokud se rozšíření zastaví na Windows a určitých verzích Linux, dá se k ručnímu restartování použít následující rutina:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název a typ rozšíření pro Windows je: `ManagedIdentityExtensionForWindows`
- Název a typ rozšíření pro Linux: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Při pokusu o export schématu pro spravované identity pro rozšíření prostředků Azure dojde k chybě skriptu služby Automation.

Pokud jsou na virtuálním počítači povolené spravované identity prostředků Azure, při pokusu o použití funkce skript Automation pro virtuální počítač nebo skupinu prostředků se zobrazí následující chyba:

![Spravované identity pro Azure Resources Automation – chyba exportu skriptu](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Spravované identity pro prostředky Azure pro rozšíření virtuálních počítačů momentálně nepodporují možnost exportu schématu do šablony skupiny prostředků. V důsledku toho vygenerovaná šablona nezobrazuje parametry konfigurace umožňující spravované identity prostředků Azure v prostředku. Tyto oddíly můžete přidat ručně podle příkladů v části [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablon](qs-configure-template-windows-vm.md).

Jakmile budou k dispozici funkce exportu schématu pro rozšíření virtuálních počítačů Azure pro prostředky Azure (plánované pro vyřazení do ledna 2019), bude se zobrazovat v části [Export skupin prostředků, které obsahují rozšíření virtuálních počítačů](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Omezení rozšíření virtuálního počítače 

Pro používání rozšíření virtuálního počítače je k dispozici několik hlavních omezení. 

 * Nejzávažnějším omezením je skutečnost, že přihlašovací údaje použité k vyžádání tokenů jsou uložené ve virtuálním počítači. Útočník, který úspěšně narušuje virtuální počítač, může exfiltrovat přihlašovací údaje. 
 * Kromě toho se rozšíření virtuálních počítačů stále nepodporuje u několika distribucí systému Linux, přičemž náklady na vývoj můžete upravit, sestavit a otestovat v každé z těchto distribucí. V současné době jsou podporovány pouze následující distribuce systému Linux: 
    * CoreOS stabilní
    * CentOS 7.1 
    * Red Hat 7,2 
    * Ubuntu 15.04 
    * Ubuntu 16.04
 * Nasazení virtuálních počítačů se spravovanými identitami má dopad na výkon, protože je taky potřeba zřídit rozšíření virtuálního počítače. 
 * Nakonec může rozšíření virtuálního počítače podporovat jenom 32 uživatelem přiřazených spravovaných identit na virtuální počítač. 

## <a name="azure-instance-metadata-service"></a>Instance Metadata Service Azure

[Azure instance metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) je koncový bod REST, který poskytuje informace o spuštěných instancích virtuálních počítačů, které se dají použít ke správě a konfiguraci virtuálních počítačů. Koncový bod je k dispozici na dobře známé IP adrese, která není směrovatelný (`169.254.169.254`), ke které se dá získat přístup jenom z virtuálního počítače.

Použití Azure IMDS k vyžádání tokenů má několik výhod. 

1. Služba je pro virtuální počítač externě, takže přihlašovací údaje používané spravovanými identitami již nejsou na virtuálním počítači přítomny. Místo toho jsou hostované a zabezpečené na hostitelském počítači virtuálního počítače Azure.   
2. Všechny operační systémy Windows a Linux podporované v Azure IaaS můžou používat spravované identity.
3. Nasazení je rychlejší a jednodušší, protože rozšíření virtuálního počítače už není potřeba zřizovat.
4. Pomocí koncového bodu IMDS je možné přiřadit k jednomu virtuálnímu počítači až 1000 spravovaných identit přiřazených uživateli.
5. Neexistují žádné významné změny požadavků, které používají IMDS, na rozdíl od těch, které používají rozšíření virtuálního počítače. proto je poměrně jednoduché přenášet přes existující nasazení, která aktuálně používají rozšíření virtuálního počítače.

Z těchto důvodů bude služba Azure IMDS ze seznamu odebraná způsob, jak vyžádat tokeny, jakmile je rozšíření virtuálního počítače zastaralé. 


## <a name="next-steps"></a>Další kroky

* [Použití spravovaných identit pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu](how-to-use-vm-token.md)
* [Instance Metadata Service Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

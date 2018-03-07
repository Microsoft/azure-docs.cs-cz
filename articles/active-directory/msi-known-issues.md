---
title: "Nejčastější dotazy a známé problémy s spravované služby Identity (MSI) pro Azure Active Directory"
description: "Známé problémy s spravované identita služby pro Azure Active Directory."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: c8b25082170eb03c1ebc5965e273868982a3846f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Nejčastější dotazy a známé problémy s spravované služby Identity (MSI) pro Azure Active Directory

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

### <a name="is-there-a-private-preview-available-for-additional-features"></a>Je privátní Preview verzi k dispozici pro další funkce?

Ano. Pokud chcete, aby byla považována za pro registraci v privátní Preview verzi [najdete na naší stránce registrace](https://aka.ms/azuremsiprivatepreview).

### <a name="does-msi-work-with-azure-cloud-services"></a>Funguje s Azure Cloud Services MSI?

Ne, nejsou žádné plány pro podporu MSI v Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Funguje s Active Directory Authentication Library (ADAL) nebo knihovny ověřování společnosti Microsoft (MSAL) MSI?

Ne, není s ADAL nebo MSAL ještě integrovaná MSI. Podrobnosti o získávání tokenu MSI používá koncový bod MSI REST najdete v tématu [jak používat Azure virtuálního počítače spravované služby Identity (MSI) pro získání tokenu](msi-how-to-use-vm-msi-token.md).

### <a name="what-are-the-supported-linux-distributions"></a>Jaké jsou podporované distribuce systému Linux?

Následující Linuxových distribucích podporují MSI: 

- Stabilní jádro operačního systému
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Aktuálně nejsou podporované jiných Linuxových distribucích a rozšíření se nemusí podařit na nepodporované distribuce.

Rozšíření na CentOS 6.9 funguje. Však z důvodu nedostatku systémové podpory v 6.9 rozšíření nebude automatické restartování Pokud došlo k chybě nebo byla zastavena. Restartuje po restartování virtuálního počítače. Rozšíření restartovat ručně, najdete v tématu [jak je restartovat příponou MSI?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Jak je restartovat příponou MSI?
V systému Windows a některé verze systému Linux Pokud rozšíření zastaví, následující rutiny lze ho restartovat ručně:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název rozšíření pro Windows je zadání: ManagedIdentityExtensionForWindows
- Název rozšíření a typ pro Linux: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Známé problémy

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>"Skriptu pro automatizaci" selhání při pokusu o export schématu pro rozšíření MSI

Pokud identita spravované služby je povoleno na virtuálním počítači, se zobrazí následující chyba, při pokusu o použití funkce "Skript automatizace" pro virtuální počítač nebo jeho skupin prostředků:

![Chyba při exportu MSI automation skriptu](media/msi-known-issues/automation-script-export-error.png)

Rozšíření virtuálního počítače identita spravované služby v současné době nepodporuje možnost jeho schéma exportu do šablony skupiny prostředků. V důsledku toho vygenerované šablony nezobrazuje parametry konfigurace umožňující spravovat Identity služby u daného prostředku. Tyto části můžete přidat ručně pomocí následujících příkladech v [konfigurace Identity služby virtuálních počítačů spravovaných pomocí šablony](msi-qs-configure-template-windows-vm.md).

Když funkce exportu schématu je k dispozici pro rozšíření virtuálního počítače MSI, objeví se v [export skupiny prostředků, které obsahují rozšíření virtuálního počítače](../virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Okno Konfigurace se nezobrazí na portálu Azure

Pokud v okně Konfigurace virtuálního počítače se nezobrazí na vašem virtuálním počítači, pak MSI není povolená na portálu ve vašem regionu ještě.  Zkuste to znovu později.  Můžete také povolit MSI pro virtuální počítač pomocí [prostředí PowerShell](msi-qs-configure-powershell-windows-vm.md) nebo [rozhraní příkazového řádku Azure](msi-qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Nelze přiřadit přístup k virtuálním počítačům v okně řízení přístupu (IAM)

Pokud **virtuálního počítače** nezobrazí na portálu Azure jako volba pro **přiřadit přístup** v **řízení přístupu (IAM)** > **přidat oprávnění**, pak identita spravované služby nebyla ještě na portálu ve vašem regionu povolena. Zkuste to znovu později.  Identita spravované služby pro přiřazení role stále vyberete vyhledávání pro objekt služby MSI.  Zadejte název virtuálního počítače v **vyberte** pole a objektu služby se zobrazí ve výsledcích hledání.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuální počítač se nepodaří spustit po přesunutí ze skupiny prostředků nebo předplatného

Pokud přesunete virtuální počítač v běžícím stavu, pokračuje v činnosti během přesunu. Ale po přesunu, pokud virtuální počítač je zastavena a restartována, ho nebude možné spustit. Tento problém se stane, protože virtuální počítač není aktualizován odkaz na identitě MSI a pokračuje tak, aby odkazoval na ni ve staré skupině prostředků.

**Alternativní řešení** 
 
Spustíte aktualizaci na virtuální počítač, takže ho můžete získat správné hodnoty pro soubor MSI. Můžete to udělat změnu vlastnosti virtuálního počítače aktualizovat odkaz na identitě MSI. Například můžete nastavit novou hodnotu značky na virtuální počítač pomocí následujícího příkazu:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Tento příkaz nastaví novou značku "fixVM" s hodnotou 1 na virtuálním počítači. 
 
Nastavením této vlastnosti virtuálního počítače aktualizuje správný identifikátor URI prostředku MSI a pak musí být možné spustit virtuální počítač. 
 
Po spuštění virtuálního počítače značky lze odebrat pomocí následující příkaz:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

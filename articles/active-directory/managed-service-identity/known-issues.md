---
title: Nejčastější dotazy a známé problémy s spravované služby Identity (MSI) pro Azure Active Directory
description: Známé problémy s spravované identita služby pro Azure Active Directory.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: f643b1203283d7aac01aa8821a180c898f33ec25
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Nejčastější dotazy a známé problémy s spravované služby Identity (MSI) pro Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

### <a name="does-msi-work-with-azure-cloud-services"></a>Funguje s Azure Cloud Services MSI?

Ne, nejsou žádné plány pro podporu MSI v Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Funguje s Active Directory Authentication Library (ADAL) nebo knihovny ověřování společnosti Microsoft (MSAL) MSI?

Ne, není s ADAL nebo MSAL ještě integrovaná MSI. Podrobnosti o získávání tokenu MSI používá koncový bod MSI REST najdete v tématu [jak používat Azure virtuálního počítače spravované služby Identity (MSI) pro získání tokenu](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>Co je hranicí zabezpečení Identita spravované služby?

Prostředek, ke kterému je připojen k je hranicí zabezpečení identity. Například hranice zabezpečení u MSI virtuální počítač je virtuální počítač. Všechny kód spuštěný na tento virtuální počítač, je možné volat koncový bod MSI a žádosti o tokeny. Je podobné prostředí s jiným prostředkům, které podporují MSI.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>Použít koncový bod virtuálního počítače IMDS MSI nebo koncový bod virtuálního počítače MSI rozšíření?

Při použití Instalační služby MSI s virtuálními počítači, doporučujeme pomocí Instalační služby MSI IMDS koncový bod. Služba Azure Instance metadat je koncový bod REST přístupné pro všechny virtuální počítače IaaS vytvořit prostřednictvím Správce Azure Resource Manager. Některé z výhod pomocí Instalační služby MSI přes IMDS jsou:

1. Všechny operační systémy podporované Azure IaaS můžete použít instalační služby MSI přes IMDS. 
2. Už nemusíte instalovat rozšíření na vašem virtuálním počítači povolit MSI. 
3. Certifikáty používané MSI již nejsou ve virtuálním počítači. 
4. Koncový bod IMDS je dobře známé směrovat IP adresu, k dispozici pouze z virtuálního počítače. 

Rozšíření virtuálního počítače MSI je stále dostupné k použití dnes; ale postoupíte jsme bude použita výchozí pomocí IMDS koncový bod. Rozšíření virtuálního počítače MSI se spustí na vyřazení plánu brzy. 

Další informace o Azure Instance Metada Service najdete v tématu [IMDS dokumentace](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Jaké jsou podporované distribuce systému Linux?

Všechny Linuxových distribucích nepodporuje Azure IaaS můžete použít s MSI přes IMDS koncový bod. 

Poznámka: Rozšíření virtuálního počítače MSI se podporuje jenom následující distribucí Linux:
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

![Chyba při exportu MSI automation skriptu](../media/msi-known-issues/automation-script-export-error.png)

Rozšíření virtuálního počítače identita spravované služby v současné době nepodporuje možnost jeho schéma exportu do šablony skupiny prostředků. V důsledku toho vygenerované šablony nezobrazuje parametry konfigurace umožňující spravovat Identity služby u daného prostředku. Tyto části můžete přidat ručně pomocí následujících příkladech v [konfigurace Identity služby virtuálních počítačů spravovaných pomocí šablony](qs-configure-template-windows-vm.md).

Když funkce exportu schématu je k dispozici pro rozšíření virtuálního počítače MSI, objeví se v [export skupiny prostředků, které obsahují rozšíření virtuálního počítače](../../virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Okno Konfigurace se nezobrazí na portálu Azure

Pokud v okně Konfigurace virtuálního počítače se nezobrazí na vašem virtuálním počítači, pak MSI není povolená na portálu ve vašem regionu ještě.  Zkuste to znovu později.  Můžete také povolit MSI pro virtuální počítač pomocí [prostředí PowerShell](qs-configure-powershell-windows-vm.md) nebo [rozhraní příkazového řádku Azure](qs-configure-cli-windows-vm.md).

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

## <a name="known-issues-with-user-assigned-identities"></a>Známé problémy s přiřazené identity uživatelů

- Přiřazení uživatelských přiřazené Identity jsou jenom zohledněním pro virtuální počítač a VMSS. Důležité: Identita uživatele přiřazené přiřazení se změní v nadcházejících měsících.
- Duplicitní přiřazené identit uživatelů ve stejném virtuálního počítače nebo VMSS, způsobí, že virtuální počítač nebo VMSS selhání. To zahrnuje identity, které jsou přidány s jinou velká a malá písmena. například MyUserAssignedIdentity a myuserassignedidentity. 
- Zřizování rozšíření virtuálního počítače pro virtuální počítač mohou selhat z důvodu selhání vyhledávání DNS. Restartujte virtuální počítač a zkuste to znovu. 
- Přidání identity uživatele 'neexistující' přiřazena způsobí selhání virtuálního počítače. 
- Vytvoření uživatele přiřazené identity s speciální znaky (tj. podtržítko) v názvu, není podporováno.
- Uživatel s přiřazenou identity názvy jsou omezeny na 24 znaků pro komplexní scénáře. Pro přiřazení se nezdaří přiřazeno identit uživatelů s názvy delší než 24 znaků.  
- Při přidání druhého uživatele přiřazené identity, clientID nemusí být dostupné pro žádosti o tokeny pro rozšíření virtuálního počítače. Jako omezení rizik restartujte rozšíření virtuálního počítače MSI následující dvě bash příkazy:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Když virtuální počítač má uživatel přiřazené identity, ale žádný systém přiřazené identity, na portálu uživatelské rozhraní zobrazí MSI jako zakázané. Systém přiřazené identity povolit, použijte šablonu Azure Resource Manager, Azure CLI nebo sady SDK.

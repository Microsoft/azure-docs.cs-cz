---
title: Nejčastější dotazy a známé problémy s Identity spravované služby (MSI) pro Azure Active Directory
description: Známé problémy s identita spravované služby pro Azure Active Directory.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: c48d03b6e8a3d850d02d2c36c35915f8214b00e8
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035808"
---
# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Nejčastější dotazy a známé problémy s Identity spravované služby (MSI) pro Azure Active Directory

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

### <a name="does-msi-work-with-azure-cloud-services"></a>Funguje s Azure Cloud Services MSI?

Ne, nejsou žádné plány pro podporu MSI ve službě Azure Cloud Services.

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Funguje s Active Directory Authentication Library (ADAL) nebo Microsoft Authentication Library (MSAL) MSI?

Ne, MSI není zatím integrované s ADAL nebo MSAL. Podrobnosti o získávání tokenu MSI pomocí koncového bodu MSI REST najdete v tématu [použití Azure VM Identity spravované služby (MSI) pro získání tokenu](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-a-managed-service-identity"></a>Co je hranice zabezpečení Identita spravované služby?

Hranice zabezpečení identity je prostředek, ke kterému je připojený. Například hranice zabezpečení pro virtuální počítač MSI, je virtuální počítač. Jakýkoli kód spuštěn na tomto virtuálním počítači, je možné volat koncový bod MSI a požádat o tokeny. Je podobné prostředí s další prostředky, které podporují MSI.

### <a name="should-i-use-the-msi-vm-imds-endpoint-or-the-msi-vm-extension-endpoint"></a>Můžu použít koncový bod IMDS MSI virtuálního počítače nebo koncový bod rozšíření MSI virtuálního počítače?

Pokud používáte MSI virtuálních počítačů, doporučujeme pomocí MSI IMDS koncového bodu. Služba Azure Instance Metadata je koncový bod REST, která je přístupná pro všechny virtuální počítače IaaS vytvořené prostřednictvím Azure Resource Manageru. Zde jsou některé z výhod pomocí MSI přes IMDS:

1. Všechny operační systémy podporované IaaS s nástrojem Azure můžete použít MSI přes IMDS. 
2. Už nemusíte instalovat rozšíření na vašem virtuálním počítači povolit MSI. 
3. Certifikáty používané službou MSI už nejsou k dispozici ve virtuálním počítači. 
4. Koncový bod IMDS je dobře známé nesměrovatelných IP adresu, k dispozici pouze z v rámci virtuálního počítače. 

Rozšíření MSI virtuálního počítače je stále k dispozici pro použití dnes; ale v budoucnu jsme se ve výchozím nastavení pomocí IMDS koncového bodu. Rozšíření MSI virtuálního počítače se spustí na plánu vyřazení brzy. 

Další informace o službě Metada Instance Azure, najdete v části [IMDS dokumentace](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="what-are-the-supported-linux-distributions"></a>Jaké jsou podporované distribuce systému Linux?

Všechny Linuxových distribucí, které podporuje Azure IaaS je možné pomocí MSI přes koncový bod IMDS. 

Poznámka: Rozšíření MSI virtuálního počítače se podporuje pouze následující distribucí systému Linux:
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

Ostatní Linuxové distribuce nejsou aktuálně podporovány a rozšíření může selhat v nepodporované distribucích.

Toto rozšíření funguje na CentOS 6.9. Nicméně z důvodu nedostatku systémové podpory v 6.9 rozšíření nespustí automaticky restartování Pokud došlo k chybě nebo zastavená. Restartuje po restartování virtuálního počítače. Rozšíření restartovat ručně, najdete v článku [jak je restartovat rozšíření MSI?](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>Jak restartujete rozšíření MSI
V některých verzích systému Linux a Windows Pokud rozšíření zastaví, následující rutiny můžou sloužit k ho restartovat ručně:

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název rozšíření a typ pro Windows je: ManagedIdentityExtensionForWindows
- Název rozšíření a typ pro Linux je: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Známé problémy

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>"Automatizační skript" selhání při pokusu o export schématu pro rozšíření MSI

Pokud identita spravované služby je povoleno na virtuálním počítači, se zobrazí následující chyba při pokusu o použití funkce "Skript automatizace" pro virtuální počítač nebo jeho skupina prostředků:

![Automatizační skript exportu došlo k chybě MSI](../managed-service-identity/media/msi-known-issues/automation-script-export-error.png)

Rozšíření virtuálního počítače identitu spravované služby aktuálně nepodporuje možnost exportu schématem pro šablonu skupiny prostředků. V důsledku toho vygenerovanou šablonu nezobrazuje parametry konfigurace se povolit identitu spravované služby u daného prostředku. Tyto části můžete přidat ručně podle příkladů v [konfigurace virtuálních počítačů spravovaná identita služby s použitím šablony](qs-configure-template-windows-vm.md).

Když funkce exportu schématu je k dispozici pro rozšíření MSI virtuálního počítače, objeví se v [export skupiny prostředků, obsahující rozšíření virtuálních počítačů](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Na webu Azure Portal se nezobrazí okno Konfigurace

Pokud v okně Konfigurace virtuálního počítače není na vašem virtuálním počítači, pak MSI nebylo povoleno na portálu ve vaší oblasti ještě.  Zkuste to znovu později.  Můžete také povolit MSI pro virtuální počítač pomocí [PowerShell](qs-configure-powershell-windows-vm.md) nebo [rozhraní příkazového řádku Azure](qs-configure-cli-windows-vm.md).

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>Nelze přiřadit přístup k virtuálním počítačům v okně řízení přístupu (IAM)

Pokud **virtuálního počítače** nezobrazí na portálu Azure portal jako volba pro **přiřadit přístup k** v **řízení přístupu (IAM)** > **přidat oprávnění**, pak se identita spravované služby na portálu ve vaší oblasti ještě nepovolil. Zkuste to znovu později.  Identita spravované služby pro přiřazení role můžete vybrat tak, že pro instanční objekt služby MSI.  Zadejte název virtuálního počítače v **vyberte** pole a objektu zabezpečení se zobrazí ve výsledcích hledání.

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuální počítač se nepodaří spustit po přesunutí ze skupiny prostředků nebo předplatného

Při přesunutí virtuálního počítače v běžícím stavu, pokračuje se spustí během přechodu. Ale po přesunutí, pokud je virtuální počítač zastavit, restartovat, dojde k selhání spuštění. Tento problém nastane, protože virtuální počítač není aktualizace odkazu na identitu MSI a přejděte do původní skupiny prostředků i nadále.

**Alternativní řešení** 
 
Aktualizace na virtuálním počítači aktivujte, takže ho můžete získat správné hodnoty pro soubor MSI. Můžete provést změnu vlastnosti virtuálního počítače aktualizovat odkaz na identitu MSI. Například můžete nastavit hodnotu nové značky na virtuálním počítači pomocí následujícího příkazu:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Tento příkaz nastaví nová značka "fixVM" s hodnotou 1 na virtuálním počítači. 
 
Nastavením této vlastnosti virtuálního počítače aktualizuje správný identifikátor URI prostředku MSI a pak by měl být schopni spustit virtuální počítač. 
 
Po spuštění virtuálního počítače značku je možné odebrat pomocí následující příkaz:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-identities"></a>Známé problémy s přiřazené identity uživatele

- Přiřazení Identity přiřazené uživateli jsou pouze dostupné pro virtuální počítač a VMSS. Důležité: Přiřazení Identity přiřazené uživatele se změní v nadcházejících měsících.
- Duplicitní přiřazené identity uživatelů u stejného počítače/škálovací sady, způsobí, že VM/VMSS selhání. Jedná se o identitách, které jsou přidány s jinou velikostí písmen. například MyUserAssignedIdentity a myuserassignedidentity. 
- Zřizování rozšíření virtuálního počítače k virtuálnímu počítači může selhat z důvodu chyby vyhledávání DNS. Restartujte virtuální počítač a zkuste to znovu. 
- Přidání identity přiřazené uživateli 'neexistuje' způsobí, že virtuální počítač selže. 
- Vytvoření uživatele identity se speciálními znaky (třeba podtržítko) v názvu přiřazené, se nepodporuje.
- Názvy identity přiřazené uživateli jsou omezeny na 24 znaků pro komplexní scénáře. Přiřazení se nezdaří přiřazeno identit uživatelů s názvy delší než 24 znaků.
- Pokud používáte rozšíření virtuálního počítače spravovanou identitu, je podporovaný limit 32 spravovaných identit přiřazených uživateli. Bez přípon spravovanou identitu virtuálního počítače je podporovaný limit 512.  
- Po přidání druhého uživatele přiřazení identity clientID nemusí být k dispozici na žádosti o tokeny pro rozšíření virtuálního počítače. Jako omezení rizik restartujte rozšíření MSI virtuálního počítače pomocí následujících příkazů prostředí bash dvě:
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Pokud má virtuální počítač identity přiřazené uživateli, ale žádné identitu přiřazenou systémem, uživatelské rozhraní vám portál zobrazí MSI jako zakázané. Pokud chcete povolit identitu přiřazenou systémem, pomocí šablony Azure Resource Manageru, Azure CLI nebo sady SDK.

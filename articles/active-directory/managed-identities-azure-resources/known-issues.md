---
title: Časté otázky a známé problémy se spravovanými identitami – Azure AD
description: Známé problémy se spravovanými identitami pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266530"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Časté otázky a známé problémy se spravovanými identitami pro prostředky Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy (ČASTÉ OTÁZKY)

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Fungují spravované identity pro prostředky Azure s Cloudovými službami Azure?

Ne, neexistují žádné plány na podporu spravovaných identit pro prostředky Azure v Cloudservices Azure.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Fungují spravované identity pro prostředky Azure s knihovnou ověřování služby Active Directory (ADAL) nebo s knihovnou Ověřování Microsoft (MSAL)?

Ne, spravované identity pro prostředky Azure ještě nejsou integrované s ADAL nebo MSAL. Podrobnosti o získání tokenu pro spravované identity pro prostředky Azure pomocí koncového bodu REST najdete v tématu [Jak používat spravované identity pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaká je hranice zabezpečení spravovaných identit pro prostředky Azure?

Hranice zabezpečení identity je prostředek, ke kterému je připojen. Například hranice zabezpečení pro virtuální počítač s povolenými spravovanými identitami pro prostředky Azure je virtuální počítač. Jakýkoli kód spuštěný na tomto virtuálním počítači, je možné volat spravované identity pro koncový bod prostředků Azure a požadavky na tokeny. Je to podobné prostředí s jinými prostředky, které podporují spravované identity pro prostředky Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jakou identitu bude imds výchozí, pokud nezadá identitu v požadavku?

- Pokud je povolena spravovaná identita přiřazená systémem a v požadavku není zadána žádná identita, služba IMDS bude ve výchozím nastavení nastavena na systém přiřazenou spravovanou identitu.
- Pokud systém přiřazená spravovaná identita není povolena a existuje pouze jeden uživatel přiřazený spravovanou identitu, služba IMDS bude ve výchozím nastavení nastavena na tuto spravovanou identitu přiřazenou jedním uživatelem. 
- Pokud systém přiřazená spravovaná identita není povolena a existuje více uživatelů přiřazených spravovaných identit, je vyžadováno zadání spravované identity v požadavku.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Mám použít spravované identity pro koncový bod IMDS prostředků Azure nebo koncový bod rozšíření virtuálního počítače?

Když používáte spravované identity pro prostředky Azure s virtuálními počítači, doporučujeme použít koncový bod IMDS. Služba metadat instance Azure je koncový bod REST přístupný všem virtuálním počítačům IaaS vytvořeným prostřednictvím Správce prostředků Azure. 

Některé výhody používání spravovaných identit pro prostředky Azure přes IMDS jsou:
- Všechny operační systémy podporované Azure IaaS můžou používat spravované identity pro prostředky Azure přes IMDS.
- K povolení spravovaných identit pro prostředky Azure už není potřeba instalovat rozšíření na virtuální počítač. 
- Certifikáty používané spravovanými identitami pro prostředky Azure už ve virtuálním počítači nejsou k dispozici.
- Koncový bod IMDS je dobře známá nesměrovatelná IP adresa, která je dostupná pouze z virtuálního počítačů.
- 1000 uživatelem přiřazené spravované identity lze přiřadit k jednomu virtuálnímu virtuálnímu jevu. 

Spravované identity pro rozšíření virtuálních zařízení Azure prostředky je stále k dispozici; však již nevyvíjíme nové funkce na něm. Doporučujeme přepnout na použití koncového bodu IMDS. 

Některá omezení použití koncového bodu rozšíření virtuálního aplikace jsou:
- Omezená podpora pro distribuce Linuxu: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
- K virtuálnímu virtuálnímu soudu lze přiřadit jenom 32 spravovaných identit přiřazených uživatelům.


Poznámka: Spravované identity pro rozšíření virtuálních her azure prostředků bude mimo podporu v lednu 2019. 

Další informace o službě metadat instanci Azure najdete v [dokumentaci k IMDS.](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Budou spravované identity automaticky znovu vytvořeny, pokud přesunem předplatné do jiného adresáře?

Ne. Pokud přesunete předplatné do jiného adresáře, budete muset ručně znovu vytvořit a udělit přiřazení rolí Azure RBAC znovu.
- Pro systém přiřazené spravované identity: zakázat a znovu povolit. 
- Pro uživatele přiřazené spravované identity: odstranit, znovu vytvořit a znovu připojit k potřebným prostředkům (např. virtuální počítače)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Je možné použít spravovanou identitu pro přístup k prostředku v jiném adresáři/tenantovi?

Ne. Spravované identity aktuálně nepodporují scénáře mezi adresáři. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jaká oprávnění Azure RBAC jsou vyžadována pro spravovanou identitu na prostředku? 

- Spravovaná identita přiřazená systémem: Potřebujete oprávnění k zápisu přes prostředek. Například v případě virtuálních počítačů potřebujete oprávnění Microsoft.Compute/virtualMachines/write. Tato akce je součástí předdefinovaných rolí specifických pro prostředky, jako je [přispěvatel virtuálního počítače](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Uživatelem přiřazená spravovaná identita: Potřebujete oprávnění k zápisu přes prostředek. Například v případě virtuálních počítačů potřebujete oprávnění Microsoft.Compute/virtualMachines/write. Kromě přiřazení role [spravovaného operátoru identity](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) přes spravovanou identitu.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Jak restartujete spravované identity pro rozšíření prostředků Azure?
V systému Windows a některých verzích Linuxu, pokud se rozšíření zastaví, může být k ručnímu restartování použita následující rutina:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název a typ rozšíření systému Windows je: ManagedIdentityExtensionForWindows
- Název a typ rozšíření pro Linux je: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Známé problémy

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Automatizační skript" se nezdaří při pokusu o export schématu pro spravované identity pro rozšíření prostředků Azure

Když spravované identity pro prostředky Azure je povolena na virtuálním počítači, zobrazí se následující chyba při pokusu o použití funkce "Automatizační skript" pro virtuální počítač nebo jeho skupinu prostředků:

![Chyby exportu skriptu pro spravované identity pro automatizační skripty azure](./media/msi-known-issues/automation-script-export-error.png)

Spravované identity pro rozšíření virtuálních zařízení azure prostředků (plánované pro vyřazení v lednu 2019) aktuálně nepodporuje možnost exportu jeho schéma do šablony skupiny prostředků. V důsledku toho vygenerovaná šablona nezobrazuje parametry konfigurace, které by umožnily spravované identity pro prostředky Azure v prostředku. Tyto oddíly lze přidat ručně podle příkladů v [konfigurovat spravované identity pro prostředky Azure na virtuálním počítači Azure pomocí šablon](qs-configure-template-windows-vm.md).

Když bude funkce exportu schématu dostupná pro spravované identity pro rozšíření virtuálního počítače azure prostředků (plánované pro vyřazení v lednu 2019), bude uvedená v [exportu skupin prostředků, které obsahují rozšíření virtuálních účtů](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Virtuální ms se nepodařilo spustit po přesunutí ze skupiny prostředků nebo předplatného

Pokud přesunete virtuální ho ve spuštěném stavu, bude nadále spouštět během přesunu. Však po přesunutí, pokud je virtuální hod zastavena a restartována, se nezdaří spustit. K tomuto problému dochází, protože virtuální počítač není aktualizace odkazu na spravované identity pro identitu prostředků Azure a nadále odkazovat na něj ve staré skupině prostředků.

**Řešení** 
 
Aktivuj te aktualizaci na virtuálním počítači, aby mohl získat správné hodnoty pro spravované identity pro prostředky Azure. Můžete provést změnu vlastnosti virtuálního počítače a aktualizovat odkaz na spravované identity pro identitu prostředků Azure. Můžete například nastavit novou hodnotu značky na virtuálním počítači pomocí následujícího příkazu:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Tento příkaz nastaví novou značku "fixVM" s hodnotou 1 na virtuálním počítači. 
 
Nastavením této vlastnosti se virtuální počítač aktualizuje se správnými spravovanými identitami pro identifikátor URI prostředků prostředků Azure a pak byste měli být schopni spustit virtuální počítač. 
 
Po spuštění virtuálního virtuálního aplikace lze značku odebrat pomocí následujícího příkazu:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Zřizování rozšíření virtuálního míse se nezdaří

Zřizování rozšíření virtuálního virtuálního serveru může selhat z důvodu selhání vyhledávání DNS. Restartujte virtuální počítač a akci opakujte.
 
> [!NOTE]
> Rozšíření virtuálního provozu je plánováno na vyřazení do ledna 2019. Doporučujeme přejít k použití koncového bodu IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Přenos předplatného mezi adresáři Azure AD

Spravované identity se neaktualizují při přesunutí nebo přenosu předplatného do jiného adresáře. V důsledku toho budou poškozeny všechny existující spravované identity přiřazené systémem nebo uživatelem. 

Řešení pro spravované identity v předplatném, které bylo přesunuto do jiného adresáře:

 - Pro systém přiřazené spravované identity: zakázat a znovu povolit. 
 - Pro uživatele přiřazené spravované identity: odstranit, znovu vytvořit a znovu připojit k potřebným prostředkům (např. virtuální počítače)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Přesunutí spravované identity přiřazené uživateli do jiné skupiny prostředků/předplatného

Přesunutí spravované identity přiřazené uživateli do jiné skupiny prostředků způsobí, že se identita přeruší. V důsledku toho prostředky (např. virtuální ms) pomocí této identity nebude moci požadovat tokeny pro něj. 

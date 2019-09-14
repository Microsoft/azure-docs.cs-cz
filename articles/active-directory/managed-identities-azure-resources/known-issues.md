---
title: Nejčastější dotazy a známé problémy se spravovanými identitami pro prostředky Azure
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
ms.openlocfilehash: 8d882b34bc4f057035a16b7916249cfe8f0b8d0b
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983435"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Nejčastější dotazy a známé problémy se spravovanými identitami pro prostředky Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Pracují spravované identity prostředků Azure se službou Azure Cloud Services?

Ne, neexistují žádné plány na podporu spravovaných identit pro prostředky Azure v Azure Cloud Services.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Pracují spravované identity prostředků Azure pomocí Active Directory Authentication Library (ADAL) nebo knihovny Microsoft Authentication Library (MSAL)?

Ne, spravované identity pro prostředky Azure ještě nejsou integrované s ADAL nebo MSAL. Podrobnosti o získání tokenu pro spravované identity pro prostředky Azure pomocí koncového bodu REST najdete v tématu [Jak používat spravované identity pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaká je hranice zabezpečení spravovaných identit pro prostředky Azure?

Hranice zabezpečení identity je prostředek, ke kterému je připojen. Například hranice zabezpečení pro virtuální počítač se spravovanými identitami pro prostředky Azure je virtuální počítač. Jakýkoli kód běžící na tomto virtuálním počítači může volat spravované identity pro koncový bod prostředků Azure a žádat tokeny. Podobně jako u jiných prostředků, které podporují spravované identity prostředků Azure, se jedná o podobné prostředí.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jakou identitu bude IMDS výchozí, pokud nezadáte identitu v žádosti?

- Pokud je povolená spravovaná identita přiřazená systémem a v požadavku není zadaná žádná identita, IMDS se použije jako výchozí pro spravovanou identitu přiřazenou systémem.
- Pokud není povolená spravovaná identita přiřazená systémem a existuje jenom jedna spravovaná identita přiřazená uživatelem, IMDS se použije jako výchozí pro tento jediný uživatel přiřazenou spravovanou identitu. 
- Pokud není povolená spravovaná identita přiřazená systémem a existuje víc spravovaných identit přiřazených uživateli, pak se vyžaduje určení spravované identity v žádosti.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Mám použít spravované identity pro Azure Resources IMDS nebo koncový bod rozšíření virtuálních počítačů?

Pokud používáte spravované identity pro prostředky Azure s virtuálními počítači, doporučujeme použít koncový bod IMDS. Azure Instance Metadata Service je koncový bod REST dostupný všem virtuálním počítačům s IaaS vytvořeným prostřednictvím Azure Resource Manager. 

Mezi výhody použití spravovaných identit pro prostředky Azure přes IMDS patří:
- Všechny podporované operační systémy Azure IaaS můžou používat spravované identity pro prostředky Azure prostřednictvím IMDS.
- Už nemusíte instalovat rozšíření na VIRTUÁLNÍm počítači, aby se povolily spravované identity pro prostředky Azure. 
- Certifikáty používané spravovanými identitami pro prostředky Azure už ve virtuálním počítači nejsou.
- Koncový bod IMDS je dobře známá IP adresa, která není směrovatelný, dostupná jenom v rámci virtuálního počítače.
- k jednomu virtuálnímu počítači se dají přiřadit 1000 uživatelsky přiřazených spravovaných identit. 

Rozšíření spravované identity pro prostředky Azure Resources je stále k dispozici; už ale nevyvíjíme nové funkce. Doporučujeme, abyste přepnuli na použití koncového bodu IMDS. 

Některá omezení používání koncového bodu rozšíření virtuálních počítačů jsou:
- Omezená podpora distribucí pro Linux: CoreOS stabilní, CentOS 7,1, Red Hat 7,2, Ubuntu 15,04, Ubuntu 16,04
- K virtuálnímu počítači se dají přiřadit jenom 32 spravované identity přiřazené uživateli.


Poznámka: Rozšíření spravované identity pro prostředky Azure Resources v lednu 2019 nebude podporovat. 

Další informace o Azure Instance Metadata Service najdete v [dokumentaci k IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service) .

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Budou spravované identity znovu automaticky vytvořeny při přesunu předplatného do jiného adresáře?

Ne. Pokud přesunete předplatné do jiného adresáře, budete ho muset ručně znovu vytvořit a znovu udělit přiřazení role Azure RBAC.
- Pro spravované identity přiřazené systémem: zakažte a znovu povolte. 
- Pro spravované identity přiřazené uživateli: Odstraňte, znovu ho vytvořte a znovu připojte k potřebným prostředkům (např. virtuální počítače).

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Můžu použít spravovanou identitu pro přístup k prostředkům v jiném adresáři nebo tenantovi?

Ne. Spravované identity v současné době nepodporují scénáře pro více adresářů. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jaká oprávnění Azure RBAC jsou vyžadována pro spravovanou identitu prostředku? 

- Spravovaná identita přiřazená systémem: K prostředku budete potřebovat oprávnění k zápisu. Například pro virtuální počítače potřebujete Microsoft. COMPUTE/virtualMachines/Write. Tato akce je zahrnutá v předdefinovaných rolích specifických pro prostředky, jako je [Přispěvatel virtuálních počítačů](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Spravovaná identita přiřazená uživatelem: K prostředku budete potřebovat oprávnění k zápisu. Například pro virtuální počítače potřebujete Microsoft. COMPUTE/virtualMachines/Write. Kromě spravované identity se přiřazení role [operátora identity](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) .

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Jak restartujete spravované identity pro rozšíření prostředků Azure?
Pokud se rozšíření zastaví na Windows a určitých verzích Linux, dá se k ručnímu restartování použít následující rutina:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Kde: 
- Název a typ rozšíření pro Windows: ManagedIdentityExtensionForWindows
- Název a typ rozšíření pro Linux: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Známé problémy

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Při pokusu o export schématu pro spravované identity pro rozšíření prostředků Azure dojde k chybě skriptu služby Automation.

Pokud jsou na virtuálním počítači povolené spravované identity prostředků Azure, při pokusu o použití funkce skript Automation pro virtuální počítač nebo jeho skupiny prostředků se zobrazí následující chyba:

![Spravované identity pro Azure Resources Automation – chyba exportu skriptu](./media/msi-known-issues/automation-script-export-error.png)

Rozšíření virtuálních počítačů spravovaných identit pro prostředky Azure (naplánované pro vyřazení v lednu 2019) v současné době nepodporuje možnost exportu schématu do šablony skupiny prostředků. V důsledku toho vygenerovaná šablona nezobrazuje parametry konfigurace umožňující spravované identity prostředků Azure v prostředku. Tyto oddíly můžete přidat ručně podle příkladů v části [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablon](qs-configure-template-windows-vm.md).

Jakmile budou k dispozici funkce exportu schématu pro rozšíření spravované identity pro prostředky Azure Resources (plánováno pro vyřazení do ledna 2019), bude se zobrazovat v části [Export skupin prostředků, které obsahují rozšíření virtuálních počítačů](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>Spuštění virtuálního počítače po přesunutí ze skupiny prostředků nebo předplatného se nezdařilo.

Pokud virtuální počítač přesunete do běžícího stavu, během přesunu se i nadále spustí. Pokud se ale virtuální počítač zastaví a restartuje, po přesunutí se jeho spuštění nezdaří. K tomuto problému dochází, protože virtuální počítač neaktualizuje odkaz na spravované identity pro prostředky prostředků Azure a i nadále odkazuje na něj ve staré skupině prostředků.

**Alternativní řešení** 
 
Aktivujte na virtuálním počítači aktualizaci, aby mohla získat správné hodnoty pro spravované identity prostředků Azure. Změnou vlastnosti virtuálního počítače můžete aktualizovat odkaz na spravované identity pro identitu prostředků Azure. Můžete například nastavit novou hodnotu značky na virtuálním počítači pomocí následujícího příkazu:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Tento příkaz nastaví novou značku "fixVM" s hodnotou 1 na virtuálním počítači. 
 
Nastavením této vlastnosti se virtuální počítač aktualizuje se správnými spravovanými identitami pro identifikátor URI prostředku prostředků Azure a pak byste měli být schopni virtuální počítač spustit. 
 
Po spuštění virtuálního počítače se dá značku odebrat pomocí následujícího příkazu:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Zřizování rozšíření virtuálního počítače se nezdařilo

Zřizování rozšíření virtuálního počítače může selhat kvůli selhání vyhledávání DNS. Restartujte virtuální počítač a zkuste to znovu.
 
> [!NOTE]
> Rozšíření virtuálního počítače se plánuje pro vyřazení do ledna 2019. Doporučujeme, abyste se přesunuli na použití koncového bodu IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Převod předplatného mezi adresáři služby Azure AD

Spravované identity se při přesunu nebo přenosu předplatného do jiného adresáře neaktualizují. V důsledku toho budou přerušeny všechny existující spravované identity přiřazené systémem nebo uživatelem. 

Alternativní řešení pro spravované identity v předplatném, které se přesunulo do jiného adresáře:

 - Pro spravované identity přiřazené systémem: zakažte a znovu povolte. 
 - Pro spravované identity přiřazené uživateli: Odstraňte, znovu ho vytvořte a znovu připojte k potřebným prostředkům (např. virtuální počítače).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Přesunutí spravované identity přiřazené uživatelem do jiné skupiny prostředků nebo předplatného

Přesunutím spravované identity přiřazené uživatelem do jiné skupiny prostředků dojde k přerušení identity. V důsledku toho prostředky (např. virtuální počítač) používající tuto identitu nebudou moci žádat o tokeny. 

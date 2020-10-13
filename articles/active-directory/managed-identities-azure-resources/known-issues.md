---
title: Nejčastější dotazy a známé problémy se spravovanými identitami – Azure AD
description: Známé problémy se spravovanými identitami pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 08/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: cf9f484a3f9285d1be06443b39bd50ec73ccf632
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665289"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Nejčastější dotazy a známé problémy se spravovanými identitami pro prostředky Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Nejčastější dotazy

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Jak můžete najít prostředky, které mají spravovanou identitu?

Seznam prostředků, které mají spravovanou identitu přiřazenou systémem, najdete pomocí následujícího příkazu rozhraní příkazového řádku Azure: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>Mají spravované identity zálohovaný objekt aplikace?

Ne. Spravované identity a registrace Aplikace Azure AD nejsou v adresáři stejné. 

Registrace aplikací mít dvě komponenty: objekt aplikace + objekt instančního objektu. Spravované identity pro prostředky Azure mají jenom jednu z těchto součástí: instanční objekt služby. 

Spravované identity nemají v adresáři objekt aplikace, což je to, co se běžně používá k udělení oprávnění aplikace pro MS Graph. Místo toho je třeba oprávnění MS Graph pro spravované identity udělit přímo k instančnímu objektu.  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Pracují spravované identity prostředků Azure se službou Azure Cloud Services?

Ne, neexistují žádné plány na podporu spravovaných identit pro prostředky Azure v Azure Cloud Services.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Jaké jsou přihlašovací údaje přidružené ke spravované identitě? Jak dlouho je platný a jak často se otáčí?

> [!NOTE]
> Způsob, jakým jsou spravované identity ověřovány, jsou podrobnosti interní implementace, které se mohou změnit bez předchozího upozornění.

Spravované identity používají ověřování založené na certifikátech. Platnost přihlašovacích údajů každé spravované identity vyprší po 90 dnech a je zahrnutá za 45 dnů.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Jaká je hranice zabezpečení spravovaných identit pro prostředky Azure?

Hranice zabezpečení identity je prostředek, ke kterému je připojen. Například hranice zabezpečení pro virtuální počítač se spravovanými identitami pro prostředky Azure je virtuální počítač. Jakýkoli kód běžící na tomto virtuálním počítači může volat spravované identity pro koncový bod prostředků Azure a žádat tokeny. Podobně jako u jiných prostředků, které podporují spravované identity prostředků Azure, se jedná o podobné prostředí.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Jakou identitu bude IMDS výchozí, pokud nezadáte identitu v žádosti?

- Pokud je povolená spravovaná identita přiřazená systémem a v požadavku není zadaná žádná identita, IMDS se použije jako výchozí pro spravovanou identitu přiřazenou systémem.
- Pokud není povolená spravovaná identita přiřazená systémem a existuje jenom jedna spravovaná identita přiřazená uživatelem, IMDS se použije jako výchozí pro tento jediný uživatel přiřazenou spravovanou identitu. 
- Pokud není povolená spravovaná identita přiřazená systémem a existuje víc spravovaných identit přiřazených uživateli, pak se vyžaduje určení spravované identity v žádosti.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Budou spravované identity znovu automaticky vytvořeny při přesunu předplatného do jiného adresáře?

Ne. Pokud přesunete předplatné do jiného adresáře, budete ho muset ručně znovu vytvořit a znovu udělit přiřazení rolí Azure.
- Pro spravované identity přiřazené systémem: zakažte a znovu povolte. 
- Pro spravované identity přiřazené uživateli: Odstraňte, znovu ho vytvořte a znovu připojte k potřebným prostředkům (např. virtuální počítače).

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Můžu použít spravovanou identitu pro přístup k prostředkům v jiném adresáři nebo tenantovi?

Ne. Spravované identity v současné době nepodporují scénáře pro více adresářů. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Jaká oprávnění Azure RBAC jsou vyžadována pro spravovanou identitu prostředku? 

- Spravovaná identita přiřazená systémem: ke zdroji potřebujete oprávnění k zápisu. Například v případě virtuálních počítačů potřebujete oprávnění Microsoft.Compute/virtualMachines/write. Tato akce je zahrnutá v předdefinovaných rolích specifických pro prostředky, jako je [Přispěvatel virtuálních počítačů](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Spravovaná identita přiřazená uživatelem: k prostředku potřebujete oprávnění k zápisu. Například v případě virtuálních počítačů potřebujete oprávnění Microsoft.Compute/virtualMachines/write. Kromě spravované identity se přiřazení role [operátora identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) .


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
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Tento příkaz nastaví novou značku "fixVM" s hodnotou 1 na virtuálním počítači. 
 
Nastavením této vlastnosti se virtuální počítač aktualizuje se správnými spravovanými identitami pro identifikátor URI prostředku prostředků Azure a pak byste měli být schopni virtuální počítač spustit. 
 
Po spuštění virtuálního počítače se dá značku odebrat pomocí následujícího příkazu:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Převod předplatného mezi adresáři služby Azure AD

Spravované identity se při přesunu nebo přenosu předplatného do jiného adresáře neaktualizují. V důsledku toho budou přerušeny všechny existující spravované identity přiřazené systémem nebo uživatelem. 

Alternativní řešení pro spravované identity v předplatném, které se přesunulo do jiného adresáře:

 - Pro spravované identity přiřazené systémem: zakažte a znovu povolte. 
 - Pro spravované identity přiřazené uživateli: Odstraňte, znovu ho vytvořte a znovu připojte k potřebným prostředkům (např. virtuální počítače).

Další informace najdete v tématu věnovaném [převodu předplatných Azure do jiného adresáře Azure AD](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Přesunutí spravované identity přiřazené uživatelem do jiné skupiny prostředků nebo předplatného

Přesunutí spravované identity přiřazené uživatelem do jiné skupiny prostředků se nepodporuje.

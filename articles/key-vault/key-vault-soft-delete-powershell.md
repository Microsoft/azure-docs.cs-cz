---
ms.assetid: ''
title: Azure Key Vault - použití konfigurace soft odstranění pomocí prostředí PowerShell
description: Pomocí prostředí PowerShell výstřižků kódu případu příklady konfigurace soft odstranění
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/21/2017
ms.author: alleonar
ms.openlocfilehash: 48569e31e6400e3ec8958e0bceda1fd3b72207ea
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27927987"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak používat Key Vault konfigurace soft odstranění pomocí prostředí PowerShell

Azure Key Vault obnovitelného odstranění funkce umožňuje obnovení odstraněné trezory a objekty trezoru. Konkrétně obnovitelného odstranění adresy následující scénáře:

- Podpora pro obnovitelné odstranění trezoru klíčů
- Podpora pro obnovitelné odstranění trezoru klíčů objektů; klíče, tajné údaje a certifikáty

## <a name="prerequisites"></a>Požadavky

- Prostředí Azure PowerShell 4.0.0 nebo novější – Pokud nepoužíváte již je tento instalační program, nainstalujte prostředí Azure PowerShell a přidružit ho ke svému předplatnému Azure, najdete [postup instalace a konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Zastaralou verzi naše klíč trezoru PowerShell výstupní formátování souboru, který je **může** ho možné zavést do vašeho prostředí místo správnou verzi. Jsme se očekává aktualizovanou verzi prostředí PowerShell tak, aby obsahovala oprava potřebné pro formátování výstupu a aktualizuje v tomto tématu v daném čase. Aktuální řešení, by měl narazíte na potíže formátování je:
> - Pomocí následujícího dotazu, pokud si všimnete nevidíte konfigurace soft odstranění povolena vlastnost popsaných v tomto tématu: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Informace o konkrétní refernece Key Vault pro prostředí PowerShell najdete v tématu [Azure Key Vault PowerShell odkaz](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## <a name="required-permissions"></a>Požadovaná oprávnění

Key Vault operace jsou samostatně spravované prostřednictvím oprávnění k přístupu na základě rolí k řízení (RBAC) následujícím způsobem:

| Operace | Popis | Oprávnění uživatele |
|:--|:--|:--|
|Seznam|Seznamy odstraňovat trezorů klíčů.|Microsoft.KeyVault/deletedVaults/read|
|Zotavit|Obnoví odstraněné trezoru klíčů.|Microsoft.KeyVault/vaults/write|
|Vyprázdnit|Trvale odstraní odstraněné trezoru klíčů a veškerý jeho obsah.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Další informace o oprávněních a řízení přístupu najdete v tématu [zabezpečit váš trezor klíčů](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Povolení konfigurace soft odstranění

Abyste mohli obnovit odstraněné trezoru klíčů nebo objekty uložené v trezoru klíčů, musíte nejdřív povolit konfigurace soft odstranění tohoto klíče trezoru.

### <a name="existing-key-vault"></a>Existující trezor klíčů

Pro existující trezor klíčů s názvem ContosoVault následujícím způsobem povolte obnovitelného odstranění. 

>[!NOTE]
>Aktuálně je nutné použít zpracování prostředků Azure Resource Manager zápis přímo *enableSoftDelete* vlastnost prostředku Key Vault.

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nový trezor klíčů

Povolení konfigurace soft odstranění pro nového trezoru klíčů se provádí v okamžiku vytvoření přidáním příznak konfigurace soft odstranění povolit, aby vaše vytvoření příkazu.

```powershell
New-AzureRmKeyVault -VaultName "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Ověřte povolování konfigurace soft odstranění

Ověřte, že trezoru klíčů má konfigurace soft odstranění povolena, spusťte *získat* příkazů a podívejte se 'logicky odstranit povolen?" atribut a jeho nastavení true nebo false.

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Odstranění trezoru klíčů chráněn konfigurace soft odstranění

Příkaz k odstranění (nebo odebrání) trezoru klíčů zůstane stejný, ale její chování mění v závislosti na tom, jestli jste povolili obnovitelného odstranění nebo ne.

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

> [!IMPORTANT]
>Pokud spustíte předchozí příkaz pro trezor klíčů, který nemá konfigurace soft odstranění povoleno, bude trvale odstranit tento trezor klíčů a veškerý jeho obsah bez jakékoli možnosti pro obnovení.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak odstranit soft chrání vaše trezorů klíčů

S konfigurace soft odstranění povoleno:

- Při odstranění trezoru klíčů není odebrán z jeho skupin prostředků a je umístěn v vyhrazené oboru názvů, který je pouze přidružená k umístění, kde se vytvořila. 
- Objekty odstraněné klíč trezoru, jako jsou nedostupné klíče, tajných klíčů a certifikátů a tak zůstanou při jejich obsahující trezoru klíčů je ve stavu deleted. 
- Název DNS pro trezoru klíčů v odstraněném stavu je stále vyhrazené proto nelze vytvořit nový trezor klíčů se stejným názvem.  

Můžete si zobrazit trezorů klíčů stavu deleted, spojené s vaším předplatným, pomocí následujícího příkazu:

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedStateVault 

Name           : ContosoVault
Location             : westus
Id                   : /subscriptions/xxx/providers/Microsoft.KeyVault/locations/westus/deletedVaults/ContosoVault
Resource ID          : /subscriptions/xxx/resourceGroups/ContosoVault/providers/Microsoft.KeyVault/vaults/ContosoVault
Deletion Date        : 5/9/2017 12:14:14 AM
Scheduled Purge Date : 8/7/2017 12:14:14 AM
Tags                 :
```

*ID prostředku* ve výstupu odkazuje na původní ID prostředku trezoru. Vzhledem k tomu, že tento trezor klíčů je teď ve stavu deleted, neexistuje žádný prostředek s ID tohoto zdroje. *Id* pole lze použít k identifikaci prostředků při obnovení nebo vyprazdňování. *Naplánované datum vyprázdnění* pole určuje, kdy se trvale odstraní trezoru (Vymazat) Pokud nebyla provedena žádná akce pro tento trezor odstraněné. Výchozí dobu uchování, používá k výpočtu *naplánované Vyprázdnit data*, je 90 dnů.

## <a name="recovering-a-key-vault"></a>Obnovení trezoru klíčů

Pokud chcete obnovit trezoru klíčů, je třeba zadat název trezoru klíčů, skupinu prostředků a umístění. Všimněte si umístění a skupiny prostředků odstraněné trezoru klíčů, jako je třeba tyto klíče trezoru procesu obnovení.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Když je obnovena trezoru klíčů, výsledkem je nový prostředek s ID trezoru klíčů původního zdroje. Pokud byl odebrán skupině prostředků, které existovalo trezoru klíčů, musí před trezoru klíčů lze obnovit vytvořit novou skupinu prostředků se stejným názvem.

## <a name="key-vault-objects-and-soft-delete"></a>Key Vault objekty a konfigurace soft odstranění

Pro klíč 'ContosoFirstKey' v trezoru klíčů s názvem 'ContosoVault' s konfigurace soft odstranění povoleno, zde je způsob odstranili byste klíči.

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

S vaší povolené pro obnovitelného odstranění trezoru klíčů zobrazí odstraněný klíč i stejně, jako je odstraněn s výjimkou, že při explicitně seznamu nebo načtení odstraněné klíčů. Většinu operací pro klíč ve stavu deleted se nezdaří s výjimkou výpis odstraněný klíč, obnovení nebo vyprazdňování ho. 

Například požadavek na seznamu odstranit klíčů v trezoru klíčů, použijte následující příkaz:

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Přechodový stav. 

Pokud odstraníte klíč v trezoru klíčů s konfigurace soft odstranění povolené, může trvat několik sekund pro přechod k dokončení. Při tomto přechod stavu může zdát, že klíč není v aktivním stavu nebo odstraněném stavu. Tento příkaz zobrazí seznam všech odstraněných klíčů v trezoru klíčů s názvem 'ContosoVault'.

```powershell
  Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
  Vault Name           : ContosoVault
  Name                 : ContosoFirstKey
  Id                   : https://ContosoVault.vault.azure.net:443/keys/ContosoFirstKey
  Deleted Date         : 2/14/2017 8:20:52 PM
  Scheduled Purge Date : 5/15/2017 8:20:52 PM
  Enabled              : True
  Expires              :
  Not Before           :
  Created              : 2/14/2017 8:16:07 PM
  Updated              : 2/14/2017 8:16:07 PM
  Tags                 :
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Pomocí konfigurace soft odstranění trezoru klíčů objekty

Jenom jako trezorů klíčů, odstraněný klíč tajný klíč nebo certifikát zůstane ve stavu deleted po dobu 90 dnů Pokud jej obnovit, nebo ji vymazat. 

#### <a name="keys"></a>Klíče

K obnovení odstraněné klíče:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Trvale odstranit klíč:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

>[!NOTE]
>Vymazání klíč se trvale odstraní, což znamená, že nebude použitelná pro obnovení.

**Obnovit** a **mazání** akce mají své vlastní oprávnění v zásadách přístupu k trezoru klíčů. Pro uživatele nebo instanční objekt by mohl spustit **obnovit** nebo **mazání** akce musí mít odpovídající oprávnění pro tento objekt (klíč nebo tajný klíč) v zásadách přístupu trezoru klíčů. Ve výchozím nastavení **mazání** oprávnění nebyla přidána do zásad přístupu k trezoru klíčů, když "vše" zástupce se používá k udělení oprávnění pro všechny uživatele. Je třeba explicitně udělit **mazání** oprávnění. Například následující příkaz uděluje user@contoso.com oprávnění k provádění různých operací na klíče ve *ContosoVault* včetně **mazání**.

#### <a name="set-a-key-vault-access-policy"></a>Nastavit zásady přístupu k trezoru klíčů

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Pokud máte existující trezor klíčů, který má právě měl konfigurace soft odstranění povoleno, nemusí mít **obnovit** a **mazání** oprávnění.

#### <a name="secrets"></a>Tajné kódy

Podobně jako klíče jsou tajných klíčů v trezoru klíčů provozovat na s vlastní příkazy. Následující, jsou příkazy pro odstranění, výpis, obnovení a mazání tajných klíčů.

- Odstranění tajného klíče s názvem SQLPassword: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Zobrazí seznam všech odstraněných tajných klíčů v trezoru klíčů: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Tajný klíč v odstraněném stavu obnovení: 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Vyprázdnění tajný klíč v odstraněném stavu: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
```

>[!NOTE]
>Vymazání tajného klíče se trvale odstraní, což znamená, že nebude použitelná pro obnovení.

## <a name="purging-and-key-vaults"></a>Trezory mazání a klíč

### <a name="key-vault-objects"></a>Objekty trezoru klíčů

Vymazání klíč, tajný klíč nebo certifikát se trvale odstraní, což znamená, že nebude použitelná pro obnovení. Trezor klíčů, který obsahuje odstraněný objekt se ale zachovají stejně jako všechny ostatní objekty v trezoru klíčů. 

### <a name="key-vaults-as-containers"></a>Klíč trezory jako kontejnery
Když se vyprazdňují trezoru klíčů, veškerý její obsah, včetně klíče a tajné klíče, certifikáty, se trvale odstraní. K vyprázdnění trezoru klíčů, použijte `Remove-AzureRmKeyVault` příkaz s možností `-InRemovedState` a zadáním umístění odstraněného klíče trezoru se `-Location location` argument. Můžete najít umístění odstraněného trezoru pomocí příkazu `Get-AzureRmKeyVault -InRemovedState`.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

>[!NOTE]
>Vymazání trezoru klíčů se trvale odstraní, což znamená, že nebude použitelná pro obnovení.

### <a name="purge-permissions-required"></a>Vyprázdnění oprávněních
- K vyprázdnění odstraněné trezoru klíčů, tak, aby trezoru a veškerý jeho obsah jsou trvale odstraněny, musí uživatel oprávnění k provedení *Microsoft.KeyVault/locations/deletedVaults/purge/action* operaci. 
- Pro zobrazení seznamu odstraněný klíč, trezor uživatel potřebuje oprávnění k provedení *Microsoft.KeyVault/deletedVaults/read* oprávnění. 
- Pouze správce předplatného ve výchozím nastavení má tato oprávnění. 

### <a name="scheduled-purge"></a>Naplánované vyprázdnění

Výpis vaše objekty odstraněné trezoru klíčů zobrazí, kdy mají schedled vyprázdní pomocí Key Vault. *Naplánované datum vyprázdnění* pole označuje, když objekt trezoru klíčů se trvale odstraní, pokud nebyla provedena žádná akce. Ve výchozím nastavení Doba uchování pro objekt odstraněného trezoru klíčů je 90 dnů.

>[!NOTE]
>Objekt vymazány trezoru, aktivuje její *naplánované datum vyprázdnění* pole, je trvale odstraněn. Není použitelná pro obnovení.

## <a name="other-resources"></a>Další prostředky

- Přehled funkce obnovitelného odstranění Key Vault najdete v tématu [Přehled konfigurace soft odstranění Azure Key Vault](key-vault-ovw-soft-delete.md).
- Obecné informace o použití Azure Key Vault najdete v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md).


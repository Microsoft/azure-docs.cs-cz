---
ms.assetid: ''
title: Služba Azure Key Vault – jak používat obnovitelné odstranění s využitím Powershellu
description: Příklady případu použití obnovitelného odstranění s využitím výstřižky kódu Powershellu
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 10/16/2018
ms.author: bryanla
ms.openlocfilehash: 99f81e14ca631eccee154a5658bf717cbe07b3da
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364366"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak používat obnovitelné odstranění Key Vaultu s využitím Powershellu

Funkce obnovitelného odstranění Azure Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru. Konkrétně obnovitelného odstranění adresy následující scénáře:

- Podpora pro obnovitelné odstranění trezoru klíčů.
- Podpora pro obnovitelné odstranění key vaultu objektů; klíče, tajné kódy a certifikáty

## <a name="prerequisites"></a>Požadavky

- Prostředí Azure PowerShell 4.0.0 nebo později – Pokud nemáte tomto již instalační program, nainstalujte Azure PowerShell a přidružit ho k vašemu předplatnému Azure, přečtěte si téma [instalace a konfigurace Azure Powershellu](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> Soubor zastaralou verzi naší formátování výstupu Key Vault prostředí PowerShell, který je **může** načíst do vašeho prostředí místo správnou verzi. Jsou předvídání aktualizovanou verzi prostředí PowerShell tak, aby obsahovala potřebné opravy pro formátování výstupu jsme v tuto chvíli aktualizuje v tomto tématu. Aktuálním řešením, by měl narazíte na potíže formátování je:
> - Pomocí následujícího dotazu, pokud si všimnete, že se nezobrazují obnovitelného odstranění povolena vlastnost popsané v tomto tématu: `$vault = Get-AzureRmKeyVault -VaultName myvault; $vault.EnableSoftDelete`.


Informace o konkrétní refernece služby Key Vault pro prostředí PowerShell najdete v tématu [Azure Key Vault prostředí PowerShell odkaz](https://docs.microsoft.com/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

## <a name="required-permissions"></a>Požadovaná oprávnění

Operace služby Key Vault jsou řízeny odděleně prostřednictvím přístupu na základě role (RBAC) ovládací prvek oprávnění následujícím způsobem:

| Operace | Popis | Oprávnění uživatele |
|:--|:--|:--|
|Seznam|Seznamy odstraňovat trezory klíčů.|Microsoft.KeyVault/deletedVaults/read|
|Zotavit|Obnoví odstraněný trezor klíčů.|Microsoft.KeyVault/vaults/write|
|Vyprázdnit|Odstraní trvale odstraněný trezor klíčů a veškerý jeho obsah.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Další informace o oprávněních a řízení přístupu najdete v tématu [zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Povolení obnovitelného odstranění

Povolíte "obnovitelného odstranění" aby bylo možné obnovit odstraněný trezor klíčů, nebo objekty uložené ve službě key vault.

> [!IMPORTANT]
> Povolit obnovitelné odstranění v trezoru klíčů je nevratná akce. Jakmile je nastavená vlastnost obnovitelného odstranění "PRAVDA", nelze změnit ani odebrat.  

### <a name="existing-key-vault"></a>Existujícího trezoru klíčů

U existujícího trezoru klíčů s názvem ContosoVault povolte obnovitelné odstranění následujícím způsobem. 

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzureRmResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nový trezor klíčů

Povolení obnovitelného odstranění pro nový trezor klíčů se provádí v okamžiku vytvoření přidáním příznak povolení obnovitelného odstranění pro vaše vytvořit příkaz.

```powershell
New-AzureRmKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Ověření povolení obnovitelného odstranění

Chcete-li zkontrolovat, jestli má trezor klíčů povolené obnovitelné odstranění, *zobrazit* příkaz a vyhledejte "Obnovitelné odstranění povolené?" Atribut:

```powershell
Get-AzureRmKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Odstraňuje se trezor klíčů chráněných pomocí obnovitelného odstranění

Příkaz pro odstranění trezoru klíčů změny v chování v závislosti na tom, zda je povoleno obnovitelného odstranění.

> [!IMPORTANT]
>Pokud spustíte následující příkaz pro trezor klíčů, který nemá povolené obnovitelné odstranění, trvale odstraníte tento trezor klíčů a veškerý jeho obsah bez použití možností pro obnovení!

```powershell
Remove-AzureRmKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak se obnovitelného odstranění chrání vaše trezory klíčů

S obnovitelným odstraněním povoleno:

- Odstraněný trezor klíčů je odebrán ze skupiny prostředků a je umístěn v vyhrazený obor názvů, přidružený k místu, kde se vytvořila. 
- Odstraněné objekty, jako jsou klíče, tajné kódy a certifikáty, jsou nedostupná, dokud jejich obsahující trezor klíčů není ve stavu odstraněno. 
- Je vyhrazený název DNS pro odstraněný trezor klíčů, brání vytváří nový trezor klíčů se stejným názvem.  

Trezory klíčů stavu odstraněno, přidružených k vašemu předplatnému, mohou zobrazit pomocí následujícího příkazu:

```powershell
PS C:\> Get-AzureRmKeyVault -InRemovedState 
```

- *ID* slouží k identifikaci prostředku při obnovení nebo vyprazdňování. 
- *ID prostředku* je původní ID prostředku trezoru. Vzhledem k tomu, že tento trezor klíčů je teď ve stavu odstraněno, neexistuje žádný prostředek s ID tohoto prostředku. 
- *Naplánované datum vyprázdnit* je při trezoru se trvale odstraní, pokud nebyla provedena žádná akce. Výchozí dobu uchování, používá k výpočtu *naplánované datum vyprázdnit*, je 90 dní.

## <a name="recovering-a-key-vault"></a>Obnovení služby key vault

Obnovení služby key vault, zadejte název služby key vault, skupinu prostředků a umístění. Poznamenejte si umístění a skupině prostředků odstraněného trezoru klíčů, podle potřeby pro proces obnovení.

```powershell
Undo-AzureRmKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Při obnovení služby key vault je vytvořen nový prostředek s ID služby key vault původního zdroje. Pokud je odebrán původní skupiny prostředků, jeden je nutné vytvořit se stejným názvem před pokusem o obnovení.

## <a name="key-vault-objects-and-soft-delete"></a>Objekty služby Key Vault a obnovitelného odstranění

Následující příkaz odstraní klíči "ContosoFirstKey" v trezoru klíčů s názvem "ContosoVault", který má povolené obnovitelné odstranění:

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Pomocí trezoru klíčů povolená pro obnovitelné odstranění odstraněný klíč stále se zobrazí, která se má odstranit, pokud explicitně uvádět odstraněné klíče. Většinu operací s klíči ve stavu odstraněno selže s výjimkou výpis, obnovení, mazání odstraněných klíč. 

Například následující příkaz zobrazí seznam odstraněných klíče v trezoru klíčů "ContosoVault":

```powershell
Get-AzureKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Přechod stavu 

Při odstranění klíče ve službě key vault s obnovitelným odstraněním povolené, může trvat několik sekund na přechod na dokončení. Během tohoto přechodu je může zdát, že klíč není v aktivním stavu nebo stavu odstraněno. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Použití obnovitelného odstranění s využitím objektů trezoru klíčů

Stejně jako trezorů klíčů odstraněné klíče, tajné nebo certifikát, zůstane ve stavu odstraněno až 90 dnů, pokud ji obnovit nebo ho vymazat. 

#### <a name="keys"></a>Klíče

Obnovit klíč obnovitelně odstraněný:

```powershell
Undo-AzureKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Trvale odstranit, (označované také jako vyprazdňování) obnovitelně odstraněný klíč:

> [!IMPORTANT]
> Odstranění klíče ho trvale odstraníte a nesmí být obnovitelná! 

```powershell
Remove-AzureKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

**Obnovit** a **vyprázdnit** akce mají své vlastní oprávnění přidružené zásady přístupu trezoru klíčů. Pro uživatele nebo instanční objekt, který bude moci být prováděny **obnovit** nebo **vyprázdnit** akce, musí mít odpovídající oprávnění pro tento klíč nebo tajný klíč. Ve výchozím nastavení **vyprázdnit** není přidán do zásady přístupu trezoru klíčů, když místní 'vše' se používá k udělení oprávnění. Konkrétně je nutné udělit **vyprázdnit** oprávnění. 

#### <a name="set-a-key-vault-access-policy"></a>Nastavit zásady přístupu trezoru klíčů

Zadáním následujícího příkazu uděluje user@contoso.com oprávnění používat několik operací s klíči v *ContosoVault* včetně **vyprázdnit**:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Pokud máte existujícího trezoru klíčů, pro který byla pouze povolené obnovitelné odstranění, pravděpodobně nemáte **obnovit** a **vyprázdnit** oprávnění.

#### <a name="secrets"></a>Tajné kódy

Podobně jako klíče tajné kódy se spravují pomocí vlastních příkazů:

- Odstranění tajného kódu s názvem SQLPassword: 
```powershell
Remove-AzureKeyVaultSecret -VaultName ContosoVault -name SQLPassword
```

- Vypište všechny odstraněné tajné kódy ve službě key vault: 
```powershell
Get-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState
```

- Obnovte tajný klíč ve stavu odstraněno: 
```powershell
Undo-AzureKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
```

- Odstranění tajného kódu ve stavu odstraněno: 

  > [!IMPORTANT]
  > Odstranění tajného kódu ho trvale odstraníte a nesmí být obnovitelná!

  ```powershell
  Remove-AzureKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

## <a name="purging-and-key-vaults"></a>Trezory klíčů a mazání

### <a name="key-vault-objects"></a>Objekty služby Key vault

Odstranění klíče, tajné nebo certifikát, způsobí trvalé odstranění a ho nepůjde obnovit. Trezor klíčů, který obsahoval odstraněného objektu se nijak nezmění, ale stejně jako všechny ostatní objekty v trezoru klíčů. 

### <a name="key-vaults-as-containers"></a>Trezory klíčů jako kontejnery
Při vymazání se trezor klíčů, jeho celý obsah se trvale odstraní, včetně klíče, tajné kódy a certifikáty. Chcete-li odstranit trezor klíčů, použijte `Remove-AzureRmKeyVault` příkaz s možností `-InRemovedState` a zadáním umístění odstraněného trezoru klíčů pomocí `-Location location` argument. Můžete najít umístění odstraněného trezoru pomocí příkazu `Get-AzureRmKeyVault -InRemovedState`.

>[!IMPORTANT]
>Odstraňovat zařízení nepřipojená k trezoru klíčů ho trvale odstraníte, což znamená, že ho nepůjde obnovit.

```powershell
Remove-AzureRmKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Vyprázdnit oprávněních
- Vymazání odstraněný trezor klíčů, uživatel potřebuje oprávnění RBAC *Microsoft.KeyVault/locations/deletedVaults/purge/action* operace. 
- Pro zobrazení seznamu odstraněného trezoru klíčů, uživatel potřebuje oprávnění RBAC *Microsoft.KeyVault/deletedVaults/read* operace. 
- Ve výchozím nastavení tato oprávnění má jenom správce předplatného. 

### <a name="scheduled-purge"></a>Naplánované odstranění

Seznam objektů odstraněného trezoru klíčů. také ukazuje, kdy jsou naplánované vymazat službou Key Vault. *Naplánované datum vyprázdnit* označuje, kdy objekt služby key vault se trvale odstraní, pokud nebyla provedena žádná akce. Ve výchozím nastavení Doba uchování pro objekt odstraněného trezoru klíčů je 90 dní.

>[!IMPORTANT]
>Objekt odstraněný trezor, aktivuje její *naplánované datum vyprázdnit* pole, se trvale odstraní. Se nedá vrátit zpátky!

## <a name="other-resources"></a>Další prostředky

- Přehled funkce obnovitelného odstranění služby Key Vault najdete v tématu [přehled obnovitelného odstranění služby Azure Key Vault](key-vault-ovw-soft-delete.md).
- Obecný přehled o využití služby Azure Key Vault najdete v části [Začínáme s Azure Key Vault](key-vault-get-started.md).


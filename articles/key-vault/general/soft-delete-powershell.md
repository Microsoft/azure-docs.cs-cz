---
title: Azure Key Vault – použití obnovitelného odstranění s využitím PowerShellu
description: Naučte se používat Azure PowerShell k použití funkce obnovitelného odstranění Azure Key Vault, která umožňuje obnovení trezorů klíčů a objektů trezoru klíčů.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/11/2020
ms.author: sudbalas
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 05ff5d2f7ff87d2ce58f135330487f746cc5c1c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90528359"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu

Funkce obnovitelného odstranění Azure Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru. Konkrétně obnovitelné odstranění řeší následující scénáře:

- Podpora obnovitelného odstranění trezoru klíčů
- Podpora obnovitelného mazání objektů trezoru klíčů; klíče, tajné klíče a certifikáty

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 nebo novější – Pokud toto nastavení již nemáte, nainstalujte Azure PowerShell a přidružte ho k předplatnému Azure, viz [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/). 

>[!NOTE]
> Existuje zastaralá verze našeho Key Vaultho souborového formátování PowerShellu **, která se dá načíst** do vašeho prostředí namísto správné verze. Očekáváme aktualizovanou verzi PowerShellu, která bude obsahovat potřebnou opravu formátování výstupu, a v tomto okamžiku aktualizuje toto téma. Aktuální alternativní řešení: Pokud se setkáte s tímto problémem formátování, je:
> - Následující dotaz použijte, pokud si všimnete, že se vám nezobrazuje vlastnost s povoleným odstraněním hesla, která je popsaná v tomto tématu: `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete` .


Informace o Key Vault specifických referenčních informacích pro PowerShell najdete v tématu [Azure Key Vault PowerShell reference](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Požadovaná oprávnění

Operace Key Vault se samostatně spravují prostřednictvím oprávnění řízení přístupu na základě role (RBAC) následujícím způsobem:

| Operace | Popis | Oprávnění uživatele |
|:--|:--|:--|
|Seznam|Zobrazí seznam odstraněných trezorů klíčů.|Microsoft. deletedVaults trezor//Read|
|Zotavit|Obnoví odstraněný Trezor klíčů.|Trezor Microsoft. a trezory/zápis|
|Vyprázdnit|Trvale odstraní odstraněný Trezor klíčů a veškerý jeho obsah.|Microsoft. webdeletedVaults trezor/Locations//vyprázdnění/Action|

Další informace o oprávněních a řízení přístupu najdete v tématu [zabezpečení trezoru klíčů](secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Povolení obnovitelného odstranění

Pokud chcete povolit obnovení odstraněného trezoru klíčů nebo objektů uložených v trezoru klíčů, povolte možnost obnovitelné odstranění.

> [!IMPORTANT]
> Povolení možnosti obnovitelného odstranění u trezoru klíčů je nevratná akce. Když je vlastnost obnovitelného odstranění nastavená na hodnotu true, nedá se změnit ani odebrat.  

### <a name="existing-key-vault"></a>Existující Trezor klíčů

Pro existující Trezor klíčů s názvem ContosoVault povolte obnovitelné odstranění následujícím způsobem. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nový trezor klíčů

Obnovitelné odstranění je standardně automaticky zapnuté pro všechny nové trezory klíčů. Od prosince 31 2020 již nebude možné zakázat obnovitelné odstranění v jakémkoli trezoru klíčů. 

### <a name="verify-soft-delete-enablement"></a>Ověřit povolení pro obnovitelné odstranění

Pokud chcete ověřit, jestli je Trezor klíčů povolený pomocí obnovitelného odstranění, spusťte příkaz *Zobrazit* a vyhledejte možnost obnovitelné odstranění povoleno?. přidělen

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Odstranění trezoru chráněného klíče, který je k odstranění obnovitelné

Příkaz pro odstranění trezoru klíčů v chování v závislosti na tom, jestli je povolené obnovitelné odstranění.

> [!IMPORTANT]
>Pokud pro Trezor klíčů, který nemá povolené obnovitelné odstranění, spustíte následující příkaz, trvale odstraníte tento trezor klíčů a veškerý jeho obsah bez možností obnovení.

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak obnovitelné odstranění chrání vaše trezory klíčů

S povolenou možnost obnovitelného odstranění:

- Odstraněný Trezor klíčů se odebere ze skupiny prostředků a umístí do rezervovaného oboru názvů přidruženého k umístění, kde se vytvořil. 
- Odstraněné objekty, jako jsou klíče, tajné klíče a certifikáty, jsou nedostupné, pokud jsou ve stavu odstraněno, dokud je jejich nadřazený Trezor klíčů. 
- Název DNS odstraněného trezoru klíčů je rezervovaný a brání vytvoření nového trezoru klíčů se stejným názvem.  

Odstraněné trezory klíčů stavů, které jsou přidružené k vašemu předplatnému, můžete zobrazit pomocí následujícího příkazu:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID* se dá použít k identifikaci prostředku při obnovování nebo vyprazdňování. 
- *ID prostředku* je původní ID prostředku tohoto trezoru. Vzhledem k tomu, že tento trezor klíčů je teď v odstraněném stavu, neexistuje žádný prostředek s tímto ID prostředku. 
- *Naplánované datum vyprázdnění* je v případě, že se trezor trvale odstraní, pokud se neprovede žádná akce. Výchozí doba uchování, která se používá k výpočtu *naplánovaného data vyprázdnění*, je 90 dní.

## <a name="recovering-a-key-vault"></a>Obnovování trezoru klíčů

Chcete-li obnovit Trezor klíčů, zadejte název trezoru klíčů, skupinu prostředků a umístění. Poznamenejte si umístění a skupinu prostředků odstraněného trezoru klíčů, jak je budete potřebovat pro proces obnovení.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Po obnovení trezoru klíčů se vytvoří nový prostředek s původním ID prostředku trezoru klíčů. Pokud je původní skupina prostředků odebrána, musí se před pokusem o obnovení vytvořit jedna se stejným názvem.

## <a name="deleting-and-purging-key-vault-objects"></a>Odstraňování a vyprazdňování objektů trezoru klíčů

Následující příkaz odstraní klíč "ContosoFirstKey" v trezoru klíčů s názvem "ContosoVault", který má povolené obnovitelné odstranění:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Když je váš Trezor klíčů povolený pro obnovitelné odstranění, zdá se, že odstraněný klíč se odstraní, pokud explicitně vypíšete odstraněné klíče. Většina operací s klíčem ve stavu odstraněno selže, s výjimkou výpisu, obnovení a vyprázdnění odstraněného klíče. 

Například následující příkaz vypíše odstraněné klíče v trezoru klíčů ' ContosoVault ':

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stav přechodu 

Když odstraníte klíč v trezoru klíčů s povoleným obnovitelném odstraněním, může trvat několik sekund, než se přechod dokončí. Během tohoto přechodu se může zdát, že klíč není v aktivním stavu nebo odstraněném stavu. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Použití obnovitelného odstranění s objekty trezoru klíčů

Stejně jako trezory klíčů, odstraněný klíč, tajný klíč nebo certifikát zůstávají v odstraněném stavu po dobu až 90 dnů, pokud je neobnovíte nebo nevymažete. 

#### <a name="keys"></a>Klíče

Obnovení klíče odstraněného přípravné:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

K trvalému odstranění (označované také jako vyprazdňování) klíč odstraněný:

> [!IMPORTANT]
> Vyprázdněním klíče se trvale odstraní a nebude možné ho obnovit. 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Akce **obnovit** a **Odstranit** mají svá vlastní oprávnění přidružená do zásad přístupu trezoru klíčů. Aby mohl uživatel nebo instanční objekt spustit akci **obnovení** nebo **vyprázdnění** , musí mít příslušná oprávnění pro tento klíč nebo tajný klíč. Ve výchozím nastavení se k zásadám přístupu trezoru klíčů při použití zkratky All pro udělení všech oprávnění nepřidá **vyprázdnění** . Musíte výslovně udělit oprávnění k **vyprázdnění** . 

#### <a name="set-a-key-vault-access-policy"></a>Nastavení zásad přístupu trezoru klíčů

Následující příkaz udělí user@contoso.com oprávnění k použití několika operací na klíčích v *ContosoVault* včetně **mazání**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Máte-li existující Trezor klíčů, který má k dispozici pouze obnovitelné odstranění, pravděpodobně nemáte oprávnění k **obnovení** a **vyprázdnění** .

#### <a name="secrets"></a>Tajné kódy

Podobně jako klíče jsou tajné klíče spravované s vlastními příkazy:

- Odstraňte tajný kód s názvem SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Vypíše všechny odstraněné tajné klíče v trezoru klíčů: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Obnovte tajný klíč v odstraněném stavu: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Vyprázdnit tajný klíč v odstraněném stavu: 

  > [!IMPORTANT]
  > Vyprázdnění tajného klíče se trvale odstraní a nebude možné ho obnovit.

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certifikáty

Certifikáty můžete spravovat pomocí následujících příkazů:

- Odstranit certifikát: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Vypíše všechny odstraněné certifikáty v trezoru klíčů: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Obnovení certifikátu v odstraněném stavu: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Vyprázdnit certifikát v odstraněném stavu: 

  > [!IMPORTANT]
  > Vymazáním certifikátu ho trvale odstraníte a nebude možné ho obnovit.

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Vyprazdňování chráněného trezoru klíčů pro obnovitelné odstranění

> [!IMPORTANT]
> Vymazáním trezoru klíčů nebo některého z jeho obsažených objektů ho trvale odstraníte, což znamená, že nebude možné ho obnovit!

Funkce vyprázdnění se používá k trvalému odstranění objektu trezoru klíčů nebo celého trezoru klíčů, který se dřív odstranil. Jak je znázorněno v předchozí části, objekty uložené v trezoru klíčů s povolenou funkcí obnovitelného odstranění můžou projít více stavy:
- **Aktivní**: před odstraněním
- **Obnovitelné – odstraněno**: po odstranění bude možné ho uvést a obnovit zpátky do stavu aktivní.
- **Trvale odstraněno**: po vymazání nelze obnovení provést.


Totéž platí pro Trezor klíčů. Aby bylo možné trvale odstranit dočasně odstraněný Trezor klíčů a jeho obsah, je nutné Trezor klíčů vyprázdnit.

### <a name="purging-a-key-vault"></a>Vyprazdňování trezoru klíčů

Když se odstraní Trezor klíčů, veškerý obsah se trvale odstraní, včetně klíčů, tajných klíčů a certifikátů. Pokud chcete vymazat odstraněný Trezor klíčů, použijte `Remove-AzKeyVault` příkaz s možností `-InRemovedState` a zadáním umístění odstraněného trezoru klíčů k `-Location location` argumentu. Umístění odstraněného trezoru můžete najít pomocí příkazu `Get-AzKeyVault -InRemovedState` .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Vyžadovaná oprávnění pro vyprázdnění
- K vymazání odstraněného trezoru klíčů uživatel potřebuje oprávnění RBAC pro operaci *Microsoft. Key trezor/Locations/deletedVaults/vyprázdnění/Action* . 
- K vypsání odstraněného trezoru klíčů uživatel potřebuje oprávnění RBAC pro operaci *Microsoft. Key trezor/deletedVaults/Read* . 
- Ve výchozím nastavení má tato oprávnění pouze správce předplatného. 

### <a name="scheduled-purge"></a>Naplánované vyprázdnění

Výpis odstraněných objektů trezoru klíčů se zobrazí také v případě, že je naplánováno jejich vymazání Key Vault. *Naplánované datum mazání* indikuje, že se objekt trezoru klíčů trvale odstraní, pokud se neprovede žádná akce. Ve výchozím nastavení je doba uchování odstraněného objektu trezoru klíčů 90 dní.

>[!IMPORTANT]
>Vyčištěný objekt trezoru aktivovaný v poli *plánovaného data vyprázdnit* se trvale odstraní. Nedá se obnovit.

## <a name="enabling-purge-protection"></a>Povoluje se ochrana vyprázdnění.

Pokud je zapnutá ochrana vyprázdnění, trezor nebo objekt v odstraněném stavu nelze odstranit, dokud neuplyne doba uchování. Tento trezor nebo objekt je stále možné obnovit. Tato funkce poskytuje přidanou jistotu, že trezor nebo objekt nikdy nebude možné trvale odstranit, dokud neuplyne doba uchování. Výchozí doba uchování je 90 dní, ale během vytváření trezoru klíčů je možné nastavit interval zásad uchovávání informací na hodnotu od 7 do 90 dnů. Zásady uchovávání informací o vyprázdnění ochrany používají stejný interval. Po nastavení se interval zásad uchovávání dat nedá změnit.

Ochranu vyprázdnění můžete povolit jenom v případě, že je povolené i obnovitelné odstranění. Zakázání ochrany vyprázdnění se nepodporuje. 

Pokud chcete při vytváření trezoru zapnout ochranu pomocí obnovitelného odstranění i vymazání, použijte rutinu [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0) :

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Pokud chcete přidat ochranu vyprázdnit do existujícího trezoru (který už má povolené obnovitelné odstranění), použijte rutiny [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)a [set-AzResource](/powershell/module/az.resources/set-azresource?view=azps-1.5.0) :

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Další prostředky

- Přehled funkce obnovitelného odstranění Key Vault najdete v článku [přehled Azure Key Vault obnovitelného odstranění](soft-delete-overview.md).
- Obecný přehled využití Azure Key Vault najdete v tématu [co je Azure Key Vault?](overview.md).

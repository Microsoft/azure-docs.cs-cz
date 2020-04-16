---
title: Azure Key Vault – použití obnovitelného odstranění pomocí PowerShellu
description: Příklady případu obnovitelného odstranění pomocí výstřižků kódu prostředí PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: c74bea8aa1a8e2f9de47b501f9afd9540cfc61b9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422911"
---
# <a name="how-to-use-key-vault-soft-delete-with-powershell"></a>Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu

Azure Key Vault je funkce obnovitelného odstranění umožňuje obnovení odstraněné trezory a objekty trezoru. Konkrétně obnovitelné odstranění řeší následující scénáře:

- Podpora obnovitelného odstranění trezoru klíčů
- Podpora obnovitelného odstranění objektů trezoru klíčů; klíče, tajné klíče a certifikáty

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Azure PowerShell 1.0.0 nebo novější – Pokud toto nastavení ještě nemáte, nainstalujte Azure PowerShell a přidružte ho k předplatnému Azure, přečtěte si téma [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview). 

>[!NOTE]
> K dispozici je zastaralá verze našeho výstupního formátu úložiště klíčů PowerShell, který **může** být načten do vašeho prostředí namísto správné verze. Očekáváme, že aktualizovaná verze prostředí PowerShell bude obsahovat potřebnou opravu pro formátování výstupu a v té době toto téma aktualizujeme. Aktuální řešení, pokud narazíte na tento problém formátování, je:
> - Pokud si všimnete, že se vám nezobrazuje vlastnost s povoleným `$vault = Get-AzKeyVault -VaultName myvault; $vault.EnableSoftDelete`odstraněním soft delete popsaná v tomto tématu, použijte následující dotaz: .


Informace o referencích specifických pro úložiště klíčů pro PowerShell najdete v [tématu Odkaz na powershell azure trezoru klíčů](/powershell/module/az.keyvault).

## <a name="required-permissions"></a>Požadovaná oprávnění

Operace trezoru klíčů jsou spravovány samostatně pomocí oprávnění řízení přístupu na základě rolí (RBAC) následujícím způsobem:

| Operace | Popis | Oprávnění uživatele |
|:--|:--|:--|
|Seznam|Zobrazí seznam odstraněných trezorů klíčů.|Microsoft.KeyVault/deletedVaults/read|
|Zotavit|Obnoví úložiště odstraněných klíčů.|Microsoft.KeyVault/trezory/zápis|
|Vyprázdnit|Trvale odebere odstraněný trezor klíčů a veškerý jeho obsah.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Další informace o oprávněních a řízení přístupu naleznete v [tématu Zabezpečení trezoru klíčů](secure-your-key-vault.md)).

## <a name="enabling-soft-delete"></a>Povolení obnovitelného odstranění

Povolíte "obnovitelné odstranění", chcete-li povolit obnovení odstraněného trezoru klíčů nebo objektů uložených v trezoru klíčů.

> [!IMPORTANT]
> Povolení "obnovitelného odstranění" v trezoru klíčů je nevratná akce. Jakmile je vlastnost obnovitelného odstranění nastavena na hodnotu "true", nelze ji změnit ani odebrat.  

### <a name="existing-key-vault"></a>Existující trezor klíčů

U existujícího trezoru klíčů s názvem ContosoVault povolte obnovitelné odstranění následujícím způsobem. 

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

### <a name="new-key-vault"></a>Nový trezor klíčů

Povolení obnovitelného odstranění pro nový trezor klíčů se provádí v době vytvoření přidáním příznaku obnovitelného povolení k vytvoření příkazu.

```powershell
New-AzKeyVault -Name "ContosoVault" -ResourceGroupName "ContosoRG" -Location "westus" -EnableSoftDelete
```

### <a name="verify-soft-delete-enablement"></a>Ověření povolení obnovitelného odstranění

Chcete-li ověřit, zda je povolen měkký výmaz, spusťte příkaz *show* a vyhledejte příkaz Obnovitelné odstranění povoleno. Atribut:

```powershell
Get-AzKeyVault -VaultName "ContosoVault"
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>Odstranění trezoru chráněných klíčů s měkkým odstraněním

Příkaz k odstranění trezoru klíčů se mění v chování v závislosti na tom, zda je povoleno obnovitelné odstranění.

> [!IMPORTANT]
>Pokud spustíte následující příkaz pro trezor klíčů, který nemá povolený softwarový odstranění, trvale odstraníte tento trezor klíčů a veškerý jeho obsah bez možností obnovení!

```powershell
Remove-AzKeyVault -VaultName 'ContosoVault'
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak obnovitelné odstranění chrání trezory klíčů

S povoleným softwarovým odstraněním:

- Odstraněný trezor klíčů je odebrán ze skupiny prostředků a umístěn do vyhrazeného oboru názvů přidruženého k umístění, kde byl vytvořen. 
- Odstraněné objekty, jako jsou klíče, tajné klíče a certifikáty, jsou nepřístupné, pokud jejich obsahující trezor klíčů je v odstraněném stavu. 
- Název DNS odstraněného trezoru klíčů je vyhrazen, což brání vytvoření nového trezoru klíčů se stejným názvem.  

Odstraněné trezory klíčů stavu přidružené k vašemu předplatnému můžete zobrazit pomocí následujícího příkazu:

```powershell
Get-AzKeyVault -InRemovedState 
```

- *ID* lze použít k identifikaci prostředku při obnovení nebo vymazání. 
- *ID prostředku* je původní ID prostředku tohoto trezoru. Vzhledem k tomu, že tento trezor klíčů je nyní v odstraněném stavu, neexistuje žádný prostředek s tímto ID prostředku. 
- *Plánované datum vyprázdnění je,* když bude úschovna trvale odstraněna, pokud nebude provedena žádná akce. Výchozí doba uchovávání, která se používá k výpočtu *plánovaného data vyprázdnění*, je 90 dní.

## <a name="recovering-a-key-vault"></a>Obnovení trezoru klíčů

Chcete-li obnovit trezor klíčů, zadejte název trezoru klíčů, skupinu prostředků a umístění. Všimněte si umístění a skupiny prostředků odstraněného trezoru klíčů podle potřeby pro proces obnovení.

```powershell
Undo-AzKeyVaultRemoval -VaultName ContosoVault -ResourceGroupName ContosoRG -Location westus
```

Při obnovení trezoru klíčů je vytvořen nový prostředek s původním ID prostředku trezoru klíčů. Pokud je původní skupina prostředků odebrána, musí být před pokusem o obnovení vytvořena se stejným názvem.

## <a name="deleting-and-purging-key-vault-objects"></a>Odstranění a vyprázdnění objektů trezoru klíčů

Následující příkaz odstraní klíč ContosoFirstKey v trezoru klíčů s názvem ContosoVault, který má povoleno obnovitelné odstranění:

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey
```

Pokud je trezor klíčů povolen pro obnovitelné odstranění, odstraněný klíč se stále zdá být odstraněn, pokud explicitně neuvádíte odstraněné klíče. Většina operací s klíčem v odstraněném stavu se nezdaří, s výjimkou výpisu, obnovení, vymazání odstraněného klíče. 

Následující příkaz například uvádí odstraněné klíče v trezoru klíčů ContosoVault:

```powershell
Get-AzKeyVaultKey -VaultName ContosoVault -InRemovedState
```

### <a name="transition-state"></a>Stav přechodu 

Pokud odstraníte klíč v trezoru klíčů s povoleným softwarovým odstraněním, může dokončení přechodu trvat několik sekund. Během tohoto přechodu se může zdát, že klíč není v aktivním stavu nebo odstraněném stavu. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Použití obnovitelného odstranění s objekty trezoru klíčů

Stejně jako trezory klíčů, odstraněný klíč, tajný klíč nebo certifikát zůstanou v odstraněném stavu po dobu až 90 dnů, pokud je neobnovíte nebo nevyčistíte. 

#### <a name="keys"></a>Klíče

Obnovení obnovitelného odstraněného klíče:

```powershell
Undo-AzKeyVaultKeyRemoval -VaultName ContosoVault -Name ContosoFirstKey
```

Chcete-li trvale odstranit (také známý jako vymazání) soft-deleted klíč:

> [!IMPORTANT]
> Vymazání klíče bude trvale odstranit, a to nebude obnovitelné! 

```powershell
Remove-AzKeyVaultKey -VaultName ContosoVault -Name ContosoFirstKey -InRemovedState
```

Akce **obnovení** a **vymazání** mají vlastní oprávnění přidružená k zásadám přístupu k trezoru klíčů. Aby uživatel nebo instanční objekt mohl provést akci **obnovení** nebo **vymazání,** musí mít příslušná oprávnění pro tento klíč nebo tajný klíč. Ve výchozím nastavení není **vymazání** přidáno do zásad přístupu trezoru klíčů, pokud se zástupce "all" používá k udělení všech oprávnění. Je nutné výslovně udělit oprávnění **k vyprázdnění.** 

#### <a name="set-a-key-vault-access-policy"></a>Nastavení zásad přístupu k trezoru klíčů

Následující příkaz user@contoso.com uděluje oprávnění k použití několika operací na klíčích v *contosovault,* včetně **vymazání**:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName ContosoVault -UserPrincipalName user@contoso.com -PermissionsToKeys get,create,delete,list,update,import,backup,restore,recover,purge
```

>[!NOTE] 
> Pokud máte existující trezor klíčů, který právě byl povolen obnovitelné odstranění povoleno, pravděpodobně nemáte **oprávnění k obnovení** a **vymazání.**

#### <a name="secrets"></a>Tajné kódy

Stejně jako klíče, tajné klíče jsou spravovány pomocí vlastních příkazů:

- Odstranit tajný klíč s názvem SQLPassword: 
  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -name SQLPassword
  ```

- Seznam všech odstraněných tajných kódů v trezoru klíčů: 
  ```powershell
  Get-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState
  ```

- Obnovení tajného klíče v odstraněném stavu: 
  ```powershell
  Undo-AzKeyVaultSecretRemoval -VaultName ContosoVault -Name SQLPAssword
  ```

- Vymazání tajného klíče v odstraněném stavu: 

  > [!IMPORTANT]
  > Vymazání tajného klíče jej trvale odstraní a nebude možné jej obnovit!

  ```powershell
  Remove-AzKeyVaultSecret -VaultName ContosoVault -InRemovedState -name SQLPassword
  ```

#### <a name="certificates"></a>Certifikáty

Certifikáty můžete spravovat pomocí následujících příkazů:

- Odstraňte certifikát s názvem SQLPassword: 
  ```powershell
  Remove-AzKeyVaultCertificate -VaultName ContosoVault -Name 'MyCert'
  ```

- Seznam všech odstraněných certifikátů v trezoru klíčů: 
  ```powershell
  Get-AzKeyVaultCertificate -VaultName ContosoVault -InRemovedState
  ```

- Obnovení certifikátu v odstraněném stavu: 
  ```powershell
  Undo-AzKeyVaultCertificateRemoval -VaultName ContosoVault -Name 'MyCert'
  ```

- Vymazání certifikátu v odstraněném stavu: 

  > [!IMPORTANT]
  > Vymazání certifikátu jej trvale odstraní a nebude možné jej obnovit!

  ```powershell
  Remove-AzKeyVaultcertificate -VaultName ContosoVault -Name 'MyCert' -InRemovedState 
  ```
  
## <a name="purging-a-soft-delete-protected-key-vault"></a>Vymazání chráněného trezoru klíčů s měkkým odstraněním

> [!IMPORTANT]
> Vymazání trezoru klíčů nebo jednoho z jeho obsažených objektů jej trvale odstraní, což znamená, že nebude obnovitelný!

Funkce pročištění se používá k trvalému odstranění objektu trezoru klíčů nebo celého trezoru klíčů, který byl dříve odstraněn. Jak je ukázáno v předchozí části, objekty uložené v trezoru klíčů s povolenou funkcí obnovitelného odstranění mohou projít více stavy:
- **Aktivní**: před odstraněním.
- **Obnovitelné smazáno**: po odstranění, které lze uvést a obnovit zpět do aktivního stavu.
- **Trvale odstraněno:** po vyčištění nelze obnovit.


Totéž platí pro trezor klíčů. Chcete-li trvale odstranit úložiště klíčů s měkkým odstraněním a jeho obsah, je nutné vyprázdnit samotný trezor klíčů.

### <a name="purging-a-key-vault"></a>Vymazání trezoru klíčů

Při vymazání trezoru klíčů je trvale odstraněn celý jeho obsah, včetně klíčů, tajných klíčů a certifikátů. Chcete-li vymazat úložiště klíčů s `Remove-AzKeyVault` měkkým `-InRemovedState` odstraněním, použijte příkaz s možností a `-Location location` zadáním umístění odstraněného trezoru klíčů s argumentem. Umístění odstraněného trezoru můžete najít `Get-AzKeyVault -InRemovedState`pomocí příkazu .

```powershell
Remove-AzKeyVault -VaultName ContosoVault -InRemovedState -Location westus
```

### <a name="purge-permissions-required"></a>Je vyžadována oprávnění k vymazání.
- Chcete-li odstranit odstraněný trezor klíčů, potřebuje uživatel oprávnění RBAC k operaci *Microsoft.KeyVault/locations/deletedVaults/purge/action.* 
- Chcete-li vypsat úložiště odstraněných klíčů, potřebuje uživatel oprávnění RBAC k operaci *Microsoft.KeyVault/deletedVaults/read.* 
- Ve výchozím nastavení má tato oprávnění pouze správce předplatného. 

### <a name="scheduled-purge"></a>Naplánované vyčištění

Výpis odstraněných objektů trezoru klíčů také ukazuje, kdy jsou naplánovány na vymazání trezorem klíčů. *Plánované datum vyprázdnění* označuje, kdy bude objekt trezoru klíčů trvale odstraněn, pokud nebude provedena žádná akce. Ve výchozím nastavení je doba uchování odstraněného objektu trezoru klíčů 90 dní.

>[!IMPORTANT]
>Odstraněný objekt úschovny, který je spuštěn polem *Naplánované datum vymazání,* je trvale odstraněn. To není obnovitelné!

## <a name="enabling-purge-protection"></a>Povolení ochrany proti vymazání

Je-li zapnuta ochrana proti vymazání, nelze trezor nebo objekt v odstraněném stavu vymazat, dokud neuplynula doba uchování 90 dnů. Takový trezor nebo objekt lze stále obnovit. Tato funkce poskytuje větší jistotu, že trezor nebo objekt nelze nikdy trvale odstranit, dokud neuběhne doba uchování.

Ochranu proti vymazání můžete povolit pouze v případě, že je povoleno také obnovitelné odstranění. 

Chcete-li při vytváření trezoru zapnout ochranu proti měkkému odstranění i vymazání, použijte rutinu [New-AzKeyVault:](/powershell/module/az.keyvault/new-azkeyvault?view=azps-1.5.0)

```powershell
New-AzKeyVault -Name ContosoVault -ResourceGroupName ContosoRG -Location westus -EnableSoftDelete -EnablePurgeProtection
```

Chcete-li přidat ochranu proti vymazání existujícího úložiště (které již má povolené obnovitelné odstranění), použijte rutiny [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault?view=azps-1.5.0), [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-1.5.0)a [Set-AzResource:](/powershell/module/az.resources/set-azresource?view=azps-1.5.0)

```
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "ContosoVault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true"

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

## <a name="other-resources"></a>Další prostředky

- Přehled funkce obnovitelného odstranění trezoru klíčů najdete v tématu [Přehled obnovitelného odstranění úložiště klíčů Azure](overview-soft-delete.md)Key Vault ).
- Obecný přehled využití úložiště klíčů Azure najdete v tématu [Co je Azure Key Vault?](overview.md)).

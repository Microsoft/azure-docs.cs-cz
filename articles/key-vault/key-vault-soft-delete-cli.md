---
ms.assetid: ''
title: Služba Azure Key Vault – jak používat obnovitelné odstranění s využitím rozhraní příkazového řádku
description: Příklady případu použití obnovitelného odstranění s využitím rozhraní příkazového řádku výstřižky kódu
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 10/15/2018
ms.author: bryanla
ms.openlocfilehash: af2d480e84ca69c0ecd795e38371375e6a71542b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363635"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Jak používat obnovitelné odstranění Key Vault pomocí rozhraní příkazového řádku

Funkce obnovitelného odstranění Azure Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru. Konkrétně obnovitelného odstranění adresy následující scénáře:

- Podpora pro obnovitelné odstranění trezoru klíčů.
- Podpora pro obnovitelné odstranění key vaultu objektů; klíče, tajné kódy a certifikáty

## <a name="prerequisites"></a>Požadavky

- Azure CLI – Pokud nemáte k dispozici tato nastavení pro vaše prostředí, najdete v článku [Správa služby Key Vault pomocí rozhraní příkazového řádku Azure](key-vault-manage-with-cli2.md).

Key Vault konkrétní referenční informace pro rozhraní příkazového řádku, naleznete v tématu [odkaz na Azure CLI Key Vault](https://docs.microsoft.com/cli/azure/keyvault).

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

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nový trezor klíčů

Povolení obnovitelného odstranění pro nový trezor klíčů se provádí v okamžiku vytvoření přidáním příznak povolení obnovitelného odstranění pro vaše vytvořit příkaz.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Ověření povolení obnovitelného odstranění

Chcete-li zkontrolovat, jestli má trezor klíčů povolené obnovitelné odstranění, *zobrazit* příkaz a vyhledejte "Obnovitelné odstranění povolené?" Atribut:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Odstraňuje se trezor klíčů chráněných pomocí obnovitelného odstranění

Příkaz pro odstranění trezoru klíčů změny v chování v závislosti na tom, zda je povoleno obnovitelného odstranění.

> [!IMPORTANT]
>Pokud spustíte následující příkaz pro trezor klíčů, který nemá povolené obnovitelné odstranění, trvale odstraníte tento trezor klíčů a veškerý jeho obsah bez použití možností pro obnovení!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak se obnovitelného odstranění chrání vaše trezory klíčů

S obnovitelným odstraněním povoleno:

- Odstraněný trezor klíčů je odebrán ze skupiny prostředků a je umístěn v vyhrazený obor názvů, přidružený k místu, kde se vytvořila. 
- Odstraněné objekty, jako jsou klíče, tajné kódy a certifikáty, jsou nedostupná, dokud jejich obsahující trezor klíčů není ve stavu odstraněno. 
- Je vyhrazený název DNS pro odstraněný trezor klíčů, brání vytváří nový trezor klíčů se stejným názvem.  

Trezory klíčů stavu odstraněno, přidružených k vašemu předplatnému, mohou zobrazit pomocí následujícího příkazu:

```azurecli
az keyvault list-deleted
```
- *ID* slouží k identifikaci prostředku při obnovení nebo vyprazdňování. 
- *ID prostředku* je původní ID prostředku trezoru. Vzhledem k tomu, že tento trezor klíčů je teď ve stavu odstraněno, neexistuje žádný prostředek s ID tohoto prostředku. 
- *Naplánované datum vyprázdnit* je při trezoru se trvale odstraní, pokud nebyla provedena žádná akce. Výchozí dobu uchování, používá k výpočtu *naplánované datum vyprázdnit*, je 90 dní.

## <a name="recovering-a-key-vault"></a>Obnovení služby key vault

Obnovení služby key vault, zadejte název služby key vault, skupinu prostředků a umístění. Poznamenejte si umístění a skupině prostředků odstraněného trezoru klíčů, podle potřeby pro proces obnovení.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Při obnovení služby key vault je vytvořen nový prostředek s ID služby key vault původního zdroje. Pokud je odebrán původní skupiny prostředků, jeden je nutné vytvořit se stejným názvem před pokusem o obnovení.

## <a name="key-vault-objects-and-soft-delete"></a>Objekty služby Key Vault a obnovitelného odstranění

Pro klíč "ContosoFirstKey" v trezoru klíčů s názvem "ContosoVault" s obnovitelným odstraněním povolena, zde jeho jak odstranili byste tento klíč.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Pomocí trezoru klíčů povolená pro obnovitelné odstranění odstranil klíč stále zobrazena, jako je odstraníme s výjimkou, že při explicitně vypsat nebo získat odstraněné klíče. Většinu operací s klíči ve stavu odstraněno selže s výjimkou výpis odstraněné klíče, obnovení nebo odstraňovat zařízení nepřipojená k ho. 

Například požadavek na seznamu odstraněn klíče v trezoru klíčů, použijte následující příkaz:

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>Přechod stavu 

Při odstranění klíče ve službě key vault s obnovitelným odstraněním povolené, může trvat několik sekund na přechod na dokončení. Během tohoto přechodu je může zdát, že klíč není v aktivním stavu nebo stavu odstraněno. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Použití obnovitelného odstranění s využitím objektů trezoru klíčů

Stejně jako trezorů klíčů odstraněné klíče, tajné nebo certifikát, zůstane ve stavu odstraněno až 90 dnů, pokud ji obnovit nebo ho vymazat.

#### <a name="keys"></a>Klíče

Obnovit klíč obnovitelně odstraněný:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Trvale odstranit, (označované také jako vyprazdňování) obnovitelně odstraněný klíč:

> [!IMPORTANT]
> Odstranění klíče ho trvale odstraníte a nesmí být obnovitelná! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

**Obnovit** a **vyprázdnit** akce mají své vlastní oprávnění přidružené zásady přístupu trezoru klíčů. Pro uživatele nebo instanční objekt, který bude moci být prováděny **obnovit** nebo **vyprázdnit** akce, musí mít odpovídající oprávnění pro tento klíč nebo tajný klíč. Ve výchozím nastavení **vyprázdnit** není přidán do zásady přístupu trezoru klíčů, když místní 'vše' se používá k udělení oprávnění. Konkrétně je nutné udělit **vyprázdnit** oprávnění. 

#### <a name="set-a-key-vault-access-policy"></a>Nastavit zásady přístupu trezoru klíčů

Zadáním následujícího příkazu uděluje user@contoso.com oprávnění používat několik operací s klíči v *ContosoVault* včetně **vyprázdnit**:

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Pokud máte existujícího trezoru klíčů, pro který byla pouze povolené obnovitelné odstranění, pravděpodobně nemáte **obnovit** a **vyprázdnit** oprávnění.

#### <a name="secrets"></a>Tajné kódy

Podobně jako klíče tajné kódy se spravují pomocí vlastních příkazů:

- Odstranění tajného kódu s názvem SQLPassword: 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Vypište všechny odstraněné tajné kódy ve službě key vault: 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Obnovte tajný klíč ve stavu odstraněno: 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Odstranění tajného kódu ve stavu odstraněno: 

  > [!IMPORTANT]
  > Odstranění tajného kódu ho trvale odstraníte a nesmí být obnovitelná! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-and-key-vaults"></a>Trezory klíčů a mazání

### <a name="key-vault-objects"></a>Objekty služby Key vault

Odstranění klíče, tajné nebo certifikát, způsobí trvalé odstranění a ho nepůjde obnovit. Trezor klíčů, který obsahoval odstraněného objektu se nijak nezmění, ale stejně jako všechny ostatní objekty v trezoru klíčů. 

### <a name="key-vaults-as-containers"></a>Trezory klíčů jako kontejnery
Při vymazání se trezor klíčů, jeho celý obsah se trvale odstraní, včetně klíče, tajné kódy a certifikáty. Chcete-li odstranit trezor klíčů, použijte `az keyvault purge` příkazu. Můžete najít umístění vašeho předplatného odstraněných trezorů klíčů pomocí příkazu `az keyvault list-deleted`.

>[!IMPORTANT]
>Odstraňovat zařízení nepřipojená k trezoru klíčů ho trvale odstraníte, což znamená, že ho nepůjde obnovit.

```azurecli
az keyvault purge --location westus --name ContosoVault
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


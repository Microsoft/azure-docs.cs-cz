---
ms.assetid: ''
title: Služba Azure Key Vault – jak používat obnovitelné odstranění s využitím rozhraní příkazového řádku
description: Příklady případu použití obnovitelného odstranění s využitím rozhraní příkazového řádku výstřižky kódu
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: b25d3d7bd5348d4e4ae5dc33362a9d0a2504236e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578598"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Jak používat obnovitelné odstranění Key Vault pomocí rozhraní příkazového řádku

Funkce obnovitelného odstranění Azure Key Vault umožňuje obnovení odstraněných trezorů a objektů trezoru. Konkrétně obnovitelného odstranění adresy následující scénáře:

- Podpora pro obnovitelné odstranění trezoru klíčů.
- Podpora pro obnovitelné odstranění key vaultu objektů; klíče, tajné kódy a certifikáty

## <a name="prerequisites"></a>Požadavky

- Azure CLI 2.0 – Pokud nemáte k dispozici tato nastavení pro vaše prostředí, najdete v článku [Správa služby Key Vault pomocí CLI 2.0](key-vault-manage-with-cli2.md).

Key Vault konkrétní referenční informace pro rozhraní příkazového řádku, naleznete v tématu [odkaz na Azure CLI 2.0 Key Vault](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Požadovaná oprávnění

Operace služby Key Vault jsou řízeny odděleně prostřednictvím přístupu na základě role (RBAC) ovládací prvek oprávnění následujícím způsobem:

| Operace | Popis | Oprávnění uživatele |
|:--|:--|:--|
|Seznam|Seznamy odstraňovat trezory klíčů.|Microsoft.KeyVault/deletedVaults/read|
|Zotavit|Obnoví odstraněný trezor klíčů.|Microsoft.KeyVault/vaults/write|
|Vyprázdnit|Odstraní trvale odstraněný trezor klíčů a veškerý jeho obsah.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Další informace o oprávněních a řízení přístupu najdete v tématu [zabezpečení trezoru klíčů](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Povolení obnovitelného odstranění

Abyste mohli obnovit odstraněný trezor klíčů nebo objekty uložené ve službě key vault, musíte nejprve povolit obnovitelného odstranění pro tento trezor klíčů.

### <a name="existing-key-vault"></a>Existujícího trezoru klíčů

U existujícího trezoru klíčů s názvem ContosoVault povolte obnovitelné odstranění následujícím způsobem. 

>[!NOTE]
>Aktuálně budete muset použít zpracování prostředků Azure Resource Manageru pro zápis přímo *enableSoftDelete* vlastnost na prostředek Key Vault.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nový trezor klíčů

Povolení obnovitelného odstranění pro nový trezor klíčů se provádí v okamžiku vytvoření přidáním příznak povolení obnovitelného odstranění pro vaše vytvořit příkaz.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Ověření povolení obnovitelného odstranění

Chcete-li zkontrolovat, jestli má trezor klíčů povolené obnovitelné odstranění, *zobrazit* příkaz a vyhledejte "Obnovitelné odstranění povolené?" atribut a jeho nastavení hodnotu true nebo false.

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Odstraňuje se trezor klíčů chráněných pomocí obnovitelného odstranění

Příkaz pro odstranění (nebo odebrání) trezor klíčů zůstává stejná, ale jeho chování mění v závislosti na tom, jestli jste povolili obnovitelného odstranění nebo ne.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Při spuštění předchozího příkazu pro trezor klíčů, který nemá povolené obnovitelné odstranění, trvale odstraníte tento trezor klíčů a veškerý jeho obsah bez jakýchkoli možností pro obnovení.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Jak se obnovitelného odstranění chrání vaše trezory klíčů

S obnovitelným odstraněním povoleno:

- Při odstraňování služby key vault, je odebrán ze skupiny prostředků a umístí do vyhrazený obor názvů, který je pouze přidružený k místu, kde se vytvořila. 
- Objekty v odstraněný klíč trezoru, jako jsou klíče, tajné klíče a certifikáty, jsou nedostupná a zůstat tak jejich obsahující trezor klíčů je ve stavu odstraněno. 
- Název DNS pro službu key vault ve stavu odstraněno je stále rezervovaná, takže nejde vytvořit nový trezor klíčů se stejným názvem.  

Trezory klíčů stavu odstraněno, přidružených k vašemu předplatnému, mohou zobrazit pomocí následujícího příkazu:

```azurecli
az keyvault list-deleted
```

*ID prostředku* ve výstupu odkazuje na původní ID prostředku trezoru. Vzhledem k tomu, že tento trezor klíčů je teď ve stavu odstraněno, neexistuje žádný prostředek s ID tohoto prostředku. *Id* pole můžete použít k identifikaci prostředku při obnovení nebo odstraňovat zařízení nepřipojená k. *Naplánované datum vyprázdnit* pole určuje, kdy se trvale odstraní trezor (Vymazat) Pokud nebyla provedena žádná akce pro tento odstraněný trezor. Výchozí dobu uchování, používá k výpočtu *naplánované datum vyprázdnit*, je 90 dní.

## <a name="recovering-a-key-vault"></a>Obnovení služby key vault

Chcete-li obnovit služby key vault, musíte zadat název trezoru klíčů, skupinu prostředků a umístění. Poznámka: umístění a skupině prostředků odstraněného trezoru klíčů, jako je nutné pro proces obnovení trezoru klíčů.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

Při obnovení trezoru klíčů, výsledkem je nový prostředek s ID služby key vault původního zdroje. Pokud byla odebrána skupina prostředků, kde existoval trezoru klíčů, musí před služby key vault je možné obnovit vytvořit novou skupinu prostředků se stejným názvem.

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

Při odstranění klíče ve službě key vault s obnovitelným odstraněním povolené, může trvat několik sekund na přechod na dokončení. Při tomto přechodu stavu může zdát, že klíč není v aktivním stavu nebo stavu odstraněno. Tento příkaz zobrazí všechny odstraněné klíče v trezoru klíčů s názvem "ContosoVault".

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Použití obnovitelného odstranění s využitím objektů trezoru klíčů

Stejně jako trezorů klíčů, odstranil klíč tajný klíč nebo certifikát zůstane ve stavu odstraněno až 90 dnů není-li obnovit nebo ho vymazat. 

#### <a name="keys"></a>Klíče

K obnovení odstraněné klíče:

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Chcete-li trvale odstranit klíč:

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Odstranění klíče ho trvale odstraníte, což znamená, že ho nepůjde obnovit.

**Obnovit** a **vyprázdnit** akce mají své vlastní oprávnění přidružené zásady přístupu trezoru klíčů. Pro uživatele nebo instanční objekt, který bude moci být prováděny **obnovit** nebo **vyprázdnit** akce musí mít odpovídající oprávnění pro tento objekt (klíče nebo tajného klíče) v zásady přístupu trezoru klíčů. Ve výchozím nastavení **vyprázdnit** oprávnění není přidán do zásady přístupu trezoru klíčů, když místní 'vše' se používá k udělení oprávnění pro uživatele. Je nutné explicitně udělit **vyprázdnit** oprávnění. Například následující příkaz uděluje user@contoso.com oprávnění k provádění různých operací s klíči v *ContosoVault* včetně **vyprázdnit**.

#### <a name="set-a-key-vault-access-policy"></a>Nastavit zásady přístupu trezoru klíčů

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Pokud máte existujícího trezoru klíčů, pro který byla pouze povolené obnovitelné odstranění, pravděpodobně nemáte **obnovit** a **vyprázdnit** oprávnění.

#### <a name="secrets"></a>Tajné kódy

Podobně jako klíče tajné kódy ve službě key vault provozovaná s vlastní příkazy. Projdete, jsou uvedené příkazy pro odstranění, výpisu, obnovení a odstraňovat zařízení nepřipojená k tajných kódů.

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
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Odstranění tajného kódu ho trvale odstraníte, což znamená, že ho nepůjde obnovit.

## <a name="purging-and-key-vaults"></a>Trezory klíčů a mazání

### <a name="key-vault-objects"></a>Objekty služby Key vault

Odstranění klíče, tajný klíč nebo certifikát ho trvale odstraníte, což znamená, že ho nepůjde obnovit. Trezor klíčů, který obsahoval odstraněného objektu se nijak nezmění, ale stejně jako všechny ostatní objekty v trezoru klíčů. 

### <a name="key-vaults-as-containers"></a>Trezory klíčů jako kontejnery
Při vymazání se trezor klíčů, veškerý její obsah, včetně klíče, tajné kódy a certifikáty, se trvale odstraní. Chcete-li odstranit trezor klíčů, použijte `az keyvault purge` příkazu. Můžete najít umístění vašeho předplatného odstraněných trezorů klíčů pomocí příkazu `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>Odstraňovat zařízení nepřipojená k trezoru klíčů ho trvale odstraníte, což znamená, že ho nepůjde obnovit.

### <a name="purge-permissions-required"></a>Vyprázdnit oprávněních
- Vymazání odstraněný trezor klíčů, tak, že trezor a veškerý jeho obsah se trvale odeberou, uživatel musí k provedení oprávnění RBAC *Microsoft.KeyVault/locations/deletedVaults/purge/action* operace. 
- Seznam odstraněných klíč trezoru uživatel potřebuje oprávnění RBAC k provedení *Microsoft.KeyVault/deletedVaults/read* oprávnění. 
- Ve výchozím nastavení tato oprávnění má jenom správce předplatného. 

### <a name="scheduled-purge"></a>Naplánované odstranění

Výpis objektů odstraněného trezoru klíčů. zobrazuje se schedled vymazat službou Key Vault. *Naplánované datum vyprázdnit* pole určuje, kdy objekt služby key vault se trvale odstraní, pokud nebyla provedena žádná akce. Ve výchozím nastavení Doba uchování pro objekt odstraněného trezoru klíčů je 90 dní.

>[!NOTE]
>Objekt odstraněný trezor, aktivuje její *naplánované datum vyprázdnit* pole, se trvale odstraní. Se nedá vrátit zpátky.

## <a name="other-resources"></a>Další prostředky

- Přehled funkce obnovitelného odstranění služby Key Vault najdete v tématu [přehled obnovitelného odstranění služby Azure Key Vault](key-vault-ovw-soft-delete.md).
- Obecný přehled o využití služby Azure Key Vault najdete v části [Začínáme s Azure Key Vault](key-vault-get-started.md).


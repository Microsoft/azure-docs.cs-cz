---
title: Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem
titleSuffix: Azure Cognitive Search
description: Doplňte šifrování na straně serveru přes indexy a mapy synonym v Azure Kognitivní hledání pomocí klíčů, které vytvoříte a spravujete v Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899946"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování v klidovém formátu obsahu v Azure Kognitivní hledání používání klíčů spravovaných zákazníkem v Azure Key Vault

Ve výchozím nastavení služba Azure Kognitivní hledání šifruje indexovaný obsah v klidovém stavu pomocí [klíčů spravovaných službou](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Výchozí šifrování můžete doplnit pomocí dalších vrstev šifrování s použitím klíčů, které vytvoříte a spravujete v Azure Key Vault. Tento článek vás provede jednotlivými kroky.

Šifrování na straně serveru je podporováno prostřednictvím integrace s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování šifrovacích klíčů. Pomocí Azure Key Vault můžete také auditovat použití klíče. 

Šifrování pomocí klíčů spravovaných zákazníkem se při vytváření těchto objektů konfiguruje na úrovni mapy index nebo synonym, a ne na úrovni vyhledávací služby. Nemůžete zašifrovat obsah, který už existuje. 

Klíče nemusí být ve stejném Key Vault. Jedna vyhledávací služba může hostovat víc šifrovaných indexů nebo synonym, která se zašifrují vlastními šifrovacími klíči spravovanými zákazníky uloženými v různých trezorech klíčů.  Můžete mít také indexy a mapy synonym ve stejné službě, které nejsou šifrovány pomocí klíčů spravovaných zákazníkem. 

> [!IMPORTANT] 
> Tato funkce je k dispozici na [REST API verze 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) a [.net SDK verze 8,0-Preview](search-dotnet-sdk-migration-version-9.md). V tuto chvíli není v Azure Portal žádná podpora ke konfiguraci šifrovacích klíčů spravovaných zákazníkem. Vyhledávací služba musí být vytvořená po 2019. lednu a nemůže být volná (sdílená) služba.

## <a name="prerequisites"></a>Požadavky

V tomto příkladu se používají následující služby. 

+ [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. 

+ [Vytvořte prostředek Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) nebo v rámci svého předplatného Najděte existující trezor.

+ Pro úlohy konfigurace se používá [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nebo [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) .

+ K volání REST API lze použít [post](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) a [Azure kognitivní hledání SDK](https://aka.ms/search-sdk-preview) . V tuto chvíli není k dispozici žádná podpora portálu pro šifrování spravované zákazníkem.

>[!Note]
> Vzhledem k povaze šifrování s použitím klíčů spravovaných zákazníkem nebude Azure Kognitivní hledání moct načíst vaše data, pokud se odstraní klíč trezoru klíčů Azure. Aby nedošlo ke ztrátě dat způsobenému Key Vault náhodným odstraněním klíčů, **musíte** povolit ochranu před odstraněním a vyprázdněním v Key Vault, aby ji bylo možné použít. Další informace najdete v tématu [Azure Key Vault obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 – povolit obnovení klíče

Po vytvoření prostředku Azure Key Vault Povolte ochranu proti **odstranění** a **vyprázdnění** ve vybraném trezoru klíčů spuštěním následujících příkazů PowerShellu nebo rozhraní příkazového řádku Azure CLI:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2\. vytvoření nového klíče

Pokud k šifrování obsahu Azure Kognitivní hledání používáte existující klíč, přeskočte tento krok.

1. [Přihlaste se k Azure Portal](https://portal.azure.com) a přejděte do řídicího panelu trezoru klíčů.

1. V levém navigačním podokně vyberte nastavení **klíče** a klikněte na **+ Generovat/importovat**.

1. V podokně **vytvořit klíč** klikněte v seznamu **možností**na metodu, kterou chcete použít k vytvoření klíče. Můžete **vygenerovat** nový klíč, **nahrát** existující klíč nebo použít **obnovení zálohy** k výběru zálohy klíče.

1. Zadejte **název** klíče a volitelně vyberte další vlastnosti klíče.

1. Kliknutím na tlačítko **vytvořit** spusťte nasazení.

Poznamenejte si identifikátor klíče – to se skládá z **identifikátoru URI hodnoty klíče**, **názvu klíče**a **verze klíče**. Budete je potřebovat k definování šifrovaného indexu v Azure Kognitivní hledání.
 
![Vytvoří nový klíč trezoru klíčů.](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Vytvoří nový klíč trezoru klíčů.")

## <a name="3---create-a-service-identity"></a>3\. vytvoření identity služby

Přiřazení identity ke službě Search vám umožní udělit službě vyhledávání Key Vault přístupová oprávnění. Vaše vyhledávací služba bude používat svoji identitu k ověřování pomocí trezoru klíčů Azure.

Azure Kognitivní hledání podporuje dva způsoby přiřazení identity: spravovaná identita nebo externě spravovaná Azure Active Directory aplikace. 

Pokud je to možné, použijte spravovanou identitu. Je nejjednodušší způsob, jak přiřadit identitu službě vyhledávání a ve většině scénářů fungovat. Pokud používáte více klíčů pro indexy a mapy synonym nebo pokud je vaše řešení v distribuované architektuře, která ruší ověřování na základě identity, použijte pokročilý [externě spravovaný Azure Active Directory přístup](#aad-app) , který je popsaný na konci tohoto článku.

 Obecně spravovaná identita umožňuje službě vyhledávání ověřovat Azure Key Vault bez uložení přihlašovacích údajů do kódu. Životní cyklus tohoto typu spravované identity je svázán s životním cyklem služby vyhledávání, který může mít pouze jednu spravovanou identitu. [Přečtěte si další informace o spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Pokud chcete vytvořit spravovanou identitu, [Přihlaste se k portálu toAzure](https://portal.azure.com) a otevřete řídicí panel vyhledávací služby. 

1. V levém navigačním podokně klikněte na **Identita** , změňte její stav na **zapnuto**a klikněte na **Uložit**.

![Povolení spravované identity](./media/search-enable-msi/enable-identity-portal.png "Povolení identity spravovaných")

## <a name="4---grant-key-access-permissions"></a>4 – udělení přístupových oprávnění k klíčům

Pokud chcete, aby služba vyhledávání mohla používat váš Key Vault klíč, budete muset udělit vašim vyhledávacím službám určitá přístupová oprávnění.

Přístupová oprávnění by mohla být v daném okamžiku odvolána. Po odvolání se žádný index služby vyhledávání nebo mapa synonym používající tento trezor klíčů stane nepoužitelným. Obnovení přístupových oprávnění trezoru klíčů později obnoví přístup k mapě index\synonym. Další informace najdete v tématu [zabezpečený přístup k trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Přihlaste se k Azure Portal](https://portal.azure.com) a otevřete stránku s přehledem trezoru klíčů. 

1. V levém navigačním podokně vyberte nastavení **zásady přístupu** a klikněte na **+ Přidat nový**.

   ![Přidat nové zásady přístupu trezoru klíčů](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Přidat nové zásady přístupu trezoru klíčů")

1. Klikněte na **Vybrat objekt zabezpečení** a vyberte službu Azure kognitivní hledání. Můžete ho vyhledat podle názvu nebo ID objektu, které se zobrazilo po povolení spravované identity.

   ![Výběr objektu zásad přístupu trezoru klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Výběr objektu zásad přístupu trezoru klíčů")

1. Klikněte na **klíčová oprávnění** a vyberte *získat*, *Rozbalit klíč* a *zabalit klíč*. Pomocí šablony *Azure Data Lake Storage nebo Azure Storage* můžete rychle vybrat požadovaná oprávnění.

   Pro Azure Kognitivní hledání musí být udělená následující [přístupová oprávnění](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Get* – umožní službě vyhledávání načíst veřejné části klíče ve Key Vault
   * *Zalamovat klíč* – umožní službě vyhledávání používat klíč k ochraně interního šifrovacího klíče.
   * *Rozbalení klíče* – umožní službě vyhledávání používat klíč k rozbalení interního šifrovacího klíče.

   ![Výběr oprávnění klíče zásad přístupu trezoru klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Výběr oprávnění klíče zásad přístupu trezoru klíčů")

1. Klikněte na **OK** a **uložte** změny zásad přístupu.

> [!Important]
> Šifrovaný obsah ve službě Azure Kognitivní hledání je nakonfigurovaný tak, aby používal konkrétní Azure Key Vault klíč s určitou **verzí**. Pokud změníte klíč nebo verzi, je nutné aktualizovat index nebo mapu synonym, aby používaly nové key\version **před** odstraněním předchozího key\version. Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné. po ztrátě přístupu ke klíči nebude možné obsah dešifrovat.   

## <a name="5---encrypt-content"></a>5\. šifrování obsahu

Vytvoření mapy indexů nebo synonym šifrovaných pomocí klíče spravovaného zákazníkem zatím není možné pomocí Azure Portal. Pomocí služby Azure Kognitivní hledání REST API vytvořte takový index nebo mapu synonym.

Index i mapa synonym podporují novou vlastnost **EncryptionKey** na nejvyšší úrovni, která se používá k zadání klíče. 

Pomocí **identifikátoru URI trezoru klíčů**, **názvu klíče** a **verze** klíče trezoru klíčů můžeme vytvořit definici **EncryptionKey** :

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
> [!Note] 
> Žádná z těchto podrobností trezoru klíčů není považována za tajnou a lze ji snadno načíst procházením příslušné Azure Key Vault klíčovou stránkou v Azure Portal.

Pokud používáte aplikaci AAD pro Key Vault ověřování namísto použití spravované identity, přidejte **přihlašovací údaje** k aplikaci AAD do svého šifrovacího klíče: 
```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

## <a name="example-index-encryption"></a>Příklad: šifrování indexu
Podrobnosti o vytvoření nového indexu prostřednictvím REST API najdete v tématu [vytvoření indexu (Azure Kognitivní hledání REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), kde jediným rozdílem je zadání podrobností šifrovacího klíče v rámci definice indexu: 

```json
{
 "name": "hotels",  
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
 "encryptionKey": {
   "keyVaultUri": "https://demokeyvault.vault.azure.net",
   "keyVaultKeyName": "myEncryptionKey",
   "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
 }
}
```
Nyní můžete odeslat požadavek na vytvoření indexu a pak začít používat rejstřík normálně.

## <a name="example-synonym-map-encryption"></a>Příklad: šifrování mapování synonym

Podrobnosti o vytvoření nové mapy synonym prostřednictvím REST API najdete v tématu [vytvoření mapy synonym (Azure Kognitivní hledání REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), kde jediným rozdílem je zadání podrobností šifrovacího klíče v rámci definice mapy synonym: 

```json
{   
  "name" : "synonymmap1",  
  "format" : "solr",  
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
  }
}
```
Nyní můžete odeslat požadavek na vytvoření mapy synonym a pak ho začít používat normálně.

>[!Important] 
> I když **EncryptionKey** nejde přidat k existujícím indexům kognitivní hledání Azure nebo mapováním synonym, může se aktualizovat zadáním různých hodnot pro všechny tři podrobnosti trezoru klíčů (například aktualizace verze klíče). Když se změní na nový klíč Key Vault nebo na novou verzi klíče, musí se nejdřív aktualizovat index Azure Kognitivní hledání nebo mapa synonym, která tento klíč používá, aby používala nový key\version **před** odstraněním předchozího key\version. Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné, protože po ztrátě přístupu ke klíči nebude moct obsah dešifrovat.   
> Obnovení přístupu k obsahu později obnoví přístupová oprávnění trezoru klíčů.

## <a name="aad-app"></a>Upřesnit: použití externě spravované aplikace Azure Active Directory

Pokud není možná spravovaná identita, můžete vytvořit aplikaci Azure Active Directory s objektem zabezpečení pro službu Azure Kognitivní hledání. Konkrétně spravovaná identita není za těchto podmínek životaschopná:

* Přístup k trezoru klíčů nelze udělit přímo vašim oprávněním služby Search (například pokud je vyhledávací služba v jiném tenantovi služby Active Directory než Azure Key Vault).

* Pro hostování více šifrovaných map indexes\synonym je potřeba jedna vyhledávací služba, z nichž každá používá jiný klíč z jiného trezoru klíčů, kde každý Trezor klíčů musí pro ověřování použít **jinou identitu** . Pokud používáte jinou identitu pro správu různých trezorů klíčů, není nutné zvážit použití možnosti spravovaná identita výše.  

Pro uspokojení takových topologií Azure Kognitivní hledání podporuje použití aplikací Azure Active Directory (AAD) k ověřování mezi vaší službou vyhledávání a Key Vault.    
Vytvoření aplikace AAD na portálu:

1. [Vytvořte aplikaci Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Získejte ID aplikace a ověřovací klíč](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , protože se budou vyžadovat pro vytvoření šifrovaného indexu. Hodnoty, které budete muset zadat, zahrnují **ID aplikace** a **ověřovací klíč**.

>[!Important]
> Pokud se rozhodnete použít aplikaci AAD ověřování místo spravované identity, zvažte skutečnost, že Azure Kognitivní hledání nemá oprávnění ke správě vaší aplikace AAD vaším jménem a je až do správy aplikace AAD, jako je například pravidelná. rotace ověřovacího klíče aplikace
> Při změně aplikace AAD nebo jejího ověřovacího klíče se musí nejdřív aktualizovat index služby Azure Kognitivní hledání nebo mapa synonym, která tuto aplikaci používá, aby používala novou aplikaci ID\key **před** odstraněním předchozí aplikace nebo jejího autorizačního klíče a před tím, než odvoláte Key Vault přístup k ní.
> Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné, protože po ztrátě přístupu ke klíči nebude moct obsah dešifrovat.   

## <a name="next-steps"></a>Další kroky

Pokud nejste obeznámeni s architekturou zabezpečení Azure, přečtěte si [dokumentaci k zabezpečení Azure](https://docs.microsoft.com/azure/security/)a zejména tento článek:

> [!div class="nextstepaction"]
> [Šifrování neaktivních dat](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

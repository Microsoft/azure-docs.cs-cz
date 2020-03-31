---
title: Šifrování v klidovém stavu pomocí klíčů spravovaných zákazníkem
titleSuffix: Azure Cognitive Search
description: Doplňte šifrování na straně serveru přes indexy a mapy synonym v Azure Cognitive Search pomocí klíčů, které vytvoříte a spravujete v Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: cb17fe24339ad618229b3456ece15c206f79bdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899946"
---
# <a name="encryption-at-rest-of-content-in-azure-cognitive-search-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování v úložišti obsahu v Azure Cognitive Search pomocí klíčů spravovaných zákazníky v Azure Key Vault

Ve výchozím nastavení Azure Cognitive Search šifruje indexovaný obsah v klidovém stavu pomocí [klíčů spravovaných službou](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#data-encryption-models). Výchozí šifrování můžete doplnit o další vrstvu šifrování pomocí klíčů, které vytvoříte a spravujete v azure key vaultu. Tento článek vás provede kroky.

Šifrování na straně serveru je podporováno prostřednictvím integrace s [azure key vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault je API pro generování šifrovacích klíčů. Pomocí služby Azure Key Vault můžete také auditovat využití klíčů. 

Šifrování pomocí klíčů spravovaných zákazníkem je konfigurováno na úrovni indexu nebo mapy synonyma při vytvoření těchto objektů, nikoli na úrovni vyhledávací služby. Obsah, který již existuje, nelze šifrovat. 

Klíče nemusí být všechny ve stejném trezoru klíčů. Jedna vyhledávací služba může hostovat více šifrovaných indexů nebo map synonym, z nichž každý je zašifrován vlastními šifrovacími klíči spravovanými zákazníky uloženými v různých trezorech klíčů.  Můžete také mít indexy a synonyma mapy ve stejné službě, které nejsou šifrovány pomocí klíčů spravovaných zákazníkem. 

> [!IMPORTANT] 
> Tato funkce je k dispozici na [rozhraní REST API verze 2019-05-06](https://docs.microsoft.com/rest/api/searchservice/) a [.NET SDK verze 8.0-preview](search-dotnet-sdk-migration-version-9.md). Momentálně neexistuje žádná podpora konfigurace šifrovacích klíčů spravovaných zákazníky na webu Azure Portal. Vyhledávací služba musí být vytvořena po lednu 2019 a nemůže být bezplatnou (sdílenou) službou.

## <a name="prerequisites"></a>Požadavky

V tomto příkladu se používají následující služby. 

+ [Vytvořte službu Azure Cognitive Search](search-create-service-portal.md) nebo [najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) v rámci aktuálního předplatného. 

+ [Vytvořte prostředek Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) nebo najděte existující trezor v rámci vašeho předplatného.

+ [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nebo [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) se používá pro úlohy konfigurace.

+ [Pošťák](search-get-started-postman.md), [Azure PowerShell](search-create-index-rest-api.md) a [Azure Cognitive Search SDK](https://aka.ms/search-sdk-preview) se dá použít k volání rozhraní REST API. V tuto chvíli neexistuje žádná podpora portálu pro šifrování spravované zákazníkem.

>[!Note]
> Vzhledem k povaze šifrování pomocí funkce klíčů spravovaných zákazníkem nebude Azure Cognitive Search moct načíst vaše data, pokud se odstraní klíč trezoru Azure Key. Chcete-li zabránit ztrátě dat způsobené náhodným odstraněním klíčů trezoru klíčů, **musíte** před použitím povolit funkci Obnovitelné odstranění a odstranění v trezoru klíčů. Další informace naleznete v [tématu Azure Key Vault obnovitelné odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="1---enable-key-recovery"></a>1 - Povolit obnovení klíče

Po vytvoření prostředku azure key vault povolte **soft delete** a **purge protection** ve vybraném trezoru klíčů provedením následujících příkazů PowerShellu nebo Azure CLI:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2 - Vytvoření nového klíče

Pokud používáte existující klíč k šifrování obsahu Azure Cognitive Search, tento krok přeskočte.

1. [Přihlaste se na portál Azure portal](https://portal.azure.com) a přejděte na řídicí panel trezoru klíčů.

1. V levém navigačním podokně vyberte nastavení **Klávesy** a klepněte na tlačítko **+ Generovat/importovat**.

1. V **podokně Vytvořit klíč** vyberte ze seznamu **Možnosti**metodu, kterou chcete použít k vytvoření klíče. Můžete **vygenerovat** nový klíč, **nahrát** existující klíč nebo pomocí **nástroje Obnovit zálohování** vybrat zálohu klíče.

1. Zadejte **název** klíče a volitelně vyberte další vlastnosti klíče.

1. Kliknutím na tlačítko **Vytvořit** spusťte nasazení.

Poznamenejte si identifikátor klíče – skládá se z **hodnoty klíče Uri**, názvu **klíče**a **verze klíče**. Budete je potřebovat k definování šifrovaného indexu v Azure Cognitive Search.
 
![Vytvoření nového klíče trezoru klíčů](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Vytvoření nového klíče trezoru klíčů")

## <a name="3---create-a-service-identity"></a>3 - Vytvoření identity služby

Přiřazení identity vyhledávací službě umožňuje udělit vyhledávací službě přístupová oprávnění trezoru klíčů. Vyhledávací služba použije svou identitu k ověření pomocí trezoru Azure Key.

Azure Cognitive Search podporuje dva způsoby přiřazování identity: spravovanou identitu nebo externě spravovanou aplikaci Azure Active Directory. 

Pokud je to možné, použijte spravovanou identitu. Jedná se o nejjednodušší způsob přiřazení identity k vyhledávací službě a měl by fungovat ve většině scénářů. Pokud používáte více klíčů pro indexy a mapy synonym nebo pokud je vaše řešení v distribuované architektuře, která dekvalifikuje ověřování založené na identitě, použijte pokročilý [externě spravovaný přístup Azure Active Directory](#aad-app) popsaný na konci tohoto článku.

 Obecně platí, že spravovaná identita umožňuje vaší vyhledávací službě ověřit se v azure key vaultu bez ukládání přihlašovacích údajů v kódu. Životní cyklus tohoto typu spravované identity je vázán na životní cyklus vaší vyhledávací služby, která může mít pouze jednu spravovanou identitu. [Další informace o spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Pokud chcete vytvořit spravovanou identitu, [přihlaste se na portál Azure](https://portal.azure.com) a otevřete řídicí panel vyhledávací služby. 

1. V levém navigačním podokně klepněte na **položku Identita,** změňte její stav **na Zapnuto**a klepněte na **tlačítko Uložit**.

![Povolení spravované identity](./media/search-enable-msi/enable-identity-portal.png "Povolení manged identity")

## <a name="4---grant-key-access-permissions"></a>4 - Udělení přístupových oprávnění klíče

Chcete-li vyhledávací službě povolit použití klíče trezoru klíčů, budete muset vyhledávací službě udělit určitá přístupová oprávnění.

Přístupová oprávnění mohou být kdykoli odvolána. Po odvolání se všechny indexy vyhledávacích služeb nebo mapy synonym, které používají tento trezor klíčů, stanou nepoužitelnými. Obnovení mačkání oprávnění k trezoru klíčů později obnoví přístup k mapování index\synonymum. Další informace naleznete [v tématu Zabezpečený přístup k trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Přihlaste se na Portál Azure](https://portal.azure.com) a otevřete stránku s přehledem trezoru klíčů. 

1. V levém navigačním podokně vyberte nastavení **zásad aplikace Access** a klepněte na tlačítko **+Přidat nové**.

   ![Přidání nové zásady přístupu k trezoru klíčů](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Přidání nové zásady přístupu k trezoru klíčů")

1. Klikněte na **Vybrat hlavní a** vyberte službu Azure Cognitive Search. Můžete vyhledat podle názvu nebo ID objektu, který byl zobrazen po povolení spravované identity.

   ![Vybrat zásadu přístupu k trezoru klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Vybrat zásadu přístupu k trezoru klíčů")

1. Klikněte na **Oprávnění klíče** a vyberte *Získat*, *Rozbalit klíč* a *Zalamovat klíč*. Pomocí šablony *Azure Data Lake Storage nebo Azure Storage* můžete rychle vybrat požadovaná oprávnění.

   Azure Cognitive Search musí být uděleno s následujícími [přístupovými oprávněními](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Get* - umožňuje vyhledávací službě načíst veřejné části klíče v trezoru klíčů
   * *Wrap Key* - umožňuje vyhledávací službě používat klíč k ochraně vnitřního šifrovacího klíče
   * *Rozbalit klíč* - umožňuje vyhledávací službě použít klíč k rozbalení interního šifrovacího klíče

   ![Vybrat oprávnění klíče zásad přístupu trezoru klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Vybrat oprávnění klíče zásad přístupu trezoru klíčů")

1. Klikněte na **OK** a **Uložte** změny zásad přístupu.

> [!Important]
> Šifrovaný obsah ve službě Azure Cognitive Search je nakonfigurovaný tak, aby používal konkrétní klíč Azure Key Vault s konkrétní **verzí**. Pokud změníte klíč nebo verzi, musí být index nebo mapa synonym aktualizována, aby používala nový klíč\verze **před** odstraněním předchozího klíče\verze. Pokud tak neučiníte, bude index nebo synonymum mapy nepoužitelný, na vás nebude moci dešifrovat obsah, jakmile dojde ke ztrátě přístupu ke klíči.   

## <a name="5---encrypt-content"></a>5 - Šifrování obsahu

Vytvoření mapy indexu nebo synonym zašifrované klíčem spravovaným zákazníkem ještě není možné pomocí portálu Azure Portal. Pomocí rozhraní AZURE Cognitive Search REST API vytvořte takový index nebo mapu synonym.

Mapa indexu i synonyma podporují novou vlastnost **šifrování Key** nejvyšší úrovně, která slouží k určení klíče. 

Pomocí **trezoru klíčů Uri**, **název klíče** a **klíčové verze** klíče trezoru klíčů, můžeme vytvořit **encryptionKey** definice:

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
> Žádný z těchto podrobností trezoru klíčů jsou považovány za tajné a lze snadno získat procházením příslušné stránky klíče Azure Key Vault na webu Azure Portal.

Pokud používáte aplikaci AAD pro ověřování trezoru klíčů namísto spravované identity, přidejte k šifrovacímu **klíči přihlašovací údaje pro aplikaci** AAD: 
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

## <a name="example-index-encryption"></a>Příklad: Indexové šifrování
Podrobnosti o vytvoření nového indexu prostřednictvím rozhraní REST API lze nalézt na [vytvořit index (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), kde jediný rozdíl zde je určení podrobnosti o šifrovací klíč jako součást definice indexu: 

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
Nyní můžete odeslat požadavek na vytvoření indexu a začít používat index normálně.

## <a name="example-synonym-map-encryption"></a>Příklad: Šifrování mapy synonym

Podrobnosti o vytvoření nové mapy synonymprostřednictvím rozhraní REST API najdete na [webu Create Synonym Map (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), kde jediným rozdílem je určení podrobností o šifrovacím klíči jako součást definice mapy synonyma: 

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
Nyní můžete odeslat žádost o vytvoření mapy synonyma a pak ji začít používat normálně.

>[!Important] 
> Zatímco **encryptionKey** nelze přidat do existujících indexů Azure Cognitive Search nebo synonymmap, může být aktualizován poskytnutím různých hodnot pro všechny tři podrobnosti trezoru klíčů (například aktualizace verze klíče). Při přechodu na nový klíč trezoru klíčů nebo novou verzi klíče, všechny Azure Cognitive Search index nebo synonymum mapy, která používá klíč musí být nejprve aktualizovány použít nový klíč\verze **před** odstraněním předchozí klíč\verze. Pokud tak neučiníte, bude index nebo synonymum mapy nepoužitelný, protože nebude moci dešifrovat obsah po ztrátě přístupu ke klíči.   
> Obnovení přístupových oprávnění trezoru klíčů později obnoví přístup k obsahu.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a>Upřesnit: Použití externě spravované aplikace Azure Active Directory

Pokud spravovanou identitu není možné, můžete vytvořit aplikaci Azure Active Directory s objektem zabezpečení pro službu Azure Cognitive Search. Konkrétně spravovaná identita není životaschopná za těchto podmínek:

* Nelze přímo udělit přístupová oprávnění vyhledávací služby trezoru klíčů (například pokud je vyhledávací služba v jiném tenantovi služby Active Directory než trezor klíčů Azure).

* Jedna vyhledávací služba je vyžadována k hostování více šifrovaných indexů\synonymmap, z nichž každá používá jiný klíč z jiného trezoru klíčů, kde každý trezor klíčů musí pro ověřování používat **jinou identitu.** Pokud použití jiné identity ke správě různých trezorů klíčů není požadavek, zvažte použití výše uvedené možnosti spravované identity.  

Pro přizpůsobení těchto topologií podporuje Azure Cognitive Search použití aplikací Azure Active Directory (AAD) pro ověřování mezi vyhledávací službou a trezorem klíčů.    
Vytvoření aplikace AAD na portálu:

1. [Vytvořte aplikaci Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Získejte ID aplikace a ověřovací klíč,](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) protože ty budou vyžadovány pro vytvoření šifrovaného indexu. Mezi hodnoty, které budete muset zadat, patří **ID aplikace** a **ověřovací klíč**.

>[!Important]
> Při rozhodování o použití aplikace ověřování AAD namísto spravované identity zvažte skutečnost, že Azure Cognitive Search není oprávněn spravovat vaši aplikaci AAD vaším jménem a je na vás, abyste mohli spravovat aplikaci AAD, jako je například periodická střídání ověřovacího klíče aplikace.
> Při změně aplikace AAD nebo jejího ověřovacího klíče musí být index Azure Cognitive Search nebo mapa synonym, která používá tuto aplikaci, nejprve aktualizován, aby používal nové ID\klíč aplikace **před** odstraněním předchozí aplikace nebo jejího autorizačního klíče a před zrušením přístupu k trezoru klíčů.
> Pokud tak neučiníte, bude index nebo synonymum mapy nepoužitelný, protože nebude moci dešifrovat obsah po ztrátě přístupu ke klíči.   

## <a name="next-steps"></a>Další kroky

Pokud nejste obeznámeni s architekturou zabezpečení Azure, přečtěte si [dokumentaci zabezpečení Azure](https://docs.microsoft.com/azure/security/)a zejména tento článek:

> [!div class="nextstepaction"]
> [Šifrování neaktivních dat](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)

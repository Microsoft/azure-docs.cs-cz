---
title: Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem
titleSuffix: Azure Cognitive Search
description: Doplňte šifrování na straně serveru přes indexy a mapy synonym v Azure Kognitivní hledání pomocí klíčů, které vytvoříte a spravujete v Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: 2dc7458dd905ff84455927c81b4ea93765d4f5cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88928815"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurace klíčů spravovaných zákazníkem pro šifrování dat v Azure Kognitivní hledání

Azure Kognitivní hledání automaticky šifruje indexovaný obsah v klidovém [provozu pomocí klíčů spravovaných službou](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Pokud potřebujete další ochranu, můžete výchozí šifrování doplnit o další vrstvu šifrování pomocí klíčů, které vytvoříte a spravujete v Azure Key Vault. Tento článek vás provede jednotlivými kroky nastavení šifrování CMK.

Šifrování CMK závisí na [Azure Key Vault](../key-vault/general/overview.md). Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování šifrovacích klíčů. Pomocí Azure Key Vault můžete také auditovat použití klíče, pokud [povolíte protokolování](../key-vault/general/logging.md).  

Šifrování pomocí klíčů spravovaných zákazníkem se používá pro jednotlivé indexy nebo mapy synonym při vytváření těchto objektů a není zadáno na samotné úrovni služby vyhledávání. Šifrovat lze pouze nové objekty. Nemůžete zašifrovat obsah, který už existuje.

Klíče nemusí být ve stejném trezoru klíčů. Jedna vyhledávací služba může hostovat několik šifrovaných indexů nebo map synonym, z nichž každý je zašifrovaný pomocí vlastních šifrovacích klíčů spravovaných zákazníkem, uložený v různých trezorech klíčů. Můžete mít také indexy a mapy synonym ve stejné službě, které nejsou šifrovány pomocí klíčů spravovaných zákazníkem. 

## <a name="double-encryption"></a>Dvojité šifrování

Pro služby vytvořené od 1. srpna 2020 a v určitých oblastech zahrnuje rozsah šifrování CMK dočasné disky a poskytuje [Úplné šifrování s dvojitou přesností](search-security-overview.md#double-encryption), které jsou aktuálně k dispozici v těchto oblastech: 

+ Západní USA 2
+ East US
+ Středojižní USA
+ USA (Gov) – Virginia
+ USA (Gov) – Arizona

Pokud používáte jinou oblast nebo službu vytvořenou před 1. srpna, bude šifrování CMK omezené jenom na datový disk, a to s výjimkou dočasných disků, které služba používá.

## <a name="prerequisites"></a>Požadavky

V tomto příkladu se používají následující služby a služby. 

+ [Vytvořte službu Azure kognitivní hledání](search-create-service-portal.md) nebo [Najděte existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 

+ [Vytvořte prostředek Azure Key Vault](../key-vault/secrets/quick-create-portal.md#create-a-vault) nebo Najděte existující trezor ve stejném předplatném jako Azure kognitivní hledání. Tato funkce má stejný požadavek na předplatné.

+ Pro úlohy konfigurace se používá [Azure PowerShell](/powershell/azure/) nebo [Azure CLI](/cli/azure/install-azure-cli) .

+ [Post](search-get-started-postman.md), [Azure POWERSHELL](./search-get-started-powershell.md) a [.NET SDK preview](https://aka.ms/search-sdk-preview) lze použít k volání REST API, která vytvoří indexy a mapy synonym, které obsahují parametr šifrovacího klíče. V tuto chvíli není k dispozici žádná podpora portálu pro přidání klíče do indexů nebo map synonym.

>[!Note]
> Vzhledem k povaze šifrování s použitím klíčů spravovaných zákazníkem nebude Azure Kognitivní hledání moct načíst vaše data, pokud se odstraní klíč trezoru klíčů Azure. Aby nedošlo ke ztrátě dat způsobené náhodným odstraněním Key Vault klíčů, musí být v trezoru klíčů povolená ochrana před odstraněním a vymazáním. Možnost obnovitelného odstranění je ve výchozím nastavení povolená, takže dojde k problémům jenom v případě, že jste ho záměrně zakázali. Ve výchozím nastavení není ochrana vyprázdnění povolená, ale vyžaduje se pro šifrování Azure Kognitivní hledání CMK. Další informace najdete v tématu přehledy ochrany proti [tichému odstranění](../key-vault/general/soft-delete-overview.md) a [vyprázdnění](../key-vault/general/soft-delete-overview.md#purge-protection) .

## <a name="1---enable-key-recovery"></a>1 – povolit obnovení klíče

V trezoru klíčů musí být povolená **Ochrana před** **příčtením a odstraněním** . Tyto funkce můžete nastavit pomocí portálu nebo následujících příkazů PowerShellu nebo rozhraní příkazového řádku Azure CLI.

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

1. Spusťte `Connect-AzAccount` a nastavte přihlašovací údaje Azure.

1. Spuštěním následujícího příkazu se připojte k trezoru klíčů a nahraďte ho `<vault_name>` platným názvem:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault se vytvoří s povolenou možnost obnovitelného odstranění. Pokud je v trezoru zakázaná, spusťte následující příkaz:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Povolit ochranu vyprázdnění:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Uložte své aktualizace:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Použití Azure CLI

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

## <a name="2---create-a-new-key"></a>2. vytvoření nového klíče

Pokud k šifrování obsahu Azure Kognitivní hledání používáte existující klíč, přeskočte tento krok.

1. [Přihlaste se k Azure Portal](https://portal.azure.com) a otevřete stránku s přehledem trezoru klíčů.

1. V levém navigačním podokně vyberte nastavení **klíče** a klikněte na **+ Generovat/importovat**.

1. V podokně **vytvořit klíč** klikněte v seznamu **možností**na metodu, kterou chcete použít k vytvoření klíče. Můžete **vygenerovat** nový klíč, **nahrát** existující klíč nebo použít **obnovení zálohy** k výběru zálohy klíče.

1. Zadejte **název** klíče a volitelně vyberte další vlastnosti klíče.

1. Kliknutím na tlačítko **vytvořit** spusťte nasazení.

Poznamenejte si identifikátor klíče – to se skládá z **identifikátoru URI hodnoty klíče**, **názvu klíče**a **verze klíče**. Budete je potřebovat k definování šifrovaného indexu v Azure Kognitivní hledání.
 
![Vytvoří nový klíč trezoru klíčů.](./media/search-manage-encryption-keys/create-new-key-vault-key.png "Vytvoří nový klíč trezoru klíčů.")

## <a name="3---create-a-service-identity"></a>3. vytvoření identity služby

Přiřazení identity ke službě Search vám umožní udělit službě vyhledávání Key Vault přístupová oprávnění. Vaše vyhledávací služba bude používat svoji identitu k ověřování pomocí trezoru klíčů Azure.

Azure Kognitivní hledání podporuje dva způsoby přiřazení identity: spravovaná identita nebo externě spravovaná Azure Active Directory aplikace. 

Pokud je to možné, použijte spravovanou identitu. Je nejjednodušší způsob, jak přiřadit identitu službě vyhledávání a ve většině scénářů fungovat. Pokud používáte více klíčů pro indexy a mapy synonym nebo pokud je vaše řešení v distribuované architektuře, která ruší ověřování na základě identity, použijte pokročilý [externě spravovaný Azure Active Directory přístup](#aad-app) , který je popsaný na konci tohoto článku.

 Obecně spravovaná identita umožňuje službě vyhledávání ověřovat Azure Key Vault bez uložení přihlašovacích údajů do kódu. Životní cyklus tohoto typu spravované identity je svázán s životním cyklem služby vyhledávání, který může mít pouze jednu spravovanou identitu. [Přečtěte si další informace o spravovaných identitách](../active-directory/managed-identities-azure-resources/overview.md).

1. [Přihlaste se k Azure Portal](https://portal.azure.com) a otevřete stránku Přehled vyhledávací služby. 

1. V levém navigačním podokně klikněte na **Identita** , změňte její stav na **zapnuto**a klikněte na **Uložit**.

![Povolení spravované identity](./media/search-enable-msi/enable-identity-portal.png "Povolení identity spravovaných")

## <a name="4---grant-key-access-permissions"></a>4 – udělení přístupových oprávnění k klíčům

Pokud chcete, aby služba vyhledávání mohla používat váš Key Vault klíč, budete muset udělit vašim vyhledávacím službám určitá přístupová oprávnění.

Přístupová oprávnění by mohla být v daném okamžiku odvolána. Po odvolání se žádný index služby vyhledávání nebo mapa synonym používající tento trezor klíčů stane nepoužitelným. Obnovení přístupových oprávnění trezoru klíčů později obnoví přístup k mapě index\synonym. Další informace najdete v tématu [zabezpečený přístup k trezoru klíčů](../key-vault/general/secure-your-key-vault.md).

1. [Přihlaste se k Azure Portal](https://portal.azure.com) a otevřete stránku s přehledem trezoru klíčů. 

1. V levém navigačním podokně vyberte nastavení **zásady přístupu** a klikněte na **+ Přidat nový**.

   ![Přidat nové zásady přístupu trezoru klíčů](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "Přidat nové zásady přístupu trezoru klíčů")

1. Klikněte na **Vybrat objekt zabezpečení** a vyberte službu Azure kognitivní hledání. Můžete ho vyhledat podle názvu nebo ID objektu, které se zobrazilo po povolení spravované identity.

   ![Výběr objektu zásad přístupu trezoru klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "Výběr objektu zásad přístupu trezoru klíčů")

1. Klikněte na **klíčová oprávnění** a vyberte *získat*, *Rozbalit klíč* a *zabalit klíč*. Pomocí šablony *Azure Data Lake Storage nebo Azure Storage* můžete rychle vybrat požadovaná oprávnění.

   Pro Azure Kognitivní hledání musí být udělená následující [přístupová oprávnění](../key-vault/keys/about-keys.md#key-operations):

   * *Get* – umožní službě vyhledávání načíst veřejné části klíče ve Key Vault
   * *Zalamovat klíč* – umožní službě vyhledávání používat klíč k ochraně interního šifrovacího klíče.
   * *Rozbalení klíče* – umožní službě vyhledávání používat klíč k rozbalení interního šifrovacího klíče.

   ![Výběr oprávnění klíče zásad přístupu trezoru klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "Výběr oprávnění klíče zásad přístupu trezoru klíčů")

1. V případě **oprávnění tajného klíče**vyberte *získat*.

1. V případě **oprávnění certifikátu**vyberte *získat*.

1. Klikněte na **OK** a **uložte** změny zásad přístupu.

> [!Important]
> Šifrovaný obsah ve službě Azure Kognitivní hledání je nakonfigurovaný tak, aby používal konkrétní Azure Key Vault klíč s určitou **verzí**. Pokud změníte klíč nebo verzi, je nutné aktualizovat index nebo mapu synonym, aby používaly nové key\version **před** odstraněním předchozího key\version.. Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné. po ztrátě přístupu ke klíči nebude možné obsah dešifrovat.   

## <a name="5---encrypt-content"></a>5. šifrování obsahu

Chcete-li přidat klíč spravovaný zákazníkem na mapě nebo mapování synonym, je nutné použít [REST API vyhledávání](/rest/api/searchservice/) nebo sadu SDK. Portál nevystavuje mapy synonym ani vlastnosti šifrování. Když použijete platné rozhraní API, indexy i mapy synonym podporují vlastnost **EncryptionKey** nejvyšší úrovně. 

Pomocí **identifikátoru URI trezoru klíčů**, **názvu klíče** a **verze** klíče trezoru klíčů vytvořte definici **EncryptionKey** následujícím způsobem:

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
Podrobnosti o vytvoření nového indexu prostřednictvím REST API najdete v tématu [vytvoření indexu (Azure Kognitivní hledání REST API)](/rest/api/searchservice/create-index), kde jediným rozdílem je zadání podrobností šifrovacího klíče v rámci definice indexu: 

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

Podrobnosti o vytvoření nové mapy synonym prostřednictvím REST API najdete v tématu [vytvoření mapy synonym (Azure Kognitivní hledání REST API)](/rest/api/searchservice/create-synonym-map), kde jediným rozdílem je zadání podrobností šifrovacího klíče v rámci definice mapy synonym: 

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
> I když **EncryptionKey** nejde přidat k existujícím indexům kognitivní hledání Azure nebo mapováním synonym, může se aktualizovat zadáním různých hodnot pro všechny tři podrobnosti trezoru klíčů (například aktualizace verze klíče). Když se změní na nový klíč Key Vault nebo na novou verzi klíče, musí se nejdřív aktualizovat index Azure Kognitivní hledání nebo mapa synonym, která tento klíč používá, aby používala nový key\version **před** odstraněním předchozího key\version.. Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné, protože po ztrátě přístupu ke klíči nebude moct obsah dešifrovat.   
> Obnovení přístupu k obsahu později obnoví přístupová oprávnění trezoru klíčů.

## <a name="advanced-use-an-externally-managed-azure-active-directory-application"></a><a name="aad-app"></a> Upřesnit: použití externě spravované aplikace Azure Active Directory

Pokud není možná spravovaná identita, můžete vytvořit aplikaci Azure Active Directory s objektem zabezpečení pro službu Azure Kognitivní hledání. Konkrétně spravovaná identita není za těchto podmínek životaschopná:

* Přístup k trezoru klíčů nelze udělit přímo vašim oprávněním služby Search (například pokud je vyhledávací služba v jiném tenantovi služby Active Directory než Azure Key Vault).

* Pro hostování více šifrovaných map indexes\synonym je potřeba jedna vyhledávací služba, z nichž každá používá jiný klíč z jiného trezoru klíčů, kde každý Trezor klíčů musí pro ověřování použít **jinou identitu** . Pokud používáte jinou identitu pro správu různých trezorů klíčů, není nutné zvážit použití možnosti spravovaná identita výše.  

Pro uspokojení takových topologií Azure Kognitivní hledání podporuje použití aplikací Azure Active Directory (AAD) k ověřování mezi vaší službou vyhledávání a Key Vault.    
Vytvoření aplikace AAD na portálu:

1. [Vytvořte aplikaci Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md).

1. [Získejte ID aplikace a ověřovací klíč](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) , protože se budou vyžadovat pro vytvoření šifrovaného indexu. Hodnoty, které budete muset zadat, zahrnují **ID aplikace** a **ověřovací klíč**.

>[!Important]
> Pokud se rozhodnete použít aplikaci AAD ověřování místo spravované identity, zvažte skutečnost, že Azure Kognitivní hledání nemá oprávnění ke správě vaší aplikace AAD vaším jménem, a je až do správy aplikace AAD, jako je například pravidelné otočení ověřovacího klíče aplikace.
> Při změně aplikace AAD nebo jejího ověřovacího klíče se musí nejdřív aktualizovat index služby Azure Kognitivní hledání nebo mapa synonym, která tuto aplikaci používá, aby používala novou aplikaci ID\key **před** odstraněním předchozí aplikace nebo jejího autorizačního klíče a před tím, než odvoláte Key Vault přístup k ní.
> Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné, protože po ztrátě přístupu ke klíči nebude moct obsah dešifrovat.

## <a name="work-with-encrypted-content"></a>Práce s šifrovaným obsahem

S šifrováním CMK zjistíte latenci pro indexování i dotazy z důvodu nadbytečného šifrování/dešifrování. Azure Kognitivní hledání neprotokoluje aktivitu šifrování, ale můžete monitorovat přístup k klíčům prostřednictvím protokolování trezoru klíčů. Doporučujeme [Povolit protokolování](../key-vault/general/logging.md) jako součást nastavení trezoru klíčů.

V průběhu času se očekává, že dojde k rotaci klíčů. Při každém otočení klíčů je důležité postupovat podle tohoto pořadí:

1. [Určete klíč používaný indexem nebo mapou synonym](search-security-get-encryption-keys.md).
1. [V trezoru klíčů vytvořte nový klíč](../key-vault/keys/quick-create-portal.md), ale nechte k dispozici původní klíč.
1. [Aktualizujte vlastnosti EncryptionKey](/rest/api/searchservice/update-index) v indexu nebo v mapě synonym tak, aby používaly nové hodnoty. Pouze objekty, které byly vytvořeny pomocí této vlastnosti, lze aktualizovat tak, aby používaly jinou hodnotu.
1. Zakáže nebo odstraní předchozí klíč v trezoru klíčů. Sledujte přístup k klíči a ověřte, že se používá nový klíč.

Služba Search z důvodů výkonu ukládá do mezipaměti klíč po dobu až několik hodin. Pokud klíč zakážete nebo odstraníte bez zadání nového, budou dotazy dál fungovat na dočasném základě, dokud nevyprší platnost mezipaměti. Jakmile však služba vyhledávání nebude moci dešifrovat obsah, zobrazí se tato zpráva: "přístup byl zakázán. Použitý klíč dotazu byl pravděpodobně odvolán – zkuste to znovu. " 

## <a name="next-steps"></a>Další kroky

Pokud nejste obeznámeni s architekturou zabezpečení Azure, přečtěte si [dokumentaci k zabezpečení Azure](../security/index.yml)a zejména tento článek:

> [!div class="nextstepaction"]
> [Šifrování neaktivních dat](../security/fundamentals/encryption-atrest.md)
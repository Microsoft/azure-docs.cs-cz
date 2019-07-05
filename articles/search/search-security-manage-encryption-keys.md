---
title: Šifrování neaktivních pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault (preview) – Azure Search
description: Šifrování na straně serveru příplatek nad indexy a mapy synonym ve službě Azure Search pomocí klíčů, které můžete vytvořit a spravovat ve službě Azure Key Vault.
author: NatiNimni
manager: jlembicz
ms.author: natinimn
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.custom: ''
ms.openlocfilehash: 949628fa52b4b020d70b75f4a0e7895f1e0f8bba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485324"
---
# <a name="azure-search-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Azure Search šifrování pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault

> [!Note]
> Šifrování pomocí klíčů spravovaných zákazníkem je ve verzi preview a není určen pro použití v produkčním prostředí. [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) tuto funkci poskytuje. Můžete také použít .NET SDK verze 8.0-preview.
>
> Tato funkce není k dispozici pro bezplatné služby. Je nutné použít fakturovatelné vyhledávací služba vytvoří nebo později 2019-01-01. Neexistuje žádná podpora portálu v tuto chvíli.

Ve výchozím nastavení, Azure Search šifruje obsah uživatelů v klidovém stavu pomocí [klíče spravované službou](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest#data-encryption-models). Výchozí šifrování můžete doplnit vrstvou další šifrování pomocí klíče, které můžete vytvořit a spravovat ve službě Azure Key Vault. Tento článek vás provede jednotlivými kroky.

Šifrování na straně serveru je podporované prostřednictvím integrace s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview). Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo rozhraní API služby Azure Key Vault můžete použít ke generování šifrovací klíče. S Azure Key Vault můžete také auditovat použití klíče. 

Šifrování pomocí klíčů spravovaných zákazníkem je konfigurováno na index nebo synonymum mapy úrovni při vytváření těchto objektů a nikoli na úroveň služby vyhledávání. Nelze zašifrovat obsah, který již existuje. 

Můžete použít různé klíče z různých trezory klíčů. To znamená, že jeden vyhledávací služba může hostovat více šifrované indexes\synonym map, každý šifrována potenciálně různé klíče spravovaného zákazníkem, společně s indexes\synonym mapy, které nejsou šifrovány pomocí klíčů spravovaných zákazníkem. 

## <a name="prerequisites"></a>Požadavky

V tomto příkladu se používají tyto služby. 

+ [Vytvoření služby Azure Search](search-create-service-portal.md) nebo [najít existující službu](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) pod vaším aktuálním předplatným. Můžete použít bezplatnou službu pro účely tohoto kurzu.

+ [Vytvoří prostředek služby Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault) nebo najít existující trezor v rámci vašeho předplatného.

+ [Prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) se používá pro úlohy konfigurace.

+ [Postman](search-get-started-postman.md), [prostředí Azure PowerShell](search-create-index-rest-api.md) a [SDK služby Azure Search](https://aka.ms/search-sdk-preview) je možné volat rozhraní REST API ve verzi preview. Neexistuje žádný portál nebo sadu .NET SDK: podpora pro spravované zákazníkem šifrování v tuto chvíli.

## <a name="1---enable-key-recovery"></a>1 - povolit obnovení klíče

Tento krok je volitelný, ale důrazně doporučené. Po vytvoření prostředku Azure Key Vault, povolte **obnovitelné odstranění** a **vyprázdnit ochrany** ve vybrané službě Key vault spuštěním následujících příkazů Powershellu nebo rozhraní příkazového řádku Azure:   

```powershell
$resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'

$resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'

Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
```

```azurecli-interactive
az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
```

>[!Note]
> Z důvodu podstaty šifrování pomocí klíčů spravovaných zákazníkem funkce Azure Search nebude možné načíst vaše data, pokud vaše klíče služby Azure Key vault se odstraní. Aby se zabránilo ztráty dat způsobené před náhodným odstraněním klíčů služby Key Vault, důrazně doporučujeme, abyste povolili obnovitelného odstranění a mazání ochrany pro vybraný trezor klíčů. Další informace najdete v tématu [obnovitelného odstranění služby Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete).   

## <a name="2---create-a-new-key"></a>2 – Vytvoření nového klíče

Pokud používáte existující klíč k šifrování obsahu Azure Search, tento krok přeskočte.

1. [Přihlaste se k webu Azure portal](https://portal.azure.com) a přejděte do řídicího panelu trezoru klíčů.

1. Vyberte **klíče** nastavení v levém navigačním podokně a klikněte na tlačítko **+ vygenerovat/importovat**.

1. V **vytvořte klíč** podokno, ze seznamu **možnosti**, zvolte metodu, kterou chcete použít k vytvoření klíče. Je možné **generovat** nový klíč, **nahrát** z existujícího klíče, nebo použijte **obnovit zálohování** vyberte zálohování klíče.

1. Zadejte **název** pro klíč a volitelně vyberte další klíčové vlastnosti.

1. Klikněte na **vytvořit** tlačítko pro spuštění nasazení.

Poznačte si identifikátor klíče – to se skládá z **hodnotu identifikátoru Uri klíče**, **název klíče**a **verze klíče**. Budete potřebovat k definování šifrované indexu ve službě Azure Search.
 
![Vytvořte nový klíč služby key vault](./media/search-manage-encryption-keys/create-new-key-vault-key.png "vytvořte nový klíč služby key vault")

## <a name="3---create-a-service-identity"></a>3 – vytvoření identity služby

Přiřazování identity do služby search můžete udělit oprávnění pro přístup k vaší vyhledávací služby Key Vault. Vaše vyhledávací služba budete používat k ověřování pomocí služby Azure Key vault svoji identitu.

Služba Azure Search podporuje dva způsoby, jak pro přiřazení identit: spravovaná identita nebo externě spravované aplikace Azure Active Directory. 

Pokud je to možné používejte spravovanou identitu. Je nejjednodušší způsob, jak přiřadit identitu vaší vyhledávací služby a ve většině případů by mělo fungovat. Pokud používáte více klíčů pro indexy a map synonym, nebo pokud je vaše řešení v distribuované architektuře, která znemožňuje, aby ověřování na základě identity, použijte pokročilé [externě spravované služby Azure Active Directory přístup](#aad-app)najdete na konci tohoto článku.

 Obecně platí umožňuje spravovanou identitu vaší vyhledávací služby pro ověření do služby Azure Key Vault bez uložení přihlašovacích údajů v kódu. Životní cyklus tohoto typu spravovaná identita je vázán na životní cyklus vaší služby search, což může mít jen jedna spravovaná identita. [Další informace o spravované identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

1. Chcete-li vytvořit spravovanou identitu, [podepsat portálu toAzure](https://portal.azure.com) a otevřete řídicí panel služby search. 

1. Klikněte na tlačítko **Identity** v levém navigačním podokně, změnit její stav na **na**a klikněte na tlačítko **Uložit**.

![Povolit spravované identity](./media/search-enable-msi/enable-identity-portal.png "povolit spravovaných identit")

## <a name="4---grant-key-access-permissions"></a>4 – udělení oprávnění k přístupu ke klíčům

Pokud chcete povolit služby search k použití příslušného klíče služby Key Vault, budete muset udělit hledání služby určitá oprávnění přístupu.

V každém okamžiku může odvolat oprávnění k přístupu. Jakmile odvolán, jakékoli hledání index nebo synonymum mapy služeb, který používá tento trezor klíčů už nepůjdou použít. Obnovení oprávnění pro Key vault přístup později obnoví index\synonym mapování přístupu. Další informace najdete v tématu [zabezpečený přístup k trezoru klíčů](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

1. [Přihlaste se k webu Azure portal](https://portal.azure.com) a otevřete stránku přehled vašeho trezoru klíčů. 

1. Vyberte **zásady přístupu** nastavení v levém navigačním podokně a klikněte na tlačítko **+ přidat nový**.

   ![Přidat nové zásady přístupu trezoru klíčů](./media/search-manage-encryption-keys/add-new-key-vault-access-policy.png "přidat nové zásady přístupu trezoru klíčů")

1. Klikněte na tlačítko **vybrat objekt zabezpečení** a vyberte svoji službu Azure Search. Můžete vyhledat ho podle názvu nebo ID objektu, který se zobrazí po povolení identity spravované.

   ![Objekt zabezpečení zásad přístupu vyberte trezor klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-principal.png "hlavní zásad přístupu vyberte trezor klíčů")

1. Klikněte na **oprávnění klíče** a vyberte *získat*, *rozbalení klíče* a *zabalit klíč*. Můžete použít *Azure Data Lake Storage nebo Azure Storage* šablony pro rychlý výběr požadovaná oprávnění.

   Služba Azure search musí mít udělena následujícím [přístupová oprávnění](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-operations):

   * *Získat* – umožňuje službě search k načtení veřejné části klíče ve službě Key Vault
   * *Zabalit klíč* – umožňuje službě search k použití příslušného klíče k ochraně interní šifrovacího klíče
   * *Rozbalit klíč* – umožňuje službě search k použití příslušného klíče k rozbalení interní šifrovacího klíče

   ![Vyberte klíče oprávnění zásad přístupu trezoru klíčů](./media/search-manage-encryption-keys/select-key-vault-access-policy-key-permissions.png "vyberte klíče oprávnění zásad přístupu trezoru klíčů")

1. Klikněte na tlačítko **OK** a **Uložit** změny zásad přístupu.

> [!Important]
> Šifrovaný obsah ve službě Azure search je nakonfigurován na použití konkrétního klíče Azure Key Vault s konkrétním **verze**. Pokud změníte klíč nebo verze, mapu synonym nebo index se musí aktualizovat na použití nové key\version **před** odstraňuje předchozí key\version. Pokud tak neučiníte, nebudou zobrazovat index nebo synonymum mapu nepůjdou použít, na které nebude možné dešifrovat obsah, jakmile dojde ke ztrátě přístupu ke klíčům.   

## <a name="5---encrypt-content"></a>5 - šifrování obsahu

Vytváří se mapování index nebo synonymum šifrované pomocí klíče spravovaného zákazníkem ještě není možné pomocí webu Azure portal. Použití REST API služby Azure Search k vytvoření těchto index nebo synonymum mapy.

Index a synonymum mapování podporu nový nejvyšší úrovně **encryptionKey** vlastnost použitá k určení klíče. 

Použití **identifikátor Uri služby key vault**, **název klíče** a **verze klíče** z vaší služby key vault, můžeme vytvořit **encryptionKey** definice:

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
> Žádná z těchto podrobnosti trezoru klíčů se považují za tajného kódu a může být snadno načíst tak, že přejdete na relevantní služby Azure Key Vault klíče stránku na webu Azure portal.

Pokud používáte aplikaci AAD k ověřování služby Key Vault namísto použití spravované identity, přidat aplikaci AAD **přístup k přihlašovacím údajům** k šifrovacímu klíči: 
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

## <a name="example-index-encryption"></a>Příklad: Index šifrování
Podrobnosti o vytvoření nového indexu prostřednictvím rozhraní REST API nepovedlo najít v [vytvoření indexu (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index), kde je jediný rozdíl zde zadáte podrobnosti šifrování klíče jako součást definice indexu: 

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
Můžete nyní odeslat žádost o vytvoření indexu a pak spusťte pomocí indexu normálně.

## <a name="example-synonym-map-encryption"></a>Příklad: Šifrování mapu synonym

Podrobnosti o vytvoření nové mapy synonym přes rozhraní REST API najdete v [(Azure Search Service REST API) vytvořte mapu Synonym](https://docs.microsoft.com/rest/api/searchservice/create-synonym-map), kde je určení klíčové podrobnosti o šifrování jako součást definice mapu synonym jediný rozdíl zde: 

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
Můžete nyní odeslat požadavek na vytvoření mapy synonym a potom jej začít používat normálně.

>[!Important] 
> Zatímco **encryptionKey** nelze přidat do stávající indexy Azure Search nebo mapy synonym, může být aktualizován zadáním jiné hodnoty pro všechny tři podrobnosti trezoru klíčů (například aktualizace verze klíče). Při změně na nový klíč služby Key Vault nebo nové verze, Azure Search index nebo synonymum mapu, která používá klíč se musí aktualizovat nejprve na použití nového key\version **před** odstraňuje předchozí key\version. Pokud tak neučiníte, nebudou zobrazovat index nebo dojde ke ztrátě mapy synonym nepůjdou použít, protože nebude možné dešifrovat jednou klíčů přístup k obsahu.   
> Obnovení oprávnění pro Key vault přístup později obnovíte přístup k obsahu.

## <a name="aad-app"></a> Pokročilé: Použít externě spravované aplikace Azure Active Directory

Pokud není možné, že můžete vytvořit aplikaci Azure Active Directory s zabezpečení instančního objektu služby Azure Search spravovanou identitu. Konkrétně spravovaná identita není přijatelné za těchto podmínek:

* Nelze přímo udělit hledání služby přístupová oprávnění ke službě Key vault (například pokud je v jiném tenantovi Active Directory než Azure Key Vault).

* Jeden vyhledávací službě se vyžaduje k hostování více mapami šifrované indexes\synonym každý pomocí za jiný klíč z jiný trezor klíčů, ve kterém musí používat každý trezor klíčů **jinou identitou** pro ověřování. Pokud pod jinou identitou ke správě různých trezory klíčů není povinné, zvažte použití spravované identity možnost výše.  

Azure tak, aby vyhovovaly těchto topologií, vyhledejte podporuje ověřování mezi vaší vyhledávací služby a služby Key Vault pomocí aplikace Azure Active Directory (AAD).    
Chcete-li vytvořit aplikaci AAD na portálu:

1. [Vytvořte aplikaci Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#create-an-azure-active-directory-application).

1. [Získejte ID a ověřovacího klíče aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) jako ty, bude nezbytné k vytváření indexu šifrovaná. Je potřeba zadat hodnoty zahrnují **ID aplikace** a **ověřovací klíč**.

>[!Important]
> Při rozhodování o použití ověřování aplikaci AAD místo spravovanou identitu, vezměte v úvahu skutečnost, že Azure Search nemá oprávnění ke správě svojí aplikaci AAD vaším jménem a je na vás spravovat svoji aplikaci AAD, jako je pravidelná otáčení ověřovací klíč aplikace.
> Při změně aplikaci AAD nebo její ověřovací klíč, Azure Search index nebo synonymum mapu, která používá tuto aplikaci musí nejdřív aktualizují, aby využívaly novou aplikaci ID\key **před** odstranění předchozí aplikace nebo její autorizační klíč a před odvoláte vaší služby Key Vault přístup k němu.
> Pokud tak neučiníte, nebudou zobrazovat index nebo dojde ke ztrátě mapy synonym nepůjdou použít, protože nebude možné dešifrovat jednou klíčů přístup k obsahu.   

## <a name="next-steps"></a>Další postup

Pokud nejste obeznámeni s architekturou zabezpečení Azure, přečtěte si [dokumentace ke službě Azure Security](https://docs.microsoft.com/azure/security/), zejména v tomto článku:

> [!div class="nextstepaction"]
> [Šifrování neaktivních dat](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)

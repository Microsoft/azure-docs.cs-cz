---
title: Šifrování v klidovém formátu pomocí klíčů spravovaných zákazníkem
titleSuffix: Azure Cognitive Search
description: Doplňte šifrování na straně serveru přes indexy a mapy synonym v Azure Kognitivní hledání pomocí klíčů, které vytvoříte a spravujete v Azure Key Vault.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 8295e619cfda0d4b83a7356d5fd21d4b80f83849
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530880"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurace klíčů spravovaných zákazníkem pro šifrování dat v Azure Kognitivní hledání

Azure Kognitivní hledání automaticky šifruje indexovaný obsah v klidovém [provozu pomocí klíčů spravovaných službou](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components). Pokud potřebujete další ochranu, můžete výchozí šifrování doplnit o další vrstvu šifrování pomocí klíčů, které vytvoříte a spravujete v Azure Key Vault. Tento článek vás provede jednotlivými kroky nastavení šifrování klíče spravovaného zákazníkem.

Šifrování klíče spravovaného zákazníkem závisí na [Azure Key Vault](../key-vault/general/overview.md). Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování šifrovacích klíčů. Pomocí Azure Key Vault můžete také auditovat použití klíče, pokud [povolíte protokolování](../key-vault/general/logging.md).  

Šifrování pomocí klíčů spravovaných zákazníkem se používá pro jednotlivé indexy nebo mapy synonym při vytváření těchto objektů a není zadáno na samotné úrovni služby vyhledávání. Šifrovat lze pouze nové objekty. Nemůžete zašifrovat obsah, který už existuje.

Klíče nemusí být ve stejném trezoru klíčů. Jedna vyhledávací služba může hostovat několik šifrovaných indexů nebo map synonym, z nichž každý je zašifrovaný pomocí vlastních šifrovacích klíčů spravovaných zákazníkem, uložený v různých trezorech klíčů. Můžete mít také indexy a mapy synonym ve stejné službě, které nejsou šifrovány pomocí klíčů spravovaných zákazníkem.

>[!Important]
> Pokud implementujete klíče spravované zákazníkem, nezapomeňte při běžném rotaci klíčů trezoru klíčů a tajných klíčů a používání služby Active Directory použít přísné postupy. Před odstraněním starých klíčů vždycky aktualizujte veškerý zašifrovaný obsah tak, aby používal nové tajné kódy a klíče. Pokud tento krok obdržíte, váš obsah se nedá dešifrovat.

## <a name="double-encryption"></a>Dvojité šifrování

Pro služby vytvořené od 1. srpna 2020 a v určitých oblastech zahrnuje rozsah šifrování klíče spravovaného zákazníkem dočasné disky, [které jsou v](search-security-overview.md#double-encryption)současné době dostupné v těchto oblastech: 

+ Západní USA 2
+ East US
+ Středojižní USA
+ USA (Gov) – Virginia
+ USA (Gov) – Arizona

Pokud používáte jinou oblast nebo službu vytvořenou před 1. srpna, pak se šifrování spravovaného klíče omezí jenom na datový disk, a to s výjimkou dočasných disků, které služba používá.

## <a name="prerequisites"></a>Předpoklady

V tomto scénáři se používají následující nástroje a služby.

+ [Azure kognitivní hledání](search-create-service-portal.md) na [Fakturovatelné úrovni](search-sku-tier.md#tier-descriptions) (Basic nebo vyšší, v libovolné oblasti).
+ [Azure Key Vault](../key-vault/general/overview.md)můžete Trezor klíčů vytvořit pomocí [Azure Portal](../key-vault//general/quick-create-portal.md), rozhraní příkazového [řádku Azure](../key-vault//general/quick-create-cli.md)nebo [Azure PowerShell](../key-vault//general/quick-create-powershell.md). ve stejném předplatném jako Azure Kognitivní hledání. V trezoru klíčů musí být povolená **Ochrana před** **příčtením a odstraněním** .
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Pokud ho nemáte, [nastavte nového tenanta](../active-directory/develop/quickstart-create-new-tenant.md).

Měli byste mít vyhledávací aplikaci, která může vytvořit zašifrovaný objekt. Do tohoto kódu odkazujete na klíč trezoru klíčů a registrační informace služby Active Directory. Tento kód může být pracovní aplikace nebo kód prototypu, jako je například [kód C# DotNetHowToEncryptionUsingCMK Sample](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK).

> [!TIP]
> K volání rozhraní REST API, která vytvářejí indexy a mapy synonym, které obsahují parametr šifrovacího klíče, můžete použít [post nebo Visual Studio Code](search-get-started-rest.md)nebo [Azure PowerShell](./search-get-started-powershell.md). V tuto chvíli není k dispozici žádná podpora portálu pro přidání klíče do indexů nebo map synonym.

## <a name="1---enable-key-recovery"></a>1 – povolit obnovení klíče

Vzhledem k povaze šifrování u klíčů spravovaných zákazníkem nemůže nikdo načíst vaše data, pokud se odstraní klíč trezoru klíčů Azure. Aby nedošlo ke ztrátě dat způsobené náhodným odstraněním Key Vault klíčů, musí být v trezoru klíčů povolená ochrana před odstraněním a vymazáním. Možnost obnovitelného odstranění je ve výchozím nastavení povolená, takže dojde k problémům jenom v případě, že jste ho záměrně zakázali. Ve výchozím nastavení není ochrana vyprázdnění povolena, ale je vyžadována pro šifrování klíče spravovaného zákazníkem v Kognitivní hledání. Další informace najdete v tématu přehledy ochrany proti [tichému odstranění](../key-vault/general/soft-delete-overview.md) a [vyprázdnění](../key-vault/general/soft-delete-overview.md#purge-protection) .

Obě vlastnosti můžete nastavit pomocí portálu, PowerShellu nebo příkazů rozhraní příkazového řádku Azure CLI.

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal

1. [Přihlaste se k Azure Portal](https://portal.azure.com) a otevřete stránku s přehledem trezoru klíčů.

1. Na stránce **Přehled** v části **základy** Povolte ochranu **obnovitelného odstranění** a **vyprázdnění**.

### <a name="using-powershell"></a>Použití PowerShellu

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

+ Pokud máte nainstalováno rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli), můžete spuštěním následujícího příkazu povolit požadované vlastnosti.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 – vytvoření klíče v Key Vault

Tento krok přeskočte, pokud již máte v Azure Key Vault klíč.

1. [Přihlaste se k Azure Portal](https://portal.azure.com) a otevřete stránku s přehledem trezoru klíčů.

1. Na levé straně vyberte **klávesy** a pak vyberte **+ Generovat/importovat**.

1. V podokně **vytvořit klíč** klikněte v seznamu **možností** na metodu, kterou chcete použít k vytvoření klíče. Můžete **vygenerovat** nový klíč, **nahrát** existující klíč nebo použít **obnovení zálohy** k výběru zálohy klíče.

1. Zadejte **název** klíče a volitelně vyberte další vlastnosti klíče.

1. Vyberte **vytvořit** a spusťte nasazení.

1. Poznamenejte si identifikátor klíče – skládá se z **identifikátoru URI hodnoty klíče**, **názvu klíče** a **verze klíče**. Tento identifikátor budete potřebovat k definování šifrovaného indexu v Azure Kognitivní hledání.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Vytvoří nový klíč trezoru klíčů.":::

## <a name="3---register-an-app-in-active-directory"></a>3. registrace aplikace ve službě Active Directory

1. V [Azure Portal](https://portal.azure.com)vyhledejte prostředek Azure Active Directory pro vaše předplatné.

1. Na levé straně v části **Spravovat** vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.

1. Dejte registraci název, třeba název, který se podobá názvu vyhledávací aplikace. Vyberte **Zaregistrovat**.

1. Po vytvoření registrace aplikace zkopírujte ID aplikace. Tento řetězec budete muset zadat do aplikace. 

   Pokud procházíte [DotNetHowToEncryptionUsingCMKem](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK), vložte tuto hodnotu do **appsettings.js** do souboru.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="ID aplikace v části Essentials":::

1. Pak na levé straně vyberte **certifikáty & tajné klíče** .

1. Vyberte **Nový tajný klíč klienta**. Zadejte tajný kód pro zobrazovaný název a vyberte **Přidat**.

1. Zkopírujte tajný klíč aplikace. Pokud procházíte ukázkou, vložte tuto hodnotu do **appsettings.js** do souboru.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Tajný kód aplikace":::

## <a name="4---grant-key-access-permissions"></a>4 – udělení přístupových oprávnění k klíčům

V tomto kroku vytvoříte zásadu přístupu v Key Vault. Tato zásada poskytuje aplikaci, kterou jste zaregistrovali s oprávněním služby Active Directory, k používání vašeho klíče spravovaného zákazníkem.

Přístupová oprávnění by mohla být v daném okamžiku odvolána. Po odvolání se žádný index služby vyhledávání nebo mapa synonym používající tento trezor klíčů stane nepoužitelným. Obnovení přístupových oprávnění trezoru klíčů později obnoví přístup k mapě index\synonym. Další informace najdete v tématu [zabezpečený přístup k trezoru klíčů](../key-vault/general/secure-your-key-vault.md).

1. Pořád v Azure Portal otevřete stránku s **přehledem** trezoru klíčů. 

1. Vyberte **zásady přístupu** na levé straně a vyberte **+ Přidat zásady přístupu**.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Přidat nové zásady přístupu trezoru klíčů":::

1. Zvolte **Vybrat objekt zabezpečení** a vyberte aplikaci, kterou jste zaregistrovali ve službě Active Directory. Můžete ho vyhledat podle názvu.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Výběr objektu zásad přístupu trezoru klíčů":::

1. V možnosti **klíčová oprávnění** vyberte *získat*, *Rozbalit klíč* a *zabalit klíč*.

1. V **oprávnění ke tajným klíčům** vyberte *získat*.

1. V **oprávnění certifikát** vyberte *získat*.

1. Vyberte **Přidat** a pak **Uložit**.

> [!Important]
> Šifrovaný obsah ve službě Azure Kognitivní hledání je nakonfigurovaný tak, aby používal konkrétní Azure Key Vault klíč s určitou **verzí**. Pokud změníte klíč nebo verzi, je nutné aktualizovat index nebo mapu synonym, aby používaly nové key\version **před** odstraněním předchozího key\version.. Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné. po ztrátě přístupu ke klíči nebude možné obsah dešifrovat.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5. šifrování obsahu

Chcete-li přidat klíč spravovaný zákazníkem na mapě, zdroj dat, dovednosti, indexer nebo mapu synonym, je nutné použít [REST API vyhledávání](/rest/api/searchservice/) nebo sadu SDK. Portál nevystavuje mapy synonym ani vlastnosti šifrování. Při použití platných indexů rozhraní API, zdrojů dat, dovednosti, indexerů a mapování synonym podporují vlastnost **EncryptionKey** nejvyšší úrovně.

V tomto příkladu se používá REST API s hodnotami pro Azure Key Vault a Azure Active Directory:

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

> [!Note]
> Žádná z těchto podrobností trezoru klíčů není považována za tajnou a lze ji snadno načíst procházením příslušné Azure Key Vault klíčovou stránkou v Azure Portal.

## <a name="example-index-encryption"></a>Příklad: šifrování indexu

Pomocí [REST API Create index kognitivní hledání Azure](/rest/api/searchservice/create-index)vytvořte zašifrovaný index. Pomocí `encryptionKey` vlastnosti určete, který šifrovací klíč se má použít.
> [!Note]
> Žádná z těchto podrobností trezoru klíčů není považována za tajnou a lze ji snadno načíst procházením příslušné Azure Key Vault klíčovou stránkou v Azure Portal.

## <a name="rest-examples"></a>Příklady REST

V této části se zobrazuje úplný formát JSON pro šifrovaný index a mapu synonym.

### <a name="index-encryption"></a>Šifrování indexu

Podrobné informace o vytvoření nového indexu prostřednictvím REST API najdete v tématu [Create index (REST API)](/rest/api/searchservice/create-index), kde jediný rozdíl tady určuje podrobnosti šifrovacího klíče v rámci definice indexu:

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
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Nyní můžete odeslat požadavek na vytvoření indexu a pak začít používat rejstřík normálně.

### <a name="synonym-map-encryption"></a>Šifrování mapování synonym

Vytvořte šifrovanou mapu synonym pomocí [mapování vytvořit synonymum Azure Kognitivní hledání REST API](/rest/api/searchservice/create-synonym-map). Pomocí `encryptionKey` vlastnosti určete, který šifrovací klíč se má použít.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
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

Nyní můžete odeslat požadavek na vytvoření mapy synonym a pak ho začít používat normálně.

## <a name="example-data-source-encryption"></a>Příklad: šifrování zdroje dat

Vytvořte zašifrovaný zdroj dat pomocí [Vytvoření zdroje dat (Azure Kognitivní hledání REST API)](/rest/api/searchservice/create-data-source). Pomocí `encryptionKey` vlastnosti určete, který šifrovací klíč se má použít.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
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

Nyní můžete odeslat požadavek na vytvoření zdroje dat a pak ho začít používat normálně.

## <a name="example-skillset-encryption"></a>Příklad: šifrování dovednosti

Vytvořte šifrované dovednosti pomocí [REST API Azure kognitivní hledání pro vytvoření dovednosti](/rest/api/searchservice/create-skillset). Pomocí `encryptionKey` vlastnosti určete, který šifrovací klíč se má použít.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
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

Nyní můžete odeslat žádost o vytvoření dovednosti a pak ji začít používat normálně.

## <a name="example-indexer-encryption"></a>Příklad: šifrování indexeru

Pomocí [REST API Create indexeru pro Azure kognitivní hledání](/rest/api/searchservice/create-indexer)vytvořte zašifrovaný indexer. Pomocí `encryptionKey` vlastnosti určete, který šifrovací klíč se má použít.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
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

Nyní můžete odeslat požadavek na vytvoření indexeru a pak ho začít používat normálně.

>[!Important]
> `encryptionKey`Nelze však přidat k existujícím indexům vyhledávání nebo mapováním synonym, může být aktualizováno poskytnutím různých hodnot pro všechny tři podrobnosti trezoru klíčů (například aktualizace verze klíče). Při přechodu na nový klíč Key Vault nebo na novou verzi klíče se musí nejdřív aktualizovat libovolný index vyhledávání nebo mapa synonym, která tento klíč používá, aby bylo možné použít nový key\version **před** odstraněním předchozího key\version.. Když se to nepovede, vykreslí se index nebo mapa synonym nepoužitelné, protože po ztrátě přístupu ke klíči nebude moct obsah dešifrovat. I když se později obnoví přístupová oprávnění trezoru klíčů, obnoví se přístup k obsahu.

## <a name="simpler-alternative-trusted-service"></a>Jednodušší alternativa: Důvěryhodná služba

V závislosti na konfiguraci klienta a požadavcích na ověření je možné implementovat jednodušší přístup k klíči trezoru klíčů. Místo vytváření a používání aplikace služby Active Directory můžete vytvořit důvěryhodnou službu vyhledáváním identity pomocí systému, a to tak, že pro ni povolíte identitu spravovanou systémem. Pro přístup k klíči trezoru klíčů byste pak použili důvěryhodnou vyhledávací službu jako zásadu zabezpečení místo aplikace zaregistrovaná službou AD.

Tento přístup vám umožní vynechat postup pro registraci aplikace a tajné klíče aplikace a zjednodušit definici šifrovacího klíče jenom na komponenty trezoru klíčů (identifikátor URI, název trezoru, verze klíče).

Obecně spravovaná identita umožňuje službě vyhledávání ověřovat Azure Key Vault bez uložení přihlašovacích údajů (ApplicationID nebo ApplicationSecret) do kódu. Životní cyklus tohoto typu spravované identity je svázán s životním cyklem služby vyhledávání, který může mít pouze jednu spravovanou identitu. Další informace o tom, jak spravované identity fungují, najdete v tématu [co jsou spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

1. Zpřístupněte službu Search jako důvěryhodnou službu.
   ![Zapnout spravovanou identitu přiřazenou systémem](./media/search-managed-identities/turn-on-system-assigned-identity.png "Zapnout spravovanou identitu přiřazenou systémem")

1. Při nastavování zásad přístupu v Azure Key Vault jako princip vyberte službu Trusted Search Service (místo aplikace zaregistrované v rámci služby Active Directory). Přiřaďte stejná oprávnění (vícenásobné získání, zabalení, rozbalení) podle pokynů v kroku udělení oprávnění přístupového klíče.

1. Použití zjednodušené konstrukce rozhraní `encryptionKey` , které vynechává vlastnosti služby Active Directory.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Mezi podmínky, které vám zabrání v přijetí tohoto zjednodušeného přístupu, patří:

+ Přístup k trezoru klíčů nelze udělit přímo vašim oprávněním služby Search (například pokud je vyhledávací služba v jiném tenantovi služby Active Directory než Azure Key Vault).

+ Pro hostování více šifrovaných map indexes\synonym je potřeba jedna vyhledávací služba, z nichž každá používá jiný klíč z jiného trezoru klíčů, kde každý Trezor klíčů musí pro ověřování použít **jinou identitu** . Vzhledem k tomu, že vyhledávací služba může mít pouze jednu spravovanou identitu, požadavky na více identit mají za to zjednodušený přístup pro váš scénář.  

## <a name="work-with-encrypted-content"></a>Práce s šifrovaným obsahem

Pomocí klíčového šifrování spravovaného zákazníkem si všimnete latence indexování i dotazů z důvodu nadbytečného šifrování/dešifrování. Azure Kognitivní hledání neprotokoluje aktivitu šifrování, ale můžete monitorovat přístup k klíčům prostřednictvím protokolování trezoru klíčů. Doporučujeme [Povolit protokolování](../key-vault/general/logging.md) jako součást konfigurace trezoru klíčů.

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
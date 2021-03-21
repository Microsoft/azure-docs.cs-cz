---
title: Použití záhlaví content-type JSON pro páry klíč-hodnota
titleSuffix: Azure App Configuration
description: Naučte se používat typ obsahu JSON pro klíčové hodnoty.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: how-to
ms.date: 08/03/2020
ms.author: avgupta
ms.openlocfilehash: 19de46bc87b72ada221c63e36e87d0545304d344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102122149"
---
# <a name="leverage-content-type-to-store-json-key-values-in-app-configuration"></a>Využití typu obsahu k ukládání hodnot klíče JSON v konfiguraci aplikace

Data jsou uložena v konfiguraci aplikace jako klíčové hodnoty, kde ve výchozím nastavení jsou hodnoty považovány za typ String. Můžete ale zadat vlastní typ pomocí vlastnosti Content-Type přidružené k jednotlivým klíčovým hodnotám, abyste zachovali původní typ vašich dat nebo aby se vaše aplikace chovaly různě na základě typu obsahu.


## <a name="overview"></a>Přehled

V části Konfigurace aplikace můžete použít typ média JSON jako typ obsahu vašich klíčových hodnot a využít výhody jako:
- **Jednodušší Správa dat**: Správa klíčových hodnot, jako jsou pole, se stane mnohem jednodušším Azure Portal.
- **Rozšířené exportu dat**: při exportu dat budou zachovány primitivní typy, pole a objekty JSON.
- **Nativní podpora pro poskytovatele konfigurace aplikace**: hodnoty klíč-hodnota JSON typu Content budou v aplikacích fungovat, pokud jsou spotřebovány knihovnami poskytovatele konfigurace aplikace.

#### <a name="valid-json-content-type"></a>Platný typ obsahu JSON

Typy médií, jak je definováno [tady](https://www.iana.org/assignments/media-types/media-types.xhtml), se dají přiřadit k typu obsahu přidruženému k jednotlivým klíčovým hodnotám.
Typ média se skládá z typu a podtypu. Pokud je typ `application` a podtyp (nebo přípona) `json` , bude typ média považován za platný typ obsahu JSON.
Mezi příklady platných typů obsahu JSON patří:

- application/json
- Application/Activity + JSON
- application/vnd. panel + JSON; charset = UTF-8

#### <a name="valid-json-values"></a>Platné hodnoty JSON

Pokud má klíč-hodnota JSON Content-Type, musí být jeho hodnota v platném formátu JSON, aby je klienti mohli správně zpracovat. V opačném případě může dojít k selhání klientů nebo k jeho považovat za formát řetězce.
Mezi příklady platných hodnot JSON patří:

- Jan Macek
- 723
- false (nepravda)
- null
- "2020-01-01T12:34:56.789 Z"
- [1, 2, 3, 4]
- {"ObjectSetting": {"Targeting": {"default": true, "level": "Information"}}}

> [!NOTE]
> Ve zbývající části tohoto článku se jako **hodnota klíče JSON** označuje jakákoli klíčová hodnota v konfiguraci aplikace s platným typem obsahu JSON a platnou hodnotou JSON. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoří hodnoty klíč-hodnota JSON v konfiguraci aplikace.
> * Importuje hodnoty klíčů JSON ze souboru JSON.
> * Exportujte hodnoty klíče JSON do souboru JSON.
> * Využívání hodnot klíčového klíče JSON ve vašich aplikacích.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- V tomto kurzu se vyžaduje verze rozhraní příkazového řádku Azure 2.10.0 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]


## <a name="create-json-key-values-in-app-configuration"></a>Vytvoření hodnot klíče JSON v konfiguraci aplikace

Hodnoty klíčového řádku JSON se dají vytvořit pomocí Azure Portal, Azure CLI nebo importováním ze souboru JSON. V této části najdete pokyny k vytvoření stejných hodnot klíčů JSON pomocí všech tří metod.

### <a name="create-json-key-values-using-azure-portal"></a>Vytvoření hodnot klíčů JSON pomocí Azure Portal

Přejděte do úložiště konfigurace aplikace a vyberte možnost **Průzkumník konfigurace**  >  **vytvořit**  >  **klíč-hodnota** a přidejte následující páry klíč-hodnota:

| Klíč | Hodnota | Typ obsahu |
|---|---|---|
| Nastavení: BackgroundColor | Šetrn | application/json |
| Nastavení: FontSize | 24 | application/json |
| Nastavení: UseDefaultRouting | false (nepravda) | application/json |
| Nastavení: BlockedUsers | null | application/json |
| Nastavení: ReleaseDate | "2020-08-04T12:34:56.789 Z" | application/json |
| Nastavení: RolloutPercentage | [25, 50, 75100] | application/json |
| Nastavení: protokolování | {"Test": {"level": "debug"}, "prod": {"level": "Warning"}} | application/json |

Nechejte **popisek** prázdné a vyberte **použít**.

### <a name="create-json-key-values-using-azure-cli"></a>Vytvoření hodnot klíčů JSON pomocí Azure CLI

V následujících příkazech se vytvoří klíčové hodnoty JSON v úložišti konfigurace aplikace. Nahraďte `<appconfig_name>` názvem vašeho úložiště konfigurace vaší aplikace.

```azurecli-interactive
appConfigName=<appconfig_name>
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BackgroundColor --value \"Green\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:FontSize --value 24
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:UseDefaultRouting --value false
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:BlockedUsers --value null
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:ReleaseDate --value \"2020-08-04T12:34:56.789Z\"
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:RolloutPercentage --value [25,50,75,100]
az appconfig kv set -n $appConfigName --content-type application/json --key Settings:Logging --value {\"Test\":{\"Level\":\"Debug\"},\"Prod\":{\"Level\":\"Warning\"}}
```

> [!IMPORTANT]
> Pokud k vytvoření hodnot klíčů JSON používáte Azure CLI nebo Azure Cloud Shell, musí být zadaná hodnota uvozeným řetězcem JSON.

### <a name="import-json-key-values-from-a-file"></a>Import hodnot klíčů JSON ze souboru

Vytvořte soubor JSON s názvem `Import.json` s následujícím obsahem a importujte jako klíčové hodnoty do konfigurace aplikace:

```json
{
  "Settings": {
    "BackgroundColor": "Green",
    "BlockedUsers": null,
    "FontSize": 24,
    "Logging": {"Test":{"Level":"Debug"},"Prod":{"Level":"Warning"}},
    "ReleaseDate": "2020-08-04T12:34:56.789Z",
    "RolloutPercentage": [25,50,75,100],
    "UseDefaultRouting": false
  }
}
```

```azurecli-interactive
az appconfig kv import -s file --format json --path "~/Import.json" --content-type "application/json" --separator : --depth 2
```

> [!Note]
> `--depth`Argument se používá pro sloučení hierarchických dat ze souboru do párů klíč-hodnota. V tomto kurzu je určena hloubka pro demonstraci, že můžete také ukládat objekty JSON jako hodnoty v konfiguraci aplikace. Pokud není zadaná hloubka, objekty JSON se ve výchozím nastavení sloučí do nejnižší úrovně.

Hodnoty klíčového klíče JSON, které jste vytvořili, by měly vypadat v konfiguraci aplikace takto:

![Úložiště konfigurace obsahující klíčové hodnoty JSON](./media/create-json-settings.png)


## <a name="export-json-key-values-to-a-file"></a>Export hodnot klíče JSON do souboru

Jednou z hlavních výhod použití hodnot klíčů JSON je schopnost zachovat původní datový typ vašich hodnot během exportu. Pokud klíč-hodnota v konfiguraci aplikace neobsahuje typ obsahu JSON, bude jeho hodnota považována za řetězec. 

Vezměte v úvahu tyto klíčové hodnoty bez typu obsahu JSON:

| Klíč | Hodnota | Typ obsahu |
|---|---|---|
| Nastavení: FontSize | 24 | |
| Nastavení: UseDefaultRouting | false (nepravda) | |

Pokud exportujete tyto klíčové hodnoty do souboru JSON, hodnoty budou exportovány jako řetězce:
```json
{
  "Settings": {
    "FontSize": "24",
    "UseDefaultRouting": "false"
  }
}
```

Při exportu hodnot klíč-hodnota JSON do souboru však budou všechny hodnoty uchovávat původní datový typ. Pokud to chcete ověřit, exportujte hodnoty klíče z konfigurace aplikace do souboru JSON. Uvidíte, že exportovaný soubor má stejný obsah jako `Import.json` soubor, který jste předtím naimportovali.

```azurecli-interactive
az appconfig kv export -d file --format json --path "~/Export.json" --separator :
```

> [!NOTE]
> Pokud vaše úložiště konfigurace aplikace obsahuje některé klíčové hodnoty bez typu obsahu JSON, budou také exportovány do stejného souboru ve formátu řetězce. Pokud chcete exportovat jenom hodnoty klíčového klíče JSON, přiřaďte hodnoty klíčů JSON jedinečný popisek nebo předponu a během exportu použijte filtrování podle popisku nebo předpony.


## <a name="consuming-json-key-values-in-applications"></a>Využívání hodnot klíčového klíče JSON v aplikacích

Nejjednodušší způsob, jak využít hodnoty klíčů JSON ve vaší aplikaci, je prostřednictvím knihoven zprostředkovatele konfigurace aplikace. Pomocí knihoven zprostředkovatelů nemusíte v aplikaci implementovat speciální zpracování hodnot klíčů JSON. Budou analyzovány a převedeny tak, aby odpovídaly nativní konfiguraci vaší aplikace.

Například pokud máte v konfiguraci aplikace následující klíč-hodnota:

| Klíč | Hodnota | Typ obsahu |
|---|---|---|
| Nastavení | {"FontSize": 24; "UseDefaultRouting": false} | application/json |

Vaše konfigurace aplikace .NET bude mít následující klíčové hodnoty:

| Klíč | Hodnota |
|---|---|
| Nastavení: FontSize | 24 |
| Nastavení: UseDefaultRouting | false (nepravda) |

K novým klíčům můžete přistupovat přímo nebo se můžete rozhodnout [navazovat konfigurační hodnoty na instance objektů .NET](/aspnet/core/fundamentals/configuration/#bind-hierarchical-configuration-data-using-the-options-pattern).


> [!Important]
> Nativní podpora pro hodnoty klíčového klíče JSON je k dispozici ve zprostředkovateli konfigurace .NET verze 4.0.0 (nebo novější). Další podrobnosti najdete v části [*Další kroky*](#next-steps) .

Pokud používáte sadu SDK nebo REST API ke čtení hodnot klíče z konfigurace aplikace na základě typu obsahu, je vaše aplikace zodpovědná za analýzu hodnoty klíčového hodnoty JSON.


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Když teď víte, jak pracovat s klíčovými hodnotami JSON v úložišti konfigurace aplikace, vytvořte aplikaci pro využívání těchto klíčových hodnot:

* [Rychlý Start ASP.NET Core](./quickstart-aspnet-core-app.md)
    * Předpoklad: [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) Package v 4.0.0 nebo novější.

* [Rychlý Start .NET Core](./quickstart-dotnet-core-app.md)
    * Předpoklad: [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration) balíček v 4.0.0 nebo novějším.

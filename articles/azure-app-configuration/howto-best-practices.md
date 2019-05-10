---
title: Osvědčené postupy konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: Zjistěte, jak nejlépe používat konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: d1275a48de5cad9321186ba20860d853b8ce55ad
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65413623"
---
# <a name="azure-app-configuration-best-practices"></a>Osvědčené postupy Azure konfigurace aplikace

Tento článek popisuje běžné vzory a postupy při použití konfigurace aplikace pro Azure.

## <a name="key-groupings"></a>Klíče seskupení

Konfigurace aplikací nabízí dvě možnosti pro uspořádání klíčů: klíč předpony nebo popisky. Můžete použít jednu nebo obě.

Klíče předpony jsou začátku části klíče. Pomocí stejné předpony v názvu můžete logicky seskupit sadu klíčů. Předpony může obsahovat více komponent, které jsou propojeny oddělovačem například `/`, podobně jako cestu adresy URL, k vytvoření oboru názvů. Tato hierarchie jsou užitečné, pokud jsou ukládání klíčů pro mnoho aplikací, Služba komponent a prostředí v konfiguraci aplikací úložišť. Důležitá věc brát v úvahu je, že klíče kódu aplikace, která odkazuje na načíst hodnoty odpovídající nastavení. Klíč by neměly měnit, jinak budete muset upravit kód pokaždé, když se stane.

Popisky se atribut s klíči. Používají se nevytvořil variantní hodnoty klíče. Můžete například přiřadit popisky více verzí klíče. Verze může být iterace, prostředí nebo jiné kontextové informace. Aplikace může požadovat pouze tak, že zadáte jiný popisek zcela jinou sadu hodnot klíče. Všechny odkazy můžou zůstat beze změny.

## <a name="key-value-compositions"></a>Klíč hodnota sestavení

Konfigurace aplikace zpracovává všechny klíče uložené s ním jako nezávislé entity. Se nebude pokoušet o odvodit žádný vztah mezi klíči nebo zdědit hodnoty klíče na základě jejich hierarchie. Můžete agregovat několik sad klíčů, ale používání popisků spolu se správnou konfiguraci překrývání v kódu aplikace.

Pojďme se podívat na příklad. Budete mít nastavení **Asset1** jehož hodnota se může lišit pro prostředí "Vývoj aplikací". Můžete vytvořit klíč s názvem "Asset1" popisek prázdná a popisek s názvem "Vývoj aplikací". Vložte výchozí hodnotu pro **Asset1** v předchozí a konkrétní hodnotu pro "Vývoj" v ten. Ve vašem kódu nejdřív načtěte hodnoty klíče bez libovolný popisek a dále s popiskem "Vývoj" přepsat všechny předchozí hodnoty stejnými klíči. Pokud používáte moderní programovací architektura jako je .NET Core, můžete získat tuto funkci překrývání zdarma Pokud přístup ke konfiguraci aplikace pomocí zprostředkovatele nativní konfigurace. Následující fragment kódu ukazuje, jak můžete implementovat překrývání v aplikaci .NET Core.

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Use(KeyFilter.Any, LabelFilter.Null)
           .Use(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Aplikace bootstrap konfigurace

Pro přístup k úložišti konfigurace aplikace, můžete použít připojovací řetězec, který je k dispozici na webu Azure Portal. Připojovací řetězce obsahovat přihlašovací údaje a tajné klíče jsou považovány za. Musí být uložené ve službě Key Vault. Lepší volbou je chcete použít Azure spravované identity. S touto metodou je nutné pouze adresu URL koncového bodu konfigurace aplikací ke spuštění přístup k úložišti konfigurace. Můžete vložit adresu URL v kódu aplikace (třeba *appsettings.json* souboru). Zobrazit [integrací se službami Azure spravovaných identit](howto-integrate-azure-managed-service-identity.md) další podrobnosti.

## <a name="web-app-or-function-access-to-app-configuration"></a>Webovou aplikaci nebo funkci přístup ke konfiguraci aplikací

Můžete zadat připojovací řetězec k úložišti konfigurace aplikace do nastavení aplikace služby App Service na webu Azure portal. To můžete také ukládat ve službě Key Vault a [na něj odkazovat ze služby App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references). Můžete použít také Azure spravované identity pro přístup k úložišti konfigurace. Zobrazit [integrací se službami Azure spravovaných identit](howto-integrate-azure-managed-service-identity.md) další podrobnosti.

Alternativně můžete nabízet konfigurace z konfigurace aplikace do služby App Service. Konfigurace aplikace poskytuje exportu – funkce (v portálu Azure portal a rozhraní příkazového řádku), která odesílá data přímo do služby App Service. Pomocí této metody není potřeba vůbec Změna kódu aplikace.

## <a name="next-steps"></a>Další postup

* [Klíče a hodnoty](./concept-key-value.md)

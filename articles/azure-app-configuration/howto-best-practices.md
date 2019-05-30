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
ms.openlocfilehash: 3d9a597e7ced631627a121f3f0757e472f9a4bae
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393579"
---
# <a name="azure-app-configuration-best-practices"></a>Osvědčené postupy Azure konfigurace aplikace

Tento článek popisuje běžné vzory a osvědčené postupy při použití konfigurace aplikace pro Azure.

## <a name="key-groupings"></a>Klíče seskupení

Konfigurace aplikací nabízí dvě možnosti pro uspořádání klíče:

* Předpony adres klíč
* Popisky

Jednu nebo obě možnosti můžete použít k seskupení klíče.

*Klíč předpony* začátku části klíče. Pomocí stejné předpony v názvu můžete logicky seskupit sadu klíčů. Předpony může obsahovat více komponent, které jsou připojené prostřednictvím oddělovač, jako například `/`, podobně jako cestu adresy URL, k vytvoření oboru názvů. Tato hierarchie jsou užitečné, pokud uložená v konfiguraci aplikací úložišť klíčů pro mnoho aplikací, Služba komponent a prostředí.

Důležitá věc brát v úvahu je, že klíče kódu aplikace, která odkazuje na načíst hodnoty odpovídající nastavení. Klíče by neměly měnit, jinak bude nutné změnit váš kód pokaždé, když se stane.

*Popisky* se atribut s klíči. Slouží k vytvoření variantní hodnoty klíče. Můžete například přiřadit popisky více verzí klíče. Verze může být iterace, prostředí nebo jiné kontextové informace. Aplikace může požadovat zcela jinou sadu hodnot klíčů tak, že zadáte jiný popisek. V důsledku toho všechny odkazy na klíč zůstanou beze změny v kódu.

## <a name="key-value-compositions"></a>Klíč hodnota sestavení

Konfigurace aplikace zpracovává všechny klíče uložené s ním jako nezávislé entity. Konfigurace aplikace nebude se pokoušet odvodit žádný vztah mezi klíči nebo zdědit hodnoty klíče na základě jejich hierarchie. Můžete agregovat několik sad klíčů, ale pomocí popisků spolu se správnou konfiguraci překrývání v kódu aplikace.

Pojďme se podívat na příklad. Předpokládejme, že máte nastavení s názvem **Asset1**, jehož hodnota se může lišit podle vývojové prostředí. Vytvořit klíč s názvem "Asset1" se popisek prázdná a popisek s názvem "Vývoj aplikací". V prvním popiskem, kam si ukládáte výchozí hodnota pro **Asset1**, a vložení konkrétní hodnoty pro "Vývoj" v ten.

Ve vašem kódu nejdřív načtěte hodnoty klíče bez všechny popisky, a pak načtete stejnou sadu klíčových hodnot, podruhé s popiskem "Vývoj aplikací". Při druhém načíst hodnoty jsou přepsat předchozí hodnoty klíče. Konfigurační systém .NET Core umožňuje "zásobníku" víc kopií konfigurační data nad sebe navzájem. Pokud klíč existuje ve více než jedna sada, použije se poslední sady, který jej obsahuje. Díky moderní programovací rozhraní framework, jako je .NET Core budete mít tuto funkci překrývání zdarma Pokud přístup ke konfiguraci aplikace pomocí zprostředkovatele nativní konfigurace. Následující fragment kódu ukazuje, jak můžete implementovat překrývání v aplikaci .NET Core:

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

Pro přístup k úložišti konfigurace aplikace, můžete použít připojovací řetězec, který je k dispozici na webu Azure Portal. Protože připojovací řetězce obsahovat přihlašovací údaje, budou považovány za tajných kódů. Těchto tajných kódů musí být uložené ve službě Azure Key Vault a kódu musí ověřovat pro službu Key Vault je načítat.

Lepší volbou je použití funkce spravovaných identit v Azure Active Directory. Pomocí spravované identity potřebovat pouze koncový bod URL konfigurace aplikace bootstrap přístup do konfigurace aplikace pro store. Můžete vložit adresu URL v kódu aplikace (třeba *appsettings.json* souboru). Zobrazit [integrací se službami Azure spravovaných identit](howto-integrate-azure-managed-service-identity.md) podrobnosti.

## <a name="app-or-function-access-to-app-configuration"></a>Aplikace nebo funkce přístup ke konfiguraci aplikací

Můžete poskytnout přístup k konfigurace aplikace pro webové aplikace nebo funkce s použitím libovolného z následujících metod:

* Na webu Azure portal zadejte připojovací řetězec k úložišti konfigurace aplikací v nastavení aplikace služby App Service.
* Připojovací řetězec k úložišti konfigurace aplikace pro Store ve službě Key Vault a [na něj odkazovat ze služby App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Použití Azure spravovat identity pro přístup k úložišti konfigurace aplikace. Další informace najdete v tématu [integrací se službami Azure spravovaných identit](howto-integrate-azure-managed-service-identity.md).
* Konfigurace nabízených oznámení z konfigurace aplikace do služby App Service. Konfigurace aplikace poskytuje exportu – funkce (v portálu Azure portal a rozhraní příkazového řádku Azure CLI), která odesílá data přímo do služby App Service. S touto metodou není nutné měnit kód aplikace vůbec.

## <a name="next-steps"></a>Další postup

* [Klíče a hodnoty](./concept-key-value.md)

---
title: Osvědčené postupy pro konfiguraci aplikací Azure | Microsoft Docs
description: Informace o tom, jak nejlépe využít Azure App Configuration
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
ms.openlocfilehash: 3b43ca5b6bec64d9283a64c9bcc0a3b60c21bca4
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750420"
---
# <a name="azure-app-configuration-best-practices"></a>Osvědčené postupy pro konfiguraci aplikací Azure

Tento článek popisuje běžné vzory a osvědčené postupy při použití konfigurace aplikace Azure.

## <a name="key-groupings"></a>Seskupení klíčů

Konfigurace aplikace nabízí dvě možnosti pro uspořádání klíčů:

* Klíčové předpony
* Popisky

K seskupení klíčů můžete použít buď jednu, nebo obě možnosti.

*Předpony klíčů* jsou počátečními částmi klíčů. Můžete logicky seskupovat sadu klíčů pomocí stejné předpony v jejich názvech. Předpony mohou obsahovat více komponent propojených pomocí oddělovače, například `/`, podobně jako cesta URL, pro vytvoření oboru názvů. Tyto hierarchie jsou užitečné, když ukládáte klíče pro spoustu aplikací, služeb komponent a prostředí v jednom úložišti konfigurace aplikace.

Je důležité si uvědomit, že klíče jsou v souladu s tím, jak kód aplikace odkazuje na načtení hodnot odpovídajících nastavení. Klíče by se neměly měnit, jinak budete muset kód kdykoli upravovat.

*Popisky* jsou atributy na klíčích. Používají se k vytváření variant klíče. Můžete například přiřadit popisky k několika verzím klíče. Verze může být iterací, prostředí nebo některé jiné kontextové informace. Vaše aplikace může vyžádat zcela jinou sadu hodnot klíče zadáním jiného popisku. V důsledku toho zůstanou všechny odkazy na klíče ve vašem kódu beze změny.

## <a name="key-value-compositions"></a>Složení hodnot klíčů

Konfigurace aplikace považuje všechny klíče uložené s nimi jako nezávislé entity. Konfigurace aplikace se nepokouší odvodit žádnou relaci mezi klíči nebo zdědit hodnoty klíčů na základě jejich hierarchie. Můžete agregovat více sad klíčů, ale pomocí popisků, které jsou v kódu aplikace v kombinaci se správnými konfiguračními balíčky.

Pojďme se podívat na příklad. Předpokládejme, že máte nastavení s názvem **Asset1**, jehož hodnota se může lišit v závislosti na vývojovém prostředí. Pomocí prázdného popisku a popisku s názvem "vývoj" vytvoříte klíč s názvem "Asset1". Do prvního popisku vložíte výchozí hodnotu pro **Asset1**a do druhé hodnoty vložíte určitou hodnotu "vývoj".

Ve vašem kódu nejdříve načtete hodnoty klíčů bez popisků a potom navedete stejnou sadu hodnot klíčů podruhé s označením "vývoj". Při druhém načtení hodnot se přepíší předchozí hodnoty klíčů. Konfigurační systém .NET Core umožňuje "sestavovat" více sad konfiguračních dat nad sebou. Pokud klíč existuje ve více než jedné sadě, použije se poslední sada, která ho obsahuje. V případě moderního programovacího rozhraní, jako je .NET Core, získáte tuto funkci zásobníku zdarma, pokud pro přístup ke konfiguraci aplikací použijete nativního poskytovatele konfigurace. Následující fragment kódu ukazuje, jak lze implementovat skládání do aplikace .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

## <a name="app-configuration-bootstrap"></a>Zavedení konfigurace aplikace

Pokud chcete získat přístup k úložišti konfigurace aplikace, můžete použít jeho připojovací řetězec, který je k dispozici v Azure Portal. Protože připojovací řetězce obsahují informace o přihlašovacích údajích, považují se za tajné klíče. Tyto tajné klíče je potřeba uložit v Azure Key Vault a váš kód se musí ověřit, aby se Key Vault, aby se načetly.

Lepší možností je použít funkci spravované identity v Azure Active Directory. U spravovaných identit potřebujete jenom adresu URL koncového bodu konfigurace aplikace, abyste mohli spustit přístup k úložišti konfigurace aplikace. Adresu URL můžete vložit do kódu aplikace (například v souboru *appSettings. JSON* ). Podrobnosti najdete v tématu věnovaném [integraci se spravovanými identitami Azure](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Přístup aplikací nebo funkcí k konfiguraci aplikace

Přístup ke konfiguraci aplikací pro webové aplikace nebo funkce můžete zajistit pomocí kterékoli z následujících metod:

* Prostřednictvím Azure Portal do nastavení aplikace App Service zadejte připojovací řetězec do úložiště konfigurace aplikace.
* Uložte připojovací řetězec do úložiště konfigurace aplikace v Key Vault a [odkázat ho z App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Použijte spravované identity Azure pro přístup k úložišti konfigurace aplikace. Další informace najdete v tématu [integrace se spravovanými identitami Azure](howto-integrate-azure-managed-service-identity.md).
* Doručovat konfiguraci z konfigurace aplikace do App Service. Konfigurace aplikace poskytuje funkci exportu (v Azure Portal a Azure CLI), která odesílá data přímo do App Service. V této metodě nemusíte vůbec měnit kód aplikace.

## <a name="next-steps"></a>Další kroky

* [Klíče a hodnoty](./concept-key-value.md)

---
title: Osvědčené postupy pro konfiguraci aplikací Azure | Microsoft Docs
description: Naučte se osvědčené postupy při používání konfigurace aplikací Azure. Mezi zahrnutá témata patří například seskupení klíčů, kompozice hodnot klíčů, zavedení konfigurace aplikace a další.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d532b8aab87840f4b6ad90daedba743597f4fe43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588054"
---
# <a name="azure-app-configuration-best-practices"></a>Osvědčené postupy pro konfiguraci aplikací Azure

Tento článek popisuje běžné vzory a osvědčené postupy při použití konfigurace aplikace Azure.

## <a name="key-groupings"></a>Seskupení klíčů

Konfigurace aplikace nabízí dvě možnosti pro uspořádání klíčů:

* Klíčové předpony
* Popisky

K seskupení klíčů můžete použít buď jednu, nebo obě možnosti.

*Předpony klíčů* jsou počátečními částmi klíčů. Můžete logicky seskupovat sadu klíčů pomocí stejné předpony v jejich názvech. Předpony mohou obsahovat více součástí, které jsou propojeny pomocí oddělovače, například `/` , podobně jako cesta URL, pro vytvoření oboru názvů. Tyto hierarchie jsou užitečné, když ukládáte klíče pro spoustu aplikací, služeb komponent a prostředí v jednom úložišti konfigurace aplikace.

Je důležité si uvědomit, že klíče jsou v souladu s tím, jak kód aplikace odkazuje na načtení hodnot odpovídajících nastavení. Klíče by se neměly měnit, jinak budete muset kód kdykoli upravovat.

*Popisky* jsou atributy na klíčích. Používají se k vytváření variant klíče. Můžete například přiřadit popisky k několika verzím klíče. Verze může být iterací, prostředí nebo některé jiné kontextové informace. Vaše aplikace může vyžádat zcela jinou sadu hodnot klíče zadáním jiného popisku. V důsledku toho zůstanou všechny odkazy na klíče ve vašem kódu beze změny.

## <a name="key-value-compositions"></a>Složení hodnot klíčů

Konfigurace aplikace považuje všechny klíče uložené s nimi jako nezávislé entity. Konfigurace aplikace se nepokouší odvodit žádnou relaci mezi klíči nebo zdědit hodnoty klíčů na základě jejich hierarchie. Můžete agregovat více sad klíčů, ale pomocí popisků, které jsou v kódu aplikace v kombinaci se správnými konfiguračními balíčky.

Podívejme se na příklad. Předpokládejme, že máte nastavení s názvem **Asset1**, jehož hodnota se může lišit v závislosti na vývojovém prostředí. Pomocí prázdného popisku a popisku s názvem "vývoj" vytvoříte klíč s názvem "Asset1". Do prvního popisku vložíte výchozí hodnotu pro **Asset1**a do druhé hodnoty vložíte určitou hodnotu "vývoj".

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

[Použití popisků k povolení různých konfigurací pro různá prostředí](./howto-labels-aspnet-core.md) poskytuje kompletní příklad.

## <a name="app-configuration-bootstrap"></a>Zavedení konfigurace aplikace

Pokud chcete získat přístup k úložišti konfigurace aplikace, můžete použít jeho připojovací řetězec, který je k dispozici v Azure Portal. Protože připojovací řetězce obsahují informace o přihlašovacích údajích, považují se za tajné klíče. Tyto tajné klíče je potřeba uložit v Azure Key Vault a váš kód se musí ověřit, aby se Key Vault, aby se načetly.

Lepší možností je použít funkci spravované identity v Azure Active Directory. U spravovaných identit potřebujete jenom adresu URL koncového bodu konfigurace aplikace, abyste mohli spustit přístup k úložišti konfigurace aplikace. Do kódu aplikace můžete vložit adresu URL (například v *appsettings.js* souboru). Podrobnosti najdete v tématu věnovaném [integraci se spravovanými identitami Azure](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Přístup aplikací nebo funkcí k konfiguraci aplikace

Přístup ke konfiguraci aplikací pro webové aplikace nebo funkce můžete zajistit pomocí kterékoli z následujících metod:

* Prostřednictvím Azure Portal do nastavení aplikace App Service zadejte připojovací řetězec do úložiště konfigurace aplikace.
* Uložte připojovací řetězec do úložiště konfigurace aplikace v Key Vault a [odkázat ho z App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Použijte spravované identity Azure pro přístup k úložišti konfigurace aplikace. Další informace najdete v tématu [integrace se spravovanými identitami Azure](howto-integrate-azure-managed-service-identity.md).
* Doručovat konfiguraci z konfigurace aplikace do App Service. Konfigurace aplikace poskytuje funkci exportu (v Azure Portal a Azure CLI), která odesílá data přímo do App Service. V této metodě nemusíte vůbec měnit kód aplikace.

## <a name="reduce-requests-made-to-app-configuration"></a>Snížení požadavků provedených na konfiguraci aplikace

Nadměrné požadavky na konfiguraci aplikací můžou mít za následek omezení nebo překročení limitu. Snížení počtu provedených požadavků:

* Zvyšte časový limit aktualizace, zejména v případě, že se konfigurační hodnoty nemění často. Zadejte nový časový limit aktualizace pomocí [ `SetCacheExpiration` metody](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Sledujte jeden *klíč ověřovacího klíče*místo sledování jednotlivých klíčů. Aktualizuje veškerou konfiguraci pouze v případě, že se změní klíč Sentinel. Příklad najdete v tématu [použití dynamické konfigurace v aplikaci ASP.NET Core](enable-dynamic-configuration-aspnet-core.md) .

* Použijte Azure Event Grid k přijímání oznámení při změnách konfigurace, a ne při průběžném dotazování na změny. Další informace najdete v tématu [Směrování událostí konfigurace aplikace Azure do koncového bodu webu](./howto-app-configuration-event.md) .

## <a name="importing-configuration-data-into-app-configuration"></a>Import konfiguračních dat do konfigurace aplikace

Konfigurace aplikací nabízí možnost hromadného [importu](https://aka.ms/azconfig-importexport1) nastavení konfigurace z vašich aktuálních konfiguračních souborů pomocí Azure Portal nebo CLI. Můžete také použít stejné možnosti pro export hodnot z konfigurace aplikace, například mezi souvisejícími obchody. Pokud chcete nastavit průběžnou synchronizaci s úložištěm GitHub, můžete použít naši [akci GitHubu](https://aka.ms/azconfig-gha2) , abyste mohli nadále používat stávající postupy správy zdrojového kódu a přitom získat výhody konfigurace aplikace.

## <a name="multi-region-deployment-in-app-configuration"></a>Nasazení ve více oblastech v konfiguraci aplikace

Konfigurace aplikace je regionální služba. Pro aplikace s různými konfiguracemi v jednotlivých oblastech může ukládání těchto konfigurací v jedné instanci vytvořit jediný bod selhání. Nasazení jedné instance konfigurace aplikace na oblast napříč více oblastmi může být lepší volbou. Může pomáhat s regionálním zotavením po havárii, výkonem a silou zabezpečení. Konfigurace podle oblastí taky zlepšuje latenci a používá kvóty pro oddělené omezování, protože omezování je na instanci. Pokud chcete použít zmírnění zotavení po havárii, můžete použít [víc úložišť konfigurací](./concept-disaster-recovery.md). 

## <a name="next-steps"></a>Další kroky

* [Klíče a hodnoty](./concept-key-value.md)

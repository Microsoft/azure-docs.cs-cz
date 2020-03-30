---
title: Doporučené postupy konfigurace azure aplikací | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nejlépe využít Azure App Configuration
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
ms.custom: mvc
ms.openlocfilehash: df56f53b64a35737700529b80c004efeb31eaabc
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348659"
---
# <a name="azure-app-configuration-best-practices"></a>Doporučené postupy konfigurace azure aplikací

Tento článek popisuje běžné vzory a osvědčené postupy při použití Konfigurace aplikací Azure.

## <a name="key-groupings"></a>Skupiny klíčů

Konfigurace aplikace nabízí dvě možnosti pro uspořádání klíčů:

* Předpony klíčů
* Popisky

Klíče můžete seskupit pomocí jedné nebo obou možností.

*Předpony klíčů* jsou počáteční části klíčů. Sadu klíčů můžete logicky seskupit pomocí stejné předpony v jejich názvech. Předpony mohou obsahovat více součástí připojených oddělovačem, například `/`podobně jako cesta url, a vytvořit tak obor názvů. Tyto hierarchie jsou užitečné při ukládání klíčů pro mnoho aplikací, služeb komponent a prostředí v jednom úložišti konfigurace aplikací.

Důležité je mít na paměti, že klíče jsou to, co váš kód aplikace odkazuje na načtení hodnot odpovídající nastavení. Klíče by se neměly měnit, jinak budete muset upravit kód pokaždé, když se to stane.

*Popisky* jsou atributem klíčů. Používají se k vytvoření variant klíče. Popisky můžete například přiřadit více verzím klíče. Verze může být iterace, prostředí nebo jiné kontextové informace. Aplikace může požádat o zcela jinou sadu hodnot klíče zadáním jiného popisku. V důsledku toho všechny odkazy na klíče zůstávají beze změny v kódu.

## <a name="key-value-compositions"></a>Složení hodnoty klíče

Konfigurace aplikace považuje všechny klíče uložené s ním jako nezávislé entity. Konfigurace aplikace se nepokouší odvodit žádný vztah mezi klíči nebo dědit hodnoty klíče na základě jejich hierarchie. Můžete však agregovat více sad klíčů pomocí popisků ve spojení se správným stohováním konfigurace v kódu aplikace.

Pojďme se podívat na příklad. Předpokládejme, že máte nastavení s názvem **Asset1**, jehož hodnota se může lišit v závislosti na vývojovém prostředí. Vytvoříte klíč s názvem "Asset1" s prázdným popiskem a popiskem s názvem "Vývoj". V prvním popisku vložíte výchozí hodnotu pro **Asset1**a do druhého vložíte konkrétní hodnotu pro "Vývoj".

V kódu nejprve načíst hodnoty klíče bez popisky a potom načíst stejnou sadu hodnot klíče podruhé s popiskem "Vývoj". Při načtení hodnot podruhé jsou přepsány předchozí hodnoty klíčů. Konfigurační systém .NET Core umožňuje "stohovat" více sad konfiguračních dat na sebe. Pokud klíč existuje ve více než jedné sadě, použije se poslední sada, která jej obsahuje. S moderní programovací rámec, jako je například .NET Core, získáte tuto možnost stohování zdarma, pokud používáte nativní poskytovatele konfigurace pro přístup ke konfiguraci aplikace. Následující fragment kódu ukazuje, jak můžete implementovat stohování v aplikaci .NET Core:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Pomocí popisků povolit různé konfigurace pro různá prostředí](./howto-labels-aspnet-core.md) poskytuje úplný příklad.

## <a name="app-configuration-bootstrap"></a>Zaváděcí past konfigurace aplikace

Pro přístup k úložišti konfigurace aplikací můžete použít jeho připojovací řetězec, který je k dispozici na webu Azure Portal. Vzhledem k tomu, že připojovací řetězce obsahují informace o pověření, jsou považovány za tajné klíče. Tyto tajné klíče je třeba uložit v trezoru klíčů Azure a váš kód musí ověřit key vault načíst.

Lepší možností je použít funkci spravovaných identit ve službě Azure Active Directory. Se spravovanými identitami potřebujete k zavádění přístupu do obchodu Konfigurace aplikací je potřeba pouze adresa URL koncového bodu Konfigurace aplikace. Adresu URL můžete vložit do kódu aplikace (například do souboru *appsettings.json).* Podrobnosti [najdete v tématu Integrace se spravovanými identitami Azure.](howto-integrate-azure-managed-service-identity.md)

## <a name="app-or-function-access-to-app-configuration"></a>Přístup k aplikaci nebo funkci ke konfiguraci aplikace

Ke konfiguraci aplikací pro webové aplikace nebo funkce můžete poskytnout přístup pomocí některé z následujících metod:

* Prostřednictvím portálu Azure zadejte připojovací řetězec do úložiště konfigurace aplikací v nastavení aplikace služby App Service.
* Uložte připojovací řetězec do úložiště konfigurace aplikací v trezoru klíčů a [odkazovat na něj ze služby App Service](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references).
* Pomocí spravovaných identit Azure přemístíme úložiště Konfigurace aplikací. Další informace najdete [v tématu Integrace se spravovanými identitami Azure](howto-integrate-azure-managed-service-identity.md).
* Nabízená konfigurace z konfigurace aplikace do služby App Service. Konfigurace aplikace poskytuje funkci exportu (na webu Azure Portal a Azure CLI), která odesílá data přímo do služby App Service. Pomocí této metody není nutné kód aplikace vůbec měnit.

## <a name="reduce-requests-made-to-app-configuration"></a>Snížení požadavků na konfiguraci aplikace

Nadměrné požadavky na konfiguraci aplikace může mít za následek omezení nebo překročení poplatků. Chcete-li snížit počet žádostí:

* Zvyšte časový interval aktualizace, zejména pokud se hodnoty konfigurace často nemění. Zadejte nový časový plán aktualizace pomocí [ `SetCacheExpiration` metody](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration).

* Podívejte se na jeden *sentinelový klíč*, místo sledování jednotlivých kláves. Aktualizujte všechny konfigurace pouze v případě, že se změní klíč sentinelu. Příklad najdete [v tématu Použití dynamické konfigurace v aplikaci ASP.NET Core.](enable-dynamic-configuration-aspnet-core.md)

* Azure Event Grid slouží k přijímání oznámení při změně konfigurace, nikoli k neustálému dotazování pro všechny změny. Další informace najdete [v tématu Route Azure App Configuration events to a web endpoint.See Route Azure App Configuration events to a web endpoint](./howto-app-configuration-event.md) for more information

## <a name="importing-configuration-data-into-app-configuration"></a>Import konfiguračních dat do konfigurace aplikace

Konfigurace aplikace nabízí možnost hromadného [importu](https://aka.ms/azconfig-importexport1) nastavení konfigurace z aktuálních konfiguračních souborů pomocí portálu Azure nebo příkazového příkazu k použití příkazového příkazu. Stejné možnosti můžete také použít k exportu hodnot z konfigurace aplikace, například mezi souvisejícími obchody. Pokud chcete nastavit průběžnou synchronizaci s úložištěm GitHub, můžete použít naši [akci GitHub,](https://aka.ms/azconfig-gha2) abyste mohli pokračovat v používání stávajících postupů správy zdrojového kódu a zároveň získat výhody konfigurace aplikací.

## <a name="next-steps"></a>Další kroky

* [Klíče a hodnoty](./concept-key-value.md)

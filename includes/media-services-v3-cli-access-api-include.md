---
title: zahrnout soubor
description: zahrnout soubor
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
## <a name="access-the-media-services-api"></a>Přístup k rozhraní Media Services API

Pro připojení k rozhraním Azure Media Services API použijte ověřování instančního objektu Azure AD. Následující příkaz vytvoří aplikaci Azure AD a připojí k danému účtu instanční objekt. Ke konfiguraci vaší aplikace .NET použijete vrácené hodnoty, jak ukazuje následující krok.

Před spuštěním skriptu můžete nahradit `amsaccount` a `amsResourceGroup` názvy, které jste zvolili při vytváření těchto prostředků. `amsaccount` je název účtu Azure Media Services, ke kterému se daný instanční objekt připojí. <br/>Následující příkaz používá parametr `xml`, který vrátí kód xml, který můžete vložit do vašeho souboru app.config. Pokud parametr `xml` vynecháte, bude odpověď ve formátu `json`.

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

Tento příkaz vytvoří odpověď, která se podobá tomuto:

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>Konfigurace ukázkové aplikace

Ke spuštění této aplikace a získání přístupu k rozhraním Media Services API je nutné zadat do souboru App.config správné hodnoty pro přístup. 

1. Otevřete sadu Visual Studio.
2. Vyhledejte řešení, které jste naklonovali.
3. V Průzkumníku řešení rozbalte projekt *EncodeAndStreamFiles*.
4. Nastavte tento projekt jako projekt pro spuštění.
5. Otevřete soubor App.config.
6. Nahraďte hodnoty appSettings hodnotami, které jste získali v předchozím kroku.

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Sestavte řešení stisknutím kláves Ctrl+Shift+B.

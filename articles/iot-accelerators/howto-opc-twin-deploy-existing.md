---
title: Jak nasadit modul OPC Twin do existujícího projektu Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nasadit OPC Twin do existujícího projektu. Můžete se také dozvědět, jak řešit selhání nasazení.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b971ec13c71ccfd7d28ae6987593d09201b9b764
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824123"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Nasazení OPC Twin do existujícího projektu

Modul OPC Twin běží na IoT Edge a poskytuje několik hraničních služeb pro služby OPC Twin a Registry.

Mikroslužba OPC Twin usnadňuje komunikaci mezi továrními operátory a serverovými zařízeními OPC UA ve výrobním závodě prostřednictvím modulu OPC Twin IoT Edge. Mikroslužba zpřístupňuje služby OPC UA (Procházet, číst, psát a spouštět) prostřednictvím rozhraní REST API. 

Mikroslužba registru zařízení OPC UA poskytuje přístup k registrovaným aplikacím OPC UA a jejich koncovým bodům. Operátoři a správci mohou registrovat a odregistrovat nové aplikace OPC UA a procházet stávající, včetně jejich koncových bodů. Kromě správy aplikací a koncových bodů služba registru také katalogizuje registrované moduly OPC Twin IoT Edge. Rozhraní API služby umožňuje řízení funkcí hraničního modulu, například spuštění nebo zastavení zjišťování serveru (skenovací služby) nebo aktivaci nových dvojčat koncových bodů, ke kterým lze přistupovat pomocí mikroslužby OPC Twin.

Jádrem modulu je identita supervisor. Nadřízený spravuje dvojče koncového bodu, což odpovídá koncovým bodům serveru OPC UA, které jsou aktivovány pomocí odpovídajícího rozhraní API registru OPC UA. Tato dvojčata koncového bodu přeložit OPC UA JSON přijaté z mikroslužby OPC Twin spuštěné v cloudu do binárních zpráv OPC UA, které jsou odesílány přes stavový zabezpečený kanál do spravovaného koncového bodu. Nadřízený také poskytuje služby zjišťování, které odesílají události zjišťování zařízení službě zapnutí zařízení OPC UA pro zpracování, kde tyto události vedou k aktualizacím registru OPC UA.  Tento článek ukazuje, jak nasadit modul OPC Twin do existujícího projektu.

> [!NOTE]
> Další informace o podrobnostech nasazení a pokyny naleznete v [úložišti](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub .

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte nainstalovaná rozšíření PowerShellu a [AzureRM PowerShellu.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) Pokud jste tak ještě neučinili, naklonovat toto úložiště GitHub. Spusťte v PowerShellu následující příkazy:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Nasazení průmyslových služeb IoT do Azure

1. V relaci PowerShellu spusťte:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Podle pokynů přiřaďte název skupině prostředků nasazení a název webu.   Skript nasazuje mikroslužeb a jejich závislosti platformy Azure do skupiny prostředků ve vašem předplatném Azure.  Skript také zaregistruje aplikaci ve vašem tenantovi Služby Azure Active Directory (AAD) pro podporu ověřování na základě OAUTH.  Nasazení bude trvat několik minut.  Příklad toho, co se zobrazí po úspěšném nasazení řešení:

   ![Průmyslové IoT OPC Twin nasadit do stávajícího projektu](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Výstup obsahuje adresu URL veřejného koncového bodu. 

3. Po úspěšném dokončení skriptu vyberte, zda `.env` chcete soubor uložit.  Soubor `.env` prostředí potřebujete, pokud se chcete připojit ke koncovému bodu cloudu pomocí nástrojů, jako je konzola nebo nasazení modulů pro vývoj a ladění.

## <a name="troubleshooting-deployment-failures"></a>Poradce při potížích s chybami nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte krátký a jednoduchý název skupiny prostředků.  Název se používá také k názvu prostředků jako takové musí splňovat požadavky na pojmenování prostředků.  

### <a name="website-name-already-in-use"></a>Název webu, který je již používán

Je možné, že název webových stránek je již používán.  Pokud narazíte na tuto chybu, je třeba použít jiný název aplikace.

### <a name="azure-active-directory-aad-registration"></a>Registrace služby Azure Active Directory (AAD)

Skript nasazení se pokusí zaregistrovat dvě aplikace AAD ve službě Azure Active Directory.  V závislosti na vašich právech k vybranému tenantovi AAD může nasazení selhat. Existují dvě možnosti:

1. Pokud jste ze seznamu klientů vybrali klienta AAD, restartujte skript a ze seznamu zvolte jiný.
2. Případně nasadit soukromého klienta AAD v jiném předplatném, restartovat skript a vybrat jeho použití.

> [!WARNING]
> NIKDY pokračovat bez ověřování.  Pokud se tak rozhodnete, může kdokoli přistupovat k koncovým bodům OPC Twin z Internetu neověřené.   Vždy si můžete vybrat [možnost "místního" nasazení,](howto-opc-twin-deploy-dependencies.md) abyste mohli pneumatiky kopnout.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Nasazení ukázky in-one průmyslových služeb IoT

Namísto pouze služby a závislosti můžete také nasadit all-in-one ukázku.  Vše v jedné ukázce obsahuje tři OPC UA servery, Modul OPC Twin, všechny mikroslužby a ukázkovou webovou aplikaci.  Je určen pro demonstrační účely.

1. Ujistěte se, že máte klon úložiště (viz výše). Otevřete výzvu Prostředí PowerShell v kořenovém adresáři úložiště a spusťte:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Podle pokynů přiřadíte skupině prostředků nový název a název webu.  Po úspěšném nasazení se ve skriptu zobrazí adresa URL koncového bodu webové aplikace.

## <a name="deployment-script-options"></a>Možnosti skriptu nasazení

Skript má následující parametry:

```powershell
-type
```

Typ nasazení (vm, místní, ukázka)

```powershell
-resourceGroupName
```

Může se na pojmenovat existující nebo nová skupina prostředků.

```powershell
-subscriptionId
```

Volitelné ID předplatného, kde se nasadí prostředky.

```powershell
-subscriptionName
```

Nebo název předplatného.

```powershell
-resourceGroupLocation
```

Volitelné umístění skupiny prostředků. Pokud je zadán, pokusí se vytvořit novou skupinu prostředků v tomto umístění.

```powershell
-aadApplicationName
```

Název aplikace AAD pro registraci pod.

```powershell
-tenantId
```

AAD tenanta k použití.

```powershell
-credentials
```

## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili, jak nasadit OPC Twin do existujícího projektu, zde je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Bezpečná komunikace OPC UA Client a OPC UA PLC](howto-opc-vault-secure.md)

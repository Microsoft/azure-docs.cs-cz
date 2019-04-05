---
title: Jak nasadit modul pro správu zařízení Azure IoT OPC UA do existujícího projektu | Dokumentace Microsoftu
description: Jak nasadit Dvojčete OPC do existujícího projektu.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9b1dee8ea43d01825449cb6010fbfa62e4715cff
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047258"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Nasazení do existujícího projektu Dvojčete OPC

Dvojče zařízení OPC modul spouští na hraničních zařízeních IoT a poskytuje několik služeb edge do Dvojčete zařízení OPC a registru služby. 

Dvojče zařízení OPC micro služby usnadňuje komunikaci mezi operátory objekt pro vytváření a zařízení serveru OPC UA ve výrobním závodě přes modul IoT Edge Dvojčete OPC. Micro služba poskytuje služby OPC UA (Procházet, čtení, zápis a spouštění) přes REST API. 

Registr zařízení OPC UA micro service poskytuje přístup k registrované aplikace OPC UA a jejich koncové body. Operátoři a správci můžou zaregistrovat a zrušení registrace nové aplikace OPC UA a procházet ty stávající, včetně jejich koncové body. Kromě aplikací a koncový bod správy služba registru také katalogy registrovaných modulů OPC hraničních zařízeních IoT Dvojčete zařízení. Rozhraní API služby vám dává kontrolu nad edge funkce modulu, například spuštění nebo zastavení serveru zjišťování (vyhledávání služby) nebo aktivaci nové dvojčat koncového bodu, které lze přistupovat pomocí služby micro Dvojčete OPC.

Základní modul je identita nadřízeného. Správce spravuje dvojčete koncový bod, který odpovídá koncové body serveru OPC UA, které jsou aktivované pomocí odpovídajícího registru OPC UA rozhraní API. Tento koncový bod dvojčat přeložit OPC UA JSON přijal od služby OPC Dvojčete micro běží v cloudu do binární zprávy OPC UA, které se odesílají prostřednictvím zabezpečeného kanálu stavové ke koncovému bodu spravovaného. Správce taky poskytuje služby zjišťování, které odesílají události zjišťování zařízení ke službě připojení zařízení OPC UA pro zpracování, kde tyto události za následek aktualizace registru OPC UA.  Tento článek ukazuje, jak nasadit OPC Dvojčete modulu do existujícího projektu. 

> [!NOTE]
> Další informace o podrobnosti nasazení a pokyny najdete v článku Githubu [úložiště](https://github.com/Azure/azure-iiot-opc-twin-module).

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte prostředí PowerShell a [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) nainstalovaná rozšíření.   Pokud jste neprovedli ještě, naklonujte úložiště GitHub.  Otevřete příkazový řádek nebo terminálu a spusťte:

```bash
git clone --recursive https://github.com/Azure/azure-iiot-components 
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Průmyslového IoT služby nasadit do Azure

1. Otevřít příkazový řádek nebo terminálu spusťte:

   ```bash
   deploy
   ```

2. Postupujte podle pokynů přiřadit název k nasazení skupiny prostředků a název k webu.   Skript nasadí mikroslužby a jejich závislosti platformy Azure do skupiny prostředků ve vašem předplatném Azure.  Skript také zaregistruje aplikace ve vašem tenantovi Azure Active Directory (AAD) pro podporu ověřování OAuth.  Nasazení bude trvat několik minut.  Příklad co se zobrazí po úspěšném nasazení řešení:

   ![Dvojče OPC průmyslového IoT nasazení do existujícího projektu](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Výstup obsahuje adresu URL veřejného koncového bodu. 

3. Po úspěšném dokončení skriptu vyberte, jestli chcete k uložení souboru .env.  Souboru .env prostředí budete potřebovat, pokud se chcete připojit ke koncovým bodům cloudu pomocí nástrojů, jako jsou konzole nebo nasadit moduly pro vývoj a ladění.

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží s chybami nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte název skupiny prostředků krátce a jednoduše.  Název se používá také k název prostředkům jako takové ho musí dodržovat, s prostředkem požadavkům na názvy.  

### <a name="website-name-already-in-use"></a>Název webu již používáno

Je možné, že název webu je již používán.  Pokud narazíte na tuto chybu, budete muset použít jiný název aplikace.

### <a name="azure-active-directory-aad-registration"></a>Registrace služby Azure Active Directory (AAD)

Skript nasazení se pokusí zaregistrovat dvě aplikace AAD v Azure Active Directory.  V závislosti na vašich práv k vybraného tenanta AAD může nasazení selhat. Existují dvě možnosti:

1. Pokud jste zvolili tenanta AAD ze seznamu tenantů, restartujte jej a zvolte jiný ze seznamu.
2. Můžete také nasadit privátní tenanta AAD v jiném předplatném, restartujte jej a vyberte jeho použití.

> [!WARNING]
> NIKDY pokračovat bez ověřování.  Pokud se rozhodnete tak učinit, všem uživatelům přístup vašich koncových bodů správy zařízení OPC z Internetu nebyl ověřen.   Vždy můžete použít [možnost nasazení "local"](howto-opc-twin-deploy-dependencies.md) spustit pytli.

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Nasazení All-in-one průmyslového IoT služby demo

Namísto pouze služeb a závislosti můžete taky nasadit All-in-one ukázku.  Vše v jedné ukázka obsahuje tři servery OPC UA, OPC Dvojčete modulu, všechny mikroslužby a ukázkové webové aplikace.  Je určena pro demonstrační účely.

1. Ujistěte se, že máte klon úložiště (viz výše). Otevřete příkazový řádek nebo terminálu v kořenové složce úložiště a spusťte:

   ```bash
   deploy -type demo
   ```

2. Postupujte podle pokynů a přiřadit nový název skupiny prostředků a název webu.  Po úspěšném nasazení, skript zobrazí adresu URL koncového bodu webové aplikace.

## <a name="deployment-script-options"></a>Možnosti skriptu nasazení

Skript používá následující parametry:

```bash
-type
```

Typ nasazení (virtuální počítač, místních, ukázka)

```bash
-resourceGroupName
```

Může být název stávající nebo novou skupinu prostředků.

```bash
-subscriptionId
```

Volitelný parametr, ID předplatného, ve kterém se prostředky nasadí.

```bash
-subscriptionName
```

Nebo název předplatného.

```bash
-resourceGroupLocation
```

Volitelný parametr, umístění skupiny prostředků. Je-li zadána, pokusí se vytvořit novou skupinu prostředků v tomto umístění.

```bash
-aadApplicationName
```

Název pro aplikaci AAD k registraci v rámci. 

```bash
-tenantId
```

Tenanta AAD se má použít.

```bash
-credentials
```

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasadit Dvojčete OPC do existujícího projektu, je zde navrhované další krok:

> [!div class="nextstepaction"]
> [Zabezpečená komunikace klienta OPC a OPC PLC ](howto-opc-vault-deploy-existing-client-plc-communication.md)

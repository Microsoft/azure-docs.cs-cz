---
title: Jak nasadit modul s dvojitým OPCm do existujícího projektu Azure | Microsoft Docs
description: Tento článek popisuje, jak nasadit OPC vlákna do existujícího projektu. Můžete se také dozvědět, jak řešit potíže s nasazením.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: a4d005b3a4712cfff0321e7a16f49c0e882cc9e0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282115"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>Nasazení OPC vlákna do existujícího projektu

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

OPC modul se spouští na IoT Edge a poskytuje několik hraničních služeb OPCům a službám registru.

Mikroslužba s doOPCnou mikroslužbou usnadňuje komunikaci mezi operátory výrobního rozhraní a zařízeními OPC UA serveru v produkčním zařízení pomocí modulu OPC s dvojitou IoT Edge. Mikroslužba zpřístupňuje služby OPC UA (Procházet, číst, zapisovat a spouštět) prostřednictvím své REST API. 

Mikroslužba registru zařízení OPC UA poskytuje přístup k registrovaným aplikacím OPC UA a jejich koncovým bodům. Operátoři a správci můžou registrovat a odregistrovat nové aplikace OPC UA a procházet stávající, včetně jejich koncových bodů. Kromě správy aplikací a koncových bodů registruje služba registru také katalogy registrovaných IoT Edgech OPCch vláken. Rozhraní API služby poskytuje kontrolu nad funkcemi modulu Edge, například spouštění nebo zastavování zjišťování serveru (skenovací služby) nebo Aktivace nových vláken koncového bodu, ke kterým lze získat pøístup pomocí OPC s dvojitou mikroslužbou.

Jádrem modulu je identita správce. Správce spravuje vlákna koncového bodu, které odpovídá koncovým bodům serveru OPC UA, které jsou aktivovány pomocí odpovídajícího rozhraní API registru OPC UA. Tento koncový bod vystaví překlad formátu JSON OPC UA přijatý od OPC s dvojitou životností běžící v cloudu do binárních zpráv OPC UA, které se odesílají přes stavový zabezpečený kanál do spravovaného koncového bodu. Správce taky poskytuje služby zjišťování, které odesílají události zjišťování zařízení do služby připojování zařízení OPC UA ke zpracování, kde tyto události vedou k aktualizacím registru OPC UA.  V tomto článku se dozvíte, jak nasadit modul OPC, který je v existujícím projektu.

> [!NOTE]
> Další informace o podrobnostech a pokynech k nasazení najdete v [úložišti](https://github.com/Azure/azure-iiot-opc-twin-module)GitHub.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že máte nainstalované rozšíření PowerShell a [AzureRM prostředí PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) . Pokud jste to ještě neudělali, naklonujte toto úložiště GitHub. V prostředí PowerShell spusťte následující příkazy:

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>Nasazení služeb průmyslových IoT do Azure

1. V relaci PowerShellu spusťte příkaz:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. Podle zobrazených výzev přiřaďte název ke skupině prostředků nasazení a názvu webu.   Tento skript nasadí mikroslužby a jejich závislosti na platformě Azure do skupiny prostředků ve vašem předplatném Azure.  Skript také zaregistruje aplikaci v tenantovi Azure Active Directory (AAD), aby podporovala ověřování založené na protokolu OAUTH.  Nasazení bude trvat několik minut.  Příklad toho, co se vám po úspěšném nasazení řešení zobrazí:

   ![Průmyslový OPC s dvojitým nasazením do existujícího projektu](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   Výstup zahrnuje adresu URL veřejného koncového bodu. 

3. Po úspěšném dokončení skriptu vyberte, zda chcete `.env` soubor uložit.  Soubor prostředí budete potřebovat, `.env` Pokud se chcete připojit ke koncovému bodu cloudu pomocí nástrojů, jako je konzola nebo nasazení modulů pro vývoj a ladění.

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte krátký a jednoduchý název skupiny prostředků.  Název se používá také k pojmenování prostředků, které musí splňovat požadavky na pojmenovávání prostředků.  

### <a name="website-name-already-in-use"></a>Název webu se už používá.

Je možné, že název webu se už používá.  Pokud narazíte na tuto chybu, je nutné použít jiný název aplikace.

### <a name="azure-active-directory-aad-registration"></a>Registrace Azure Active Directory (AAD)

Skript nasazení se pokusí zaregistrovat dvě aplikace AAD v Azure Active Directory.  V závislosti na vašich právech vybraného tenanta AAD může nasazení selhat. Existují dvě možnosti:

1. Pokud jste zvolili tenanta AAD ze seznamu tenantů, restartujte skript a zvolte jiný ze seznamu.
2. Případně můžete nasadit privátního tenanta AAD v jiném předplatném, restartovat skript a vybrat ho pro použití.

> [!WARNING]
> NIKDY nepokračovat bez ověřování.  Pokud se rozhodnete tak učinit, kdokoli bude mít přístup k vašim OPCm neověřeným koncovým bodům z Internetu.   V případě, že se chcete pustit do těchto pneumatik, můžete vždy vybrat [možnost místní nasazení](howto-opc-twin-deploy-dependencies.md) .

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>Nasazení kompletní ukázky služby IoT v průmyslu

Místo pouze služeb a závislostí můžete také nasadit ukázku All-in-One.  Vše v jedné ukázce obsahuje tři servery OPC UA, modul OPC s dvojitou příponou, všechny mikroslužby a ukázkovou webovou aplikaci.  Je určena pro demonstrační účely.

1. Ujistěte se, že máte klon úložiště (viz výše). V kořenovém adresáři úložiště otevřete příkazový řádek PowerShellu a spusťte tento příkaz:

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. Podle zobrazených výzev přiřaďte k této skupině prostředků nový název a název webu.  Po úspěšném nasazení se ve skriptu zobrazí adresa URL koncového bodu webové aplikace.

## <a name="deployment-script-options"></a>Možnosti skriptu nasazení

Skript má následující parametry:

```powershell
-type
```

Typ nasazení (virtuální počítač, místní, ukázka)

```powershell
-resourceGroupName
```

Může to být název existující nebo nové skupiny prostředků.

```powershell
-subscriptionId
```

Volitelné, ID předplatného, kde budou nasazeny prostředky.

```powershell
-subscriptionName
```

Nebo název předplatného.

```powershell
-resourceGroupLocation
```

Volitelné, umístění skupiny prostředků. Když se tato akce zadá, pokusí se na tomto místě vytvořit novou skupinu prostředků.

```powershell
-aadApplicationName
```

Název aplikace AAD, která se má zaregistrovat

```powershell
-tenantId
```

Tenant AAD, který se má použít

```powershell
-credentials
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nasadit OPC vlákna do existujícího projektu, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Zabezpečená komunikace klienta OPC UA a OPC UA PLC](howto-opc-vault-secure.md)

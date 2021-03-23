---
title: Nasazení vydavatele Microsoft OPCe
description: V tomto kurzu se dozvíte, jak nasadit vydavatele OPC v samostatném režimu.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: c82d15541459b5b482e427fc707b92755aa02c6c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787654"
---
# <a name="tutorial-deploy-the-opc-publisher"></a>Kurz: nasazení vydavatele OPC

Vydavatel OPC je plně podporovaný produkt společnosti Microsoft vyvinutý v otevřeném prostředí, který přemostěníuje mezeru mezi průmyslovými prostředky a cloudem Microsoft Azure. Provedete to tak, že se připojíte k prostředkům s podporou OPC UA nebo softwaru pro průmyslové připojení a publikujete data telemetrie do [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) v různých formátech, včetně IEC62541 OPC UA pubsub Standard (od verze 2,6 a vyšší).

Spouští se v [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) jako modul nebo v jednoduchém Docker jako kontejner. Vzhledem k tomu, že využívá [modul runtime pro více platforem .NET](https://docs.microsoft.com/dotnet/core/introduction), běží také nativně v systémech Linux a Windows 10.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasazení vydavatele OPC
> * Spustit nejnovější vydanou verzi OPC vydavatele jako kontejneru
> * Zadejte možnosti vytvoření kontejneru v Azure Portal

Pokud nemáte předplatné Azure, vytvořte si bezplatný zkušební účet

## <a name="prerequisites"></a>Předpoklady

- Je třeba vytvořit IoT Hub.
- Musí se vytvořit zařízení IoT Edge.

## <a name="deploy-the-opc-publisher-from-the-azure-marketplace"></a>Nasazení vydavatele OPC z Azure Marketplace

1. Vyberte předplatné Azure, které chcete použít. Pokud není k dispozici žádné předplatné Azure, je nutné ho vytvořit.
2. Vyberte IoT Hub, do kterého má Vydavatel OPC posílat data. Pokud není k dispozici žádná IoT Hub, je nutné ji vytvořit.
3. Vyberte IoT Edge zařízení, na kterém má Vydavatel OPC běžet (nebo zadejte název nového zařízení IoT Edge, které se má vytvořit).
4. Klikněte na Vytvořit. Otevře se stránka nastavit moduly na zařízení pro vybrané IoT Edge zařízení.
5. Kliknutím na "OPCPublisher" otevřete stránku "aktualizace modulu IoT Edge vydavatele OPC" a pak vyberte "možnosti vytvoření kontejneru".
6. Zadejte další možnosti vytváření kontejnerů na základě vašeho používání vydavatele OPC, viz další oddíl níže.


### <a name="accessing-the-microsoft-container-registry-docker-containers-for-opc-publisher-manually"></a>Ruční přístup k kontejnerům Docker Microsoft Container Registry pro vydavatele OPC

Nejnovější vydanou verzi OPC vydavatele můžete spustit ručně prostřednictvím:

```
docker run mcr.microsoft.com/iotedge/opc-publisher:latest <name>
```

Kde "název" je název kontejneru.

## <a name="specifying-container-create-options-in-the-azure-portal"></a>Určení možností vytvoření kontejneru v Azure Portal
Při nasazování OPC vydavatele prostřednictvím Azure Portal můžete zadat možnosti vytváření kontejnerů na stránce aktualizovat IoT Edge modulu vydavatele OPC. Tyto možnosti vytváření musí být ve formátu JSON. Argumenty příkazového řádku pro OPC vydavatele lze zadat pomocí klávesy cmd, např.:
```
"Cmd": [
    "--pf=./pn.json",
    "--aa"
],
```

Typická sada možností vytvoření kontejneru modulu IoT Edge pro vydavatele OPC je:
```
{
    "Hostname": "opcpublisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "/iiotedge:/appdata"
        ]
    }
}
```

Po zadání těchto možností bude Vydavatel OPC číst konfigurační soubor `./pn.json` . Pracovní adresář vydavatele OPC je nastaven na `/appdata` při spuštění, takže OPC Publisher tento soubor přečte v `/appdata/pn.json` kontejneru Docker. Do tohoto adresáře se vytvoří také soubor protokolu vydavatele OPC, do kterého se bude zapisovat i `/appdata` `CertificateStores` adresář (používá se pro certifikáty OPC UA). Aby byly tyto soubory k dispozici v hostitelském systému souborů IoT Edge, vyžaduje konfigurace kontejneru přípojný svazek vazby. `/iiotedge:/appdata`Vazba bude adresář mapovat `/appdata` na hostitelský adresář `/iiotedge` (který bude vytvořen modulem IoT Edge runtime, pokud neexistuje).
**Bez tohoto přípojného svazku připojení budou všechny konfigurační soubory pro OPC Publisher po restartování kontejneru ztraceny.**

Připojení k serveru OPC UA pomocí jeho názvu hostitele bez serveru DNS nakonfigurovaného v síti je možné dosáhnout přidáním `ExtraHosts` položky do `HostConfig` oddílu:

```
"HostConfig": {
    "ExtraHosts": [
        "opctestsvr:192.168.178.26"
    ]
}
```

## <a name="next-steps"></a>Další kroky 
Teď, když jste nasadili modul OPC Publisher Edge, je dalším krokem jeho konfigurace:

> [!div class="nextstepaction"]
> [Konfigurace vydavatele OPC](tutorial-publisher-configure-opc-publisher.md)
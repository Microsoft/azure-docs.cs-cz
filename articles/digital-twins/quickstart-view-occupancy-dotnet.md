---
title: 'Úvodní příručka: Vyhledání dostupných pokojů – Azure Digital Twins | Dokumenty společnosti Microsoft'
description: V tomto rychlém startu spustíte dvě ukázkové aplikace .NET Core, které budou do prostoru ve službě Azure Digital Twins odesílat simulovaná telemetrická data o pohybu a úrovni oxidu uhličitého. Cílem je najít dostupné místnosti s čerstvým vzduchem pomocí rozhraní API pro správu po výpočetním zpracování v cloudu.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 01/10/2020
ms.openlocfilehash: de8611e53339d38a03836bc9272ad42ff88a59b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371422"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Úvodní příručka: Vyhledání dostupných pokojů pomocí Azure Digital Twins

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Služba Azure Digital Twins umožňuje znovu vytvořit digitální image vašeho fyzického prostředí. Následně můžete dostávat oznámení od událostí ve vašem prostředí a přizpůsobovat své reakce na tato oznámení.

Tento rychlý start používá [dvojici ukázek .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) k digitalizaci imaginární kancelářské budovy. To vám ukáže, jak najít dostupné pokoje v této budově. S digitálními dvojčaty můžete spojit mnoho senzorů s vaším prostředím. Můžete také zjistit, zda je kvalita vzduchu v dostupném pokoji optimální pomocí simulovaného senzoru pro oxid uhličitý. Jedna z ukázkových aplikací generuje náhodná data senzorů, která vám pomohou vizualizovat tento scénář.

Následující video shrnuje nastavení rychlého startu:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Dvě konzolové aplikace, které spustíte v tomto rychlém startu, jsou napsány pomocí jazyka C#. Nainstalujte do vývojového počítače sada [.NET Core SDK verze 2.1.403 nebo vyšší.](https://www.microsoft.com/net/download) Pokud máte nainstalovanou sadku .NET Core SDK, ověřte aktuální verzi jazyka C# ve vývojovém počítači. Spustit `dotnet --version` v příkazovém řádku.

1. Stáhněte si [ukázkový projekt C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extrahujte archiv digital-twins-samples-csharp-master.zip.

## <a name="create-a-digital-twins-instance"></a>Vytvoření instance služby Digital Twins

Vytvořte novou instanci digitálních dvojčat na [portálu](https://portal.azure.com) podle kroků v této části.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Nastavení oprávnění pro aplikaci

Tato část registruje ukázkovou aplikaci do služby Azure Active Directory (Azure AD), aby mohla přistupovat k vaší instanci digitálních dvojčat. Pokud už máte registraci aplikace Azure AD, znovu ji použijte pro ukázku. Ujistěte se, že je nakonfigurován tak, jak je popsáno v této části.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Sestavení aplikace

Vytvořte aplikaci obsazenosti podle následujících kroků.

1. Otevřete příkazový řádek. Přejděte do složky, do které byly soubory `digital-twins-samples-csharp-master.zip` extrahovány.
1. Spusťte `cd occupancy-quickstart/src`.
1. Spusťte `dotnet restore`.
1. Upravte soubor [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) a aktualizujte následující proměnné:
    - **ClientId**: Zadejte ID aplikace registrace aplikace Azure AD, uvedené v předchozí části.
    - **Tenant**: Zadejte ID adresáře vašeho klienta Azure AD, také uvedeno v předchozí části.
    - **BaseUrl**: Adresa URL rozhraní API pro správu instance Digital Twins je ve formátu `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Nahraďte zástupné symboly v této adrese URL hodnotami pro vaši instanci z předchozí části.

    Aktualizovaný soubor uložte.

## <a name="provision-graph"></a>Zřízení grafu

Tento krok obsahuje prostorový graf Digitální dvojčata:

- Několik prostor.
- Jedno zařízení.
- Dva senzory.
- Vlastní funkce.
- Jedno přiřazení role.

Prostorový graf je zřízen pomocí [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) souboru.

1. Spusťte `dotnet run ProvisionSample`.

    >[!NOTE]
    >Nástroj Azure CLI přihlášení zařízení se používá k ověření uživatele azure ad. Uživatel musí zadat daný kód k ověření pomocí přihlašovací stránky [společnosti Microsoft.](https://microsoft.com/devicelogin) Po zadání kódu postupujte podle pokynů k ověření. Uživatel se musí ověřit, když je nástroj spuštěn.

    >[!TIP]
    > Při spuštění tohoto kroku zkontrolujte, zda byly proměnné správně zkopírovány, pokud se zobrazí následující chybová zpráva:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. Zřizování krok může trvat několik minut. Také zřídí služby IoT Hub v rámci instance digitálních dvojčat. Prochází smyčky, dokud IoT Hub`Running`zobrazí Status = .

    [![Zřídit ukázku - Stav=Spuštěno](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. Na konci spuštění zkopírujte `ConnectionString` zařízení pro použití v ukázku simulátoru zařízení. Zkopírujte pouze řetězec načrtnutý v tomto obrázku.

    [![Zkopírování připojovacího řetězce](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > Prostorový graf můžete zobrazit a upravit pomocí [prohlížeče grafů digitálních dvojčat Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

Ponechte okno konzoly otevřené pro pozdější použití.

## <a name="send-sensor-data"></a>Odeslání dat ze senzorů

Vytvořte a spusťte aplikaci zařízení simulátoru senzoru podle následujících kroků.

1. Otevřete nový příkazový řádek. Přejděte do projektu, který `digital-twins-samples-csharp-master` jste stáhli ve složce.
1. Spusťte `cd device-connectivity`.
1. Spusťte `dotnet restore`.
1. Upravte [soubor appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) a aktualizujte **řetězec DeviceConnectionString** s předchozím `ConnectionString`souborem . Aktualizovaný soubor uložte.
1. Spuštěním `dotnet run` spustíte odesílání dat ze senzorů. Bude odeslána do Azure Digital Twins, jak je znázorněno na následujícím obrázku.

     [![Připojení zařízení](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Nechte tento simulátor spustit, abyste mohli zobrazit výsledky vedle sebe s akcí dalšího kroku. Toto okno zobrazuje simulovaná data ze senzorů odeslaná digitálním dvojčatům. V dalším kroku se dotazuje v reálném čase najít k dispozici pokoje s čerstvým vzduchem.

    >[!TIP]
    > Při spuštění tohoto kroku `DeviceConnectionString` zkontrolujte, zda byl správně zkopírován, pokud se zobrazí následující chybová zpráva:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Zjištění dostupných prostorů s čerstvým vzduchem

Vzorek senzoru simuluje náhodné hodnoty dat pro dva senzory. Jsou to pohyb a oxid uhličitý. Dostupné prostory s čerstvým vzduchem jsou ve vzorku definovány bez přítomnosti v místnosti. Jsou také definovány hladinou oxidu uhličitého pod 1000 ppm. Pokud podmínka není splněna, prostor není k dispozici nebo kvalita ovzduší je špatná.

1. Otevřete příkazový řádek, který jste použili ke spuštění kroku zřizování dříve.
1. Spusťte `dotnet run GetAvailableAndFreshSpaces`.
1. Podívejte se na tento příkazový řádek a příkazový řádek dat senzoru vedle sebe.

    Příkazový řádek dat senzoru odesílá data o simulovaném pohybu a oxidu uhličitém digitálním dvojčatům každých pět sekund. Druhý příkazový řádek čte graf v reálném čase, aby zjistil dostupné místnosti s čerstvým vzduchem na základě náhodných simulovaných dat. Zobrazuje jednu z těchto podmínek téměř v reálném čase na základě dat ze senzorů, která byla odeslána jako poslední:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Získání dostupných prostorů s čerstvým vzduchem](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Chcete-li pochopit, co se stalo v tomto rychlém startu a jaká api `digital-twins-samples-csharp`se nazývala, otevřete kód Visual [Studio](https://code.visualstudio.com/Download) s projektem pracovního prostoru kódu, který se nachází v aplikaci . Použijte následující příkaz:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Kurzy jít hluboko do kódu. Naučí vás, jak upravit konfigurační data a jaká pravidla API se nazývají. Další informace o možnostech nastavení API pro správu najdete na stránce Digital Twins Swagger:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name (Název) | Nahradit hodnotou |
| --- | --- |
| YOUR_INSTANCE_NAME | Název instance Digitální dvojčata |
| YOUR_LOCATION | Oblast serveru, kde je hostována vaše instance |

Nebo pro větší pohodlí, přejděte na [Digitální Dvojčata Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tutoriály jdou do podrobností o tom, jak:

- Vytvořte aplikaci pro správce zařízení, abyste zvýšili produktivitu cestujících.
- Provozujte budovu efektivněji.

Chcete-li pokračovat do kurzů, nečistěte prostředky vytvořené v tomto rychlém startu. Pokud neplánujete pokračovat, odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Odstraňte složku, která byla vytvořena při stahování ukázkového úložiště.
1. V nabídce vlevo na [webu Azure Portal](https://portal.azure.com)vyberte Všechny **prostředky**. Pak vyberte prostředek digitální dvojčata. V horní části podokna **Všechny prostředky** vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste dříve došlo k potížím s odstraněním instance Digitální dvojčata, byla s opravou zavedena aktualizace služby. Opakujte odstranění instance.

## <a name="next-steps"></a>Další kroky

Tento rychlý start použil jednoduchý scénář a ukázkové aplikace, které ukazují, jak lze digitální dvojčata použít k nalezení místností s dobrými pracovními podmínkami. Pro hloubkovou analýzu tohoto scénáře si přečtěte tento kurz:

>[!div class="nextstepaction"]
>[Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu](tutorial-facilities-setup.md)

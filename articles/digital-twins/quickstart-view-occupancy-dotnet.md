---
title: 'Rychlý Start: vyhledání dostupných místností – digitální vlákna Azure | Microsoft Docs'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "79371422"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Rychlý Start: vyhledání dostupných místností pomocí digitálních vláken Azure

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Služba digitálních vláken Azure umožňuje znovu vytvořit digitální image vašeho fyzického prostředí. Následně můžete dostávat oznámení od událostí ve vašem prostředí a přizpůsobovat své reakce na tato oznámení.

V tomto rychlém startu se používá [pár ukázek .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) k digitalizaci imaginární části kancelářské budovy. Ukazuje, jak najít dostupné místnosti v tomto sestavení. V případě digitálních vláken můžete k vašemu prostředí přidružit mnoho senzorů. Můžete také zjistit, jestli je optimální kvalita ovzduší dostupné místnosti s využitím simulovaného senzoru pro oxid uhličitý. Jedna z ukázkových aplikací generuje data náhodného senzoru, která vám pomůžou s vizualizací tohoto scénáře.

Následující video shrnuje nastavení rychlého startu:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Dvě konzolové aplikace, které spouštíte v rámci tohoto rychlého startu, jsou zapsány pomocí jazyka C#. Ve vývojovém počítači nainstalujte [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) . Pokud máte nainstalován .NET Core SDK, ověřte aktuální verzi jazyka C# na vašem vývojovém počítači. Spusťte `dotnet --version` příkaz na příkazovém řádku.

1. Stáhněte si [vzorový projekt C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extrahujte archiv Digital-Twins-Samples-CSharp-Master. zip.

## <a name="create-a-digital-twins-instance"></a>Vytvoření instance služby Digital Twins

Pomocí kroků v této části vytvořte novou instanci digitálních vláken na [portálu](https://portal.azure.com) .

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Nastavení oprávnění pro aplikaci

V této části najdete ukázkovou aplikaci pro Azure Active Directory (Azure AD), aby měla přístup k instanci digitálního vlákna. Pokud už máte registraci aplikace Azure AD, znovu ji použijte pro ukázku. Ujistěte se, že je nakonfigurované jak je popsáno v této části.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Sestavení aplikace

Pomocí následujících kroků sestavte aplikaci obsazení.

1. Otevřete příkazový řádek. Přejít do složky, kde byly `digital-twins-samples-csharp-master.zip` extrahovány soubory.
1. Spusťte `cd occupancy-quickstart/src`.
1. Spusťte `dotnet restore`.
1. Upravte soubor [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) a aktualizujte následující proměnné:
    - **ClientID**: Zadejte ID aplikace pro vaši registraci aplikace služby Azure AD, kterou jste si poznamenali v předchozí části.
    - **Tenant**: Zadejte ID adresáře vašeho TENANTA Azure AD, který jste si také poznamenali v předchozí části.
    - **Baseurl**: adresa URL rozhraní API pro správu vaší instance digitálního vlákna je ve formátu `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Nahraďte zástupné symboly v této adrese URL hodnotami pro vaši instanci z předchozí části.

    Aktualizovaný soubor uložte.

## <a name="provision-graph"></a>Zřízení grafu

Tento krok zajišťuje, aby se prostor digitálních vláken v grafu:

- Několik mezer.
- Jedno zařízení.
- Dva senzory.
- Vlastní funkce.
- Jedno přiřazení role.

Prostorový graf se zřídí pomocí souboru [provisionSample. yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) .

1. Spusťte `dotnet run ProvisionSample`.

    >[!NOTE]
    >K ověření uživatele do Azure AD se používá nástroj příkazového řádku Azure CLI přihlášení k zařízení. Uživatel musí zadat daný kód k ověření pomocí [přihlašovací stránky Microsoftu](https://microsoft.com/devicelogin) . Po zadání kódu postupujte podle pokynů k ověření. Uživatel se musí ověřit, když je nástroj spuštěný.

    >[!TIP]
    > Po spuštění tohoto kroku se ujistěte, že byly proměnné správně zkopírovány, pokud se zobrazí následující chybová zpráva:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. Krok zřizování může trvat několik minut. Také zřídí IoT Hub v rámci vaší instance digitálního vlákna. Cyklicky projde, dokud IoT Hub nezobrazuje stav =`Running`.

    [![Zřídit vzorový stav = spuštěno](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. Na konci spuštění zkopírujte `ConnectionString` zařízení, které se má použít v ukázce simulátoru zařízení. Zkopírujte pouze řetězec, který je popsaný v tomto obrázku.

    [![Zkopírování připojovacího řetězce](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > Prostorový graf můžete zobrazit a upravit pomocí [prohlížeče grafu digitálních vláken Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

Nechejte okno konzoly otevřené pro pozdější použití.

## <a name="send-sensor-data"></a>Odeslání dat ze senzorů

Pomocí následujících kroků Sestavte a spusťte aplikaci zařízení simulátoru snímače.

1. Otevřete nový příkazový řádek. Přejít na projekt, který jste stáhli `digital-twins-samples-csharp-master` ve složce.
1. Spusťte `cd device-connectivity`.
1. Spusťte `dotnet restore`.
1. Pokud chcete aktualizovat **DeviceConnectionString** s předchozími `ConnectionString`, upravte [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) . Aktualizovaný soubor uložte.
1. Spusťte `dotnet run` , pokud chcete začít odesílat data ze senzorů. Pošle se do digitálních vláken Azure, jak je znázorněno na následujícím obrázku.

     [![Připojení zařízení](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Nechte tento simulátor spuštěný, abyste mohli výsledky zobrazit vedle sebe pomocí akce další krok. V tomto okně se zobrazí Simulovaná data senzorů odesílaná do digitálních vláken. Další krok se dotazuje v reálném čase na vyhledání dostupných místností s čerstvým vzduchem.

    >[!TIP]
    > Po spuštění tohoto kroku se ujistěte, že `DeviceConnectionString` se správně zkopírovala, pokud se zobrazí následující chybová zpráva:`EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Zjištění dostupných prostorů s čerstvým vzduchem

Ukázka senzoru simuluje hodnoty náhodných dat pro dva senzory. Jedná se o pohyb a oxid uhličitý. Dostupné prostory s čerstvým vzduchem jsou v této ukázce definovány, a to v místnosti bez přítomnosti. Jsou definovány také na úrovni oxidu uhličit v 1 000 ppm. Pokud podmínka není splněná, není místo dostupné nebo je kvalita vzduchu nízká.

1. Otevřete příkazový řádek, který jste použili ke spuštění kroku zřizování výše.
1. Spusťte `dotnet run GetAvailableAndFreshSpaces`.
1. Podívejte se na Tento příkazový řádek a na příkazovém řádku data snímače vedle sebe.

    Příkazový řádek senzoru odesílá data simulovaného pohybu a oxidu uhličitu do digitálních vláken každých pět sekund. Druhý příkazový řádek přečte graf v reálném čase a zjistí dostupné místnosti s čerstvým vzduchem na základě náhodných simulovaných dat. Zobrazuje jednu z těchto podmínek téměř v reálném čase na základě dat ze senzorů, která byla odeslána naposledy:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Získání dostupných prostorů s čerstvým vzduchem](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Chcete-li pochopit, co se stalo v tomto rychlém startu a [Visual Studio Code](https://code.visualstudio.com/Download) jaká rozhraní API byla volána, otevřete Visual Studio Code `digital-twins-samples-csharp`v projektu pracovního prostoru kód, který se nachází v. Použijte následující příkaz:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Kurzy se přejdou do kódu. Naučíte se, jak upravit konfigurační data a jaká rozhraní API jsou volána. Další informace o rozhraních API pro správu najdete na stránce digitálních vláken Swagger:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Název | Nahradit hodnotou |
| --- | --- |
| YOUR_INSTANCE_NAME | Název instance digitálního vlákna |
| YOUR_LOCATION | Oblast serveru, ve které je vaše instance hostovaná |

Nebo pro usnadnění práce přejděte na [digitální vlákna Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Vyčištění prostředků

V kurzech najdete podrobné informace o tom, jak:

- Sestavte aplikaci pro správce zařízení, aby zvýšila produktivitu uživatelů.
- Účinnější provozování sestavení

Pokud chcete pokračovat v kurzech, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Odstraňte složku, která byla vytvořena při stažení ukázkového úložiště.
1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**. Pak vyberte svůj prostředek digitálního vlákna. V horní části podokna **všechny prostředky** vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste dříve zjistili potíže s odstraněním instance digitálního vlákna, byla aktualizace služby opravena. Zkuste prosím instanci znovu odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jsme použili jednoduchý scénář a ukázkové aplikace, které ukazují, jak se digitální vlákna dají použít k vyhledání místností s dobrými provozními podmínkami. Pro podrobné analýzy tohoto scénáře si přečtěte tento kurz:

>[!div class="nextstepaction"]
>[Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu](tutorial-facilities-setup.md)

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
ms.date: 11/12/2019
ms.openlocfilehash: b150167ca6a808e0da337be4a609a21cd974598a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383140"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Rychlý start: Najít dostupné místnosti s použitím digitálních Dvojčat Azure

Služba Azure digitální dvojče umožňuje znovu vytvořit digitální obrázek vašemu fyzickému prostředí. Následně můžete dostávat oznámení od událostí ve vašem prostředí a přizpůsobovat své reakce na tato oznámení.

V tomto rychlém startu se používá [pár ukázek .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) k digitalizaci imaginární části kancelářské budovy. To se dozvíte, jak se mají najít místnosti k dispozici v budovy. S digitální dvojče můžete přidružit mnoho senzorů s vaším prostředím. Také můžete zjistit při optimální pomocí simulovaných senzorů k oxidu uhličitého air kvalitu dostupné místo. Jednu z ukázkových aplikací generuje náhodné senzor data pro lepší vizualizaci v tomto scénáři.

Následující video shrnuje nastavení pro rychlý start:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Dvě konzolové aplikace v tomto rychlém startu spustíte jsou zapsány pomocí C#. Ve vývojovém počítači nainstalujte [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) . Pokud je nainstalovaná sada .NET Core SDK, zkontrolujte aktuální verzi C# na vývojovém počítači. Spusťte `dotnet --version` v příkazovém řádku.

1. Stáhněte si [ukázkový C# projekt](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extrahujte archiv digitální twins-samples-csharp-master.zip.

## <a name="create-a-digital-twins-instance"></a>Vytvoření instance služby Digital Twins

Pomocí kroků v této části vytvořte novou instanci digitálních vláken na [portálu](https://portal.azure.com) .

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Nastavení oprávnění pro aplikaci

Tento oddíl registruje ukázkovou aplikaci do služby Azure Active Directory (Azure AD), tak, aby měl přístup k vaší instanci digitální dvojče. Pokud už máte registrace aplikace Azure AD, znovu použijte pro vaši ukázku. Ujistěte se, jestli je nastavená způsobem popsaným v této části.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Sestavení aplikace

Pomocí následujících kroků sestavte aplikaci obsazení.

1. Otevřete příkazový řádek. Přejít do složky, ve které byly extrahovány vaše `digital-twins-samples-csharp-master.zip` soubory.
1. Spusťte `cd occupancy-quickstart/src`.
1. Spusťte `dotnet restore`.
1. Upravte soubor [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) a aktualizujte následující proměnné:
    - **ClientID**: Zadejte ID aplikace pro vaši registraci aplikace služby Azure AD, kterou jste si poznamenali v předchozí části.
    - **Tenant**: Zadejte ID adresáře vašeho TENANTA Azure AD, který jste si také poznamenali v předchozí části.
    - **Baseurl**: adresa URL rozhraní API pro správu vaší instance digitálního vlákna je ve formátu `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Nahraďte zástupné symboly v této adrese URL hodnotami pro vaši instanci v předchozí části.

    Aktualizovaný soubor uložte.

## <a name="provision-graph"></a>Zřízení grafu

Tento krok zřídí digitální dvojče prostorových grafu s:

- Několik mezer.
- Jedno zařízení.
- Dva senzory.
- Vlastní funkce.
- Jedno přiřazení role.

Prostorový graf se zřídí pomocí souboru [provisionSample. yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) .

1. Spusťte `dotnet run ProvisionSample`.

    >[!NOTE]
    >Nástroj příkazového řádku Azure přihlášení zařízení se používá k ověření uživatele do služby Azure AD. Uživatel musí zadat daný kód k ověření pomocí [přihlašovací stránky Microsoftu](https://microsoft.com/devicelogin) . Po zadání kódu postupujte podle pokynů k ověření. Uživatel musí ověřit, když je nástroj spuštěný.

    >[!TIP]
    > Po spuštění tohoto kroku se ujistěte, že byly proměnné správně zkopírovány, pokud se zobrazí následující chybová zpráva: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. Zřizování krok může trvat několik minut. Také zřídí služby IoT Hub v rámci vaší digitální dvojče instance. Cyklicky projde, dokud IoT Hub nezobrazuje stav =`Running`.

    [![zřídit vzorový stav = spuštěno.](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png#lightbox)

1. Na konci provádění zkopírujte `ConnectionString` zařízení, které se má použít v ukázce simulátoru zařízení. Zkopírujte pouze řetězec uvedených v tomto obrázku.

    [![zkopírování připojovacího řetězce](media/quickstart-view-occupancy-dotnet/digital-twins-connection-string.png)](media/quickstart-view-occupancy-dotnet/digital-twins-connection-string.png#lightbox)

    >[!TIP]
    > Prostorový graf můžete zobrazit a upravit pomocí [prohlížeče grafu digitálních vláken Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

Nechejte okno konzoly otevřené pro pozdější použití.

## <a name="send-sensor-data"></a>Odeslání dat ze senzorů

Pomocí následujících kroků Sestavte a spusťte aplikaci zařízení simulátoru snímače.

1. Otevřete nový příkazový řádek. Přejít na projekt, který jste stáhli, do složky `digital-twins-samples-csharp-master`.
1. Spusťte `cd device-connectivity`.
1. Spusťte `dotnet restore`.
1. Pokud chcete aktualizovat **DeviceConnectionString** s předchozí `ConnectionString`, upravte [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) . Aktualizovaný soubor uložte.
1. Spusťte `dotnet run` a začněte odesílat data ze senzorů. Uvidíte, že ho odesílat digitální dvojče, jak je znázorněno na následujícím obrázku.

     [![připojení zařízení](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png#lightbox)

1. Umožní simulátor spustit tak, abyste mohli zobrazit výsledky souběžně s akce dalšího kroku. Toto okno zobrazuje simulovaných senzorů data odeslaná do digitální dvojče. Další krok dotazy v reálném čase, které se mají najít místnosti k dispozici s novou vzduchu.

    >[!TIP]
    > Po spuštění tohoto kroku se ujistěte, že se `DeviceConnectionString` správně zkopírovala, pokud se zobrazí následující chybová zpráva: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Zjištění dostupných prostorů s čerstvým vzduchem

Ukázka senzor simuluje hodnoty náhodných dat pro dvě senzory. Jsou to pohybu a k oxidu uhličitého. K dispozici prostorů s novou air jsou definovány v ukázce žádné přítomnost v místnosti. Také budete definovat podle úrovně oxidu uhličitého v části 1 000 správy portfolia projektů. Pokud podmínka není splněna, není k dispozici místa nebo je špatná kvalita air.

1. Otevřete příkazový řádek, který jste použili ke spuštění kroku zřizování výše.
1. Spusťte `dotnet run GetAvailableAndFreshSpaces`.
1. Podívejte se na tento příkazový řádek a příkazového řádku data ze senzorů vedle sebe.

    Příkazový řádek senzoru odesílá data simulovaného pohybu a oxidu uhličitu do digitálních vláken každých pět sekund. Druhý příkazový řádek přečte graf v reálném čase a zjistí dostupné místnosti s čerstvým vzduchem na základě náhodných simulovaných dat. Zobrazí jedno z těchto podmínek téměř v reálném čase na základě dat ze senzorů, odeslání poslední:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![získat dostupné prostory s čerstvým vzduchem](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png)](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png#lightbox)

Chcete-li pochopit, co se stalo v tomto rychlém startu a jaká rozhraní API byla volána, otevřete [Visual Studio Code](https://code.visualstudio.com/Download) v projektu pracovního prostoru kód v `digital-twins-samples-csharp`. Použijte následující příkaz:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

V kurzech zanořovat hluboko do kódu. Že vás naučí, jak změnit konfigurační data a co se volá rozhraní API. Další informace o rozhraní API pro správu přejděte na stránku digitální dvojče Swagger:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Název | Nahradit hodnotou |
| --- | --- |
| YOUR_INSTANCE_NAME | Název instance digitální dvojče |
| YOUR_LOCATION | Oblast serveru, ve které je vaše instance hostovaná |

Nebo pro usnadnění práce přejděte na [digitální vlákna Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Vyčištění prostředků

V kurzech najdete podrobnosti o tom, jak:

- Vytvoření aplikace pro zařízení správci zvýšit produktivitu cestujícího.
- Provoz budovy efektivněji.

A pokračujte v kurzech, není nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Odstraňte složku, která byla vytvořena, když jste si stáhli ukázkové úložiště.
1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**. Pak vyberte prostředek digitální dvojče. V horní části podokna **všechny prostředky** vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste dříve zjistili potíže s odstraněním instance digitálního vlákna, byla aktualizace služby opravena. Zkuste to prosím znovu odstraníte instanci.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jsme použili jednoduchý scénář a ukázkové aplikace, které ukazují, jak se digitální vlákna dají použít k vyhledání místností s dobrými provozními podmínkami. Podrobná analýza tohoto scénáře najdete v článku v tomto kurzu:

>[!div class="nextstepaction"]
>[Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu](tutorial-facilities-setup.md)

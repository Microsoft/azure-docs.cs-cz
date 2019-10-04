---
title: Vyhledání dostupných místností – digitální vlákna Azure | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace .NET Core k odeslání simulace simulovaného pohybu a oxidu uhličitého do prostoru v oblasti digitálních vláken Azure. Cílem je najít dostupné místnosti s čerstvým vzduchem z rozhraní API pro správu po vypočítaném zpracování v cloudu.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 10/03/2019
ms.openlocfilehash: 3c9a806b936b9f167d1700c95b1e769926abb17b
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958911"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Rychlý Start: vyhledání dostupných místností pomocí digitálních vláken Azure

Služba digitálních vláken Azure umožňuje znovu vytvořit digitální image vašeho fyzického prostředí. Pak můžete dostávat oznámení o událostech ve vašem prostředí a přizpůsobit své odpovědi.

V tomto rychlém startu se používá [pár ukázek .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) k digitalizaci imaginární části kancelářské budovy. Ukazuje, jak najít dostupné místnosti v tomto sestavení. V případě digitálních vláken můžete k vašemu prostředí přidružit mnoho senzorů. Můžete také zjistit, jestli je optimální kvalita ovzduší dostupné místnosti s využitím simulovaného senzoru pro oxid uhličitý. Jedna z ukázkových aplikací generuje data náhodného senzoru, která vám pomůžou s vizualizací tohoto scénáře.

Následující video shrnuje nastavení rychlého startu:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Požadavky

1. Pokud ještě nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Dvě konzolové aplikace, které spouštíte v rámci tohoto rychlého startu C#, jsou zapsány pomocí. Ve vývojovém počítači nainstalujte [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) . Pokud máte nainstalované .NET Core SDK, ověřte aktuální verzi nástroje C# ve vývojovém počítači. V příkazovém řádku spusťte `dotnet --version`.

1. Stáhněte si [ukázkový C# projekt](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extrahujte archiv Digital-Twins-Samples-CSharp-Master. zip.

## <a name="create-a-digital-twins-instance"></a>Vytvoření instance digitálního vlákna

Pomocí kroků v této části vytvořte novou instanci digitálních vláken na [portálu](https://portal.azure.com) .

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Nastavení oprávnění pro vaši aplikaci

V této části najdete ukázkovou aplikaci pro Azure Active Directory (Azure AD), aby měla přístup k instanci digitálního vlákna. Pokud už máte registraci aplikace Azure AD, znovu ji použijte pro ukázku. Ujistěte se, že je nakonfigurované jak je popsáno v této části.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Sestavit aplikaci

Pomocí následujících kroků sestavte aplikaci obsazení.

1. Otevřete příkazový řádek. Přejít do složky, ve které byly extrahovány soubory `digital-twins-samples-csharp-master.zip`.
1. Spusťte `cd occupancy-quickstart/src`.
1. Spusťte `dotnet restore`.
1. Upravte [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) a aktualizujte následující proměnné:
    - **ClientID**: Zadejte ID aplikace pro vaši registraci aplikace služby Azure AD, kterou jste si poznamenali v předchozí části.
    - **Tenant**: Zadejte ID adresáře vašeho TENANTA Azure AD, který jste si také poznamenali v předchozí části.
    - **Baseurl**: adresa URL rozhraní API pro správu vaší instance digitálního vlákna je ve formátu `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Nahraďte zástupné symboly v této adrese URL hodnotami pro vaši instanci z předchozí části.

## <a name="provision-graph"></a>Zřídit graf

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
    > Po spuštění tohoto kroku se ujistěte, že byly proměnné správně zkopírovány, pokud se zobrazí následující chybová zpráva: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. Krok zřizování může trvat několik minut. Také zřídí IoT Hub v rámci vaší instance digitálního vlákna. Cyklicky projde, dokud IoT Hub nezobrazuje stav = `Running`.

    [Ukázka @no__t – 1Provision](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png#lightbox)

1. Na konci spuštění zkopírujte `ConnectionString` zařízení, které se má použít v ukázce simulátoru zařízení. Zkopírujte pouze řetězec, který je popsaný v tomto obrázku.

    [@no__t – 1Copy připojovací řetězec](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png)](media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png#lightbox)

    >[!TIP]
    > Prostorový graf můžete zobrazit a upravit pomocí [prohlížeče grafu digitálních vláken Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="send-sensor-data"></a>Odeslat data snímače

Pomocí následujících kroků Sestavte a spusťte aplikaci simulátoru senzorů.

1. Otevřete nový příkazový řádek. Přejít na projekt, který jste stáhli, do složky Digital-zdvojené-Samples-Samples-CSharp-Master.
1. Spusťte `cd device-connectivity`.
1. Spusťte `dotnet restore`.
1. Pokud chcete aktualizovat **DeviceConnectionString** s předchozí `ConnectionString`, upravte [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) .
1. Pokud chcete začít odesílat data ze senzorů, spusťte `dotnet run`. Uvidíte, že se poslal do digitálních vláken, jak je znázorněno na následujícím obrázku.

     [Připojení @no__t – 1Device](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png#lightbox)

1. Nechte tento simulátor spuštěný, abyste mohli výsledky zobrazit vedle sebe pomocí akce další krok. V tomto okně se zobrazí Simulovaná data senzorů odesílaná do digitálních vláken. Další krok se dotazuje v reálném čase na vyhledání dostupných místností s čerstvým vzduchem.

    >[!TIP]
    > Po spuštění tohoto kroku se ujistěte, že `DeviceConnectionString` se správně zkopíroval, pokud se zobrazí následující chybová zpráva: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`.

## <a name="find-available-spaces-with-fresh-air"></a>Vyhledání dostupných mezer s čerstvým vzduchem

Ukázka senzoru simuluje hodnoty náhodných dat pro dva senzory. Jedná se o pohyb a oxid uhličitý. Dostupné prostory s čerstvým vzduchem jsou v této ukázce definovány, a to v místnosti bez přítomnosti. Jsou definovány také na úrovni oxidu uhličit v 1 000 ppm. Pokud podmínka není splněná, není místo dostupné nebo je kvalita vzduchu nízká.

1. Otevřete příkazový řádek, který jste použili ke spuštění předchozího kroku zřizování.
1. Spusťte `dotnet run GetAvailableAndFreshSpaces`.
1. Podívejte se na Tento příkazový řádek a na příkazovém řádku data snímače vedle sebe.

    Jedna z příkazového řádku odesílá Simulovaná data o vznesených pohybech a oxidech uhličitých dat do digitálních vláken každých pět sekund. Druhý příkaz přečte graf v reálném čase a zjistí dostupné místnosti s čerstvým vzduchem na základě náhodných simulovaných dat. Zobrazuje jednu z těchto podmínek téměř v reálném čase na základě dat ze senzorů, která byla odeslána naposledy:
   - Dostupné místnosti s čerstvým vzduchem
   - Obsazená nebo špatná kvalita místnosti.

     [@no__t – 1Get dostupné mezery s čerstvým vzduchem](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png)](media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png#lightbox)

Aby bylo možné pochopit, co se stalo v tomto rychlém startu a jaká rozhraní API byla volána, otevřete [Visual Studio Code](https://code.visualstudio.com/Download) s projektem s kódem v pracovním prostoru kód, který najdete v části Digital-zdvojené-Samples Použijte následující příkaz:

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Kurzy se přejdou do kódu. Naučíte se, jak upravit konfigurační data a jaká rozhraní API jsou volána. Další informace o rozhraních API pro správu najdete na stránce digitálních vláken Swagger:

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | Nahradit |
| --- | --- |
| YOUR_INSTANCE_NAME | Název instance digitálního vlákna |
| YOUR_LOCATION | Které oblasti serveru vaše instance hostuje |

Nebo pro usnadnění práce přejděte na [digitální vlákna Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Vyčištění prostředků

V kurzech najdete podrobné informace o tom, jak:

- Sestavte aplikaci pro správce zařízení, aby zvýšila produktivitu uživatelů.
- Účinnější provozování sestavení

Pokud chcete pokračovat v kurzech, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, odstraňte všechny prostředky vytvořené tímto rychlým startem.

1. Odstraňte složku, která byla vytvořena při stažení ukázkového úložiště.
1. V nabídce na levé straně [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**. Pak vyberte svůj prostředek digitálního vlákna. V horní části podokna **všechny prostředky** vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste narazili na potíže s odstraněním instance digitálního vlákna, aktualizovala se aktualizace služby s touto opravou. Zkuste prosím instanci znovu odstranit.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jsme použili jednoduchý scénář, který ukazuje, jak najít místnosti s dobrými provozními podmínkami. Podrobné analýzy tohoto scénáře najdete v tomto kurzu:

>[!div class="nextstepaction"]
>[Kurz: nasazení digitálních vláken Azure a konfigurace prostorového grafu](tutorial-facilities-setup.md)

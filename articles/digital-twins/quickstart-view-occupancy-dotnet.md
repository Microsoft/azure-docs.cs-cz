---
title: Zjištění dostupných místností pomocí služby Azure Digital Twins (C#) | Microsoft Docs
description: V tomto rychlém startu spustíte dvě ukázkové aplikace .NET Core, které budou do prostoru ve službě Azure Digital Twins odesílat simulovaná telemetrická data o pohybu a úrovni oxidu uhličitého. Cílem je najít dostupné místnosti s čerstvým vzduchem pomocí rozhraní API pro správu po výpočetním zpracování v cloudu.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/7/2018
ms.author: alinast
ms.openlocfilehash: 6e83ca543937948ad8028969cceca0f8787972c9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281214"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>Rychlý start: Zjištění dostupných místností pomocí služby Azure Digital Twins

Služba Azure Digital Twins umožňuje vytvořit digitální obrázek vašeho fyzického prostředí. Následně můžete dostávat oznámení od událostí ve vašem prostředí a přizpůsobovat své reakce na tato oznámení. 

Tento rychlý start používá [dvě ukázky v .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) k digitalizaci imaginární kancelářské budovy a ukazuje, jak v této budově najít dostupné místnosti. Pomocí služby Digital Twins můžete ke svému prostředí přidružit několik senzorů. Kromě dostupnosti místnosti můžete také zjistit, jestli je kvalita vzduchu v dostupné místnosti optimální, a to s pomocí simulovaného senzoru úrovně oxidu uhličitého. Jedna z ukázkových aplikací bude generovat náhodná data ze senzorů, která vám pomůžou s vizualizací tohoto scénáře.

Následující video shrnuje nastavení pro rychlý start:

> [!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Požadavky

1. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

1. Dvě konzolové aplikace, které spustíte v tomto rychlém startu, jsou napsané v jazyce C#. Na svém počítači pro vývoj budete muset nainstalovat sadu [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download). Pokud máte sadu .NET Core SDK nainstalovanou, můžete zkontrolovat aktuální verzi jazyka C# na vašem počítači pro vývoj spuštěním příkazu `dotnet --version` na příkazovém řádku.

1. Stáhněte si [ukázkový projekt jazyka C#](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) a extrahujte archiv digital-twins-samples-csharp-master.zip. 


## <a name="create-a-digital-twins-instance"></a>Vytvoření instance služby Digital Twins

Pomocí postupu v této části vytvořte na [portálu](https://portal.azure.com) novou instanci služby Digital Twins.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Nastavení oprávnění pro aplikaci

V této části se vaše ukázková aplikace zaregistruje v Azure Active Directory (AAD), aby mohla přistupovat k instanci služby Digital Twins. Pokud už máte registraci aplikace AAD, můžete ji znovu použít jako ukázku, ale ujistěte se, že je nakonfigurovaná tak, jak je uvedeno v této části. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>Sestavení aplikace

Pomocí následujících kroků sestavte aplikaci pro sledování obsazenosti:

1. Otevřete příkazový řádek a přejděte do složky, do které se extrahovaly soubory z archivu digital-twins-samples-csharp-master.zip.
1. Spusťte `cd occupancy-quickstart/src`.
1. Spusťte `dotnet restore`.
1. Upravte soubor *appSettings.json* a aktualizujte následující proměnné:
    - *ClientId:* Zadejte *ID aplikace* vaší registrace aplikace AAD, které jste si poznamenali v předchozí části.
    - *Tenant:* Zadejte *ID adresáře* vašeho tenanta AAD, které jste si také poznamenali v předchozí části.
    - *BaseUrl:* Adresa URL *rozhraní API pro správu* vaší instance služby Digital Twins, která bude v následujícím formátu: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Zástupné texty v této adrese URL nahraďte hodnotami pro vaši instanci z předchozí části.

## <a name="provision-graph"></a>Zřízení grafu

V tomto kroku se zřídí prostorový graf služby Digital Twins s několika prostory, jedním zařízením, dvěma senzory, vlastní funkcí a jedním přiřazením role. Prostorový graf se zřídí s využitím souboru [*provisionSample.yaml*](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Spusťte `dotnet run ProvisionSample`.
    >[!NOTE]
    >K ověření uživatele v Azure AD používáme nástroj Azure CLI pro přihlášení k zařízení. Uživatel se musí ověřit na [přihlašovací stránce Microsoftu](https://microsoft.com/devicelogin) zadáním poskytnutého kódu. Po zadání kódu postupujte podle zobrazených kroků a dokončete ověření. Když je tento nástroj spuštěný, uživatel se musí vždy ověřit znovu.
    
    >[!TIP]
    > Pokud se vám v tomto kroku zobrazuje následující chyba, zkontrolujte, jestli se správně zkopírovaly proměnné. 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. Krok zřizování může trvat přibližně několik minut. V rámci vaší instance služby Digital Twins se také zřídí IoT Hub a ve smyčce se bude kontrolovat, jestli už je ve stavu Spuštěno (`Running`).

    ![Ukázka zřizování][4]

1. Na konci provádění zkopírujte připojovací řetězec (`ConnectionString`) zařízení, který použijete v ukázce simulátoru zařízení. Zkopírujte pouze řetězec zvýrazněný na následujícím obrázku:

    ![Ukázka zřizování][1]

## <a name="send-sensor-data"></a>Odeslání dat ze senzorů

Aplikaci simulátoru senzorů můžete sestavit a spustit pomocí následujícího postupu:

1. Otevřete nový příkazový řádek a přejděte ke staženému projektu ve složce digital-twins-samples-csharp-master.
1. Spusťte `cd device-connectivity`.
1. Spusťte `dotnet restore`.
1. Upravte soubor *appsettings.json* a aktualizujte *DeviceConnectionString* na hodnotu `ConnectionString` uvedenou výše.
1. Spuštěním příkazu `dotnet run` začněte odesílat data ze senzorů. Měli byste vidět, jak se odesílají do služby Digital Twins, podobně jako na následujícím obrázku:

     ![Připojení zařízení][2]

1. Nechte tento simulátor spuštěný, abyste mohli zobrazit jeho výsledky společně s výsledky akce v dalším kroku. V tomto okně se budou zobrazovat simulovaná data odeslaná ze senzorů do služby Digital Twins a v dalším kroku se bude v reálném čase provádět dotazování těchto dat za účelem zjištění dostupných místností s čerstvým vzduchem.

    >[!TIP]
    > Pokud se vám v tomto kroku zobrazuje následující chyba, zkontrolujte, jestli se správně zkopírovala hodnota `DeviceConnectionString`.  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Zjištění dostupných prostorů s čerstvým vzduchem

Ukázka senzorů simuluje náhodné hodnoty dat pro dva senzory – senzor pohybu a senzor úrovně oxidu uhličitého. Dostupné prostory s čerstvým vzduchem jsou v naší ukázce definované jako prázdné a s úrovní oxidu uhličitého nižší než 1 000 ppm. Pokud tato podmínka není splněná, prostor není dostupný nebo má špatnou kvalitu vzduchu.

1. Otevřete příkazový řádek, ve kterém jste provedli výše uvedený krok zřizování.
1. Spusťte `dotnet run GetAvailableAndFreshSpaces`.
1. Zobrazte si tento příkazový řádek vedle příkazového řádku odesílání dat ze senzorů, jak je vidět níže. 
1. Jeden příkazový řádek každých 5 sekund odesílá do služby Digital Twins simulovaná data o pohybu a úrovni oxidu uhličitého. Druhý příkazový řádek v reálném čase načítá graf a na základě náhodných simulovaných dat hledá dostupné místnosti s čerstvým vzduchem. V závislosti na tom, jaká data se naposledy ze senzorů odeslala, zobrazí téměř v reálném čase jednu z těchto podmínek:
    - Dostupné místnosti s čerstvým vzduchem
    - Obsazené místnosti nebo místnosti se špatnou kvalitou vzduchu

     ![Získání dostupných prostorů s čerstvým vzduchem][3]

Pokud chcete porozumět tomu, co se v tomto rychlém startu stalo a jaká rozhraní API se volala, otevřete ve [Visual Studio Code](https://code.visualstudio.com/Download) projekt pracovního prostoru Code, který najdete ve složce digital-twins-samples-csharp (viz následující příkaz). Tyto kurzy se podrobně zabývají tímto kódem a dozvíte se v nich, jak upravit konfigurační data a jaká rozhraní API se volají. Další vysvětlení rozhraní API pro správu najdete na stránce Swaggeru pro vaši službu Digital Twins na adrese `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger`. Případně si pro zjednodušení můžete projít [Swagger pro službu Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger). 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tyto kurzy podrobně popisují sestavení aplikace, která správcům budov umožní zvýšit produktivitu uživatelů a zefektivnit provoz budovy.

Pokud chcete pokračovat k těmto kurzům, neprovádějte čištění prostředků vytvořených v rámci tohoto rychlého startu. Pokud pokračovat nechcete, pomocí následujícího postupu odstraňte všechny prostředky vytvořené tímto rychlým startem:

1. Odstraňte složku, která se vytvořila při stahování ukázkového úložiště.
1. V levé nabídce na webu [Azure Portal](http://portal.azure.com) klikněte na **Všechny prostředky** a vyberte svůj prostředek Digital Twins. V horní části podokna **Všechny prostředky** klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste získali přehled jednoduchého scénáře zjišťování místností s dobrými pracovními podmínkami. Podrobnější analýzu tohoto scénáře najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png

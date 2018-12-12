---
title: Nasazení služby Azure Digital Twins | Microsoft Docs
description: Zjistěte, jak nasadit instanci služby Azure digitální dvojče a nakonfigurovat prostředky prostorových pomocí kroků v tomto kurzu.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 61b81602342b910a50c0cc6318746ec85a659a92
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53080585"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu

Použít službu Azure digitální dvojče spojit lidé, místa a zařízení v přeměnit prostorových systému. Tato série kurzů ukazuje, jak použít k detekci obsazení místa s optimálních podmínek jakosti teplotu a air digitální dvojče Azure. 

Tyto kurzy vás provedou konzolovou aplikaci .NET pro vytváření scénáři kancelářskou budovu. Sestavování má více podlahy a místnosti v rámci každý floor. Místnostech obsahovat zařízení připojených senzorů, které detekce pohybu, okolní teploty a vzduchu kvality. 

Se dozvíte, jak pomocí služby Azure digitální dvojče replikovat fyzické oblasti a entity v budově jako digitální objekty. Události zařízení budete simulovat pomocí jiného konzolové aplikace. Poté se dozvíte, jak monitorovat události, které pocházejí z těchto fyzických oblasti a entity téměř v reálném čase. 

Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům pracujícím v budově s rezervací zasedacích místností s optimálními podmínkami. Správce zařízení sady office můžete použít nastavení trendy využití místností a sledovat práci podmínky pro účely údržby.

V prvním kurzu této série získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasazení digitální dvojče.
> * Udělení oprávnění pro vaši aplikaci.
> * Upravte digitální dvojče ukázkovou aplikaci.
> * Zřízení vašeho sestavení.


V těchto kurzech se za účelem podrobnějšího pokrytí konceptů používají a upravují stejné ukázky jako v [rychlém startu věnovaném zjištění dostupných místností](quickstart-view-occupancy-dotnet.md).


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- .NET Core SDK. Ukázky Azure digitální dvojče použité v těchto kurzech jsou napsané v C#. Ujistěte se, že k instalaci [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) na vývojovém počítači sestavení a spuštění ukázky. Zkontrolujte, že správnou verzi je nainstalovaný na svém počítači spuštěním `dotnet --version` v příkazovém okně.

- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Nasazení služby Digital Twins

Chcete-li vytvořit novou instanci služby Azure digitální dvojče postupujte podle kroků v této části. Je možné vytvořit pouze jednu instanci na jedno předplatné. Pokud už máte jedno spuštěné, přejděte k další části. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Udělení oprávnění pro aplikaci

Digitální používá Dvojčata [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) k řízení [přístup pro čtení/zápis](../active-directory/develop/v1-permissions-and-consent.md) ke službě. Všechny aplikace, které potřebuje pro připojení k vaší instanci digitální dvojče musí být zaregistrovaná s Azure AD. Postup v této části ukazuje, jak zaregistrovat ukázkovou aplikaci.

Pokud už máte registrace aplikace, můžete znovu použít pro vaši ukázku. Projděte si však tuto část a ujistěte se, že je vaše registrace aplikace správně nakonfigurovaná.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-the-digital-twins-sample"></a>Konfigurace digitální dvojče ukázky

Tato část vás provede digitální dvojče Azure aplikaci, která komunikuje se službou [digitální dvojče rozhraní REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Stažení ukázky
Pokud už máte stažené ukázky pro [rychlý start věnovaný zjištění dostupných místností](quickstart-view-occupancy-dotnet.md), můžete tyto kroky přeskočit.

1. Stáhněte si [ukázky digitální dvojče .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Extrahujte obsah složky zip na svém počítači. 

### <a name="explore-the-sample"></a>Zkoumání ukázky
Ve složce extrahované vzorku, otevřete soubor **digital-twins-samples-csharp\digital-twins-samples.code-workspace** ve Visual Studio Code. Obsahuje dva projekty: 

* Můžete použít ukázkou zřizování **obsazení quickstart** ke konfiguraci a zřízení [Prostorové řady grafu](concepts-objectmodel-spatialgraph.md#graph). Tento graf je digitalizovaná obrázek prostory vaší fyzické a prostředky v nich. Používá [objektový model](concepts-objectmodel-spatialgraph.md#model), která definuje objekty pro Chytré budovy. Úplný seznam objektů digitální dvojče a rozhraní REST API, navštivte [této dokumentace k rozhraní REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger) nebo adresy URL rozhraní API Management, pro kterou byla vytvořena [vaší instance](#deploy).

   Prozkoumat ukázku naleznete v tématu jak komunikuje s vaší instancí digitální dvojče, můžete začít s **src\actions** složky. Soubory v této složce implementovat příkazy, které použijete v následujících kurzech:
    - **ProvisionSample.cs** soubor ukazuje, jak zřídit prostorový graf.
    - **GetSpaces.cs** soubor získá informace o prostorech zřízené.
    - **GetAvailableAndFreshSpaces.cs** soubor získá výsledky vlastní funkci s názvem uživatelem definované funkce.
    - **CreateEndpoints.cs** soubor vytvoří koncové body pro interakci s dalšími službami.

* Ukázka simulace **připojení zařízení** simuluje data ze senzorů a odesílá je do služby IoT hub, který je pro vaši instanci digitální dvojče zřízený. V této ukázce použijete [v dalším kurzu po zřízení prostorový graf](tutorial-facilities-udf.md#simulate). Identifikátory ze senzorů a zařízení, které můžete použít ke konfiguraci této ukázce by měl být stejný jako budete používat ke zřízení grafu.

### <a name="configure-the-provisioning-sample"></a>Konfigurace ukázky zřizování
1. Otevřete okno příkazového řádku a přejděte na staženou ukázku. Spusťte následující příkaz:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Spuštěním následujícího příkazu obnovte závislosti ukázkového projektu:

    ```cmd/sh
    dotnet restore
    ```

1. V sadě Visual Studio Code otevřete **appSettings.json** soubor **vytížení – rychlý Start** projektu. Aktualizací následujících hodnot:
   * **ClientId**: Zadejte ID aplikace pro registraci aplikace Azure AD. Jste si poznamenali v části toto ID ve kterém jste [nastavení oprávnění aplikace](#permissions).
   * **Tenant**: Zadejte ID adresáře vašeho [tenanta Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Také uvedené v části toto ID ve kterém jste [nastavení oprávnění aplikace](#permissions).
   * **BaseUrl:** Zadejte adresu URL vaší instance služby Digital Twins. Tuto adresu URL získáte nahrazením zástupných textů v následující adrese URL za odpovídající hodnoty pro vaši instanci: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_. Tuto adresu URL můžete získat také změnou adresy URL rozhraní API správy z [část nasazení](#deploy). Nahraďte **swagger /** s **api/v1.0/**.

1. Podívejte se do seznamu digitální dvojče funkce, které můžete prozkoumat pomocí ukázky. Spusťte následující příkaz:

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-the-provisioning-process"></a>Vysvětlení procesu zřizování
Tato část ukazuje, jak ukázka zřizuje prostorový graf budovy. 

V aplikaci Visual Studio Code, přejděte **obsazení quickstart\src\actions** složky a otevřete soubor **provisionSample.cs**. Všimněte si následující funkce:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Tato funkce využívá **provisionSample.yaml** ve stejné složce. Tento soubor otevřít, a poznamenejte si hierarchii kancelářskou budovu: *příslušností*, *Floor*, *oblasti*, a *místnosti*. Každý z těchto fyzických prostorů může obsahovat *zařízení* a *senzory*. Každý záznam obsahuje i předdefinovanou `type` &mdash;například Floor, místnosti. 

Ukázka **yaml** soubor obsahuje prostorový graf, který používá `Default` digitální dvojče objektový model. Tento model poskytuje obecné názvy pro většinu typů. Obecné názvy jsou dostačující pro budovy. Příklady jsou teploty SensorDataType a mapování pro SpaceBlobType. Typ místa příklad je místnosti s podtypy FocusRoom ConferenceRoom a tak dále. 

Kdybyste potřebovali vytvořit prostorový graf pro jiný typ místa, například pro továrnu, pravděpodobně byste potřebovali jiný objektový model. Můžete zjistit, které modely jsou k dispozici pro použití pomocí příkazu `dotnet run GetOntologies` na příkazovém řádku ukázkou zřizování. 

Další informace o prostorové grafy a objektové modely, najdete v článku [modely a Prostorové řady grafu objektů Principy digitální dvojče](concepts-objectmodel-spatialgraph.md). 

### <a name="modify-the-sample-spatial-graph"></a>Upravit ukázkové prostorových grafu
**ProvisionSample.yaml** soubor obsahuje následující uzly:

- **prostředky**: `resources` uzel vytvoří prostředek služby Azure IoT Hub ke komunikaci se zařízeními ve vašem nastavení. Služby IoT hub v kořenovém uzlu grafu může komunikovat s všech zařízení a senzorů v grafu.  

- **spaces:** V objektovém modelu služby Digital Twins představuje uzel `spaces` fyzická umístění. Má každý prostor `Type` &mdash;například oblasti, místa nebo zákazník&mdash;a popisný `Name`. Mezery může patřit do jiné prostory vytváření hierarchickou strukturu. Soubor provisionSample.yaml obsahuje prostorový graf imaginární budovy. Poznámka: logické vnoření prostory typu `Floor` v rámci `Venue`, `Area` v dolní mez, a `Room` uzly do oblasti. 

- **devices:** Prostory můžou obsahovat zařízení (`devices`), což jsou fyzické nebo virtuální entity, které spravují několik senzorů. Zařízení může být například telefonu uživatele, pod senzor Raspberry Pi nebo brány. V imaginární budově v naší ukázce si všimněte, že místnost **Focus Room** obsahuje zařízení **Raspberry Pi 3 A1**. Jednotlivé uzly zařízení jsou identifikované jedinečnou hodnotou `hardwareId` (ID hardwaru), která je v této ukázce pevně zakódovaná. Pokud chcete tuto ukázku nakonfigurovat pro skutečný provoz v produkčním prostředí, nahraďte tyto hodnoty odpovídajícími hodnotami z vašeho systému.  

- **senzorů**: zařízení může obsahovat více `sensors`. Dokáže detekovat a teploty, pohybu a stav baterie, jako jsou fyzické změny záznamu. Každý uzel senzoru je jednoznačně identifikovaný hodnotou `hardwareId` (ID hardwaru), která je zde pevně zakódovaná. Pro aplikace skutečný nahraďte tyto pomocí jedinečných identifikátorů pro čidel v nastavení aplikace. Soubor provisionSample.yaml má dvě senzorů k zaznamenání *pohybu* a *CarbonDioxide*. Přidejte další senzor, který bude zaznamenávat teplotu (*Temperature*), a to přidáním následujících řádků pod řádky senzoru CarbonDioxide. Všimněte si, že tyto jsou k dispozici v provisionSample.yaml jako komentovaná řádky. Můžete je Odkomentujte tak, že odeberete `#` znak ve každého řádku. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Ujistěte se, `dataType` a `hardwareId` klíče bylo v souladu s příkazy nad tento fragment kódu. Také se ujistěte, že váš editor nenahrazuje mezery tabulátorem. 

Uložte a zavřete soubor provisionSample.yaml. V dalším kurzu budete do tohoto souboru přidejte další informace a pak zřízení budovy ukázka digitální dvojče Azure.

> [!TIP]
> Můžete zobrazit a upravit pomocí vaší prostorový graf [prohlížeč Azure digitální dvojče grafu](https://github.com/Azure/azure-digital-twins-graph-viewer).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zastavit v tuto chvíli seznámení digitální dvojče Azure, bez obav odstraňte prostředky vytvořené v tomto kurzu:

1. V levé nabídce v [webu Azure portal](http://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků digitální dvojče a vyberte **odstranit**.
   
    > [!TIP]
    > Pokud zaznamenal/zaznamenala jste potíže odstraníte instanci digitální dvojče, aktualizace služby se týká jenom s opravou. Zkuste to prosím znovu odstraníte instanci.

1. V případě potřeby odstraňte ukázkovou aplikaci na svém počítači práce. 


## <a name="next-steps"></a>Další postup

Chcete-li zjistěte, jak implementovat vlastní logiku monitorování podmínek ve vaší ukázce sestavování, přejděte na další kurz v této sérii: 
> [!div class="nextstepaction"]
> [Kurz: Zřízení budovy a monitorování pracovních podmínek](tutorial-facilities-udf.md)


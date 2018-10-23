---
title: Nasazení služby Azure Digital Twins | Microsoft Docs
description: Zjistěte, jak pomocí kroků v tomto kurzu nasadit instanci služby Azure Digital Twins a nakonfigurovat prostorové prostředky.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 9ca8a9de9a286d4b14dae4a822f3e9baf4747c60
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363329"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Kurz: Nasazení služby Azure Digital Twins a konfigurace prostorového grafu

Služba Azure Digital Twins umožňuje propojovat lidi, místa a zařízení v koherentním prostorovém systému. Tato série kurzů ukazuje, jak pomocí služby Azure Digital Twins detekovat obsazenost místností s optimálními podmínkami, co se týče teploty a kvality vzduchu. Tyto kurzy vás provedou konzolovou aplikací .NET a vytvořením scénáře vícepodlažní kancelářské budovy s několika místnostmi na jednotlivých podlažích. V místnostech jsou zařízení s připojenými senzory, které detekují pohyb, teplotu okolí a kvalitu vzduchu. Dozvíte se, jak s využitím služby Digital Twins replikovat fyzické oblasti a entity v budově jako digitální objekty. Pomocí jiné konzolové aplikace budete simulovat události zařízení. Následně zjistíte, jak téměř v reálném čase monitorovat události pocházející z těchto fyzických oblastí a entit. Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům pracujícím v budově s rezervací zasedacích místností s optimálními podmínkami. Správce kancelářských prostor může s využitím tohoto systému zjišťovat trendy využívání místností a také monitorovat pracovní podmínky pro účely údržby.

V prvním kurzu této série získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasazení služby Digital Twins
> * Udělení oprávnění pro aplikaci
> * Úprava ukázkové aplikace Digital Twins
> * Zřízení budovy


V těchto kurzech se za účelem podrobnějšího pokrytí konceptů používají a upravují stejné ukázky jako v [rychlém startu věnovaném zjištění dostupných místností](quickstart-view-occupancy-dotnet.md).


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Ukázky služby Digital Twins použité v těchto kurzech jsou napsané v jazyce C#. Na počítač pro vývoj nezapomeňte nainstalovat sadu [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) pro sestavení a spuštění ukázky. Spuštěním příkazu `dotnet --version` v příkazovém okně zkontrolujte, jestli je na vašem počítači spuštěná správná verze.

- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Nasazení služby Digital Twins

Pomocí postupu v této části vytvořte novou instanci služby Digital Twins. Pro každé předplatné je možné vytvořit pouze jednu instanci – pokud již nějakou máte spuštěnou, přeskočte k následující části. 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Udělení oprávnění pro aplikaci

K řízení [přístupu pro čtení i zápis](../active-directory/develop/v1-permissions-and-consent.md) ke službě Digital Twins se používá [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md). Všechny aplikace, které se potřebují připojit k instanci služby Digital Twins, musí být zaregistrované v Azure Active Directory. Postup v této části ukazuje, jak zaregistrovat ukázkovou aplikaci.

Pokud už máte *registraci aplikace*, můžete ji znovu použít jako ukázku. Projděte si však tuto část a ujistěte se, že je vaše registrace aplikace správně nakonfigurovaná.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>Konfigurace ukázky služby Digital Twins

Tato část vás provede aplikací Digital Twins, která komunikuje s [rozhraními API služby Digital Twins](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Stažení ukázky
Pokud už máte stažené ukázky pro [rychlý start věnovaný zjištění dostupných místností](quickstart-view-occupancy-dotnet.md), můžete tyto kroky přeskočit.

1. Stáhněte si [ukázky služby Digital Twins v .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Obsah složky ZIP extrahujte do svého počítače. 

### <a name="explore-the-sample"></a>Zkoumání ukázky
Ve Visual Studio Code otevřete soubor **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** z extrahované složky s ukázkou. Obsahuje dva projekty: 

1. Ukázka zřizování **_occupancy-quickstart_** umožňuje nakonfigurovat a zřídit [graf prostorové inteligence](concepts-objectmodel-spatialgraph.md#graph), což je digitalizovaný obrázek fyzických prostorů a prostředků, které obsahují. Využívá [objektový model](concepts-objectmodel-spatialgraph.md#model), který definuje objekty pro inteligentní budovu. Úplný seznam rozhraní REST API a objektů služby Digital Twins najdete v [této dokumentaci k rozhraní REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger) nebo na adrese URL **rozhraní API pro správu** vytvořené pro [vaši instanci](#deploy).

   Pokud chcete zjistit, jak aplikace komunikuje s vaší instancí služby Digital Twins, můžete při jejím zkoumání začít složkou **_src\actions_**. Soubory v této složce implementují příkazy, které budete v těchto kurzech používat:
    - Soubor *provisionSample.cs* ukazuje, jak zřídit prostorový graf.
    - Soubor *getSpaces.cs* získá informace o zřízených prostorech.
    - Soubor *getAvailableAndFreshSpaces.cs* získá výsledky vlastní funkce označované jako uživatelem definovaná funkce.
    - Soubor *createEndpoints.cs* vytvoří koncové body pro interakci s dalšími službami.

1. Ukázková simulace **_device-connectivity_** simuluje data ze senzorů a odesílá je do centra IoT zřízeného pro vaši instanci služby Digital Twins. Tuto ukázko použijete v [dalším kurzu po zřízení prostorového grafu](tutorial-facilities-udf.md#simulate). Identifikátory senzorů a zařízení použité ke konfiguraci této ukázky musí být stejné jako identifikátory, které použijete ke zřízení grafu.

### <a name="configure-the-provisioning-sample"></a>Konfigurace ukázky zřizování
1. Otevřete příkazové okno a přejděte ke stažené ukázce. Spusťte následující příkaz:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Spuštěním následujícího příkazu obnovte závislosti ukázkového projektu:

    ```cmd/sh
    dotnet restore
    ```

1. Ve Visual Studio Code otevřete soubor **_appSettings.json_** z projektu **occupancy-quickstart** a aktualizujte následující hodnoty:
    1. *ClientId:* Zadejte **ID aplikace** vaší registrace aplikace AAD, které jste si poznamenali v části popisující [nastavení oprávnění aplikace](#permissions).
    1. *Tenant:* Zadejte **ID adresáře** vašeho [tenanta AAD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant), které jste si také poznamenali v části popisující [nastavení oprávnění aplikace](#permissions).
    1. *BaseUrl:* Zadejte adresu URL vaší instance služby Digital Twins. Tuto adresu URL získáte nahrazením zástupných textů v následující adrese URL za odpovídající hodnoty pro vaši instanci: **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/**. Tuto adresu URL můžete získat také upravením adresy URL **rozhraní API pro správu** z [části popisující nasazení](#deploy), ve které nahradíte **swagger/** za **api/v1.0/**.

1. Spuštěním následujícího příkazu zobrazíte seznam funkcí služby Digital Twins, které můžete pomocí této ukázky prozkoumat.

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>Vysvětlení procesu zřizování
Tato část ukazuje, jak ukázka zřizuje prostorový graf budovy. 

Ve Visual Studio Code přejděte do složky **_occupancy-quickstart\src\actions_** a otevřete soubor *provisionSample.cs*. Všimněte si následující funkce:

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

Tato funkce používá soubor *provisionSample.yaml* ve stejné složce. Otevřete tento soubor a všimněte si hierarchie kancelářské budovy: *Venue* (Místo), *Floor* (Podlaží), *Area* (Oblast) a *Rooms* (Místnosti). Každý z těchto fyzických prostorů může obsahovat *zařízení* a *senzory*. Každý záznam má předdefinovaný typ (`type`), například *Floor* (Podlaží) nebo *Room* (Místnost). 

Ukázkový soubor *yaml* ukazuje prostorový graf využívající objektový model `Default` služby Digital Twins. Tento model poskytuje obecné názvy pro většinu typů (například Temperature místo SensorDataType, Map místo SpaceBlobType) a typy prostorů (například Room s podtypy FocusRoom, ConferenceRoom atd.), což je pro budovu dostačující. Kdybyste potřebovali vytvořit prostorový graf pro jiný typ místa, například pro továrnu, pravděpodobně byste potřebovali jiný objektový model. Pokud chcete zjistit, jaké modely jsou k dispozici, na příkazovém řádku pro ukázku zřizování spusťte příkaz `dotnet run GetOntologies`. Další podrobnosti o prostorových grafech a objektových modelech najdete v tématu [Vysvětlení grafu prostorové inteligence a objektového modelu služby Digital Twins](concepts-objectmodel-spatialgraph.md). 

### <a name="modify-sample-spatial-graph"></a>Úprava ukázkového prostorového grafu
Soubor *provisionSample.yaml* obsahuje následující uzly:

- **resources:** Uzel `resources` vytvoří prostředek služby IoT Hub pro komunikaci se zařízeními ve vašem systému. Centrum IoT v kořenovém uzlu grafu může komunikovat se všemi zařízeními a senzory v grafu.  

- **spaces:** V objektovém modelu služby Digital Twins představuje uzel `spaces` fyzická umístění. Každý prostor má typ (`Type`), například *Region* (Oblast), *Venue* (Místo) nebo *Customer* (Zákazník), a popisný název (`Name`). Prostory můžou patřit do jiných prostorů a tvořit tak hierarchickou strukturu. Soubor *provisionSample.yaml* obsahuje prostorový graf imaginární budovy. Všimněte si logického vnoření prostorů typu `Floor` v rámci `Venue`, `Area` na podlaží a uzlů `Room` v oblasti. 

- **devices:** Prostory můžou obsahovat zařízení (`devices`), což jsou fyzické nebo virtuální entity, které spravují několik senzorů. Zařízením může být například telefon uživatele, pod senzoru Raspberry Pi, brána firewall atd. V imaginární budově v naší ukázce si všimněte, že místnost *Focus Room* obsahuje zařízení *Raspberry Pi 3 A1*. Jednotlivé uzly zařízení jsou identifikované jedinečnou hodnotou `hardwareId` (ID hardwaru), která je v této ukázce pevně zakódovaná. Pokud chcete tuto ukázku nakonfigurovat pro skutečný provoz v produkčním prostředí, nahraďte tyto hodnoty odpovídajícími hodnotami z vašeho systému.  

- **sensors:** Zařízení může obsahovat několik senzorů (`sensors`), které můžou detekovat a zaznamenávat fyzické změny, jako je například teplota, pohyb, stav baterie atd. Každý uzel senzoru je jednoznačně identifikovaný hodnotou `hardwareId` (ID hardwaru), která je zde pevně zakódovaná. Ve skutečné aplikaci tyto hodnoty nahraďte jedinečnými identifikátory senzorů ve vašem systému. Soubor *provisionSample.yaml* obsahuje dva senzory pro zaznamenávání pohybu (*Motion*) a úrovně oxidu uhličitého (*CarbonDioxide*). Přidejte další senzor, který bude zaznamenávat teplotu (*Temperature*), a to přidáním následujících řádků pod řádky senzoru CarbonDioxide. Všimněte si, že tyto řádky jsou v souboru *provisionSample.yaml* označené jako komentář. Komentář můžete jednoduše zrušit odebráním znaku # na začátku každého řádku. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Ujistěte se, že zarovnání klíčů `dataType` a `hardwareId` odpovídá zarovnání příkazů nad tímto fragmentem kódu. Také se ujistěte, že váš editor nenahrazuje mezery tabulátorem. 

Uložte a zavřete soubor *provisionSample.yaml*. V dalším kurzu do tohoto souboru přidáte další informace a pak zřídíte ukázkovou budovu Azure Digital Twins.


## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už službu Azure Digital Twins nechcete dál zkoumat, můžete prostředky vytvořené v rámci tohoto kurzu odstranit:

1. V levé nabídce na webu [Azure Portal](http://portal.azure.com) klikněte na **Všechny prostředky**, vyberte svou skupinu prostředků Digital Twins a **odstraňte** ji.
2. V případě potřeby můžete pokračovat odstraněním ukázkové aplikace i na pracovním počítači. 


## <a name="next-steps"></a>Další kroky

V dalším kurzu této série se dozvíte, jak implementovat vlastní logiku monitorování podmínek v ukázkové budově. 
> [!div class="nextstepaction"]
> [Kurz: Zřízení budovy a monitorování pracovních podmínek](tutorial-facilities-udf.md)


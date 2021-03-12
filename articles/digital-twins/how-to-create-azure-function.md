---
title: Nastavení funkce v Azure pro zpracování dat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit funkci v Azure, která může přistupovat k digitálnímu vlákna a aktivovat je.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7bb9b6d4a6ca006952d709244e6526345d44431e
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102630253"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Připojení aplikací Function App v Azure pro zpracování dat

Aktualizace digitálních vláken na základě dat se zpracovává pomocí [**tras událostí**](concepts-route-events.md) prostřednictvím výpočetních prostředků, jako je například funkce, která je vytvořena pomocí [Azure Functions](../azure-functions/functions-overview.md). Funkce lze použít k aktualizaci digitálního vlákna v reakci na:
* data telemetrie zařízení přicházejících z IoT Hub
* Změna vlastnosti nebo jiná data přicházející z jiného digitálního vlákna v rámci grafu s dvojitou přesností

Tento článek vás provede vytvořením funkce v Azure pro použití s digitálními Vlákenmi Azure. 

Tady je přehled kroků, které obsahuje:

1. Vytvoření projektu Azure Functions v sadě Visual Studio
2. Zápis funkce s triggerem [Event Grid](../event-grid/overview.md)
3. Přidání ověřovacího kódu do funkce (aby bylo možné získat přístup k digitálním Vlákenám Azure)
4. Publikování aplikace funkcí do Azure
5. Nastavení přístupu [zabezpečení](concepts-security.md) pro aplikaci Function App

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Předpoklad: nastavení instance digitálních vláken Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Vytvoření aplikace Function App v aplikaci Visual Studio

V aplikaci Visual Studio 2019 vyberte _soubor > nový > projekt_ a vyhledejte šablonu _Azure Functions_ . Vyberte _Další_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazeným dialogovým oknem nový projekt Je zvýrazněna šablona projektu Azure Functions.":::

Zadejte název aplikace Function App a vyberte _vytvořit_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Snímek obrazovky sady Visual Studio zobrazující dialog pro konfiguraci nového projektu, včetně názvu projektu, umístění pro uložení, volby pro vytvoření nového řešení a názvu řešení.":::

Vyberte typ Function App *Event Grid Trigger* a vyberte _vytvořit_.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Snímek obrazovky sady Visual Studio zobrazující dialogové okno pro vytvoření nové aplikace Azure Functions. Je zvýrazněna možnost Trigger Event Grid.":::

Po vytvoření aplikace Function App vytvoří Visual Studio ukázku kódu v souboru **function1.cs** ve složce projektu. Tato krátká funkce se používá k protokolování událostí.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Snímek obrazovky sady Visual Studio v okně projektu pro nový projekt, který byl vytvořen. Pro ukázkovou funkci s názvem Function1 je k dispozici kód." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Zápis funkce s triggerem Event Grid

Funkci můžete napsat přidáním sady SDK do aplikace Function App. Aplikace Function App komunikuje s digitálními interakcemi Azure pomocí [sady Azure Digital Revlákens SDK pro .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

Aby bylo možné použít sadu SDK, budete muset do svého projektu zahrnout následující balíčky. Balíčky můžete nainstalovat pomocí Správce balíčků NuGet sady Visual Studio nebo balíčky přidat pomocí `dotnet` nástroje v nástroji příkazového řádku.

* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. identity](https://www.nuget.org/packages/Azure.Identity/)
* [System .NET. http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. Core](https://www.nuget.org/packages/Azure.Core/)

Potom ve Visual Studiu Průzkumník řešení otevřete soubor _function1.cs_ , kde máte vzorový kód a přidejte následující `using` příkazy pro tyto balíčky do funkce.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Přidání ověřovacího kódu do funkce

Nyní deklarujete proměnné na úrovni třídy a přidáte ověřovací kód, který umožní funkci přístup k digitálním Vlákenám Azure. Do souboru _function1.cs_ přidáte následující funkce.

* Kód pro čtení adresy URL služby Azure Digital jako **proměnné prostředí** Je dobrým zvykem přečíst si adresu URL služby z proměnné prostředí, ale nemusíte ji pevně zakódovat do funkce. [Později v tomto článku](#set-up-security-access-for-the-function-app)nastavíte hodnotu této proměnné prostředí. Další informace o proměnných prostředí najdete v tématu [*Správa aplikace Function App*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* Statická proměnná pro uložení instance HttpClient HttpClient je poměrně nákladný k vytvoření a chceme se vyhnout nutnosti udělat si to pro každé vyvolání funkce.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* Přihlašovací údaje spravované identity můžete použít v Azure Functions.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* Přidejte místní proměnnou _DigitalTwinsClient_ do funkce, která bude uchovávat vaši instanci klienta služby Azure Digital revariablees. Nevytvářejte *tuto* proměnnou v rámci vaší třídy staticky.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* Přidejte kontrolu s hodnotou null pro _adtInstanceUrl_ a zabalte svou logiku funkcí do bloku try/catch pro zachycení jakýchkoli výjimek.

Po těchto změnách bude kód vaší funkce podobný následujícímu:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Teď, když je vaše aplikace vytvořená, můžete ji publikovat do Azure pomocí kroků v následující části.

## <a name="publish-the-function-app-to-azure"></a>Publikování aplikace funkcí do Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-function-publish"></a>Ověřit publikování funkce

1. Přihlaste se pomocí svých přihlašovacích údajů v [Azure Portal](https://portal.azure.com/).
2. Na panelu hledání v horní části okna vyhledejte **název aplikace Function App**.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="V Azure Portal vyhledejte svou aplikaci Function App s názvem." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Na stránce *Function App* , která se otevře, vyberte v možnostech nabídky vlevo možnost *funkce* . Pokud je vaše funkce úspěšně publikovaná, zobrazí se v seznamu název vaší funkce.
Všimněte si, že možná budete muset několik minut počkat nebo aktualizovat stránku několikrát, než uvidíte svoji funkci uvedenou v seznamu publikovaných funkcí.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Zobrazení publikovaných funkcí v Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Aby aplikace Function App mohla získat přístup k digitálním funkcím Azure, bude potřebovat identitu spravovanou systémem s oprávněními pro přístup k instanci digitálních vláken Azure. Nastavíte tuto hodnotu jako další.

## <a name="set-up-security-access-for-the-function-app"></a>Nastavení přístupu zabezpečení pro aplikaci Function App

Přístup k zabezpečení pro aplikaci Function App můžete nastavit buď pomocí rozhraní příkazového řádku Azure nebo Azure Portal. Postupujte podle pokynů níže.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

Tyto příkazy můžete spustit v [Azure Cloud Shell](https://shell.azure.com) nebo v [místní instalaci Azure CLI](/cli/azure/install-azure-cli).

### <a name="assign-access-role"></a>Přiřadit roli přístupu

Funkce kostry z předchozích příkladů vyžaduje předání nosných tokenů, aby bylo možné provést ověření pomocí digitálních vláken Azure. Abyste se ujistili, že je tento nosný token předán, budete muset nastavit oprávnění [Identita spravované služby (MSI)](../active-directory/managed-identities-azure-resources/overview.md) pro aplikaci Function App pro přístup k digitálnímu vlákna Azure. Tento postup je nutné provést pouze jednou pro každou aplikaci Function App.

Pomocí spravované systémové identity aplikace Function App můžete dát IT roli _**vlastníka dat**_ k digitálnímu vynechání dat Azure pro instanci digitálních vláken Azure. Tím se v instanci poskytne oprávnění aplikace Function App, aby se prováděly aktivity roviny dat. Pak zajistěte, aby byla adresa URL instance digitálního vlákna Azure dostupná pro vaši funkci nastavením proměnné prostředí.

1. Chcete-li zobrazit podrobnosti o identitě spravované systémem pro funkci, použijte následující příkaz. Poznamenejte si pole _principalId_ ve výstupu.

    ```azurecli-interactive 
    az functionapp identity show -g <your-resource-group> -n <your-App-Service-(function-app)-name> 
    ```

    >[!NOTE]
    > Pokud je výsledek prázdný místo zobrazení podrobností identity, vytvořte novou systémově spravovanou identitu pro funkci pomocí tohoto příkazu:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>  
    >```
    >
    > Ve výstupu se pak zobrazí podrobnosti o identitě, včetně _principalId_ hodnoty požadované pro další krok. 

1. Pomocí hodnoty _principalId_ v následujícím příkazu přiřaďte identitu aplikace funkcí roli _Vlastník dat služby Azure Digital Twins_ pro vaši instanci Azure Digital Twins.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Konfigurace nastavení aplikace

Nakonec zajistěte, aby byla adresa URL instance digitálního vlákna Azure dostupná pro vaši funkci nastavením **proměnné prostředí** pro ni. Další informace o proměnných prostředí najdete v tématu [*Správa aplikace Function App*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> Adresa URL instance digitálních vláken Azure se provede přidáním *https://* na začátek *názvu hostitele* vaší instance digitálního vlákna Azure. Chcete-li zobrazit název hostitele spolu se všemi vlastnostmi vaší instance, můžete spustit `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Proveďte následující kroky v [Azure Portal](https://portal.azure.com/).

### <a name="assign-access-role"></a>Přiřadit roli přístupu

Spravovaná identita přiřazená systémem umožňuje prostředkům Azure ověřování v cloudových službách (například Azure Key Vault) bez uložení přihlašovacích údajů do kódu. Po povolení se všechna potřebná oprávnění dají udělit prostřednictvím řízení přístupu na základě role v Azure. Životní cyklus tohoto typu spravované identity je vázaný na životní cyklus tohoto prostředku. Každý prostředek navíc může mít pouze jednu spravovanou identitu přiřazenou systémem.

1. V [Azure Portal](https://portal.azure.com/)vyhledejte aplikaci Function App zadáním jejího názvu do vyhledávacího panelu. Z výsledků vyberte svou aplikaci. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Snímek obrazovky Azure Portal: název aplikace Function App se prohledává na panelu hledání portálu a výsledek hledání je zvýrazněný.":::

1. Na stránce Function App vyberte v navigačním panelu vlevo možnost _Identita_ , abyste mohli pracovat se spravovanou identitou funkce. Na stránce _přiřazený systém_ ověřte, že je _stav_ nastavený na **zapnuto** (Pokud není, nastavte ho hned a *uložte* změny).

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Snímek obrazovky Azure Portal: na stránce Identita pro aplikaci Function App je možnost stav nastavená na zapnuto." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Vyberte tlačítko _přiřazení rolí Azure_ , ve kterém se otevře stránka *přiřazení rolí Azure* .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Snímek obrazovky Azure Portal: zvýrazněte tlačítko přiřazení role Azure v části oprávnění na stránce Identita funkce Azure Functions." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Vyberte _+ Přidat přiřazení role (Preview)_.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Snímek obrazovky Azure Portal: zvýrazněte možnost kolem + přidat přiřazení role (Preview) na stránce přiřazení rolí Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Na stránce _Přidat přiřazení role (Preview)_ , která se otevře, vyberte následující hodnoty:

    * **Obor:** Skupina prostředků
    * **Předplatné**: vyberte předplatné Azure.
    * **Skupina prostředků**: z rozevíracího seznamu vyberte svoji skupinu prostředků.
    * **Role**: vyberte z rozevíracího seznamu možnost _vlastník dat digitálních vláken Azure_ .

    Pak podrobnosti uložte kliknutím na tlačítko _Uložit_ .

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Snímek obrazovky Azure Portal: dialogové okno pro přidání přiřazení nové role (Preview). K dispozici jsou pole pro rozsah, předplatné, skupinu prostředků a roli.":::

### <a name="configure-application-settings"></a>Konfigurace nastavení aplikace

K tomu, aby byla adresa URL instance digitálního vlákna Azure dostupná pro vaši funkci, můžete pro ni nastavit **proměnnou prostředí** . Další informace o proměnných prostředí najdete v tématu [*Správa aplikace Function App*](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). Nastavení aplikace jsou vystavena jako proměnné prostředí pro přístup k instanci digitálních vláken Azure. 

Pokud chcete nastavit proměnnou prostředí s adresou URL vaší instance, načtěte adresu URL tak, že vyhledáte název hostitele instance digitálního vlákna Azure. Na panelu hledání [Azure Portal](https://portal.azure.com) vyhledejte vaši instanci. Pak na levém navigačním panelu vyberte _Přehled_ a zobrazte _název hostitele_. Zkopírujte tuto hodnotu.

:::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Snímek obrazovky Azure Portal: na stránce s přehledem instance digitálního vlákna Azure je hodnota název hostitele zvýrazněna.":::

Nyní můžete vytvořit nastavení aplikace pomocí těchto kroků:

1. V panelu hledání na portálu vyhledejte svou aplikaci Function App a vyberte ji z výsledků.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Snímek obrazovky Azure Portal: název aplikace Function App se prohledává na panelu hledání portálu a výsledek hledání je zvýrazněný.":::

1. Na navigačním panelu vlevo vyberte _Konfigurace_ . Na kartě _nastavení aplikace_ vyberte _+ Nastavení nové aplikace_.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Snímek obrazovky Azure Portal: na stránce konfigurace aplikace Function App se zvýrazní tlačítko pro vytvoření nového nastavení aplikace.":::

1. V okně, které se otevře, použijte hodnotu název hostitele zkopírovanou výše a vytvořte nastavení aplikace.
    * **Název**: ADT_SERVICE_URL
    * **Hodnota**: https://{your-Azure-Digital-revláken-Host-Name}
    
    Vyberte _OK_ a vytvořte nastavení aplikace.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Snímek obrazovky Azure Portal: tlačítko OK je zvýrazněné po vyplňování polí název a hodnota na stránce Přidat/upravit nastavení aplikace.":::

1. Po vytvoření nastavení byste měli vidět, že se zobrazí zpátky na kartě _nastavení aplikace_ . Ověřte, *ADT_SERVICE_URL* se v seznamu objeví, a pak nové nastavení aplikace uložte tak, že vyberete tlačítko _Uložit_ .

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Snímek obrazovky Azure Portal: Stránka nastavení aplikace s zvýrazněným novým nastavením ADT_SERVICE_URL. Tlačítko Uložit je také zvýrazněno.":::

1. Jakékoli změny v nastavení aplikace vyžadují, aby se projevil restart aplikace, takže po zobrazení výzvy vyberte _pokračovat_ a restartujte aplikaci.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Snímek obrazovky Azure Portal: existuje oznámení, že všechny změny nastavení aplikace s restartováním aplikace. Tlačítko pokračovat je zvýrazněno.":::

---

## <a name="next-steps"></a>Další kroky

V tomto článku jste postupovali podle kroků v tématu Nastavení aplikace Function App v Azure pro použití s digitálními podmnožinami Azure.

Další informace najdete v tématu postup sestavení na základě základní funkce pro ingestování IoT Hub dat do digitálních vláken Azure:
* [*Postupy: ingestování telemetrie z IoT Hub*](how-to-ingest-iot-hub-data.md)

---
title: Nastavení funkce v Azure pro zpracování dat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit funkci v Azure, která může přistupovat k digitálnímu vlákna a aktivovat je.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b37277c660562721273ff9ae86dd677ee7ac7d55
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049997"
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

Funkci můžete napsat přidáním sady SDK do aplikace Function App. Aplikace Function App komunikuje s digitálními interakcemi Azure pomocí [sady Azure Digital Revlákens SDK pro .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Aby bylo možné použít sadu SDK, budete muset do svého projektu zahrnout následující balíčky. Balíčky můžete nainstalovat pomocí Správce balíčků NuGet sady Visual Studio nebo balíčky přidat pomocí `dotnet` nástroje v nástroji příkazového řádku. Použijte následující postup pro upřednostňovanou metodu.

**Možnost 1. Přidat balíčky pomocí Správce balíčků sady Visual Studio:**
    
Klikněte pravým tlačítkem na projekt a vyberte _Spravovat balíčky NuGet_ ze seznamu. Pak v okně, které se otevře, vyberte kartu _Procházet_ a vyhledejte následující balíčky. Vyberte _nainstalovat_ a _přijměte_ licenční smlouvu pro instalaci balíčků.

* `Azure.DigitalTwins.Core`
* `Azure.Identity`
* `System.Net.Http`
* `Azure.Core.Pipeline`

**Možnost 2. Přidat balíčky pomocí `dotnet` nástroje příkazového řádku:**

Alternativně můžete použít následující `dotnet add` příkazy v nástroji příkazového řádku:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```

Potom ve Visual Studiu Průzkumník řešení otevřete soubor _function1.cs_ , kde máte vzorový kód a přidejte `using` do funkce následující příkazy. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Přidání ověřovacího kódu do funkce

Nyní deklarujete proměnné na úrovni třídy a přidáte ověřovací kód, který umožní funkci přístup k digitálním Vlákenám Azure. Do souboru _function1.cs_ přidáte následující funkce.

* Kód pro čtení adresy URL služby Azure Digital jako proměnné prostředí Je vhodné si přečíst adresu URL služby z proměnné prostředí, ale nemusíte ji pevně zakódovat do funkce.

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

## <a name="set-up-security-access-for-the-function-app"></a>Nastavení přístupu zabezpečení pro aplikaci Function App

Přístup k zabezpečení pro aplikaci Function App můžete nastavit buď pomocí rozhraní příkazového řádku Azure nebo Azure Portal. Postupujte podle pokynů níže.

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Možnost 1: nastavení přístupu zabezpečení pro aplikaci Function App pomocí rozhraní příkazového řádku

Funkce kostry z předchozích příkladů vyžaduje předání nosných tokenů, aby bylo možné provést ověření pomocí digitálních vláken Azure. Abyste se ujistili, že je tento nosný token předán, budete muset pro aplikaci Function App nastavit [Identita spravované služby (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . Tento postup je nutné provést pouze jednou pro každou aplikaci Function App.

Můžete vytvořit systémově spravovanou identitu a přiřadit jí identitu aplikace Function App k roli _**vlastníka dat digitálních vláken Azure**_ pro instanci digitálního vlákna Azure. Tím se v instanci poskytne oprávnění aplikace Function App, aby se prováděly aktivity roviny dat. Pak zajistěte, aby byla adresa URL instance digitálního vlákna Azure dostupná pro vaši funkci nastavením proměnné prostředí.

Pomocí [Azure Cloud Shell](https://shell.azure.com) spusťte příkazy.

Pomocí následujícího příkazu vytvořte identitu spravovanou systémem. Poznamenejte si pole _principalId_ ve výstupu.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Pomocí hodnoty _principalId_ v následujícím příkazu přiřaďte identitu aplikace funkcí roli _Vlastník dat služby Azure Digital Twins_ pro vaši instanci Azure Digital Twins.

```azurecli-interactive 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
```
Nakonec můžete nastavit proměnnou prostředí tak, aby se adresa URL instance digitálního vlákna Azure pro vaši funkci přístupná. Další informace o nastavení proměnných prostředí naleznete v tématu [*proměnné prostředí*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> Adresa URL instance digitálních vláken Azure se provede přidáním *https://* na začátek *názvu hostitele* instance digitálního vlákna Azure. Chcete-li zobrazit název hostitele spolu se všemi vlastnostmi vaší instance, můžete spustit `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-function-app-using-azure-portal"></a>Možnost 2: nastavení přístupu zabezpečení pro aplikaci Function App pomocí Azure Portal

Spravovaná identita přiřazená systémem umožňuje prostředkům Azure ověřování v cloudových službách (například Azure Key Vault) bez uložení přihlašovacích údajů do kódu. Po povolení se všechna potřebná oprávnění dají udělit prostřednictvím řízení přístupu na základě role v Azure. Životní cyklus tohoto typu spravované identity je vázaný na životní cyklus tohoto prostředku. Každý prostředek (například virtuální počítač) může mít navíc jenom jednu spravovanou identitu přiřazenou systémem.

V [Azure Portal](https://portal.azure.com/)vyhledejte _aplikaci Function App_ na panelu hledání s názvem aplikace Function App, kterou jste vytvořili dříve. V seznamu vyberte *Function App* . 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Snímek obrazovky Azure Portal: název aplikace Function App se prohledává na panelu hledání portálu a výsledek hledání je zvýrazněný.":::

V okně Function App vyberte v navigačním panelu vlevo možnost _Identita_ a povolte spravovanou identitu.
V části _přiřazená systémová_ karta přepněte _stav_ na zapnuto a _uložte_ ho. Zobrazí se automaticky otevírané okno, ve kterém se _povolí spravovaná identita přiřazená systémem_.
Vyberte tlačítko _Ano_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Snímek obrazovky Azure Portal: na stránce Identita aplikace Function App je možnost Povolit spravovanou identitu přiřazenou systémem nastavená na hodnotu Ano. Možnost stav je nastavena na hodnotu Zapnuto.":::

Můžete ověřit v oznámeních, že se funkce úspěšně zaregistrovala v Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Snímek obrazovky Azure Portal: seznam oznámení z výběru ikony zvonku na horním panelu portálu. K dispozici je oznámení, že uživatel povolil spravovanou identitu přiřazenou systémem.":::

Všimněte si také **ID objektu** zobrazené na stránce _identity_ , jak bude použito v další části.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Snímek obrazovky Azure Portal: zvýrazněte pole ID objektu ze stránky identita funkce Azure Functions.":::

### <a name="assign-access-roles-using-azure-portal"></a>Přiřazení rolí přístupu pomocí Azure Portal

Vyberte tlačítko _přiřazení rolí Azure_ , ve kterém se otevře stránka *přiřazení rolí Azure* . Pak vyberte _+ Přidat přiřazení role (Preview)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Snímek obrazovky Azure Portal: zvýrazněte tlačítko přiřazení role Azure v části oprávnění na stránce Identita funkce Azure Functions.":::

Na stránce _Přidat přiřazení role (Preview)_ , která se otevře, vyberte:

* _Obor:_ Skupina prostředků
* _Předplatné_: vyberte předplatné Azure.
* _Skupina prostředků_: z rozevíracího seznamu vyberte svoji skupinu prostředků.
* _Role_: vyberte z rozevíracího seznamu možnost _vlastník dat digitálních vláken Azure_ .

Pak podrobnosti uložte kliknutím na tlačítko _Uložit_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Snímek obrazovky Azure Portal: dialogové okno pro přidání přiřazení nové role (Preview). K dispozici jsou pole pro rozsah, předplatné, skupinu prostředků a roli.":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurace nastavení aplikace pomocí Azure Portal

Adresu URL instance digitálního vlákna Azure, která je pro vaši funkci přístupná, můžete nastavit tak, že nastavíte proměnnou prostředí. Další informace najdete v tématu [*proměnné prostředí*](/sandbox/functions-recipes/environment-variables). Nastavení aplikace jsou vystavena jako proměnné prostředí pro přístup k instanci digitálního vlákna. 

Pokud chcete nastavit proměnnou prostředí s adresou URL vaší instance, načtěte adresu URL tak, že vyhledáte název hostitele instance digitálního vlákna Azure. Na panelu hledání [Azure Portal](https://portal.azure.com) vyhledejte vaši instanci. Pak na levém navigačním panelu vyberte _Přehled_ a zobrazte _název hostitele_. Zkopírujte tuto hodnotu.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Snímek obrazovky Azure Portal: na stránce s přehledem instance digitálního vlákna Azure je hodnota název hostitele zvýrazněna.":::

Nyní můžete vytvořit nastavení aplikace podle následujících kroků:

1. V panelu hledání na portálu vyhledejte svou aplikaci Function App a vyberte ji z výsledků.
1. Vyberte _konfiguraci_ na navigačním panelu vlevo a vytvořte nové nastavení aplikace.
1. Na kartě _nastavení aplikace_ vyberte _+ Nastavení nové aplikace_ .

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Snímek obrazovky Azure Portal: název aplikace Function App se prohledává na panelu hledání portálu a výsledek hledání je zvýrazněný.":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Snímek obrazovky Azure Portal: na stránce konfigurace aplikace Function App se zvýrazní tlačítko pro vytvoření nového nastavení aplikace.":::

V okně, které se otevře, použijte hodnotu název hostitele zkopírovanou výše a vytvořte nastavení aplikace.
* **Název**: ADT_SERVICE_URL
* **Hodnota**: https://{your-Azure-Digital-revláken-Host-Name}

Vyberte _OK_ a vytvořte nastavení aplikace.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Snímek obrazovky Azure Portal: tlačítko OK je zvýrazněné po vyplňování polí název a hodnota na stránce Přidat/upravit nastavení aplikace.":::

Nastavení aplikace můžete zobrazit pomocí názvu aplikace v poli _název_ . Pak nastavení aplikace uložte tak, že vyberete tlačítko _Uložit_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Snímek obrazovky Azure Portal: Stránka nastavení aplikace s zvýrazněným novým nastavením ADT_SERVICE_URL. Tlačítko Uložit je také zvýrazněno.":::

Změny nastavení aplikace budou vyžadovat, aby se restart aplikace projevil. Vyberte _pokračovat_ a restartujte aplikaci.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Snímek obrazovky Azure Portal: existuje oznámení, že všechny změny nastavení aplikace s restartováním aplikace. Tlačítko pokračovat je zvýrazněno.":::

Kliknutím na ikonu _oznámení_ můžete zobrazit tato nastavení aplikace. Pokud není nastavení aplikace vytvořeno, můžete znovu přidat nastavení aplikace podle výše uvedeného postupu.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Snímek obrazovky Azure Portal: seznam oznámení z výběru ikony zvonku na horním panelu portálu. Došlo k oznámení, že nastavení webové aplikace se úspěšně aktualizovala.":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste postupovali podle kroků v tématu Nastavení aplikace Function App v Azure pro použití s digitálními podmnožinami Azure.

Další informace najdete v tématu postup sestavení na základě základní funkce pro ingestování IoT Hub dat do digitálních vláken Azure:
* [*Postupy: ingestování telemetrie z IoT Hub*](how-to-ingest-iot-hub-data.md)

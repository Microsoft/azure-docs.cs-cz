---
title: Nastavení funkce v Azure pro zpracování dat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit funkci v Azure, která může přistupovat k digitálnímu vlákna a aktivovat je.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7f491bbe61e8574a7275d9ef5c87d05fa61dc7c4
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035304"
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
4. Publikování aplikace Function App do Azure
5. Nastavení přístupu [zabezpečení](concepts-security.md) pro aplikaci Function App

## <a name="prerequisite-set-up-azure-digital-twins-instance"></a>Předpoklad: nastavení instance digitálních vláken Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Vytvoření aplikace Function App v aplikaci Visual Studio

V aplikaci Visual Studio 2019 vyberte _soubor > nový > projekt_ a vyhledejte šablonu _Azure Functions_ vyberte možnost _Další_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Zadejte název aplikace Function App a vyberte _vytvořit_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: konfigurace nového projektu":::

Vyberte typ *triggeru* function App Event Grid a vyberte _vytvořit_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: dialogové okno aktivační události Azure Functions projektu":::

Po vytvoření aplikace Function App bude mít Visual Studio automaticky vyplněný vzorek kódu v souboru **Function.cs** ve složce projektu. Tato krátká funkce se používá k protokolování událostí.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: okno projektu s ukázkovým kódem":::

## <a name="write-a-function-with-an-event-grid-trigger"></a>Zápis funkce s triggerem Event Grid

Funkci můžete napsat přidáním sady SDK do aplikace Function App. Aplikace Function App komunikuje s digitálními interakcemi Azure pomocí [sady Azure Digital Revlákens SDK pro .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 

Aby bylo možné použít sadu SDK, budete muset do svého projektu zahrnout následující balíčky. Balíčky můžete buď nainstalovat pomocí Správce balíčků NuGet sady Visual Studio, nebo balíčky přidat pomocí `dotnet` nástroje příkazového řádku. Vyberte jednu z těchto metod: 

**Možnost 1. Přidat balíčky pomocí Správce balíčků sady Visual Studio:**
    
To můžete provést tak, že kliknete pravým tlačítkem na projekt a v seznamu vyberete _Spravovat balíčky NuGet_ . Pak v okně, které se otevře, vyberte _Procházet_ kartu a vyhledejte následující balíčky. Vyberte _nainstalovat_ a _přijměte_ licenční smlouvu pro instalaci balíčků.

* `Azure.DigitalTwins.Core`
* `Azure.Identity` 

Pro konfiguraci kanálu Azure SDK pro správné nastavení pro Azure Functions budete potřebovat taky následující balíčky. Pokud chcete nainstalovat všechny balíčky, opakujte stejný postup jako výše.

* `System.Net.Http`
* `Azure.Core.Pipeline`

**Možnost 2. Přidat balíčky pomocí `dotnet` nástroje příkazového řádku:**

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.2.2
dotnet add package System.Net.Http
dotnet add package Azure.Core.Pipeline
```
Potom ve Visual Studiu Průzkumník řešení otevřete soubor _Function.cs_ , kde máte vzorový kód a přidejte následující příkazy _using_ do funkce. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-function"></a>Přidání ověřovacího kódu do funkce

Nyní deklarujete proměnné na úrovni třídy a přidáte ověřovací kód, který umožní funkci přístup k digitálním Vlákenám Azure. Do své funkce v souboru {název funkce}. cs přidáte následující:

* Načte adresu URL služby ADT jako proměnnou prostředí. Je vhodné si přečíst adresu URL služby z proměnné prostředí, ale nemusíte ji pevně zakódovat do funkce.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Statická proměnná pro uložení instance HttpClient HttpClient je poměrně nákladný k vytvoření a chceme se vyhnout nutnosti udělat si to pro každé vyvolání funkce.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Přihlašovací údaje spravované identity můžete použít v Azure Functions.
```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
```
* Přidejte místní proměnnou _DigitalTwinsClient_ do vaší funkce, abyste pomohli instanci klienta Azure s digitálními podmnožinami v projektu funkce. Nevytvářejte *tuto* proměnnou v rámci vaší třídy staticky.
```csharp
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
```
* Přidejte kontrolu null pro _adtInstanceUrl_ a zabalte svou logiku funkcí do bloku try catch pro zachycení jakýchkoli výjimek.

Po těchto změnách bude kód vaší funkce podobný následujícímu:

```csharp
// Default URL for triggering event grid function in the local environment.
// http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
using System;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;

namespace adtIngestFunctionSample
{
    public class Function1
    {
        //Your Digital Twin URL is stored in an application setting in Azure Functions
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("TwinsFunction")]
        public void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");
            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
                /*
                * Add your business logic here
                */
            }
            catch (Exception e)
            {
                log.LogError(e.Message);
            }

        }
    }
}
```

## <a name="publish-the-function-app-to-azure"></a>Publikování aplikace Function App do Azure

Pokud chcete projekt publikovat do aplikace Function App v Azure, klikněte pravým tlačítkem na projekt funkce (ne řešení) v Průzkumník řešení a zvolte **publikovat**.

> [!IMPORTANT] 
> Publikování do aplikace Function App v Azure má za následek další poplatky za vaše předplatné bez ohledu na digitální vlákna Azure.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: publikování funkce do Azure":::

Jako cíl publikování vyberte **Azure** a pak vyberte **Další**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: dialogové okno Publikovat Azure Functions vyberte Azure. ":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: dialogové okno publikování funkce vyberte Azure Function App (Windows) nebo (Linux) na základě vašeho počítače.":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: dialogové okno publikování funkce, vytvoření nové funkce Azure Functions":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: dialogové okno Publikovat funkci, vyplňte pole a vyberte vytvořit.":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: dialogové okno publikování funkce, výběr aplikace Function App ze seznamu a dokončení":::

Na následující stránce zadejte požadovaný název nové aplikace Function App, skupiny prostředků a dalších podrobností.
Aby aplikace Function App mohla přistupovat k digitálním funkcím Azure, musí mít identitu spravovanou systémem a mít oprávnění pro přístup k instanci digitálních vláken Azure.

V dalším kroku můžete nastavit přístup zabezpečení pro funkci pomocí rozhraní příkazového řádku nebo Azure Portal. Vyberte jednu z těchto metod:

## <a name="set-up-security-access-for-the-function-app"></a>Nastavení přístupu zabezpečení pro aplikaci Function App
Přístup k zabezpečení pro aplikaci Function App můžete nastavit pomocí jedné z těchto možností:

### <a name="option-1-set-up-security-access-for-the-function-app-using-cli"></a>Možnost 1: nastavení přístupu zabezpečení pro aplikaci Function App pomocí rozhraní příkazového řádku

Funkce kostry z předchozích příkladů vyžaduje předání nosných tokenů, aby bylo možné provést ověření pomocí digitálních vláken Azure. Abyste se ujistili, že je tento nosný token předán, budete muset pro aplikaci Function App nastavit [Identita spravované služby (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . Tento postup je nutné provést pouze jednou pro každou aplikaci Function App.

Můžete vytvořit systémově spravovanou identitu a přiřadit jí identitu aplikace Function App k roli _**vlastníka dat digitálních vláken Azure**_ pro instanci digitálního vlákna Azure. Tím se v instanci poskytne oprávnění aplikace Function App, aby se prováděly aktivity roviny dat. Pak zajistěte, aby byla adresa URL instance digitálního vlákna Azure dostupná pro vaši funkci nastavením proměnné prostředí.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Pomocí [Azure Cloud Shell](https://shell.azure.com) spusťte příkazy.

Pomocí následujícího příkazu vytvořte identitu spravovanou systémem. Poznamenejte si pole _principalId_ ve výstupu.

```azurecli-interactive 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Pomocí hodnoty _principalId_ v následujícím příkazu přiřaďte identitě aplikace funkcí do role _vlastníka dat digitálních vláken Azure_ pro vaši instanci digitálních vláken Azure.

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

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Azure Portal: hledání aplikace Function App":::

V okně Function App vyberte v navigačním panelu vlevo možnost _Identita_ a povolte spravovanou identitu.
V části _přiřazená systémová_ karta přepněte _stav_ na zapnuto a _uložte_ ho. Zobrazí se automaticky otevírané okno, ve kterém se _povolí spravovaná identita přiřazená systémem_.
Vyberte tlačítko _Ano_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Azure Portal: povolení identity spravované systémem":::

Můžete ověřit v oznámeních, že se funkce úspěšně zaregistrovala v Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Azure Portal: oznámení":::

Všimněte si také **ID objektu** zobrazené na stránce _identity_ , jak bude použito v další části.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Zkopírujte ID objektu, který chcete použít v budoucnu.":::

### <a name="assign-access-roles-using-azure-portal"></a>Přiřazení rolí přístupu pomocí Azure Portal

Vyberte tlačítko _přiřazení rolí Azure_ , ve kterém se otevře stránka *přiřazení rolí Azure* . Pak vyberte _+ Přidat přiřazení role (Preview)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Azure Portal: přidání přiřazení role":::

Na stránce _Přidat přiřazení role (Preview)_ , která se otevře, vyberte:

* _Obor:_ Skupina prostředků
* _Předplatné_: vyberte předplatné Azure.
* _Skupina prostředků_: z rozevíracího seznamu vyberte svoji skupinu prostředků.
* _Role_: vyberte z rozevíracího seznamu možnost _vlastník dat digitálních vláken Azure_ .

Pak podrobnosti uložte kliknutím na tlačítko _Uložit_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Azure Portal: přidání přiřazení role (Preview) ":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurace nastavení aplikace pomocí Azure Portal

Adresu URL instance digitálního vlákna Azure, která je pro vaši funkci přístupná, můžete nastavit tak, že nastavíte proměnnou prostředí. Další informace najdete v tématu [*proměnné prostředí*](/sandbox/functions-recipes/environment-variables). Nastavení aplikace jsou vystavena jako proměnné prostředí pro přístup k instanci digitálního vlákna. 

Budete potřebovat ADT_INSTANCE_URL k vytvoření nastavení aplikace.

Můžete získat ADT_INSTANCE_URL připojením **_https://_** k názvu hostitele instance. V Azure Portal můžete najít název hostitele instance digitálního vlákna, a to tak, že na panelu hledání vyhledáte svou instanci. Pak na levém navigačním panelu vyberte _Přehled_ a zobrazte _název hostitele_. Zkopírujte tuto hodnotu pro vytvoření nastavení aplikace.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Azure Portal: Přehled – > kopírování názvu hostitele, který se má použít v poli _Value_.":::

Nyní můžete vytvořit nastavení aplikace podle následujících kroků:

* Vyhledejte svoji aplikaci pomocí názvu aplikace Function App na panelu hledání a ze seznamu vyberte aplikaci Function App.
* Vyberte _konfiguraci_ na navigačním panelu vlevo a vytvořte nové nastavení aplikace.
* Na kartě _nastavení aplikace_ vyberte _+ Nastavení nové aplikace_ .

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Azure Portal: hledání existující aplikace Function App":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Azure Portal: Konfigurace nastavení aplikace":::

V okně, které se otevře, použijte hodnotu zkopírovanou z výše k vytvoření nastavení aplikace. \
_Název_  : ADT_SERVICE_URL \
_Hodnota_ : https://{your-Azure-Digital-zdvojené-hostname}

Vyberte _OK_ a vytvořte nastavení aplikace.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Azure Portal: přidejte nastavení aplikace.":::

Nastavení aplikace můžete zobrazit pomocí názvu aplikace v poli _název_ . Pak nastavení aplikace uložte výběrem tlačítka _Uložit_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Azure Portal: zobrazení vytvořené aplikace a restartování aplikace":::

Všechny změny nastavení aplikace vyžadují restart aplikace. Vyberte _pokračovat_ a restartujte aplikaci.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Azure Portal: uložení nastavení aplikace":::

Kliknutím na ikonu _oznámení_ můžete zobrazit tato nastavení aplikace. Pokud není nastavení aplikace vytvořeno, můžete znovu přidat nastavení aplikace podle výše uvedeného postupu.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Azure Portal: oznámení pro aktualizaci nastavení aplikace":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste postupovali podle kroků v tématu Nastavení aplikace Function App v Azure pro použití s digitálními podmnožinami Azure. V dalším kroku můžete k přihlášení k odběru funkce Event Grid, aby bylo možné naslouchat na koncovém bodu. Tento koncový bod může být:
* Event Grid koncový bod připojený k digitálním podprocesům Azure ke zpracování zpráv přicházejících z digitálních vláken Azure (například zpráv o změnách vlastností, zpráv telemetrie generovaných [digitálními](concepts-twins-graph.md) podprocesy ve dvojitých grafech nebo ve zprávách o životním cyklu).
* Témata systému IoT používaná IoT Hub k posílání telemetrie a dalších událostí zařízení
* Event Grid koncový bod přijímající zprávy z jiných služeb

Další informace najdete v tématu postup sestavení na základě základní funkce pro ingestování IoT Hub dat do digitálních vláken Azure:
* [*Postupy: ingestování telemetrie z IoT Hub*](how-to-ingest-iot-hub-data.md)

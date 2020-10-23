---
title: Nastavení funkce Azure pro zpracování dat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit funkci Azure, která může přistupovat k digitálnímu vlákna a aktivovat je.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 78addb76e2ce7a2679358e241650cc5cc827791f
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461613"
---
# <a name="connect-azure-functions-apps-for-processing-data"></a>Připojení aplikací Azure Functions pro zpracování dat

Aktualizace digitálních vláken na základě dat se zpracovává pomocí [**tras událostí**](concepts-route-events.md) prostřednictvím výpočetních prostředků, jako je [Azure Functions](../azure-functions/functions-overview.md). Funkce Azure Functions se dá použít k aktualizaci digitálního vlákna v reakci na:
* data telemetrie zařízení přicházejících z IoT Hub
* Změna vlastnosti nebo jiná data přicházející z jiného digitálního vlákna v rámci grafu s dvojitou přesností

Tento článek vás provede vytvořením funkce Azure pro použití s digitálními událostmi Azure. 

Tady je přehled kroků, které obsahuje:

1. Vytvoření aplikace Azure Functions v aplikaci Visual Studio
2. Zápis funkce Azure pomocí triggeru [Event Grid](../event-grid/overview.md)
3. Přidání ověřovacího kódu do funkce (aby bylo možné získat přístup k digitálním Vlákenám Azure)
4. Publikování aplikace Function App do Azure
5. Nastavení přístupu [zabezpečení](concepts-security.md) pro aplikaci Azure Function App

## <a name="create-an-azure-functions-app-in-visual-studio"></a>Vytvoření aplikace Azure Functions v aplikaci Visual Studio

V aplikaci Visual Studio 2019 vyberte _soubor > nový > projekt_ a vyhledejte šablonu _Azure Functions_ vyberte možnost _Další_.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Zadejte název aplikace Function App a vyberte _vytvořit_.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Vyberte typ *triggeru* function App Event Grid a vyberte _vytvořit_.

:::image type="content" source="media/how-to-create-azure-function/eventgridtrigger-function.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Po vytvoření aplikace Function App bude mít Visual Studio automaticky vyplněný vzorek kódu v souboru **Function.cs** ve složce projektu. Tato krátká funkce Azure slouží k protokolování událostí.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

## <a name="write-an-azure-function-with-an-event-grid-trigger"></a>Zápis funkce Azure pomocí triggeru Event Grid

Funkci Azure můžete napsat přidáním sady SDK do aplikace Function App. Aplikace Function App komunikuje s digitálními interakcemi Azure pomocí [sady Azure Digital Revlákens SDK pro .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet-preview&preserve-view=true). 

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
Potom ve Visual Studiu Průzkumník řešení otevřete soubor _Function.cs_ , kde máte vzorový kód a přidejte následující příkazy _using_ do funkce Azure Function. 

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Net.Http;
using Azure.Core.Pipeline;
```
## <a name="add-authentication-code-to-the-azure-function"></a>Přidání ověřovacího kódu do funkce Azure Functions

Nyní deklarujete proměnné na úrovni třídy a přidáte ověřovací kód, který umožní funkci přístup k digitálním Vlákenám Azure. Do funkce Azure v souboru {název funkce}. cs přidáte následující:

* Načte adresu URL služby ADT jako proměnnou prostředí. Je vhodné si přečíst adresu URL služby z proměnné prostředí, ale nemusíte ji pevně zakódovat do funkce.
```csharp     
private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
```
* Statická proměnná pro uložení instance HttpClient HttpClient je poměrně nákladný k vytvoření a chceme se vyhnout nutnosti udělat si to pro každé vyvolání funkce.
```csharp
private static readonly HttpClient httpClient = new HttpClient();
```
* Můžete použít přihlašovací údaje spravované identity ve službě Azure Functions.
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

Pokud chcete publikovat aplikaci Function App do Azure, klikněte pravým tlačítkem na projekt funkce (ne řešení) v Průzkumník řešení a zvolte **publikovat**.

> [!IMPORTANT] 
> Publikování funkce Azure Functions bude mít za následek další poplatky na vaše předplatné, nezávisle na digitálních událostech Azure.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Jako cíl publikování vyberte **Azure** a pak vyberte **Další**.

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-1.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-2.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-3.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-4.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

:::image type="content" source="media/how-to-create-azure-function/publish-azure-function-5.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Na následující stránce zadejte požadovaný název nové aplikace Function App, skupiny prostředků a dalších podrobností.
Aby vaše aplikace Functions mohla přistupovat k digitálním funkcím Azure, musí mít identitu spravovanou systémem a mít oprávnění pro přístup k instanci digitálních vláken Azure.

V dalším kroku můžete nastavit přístup zabezpečení pro funkci pomocí rozhraní příkazového řádku nebo Azure Portal. Vyberte jednu z těchto metod:

## <a name="set-up-security-access-for-the-azure-function-app"></a>Nastavení přístupu zabezpečení pro aplikaci Azure Function App
Přístup zabezpečení pro aplikaci Azure Function App můžete nastavit pomocí jedné z těchto možností:

### <a name="option-1-set-up-security-access-for-the-azure-function-app-using-cli"></a>Možnost 1: nastavení přístupu zabezpečení pro aplikaci funkce Azure pomocí rozhraní příkazového řádku

Funkce Azure Functions z předchozích příkladů vyžaduje předání nosných tokenů, aby bylo možné ho ověřit pomocí digitálních vláken Azure. Abyste se ujistili, že je tento nosný token předán, budete muset pro aplikaci Function App nastavit [Identita spravované služby (MSI)](../active-directory/managed-identities-azure-resources/overview.md) . Tento postup je nutné provést pouze jednou pro každou aplikaci Function App.

Můžete vytvořit systémově spravovanou identitu a přiřadit jí identitu aplikace Function App k roli _vlastníka (Preview) digitálních vláken Azure_ pro instanci digitálního vlákna Azure. Tím se v instanci poskytne oprávnění aplikace Function App, aby se prováděly aktivity roviny dat. Pak zajistěte, aby byla adresa URL instance digitálního vlákna Azure dostupná pro vaši funkci nastavením proměnné prostředí.

 Pomocí [Azure Cloud Shell](https://shell.azure.com) spusťte příkazy.

Pomocí následujícího příkazu vytvořte identitu spravovanou systémem. Poznamenejte si pole _principalId_ ve výstupu.

```azurecli 
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>   
```
Pomocí hodnoty _principalId_ v následujícím příkazu přiřaďte identitě aplikace Function App roli _vlastníka digitálních vláken Azure (Preview)_ pro instanci digitálního vlákna Azure.

```azurecli 
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```
Nakonec můžete nastavit proměnnou prostředí tak, aby se adresa URL instance digitálního vlákna Azure pro vaši funkci přístupná. Další informace o nastavení proměnných prostředí naleznete v tématu [*proměnné prostředí*](/sandbox/functions-recipes/environment-variables). 

> [!TIP]
> Adresa URL instance digitálních vláken Azure se provede přidáním *https://* na začátek *názvu hostitele*instance digitálního vlákna Azure. Chcete-li zobrazit název hostitele spolu se všemi vlastnostmi vaší instance, můžete spustit `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-hostname>"
```
### <a name="option-2-set-up-security-access-for-the-azure-function-app-using-azure-portal"></a>Možnost 2: nastavení přístupu zabezpečení pro aplikaci funkce Azure pomocí Azure Portal

Spravovaná identita přiřazená systémem umožňuje prostředkům Azure ověřování v cloudových službách (například Azure Key Vault) bez uložení přihlašovacích údajů do kódu. Po povolení se všechna potřebná oprávnění dají udělit prostřednictvím řízení přístupu na základě role v Azure. Životní cyklus tohoto typu spravované identity je vázaný na životní cyklus tohoto prostředku. Každý prostředek (například virtuální počítač) může mít navíc jenom jednu spravovanou identitu přiřazenou systémem.

V [Azure Portal](https://portal.azure.com/)vyhledejte _aplikaci Function App_ na panelu hledání s názvem aplikace Function App, kterou jste vytvořili dříve. V seznamu vyberte *Function App* . 

:::image type="content" source="media/how-to-create-azure-function/portal-search-for-functionapp.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

V okně Function App vyberte v navigačním panelu vlevo možnost _Identita_ a povolte spravovanou identitu.
V části _přiřazená systémová_ karta přepněte _stav_ na zapnuto a _uložte_ ho. Zobrazí se automaticky otevírané okno, ve kterém se _povolí spravovaná identita přiřazená systémem_.
Vyberte tlačítko _Ano_ . 

:::image type="content" source="media/how-to-create-azure-function/enable-system-managed-identity.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Můžete ověřit v oznámeních, že se funkce úspěšně zaregistrovala v Azure Active Directory.

:::image type="content" source="media/how-to-create-azure-function/notifications-enable-managed-identity.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Všimněte si také **ID objektu** zobrazené na stránce _identity_ , jak bude použito v další části.

:::image type="content" source="media/how-to-create-azure-function/object-id.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

### <a name="assign-access-roles-using-azure-portal"></a>Přiřazení rolí přístupu pomocí Azure Portal

Vyberte tlačítko _přiřazení rolí Azure_ , ve kterém se otevře stránka *přiřazení rolí Azure* . Pak vyberte _+ Přidat přiřazení role (Preview)_.

:::image type="content" source="media/how-to-create-azure-function/add-role-assignments.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Na stránce _Přidat přiřazení role (Preview)_ , která se otevře, vyberte:

* _Obor:_ Skupina prostředků
* _Předplatné_: vyberte předplatné Azure.
* _Skupina prostředků_: z rozevíracího seznamu vyberte svoji skupinu prostředků.
* _Role_: v rozevíracím seznamu vyberte _vlastníka digitálních vláken Azure (Preview)_ .

Pak podrobnosti uložte kliknutím na tlačítko _Uložit_ .

:::image type="content" source="media/how-to-create-azure-function/add-role-assignment.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

### <a name="configure-application-settings-using-azure-portal"></a>Konfigurace nastavení aplikace pomocí Azure Portal

Adresu URL instance digitálního vlákna Azure, která je pro vaši funkci přístupná, můžete nastavit tak, že nastavíte proměnnou prostředí. Další informace najdete v tématu [*proměnné prostředí*](/sandbox/functions-recipes/environment-variables). Nastavení aplikace jsou vystavena jako proměnné prostředí pro přístup k instanci digitálního vlákna. 

Budete potřebovat ADT_INSTANCE_URL k vytvoření nastavení aplikace.

Můžete získat ADT_INSTANCE_URL připojením **_https://_** k názvu hostitele instance. V Azure Portal můžete najít název hostitele instance digitálního vlákna, a to tak, že na panelu hledání vyhledáte svou instanci. Pak na levém navigačním panelu vyberte _Přehled_ a zobrazte _název hostitele_. Zkopírujte tuto hodnotu pro vytvoření nastavení aplikace.

:::image type="content" source="media/how-to-create-azure-function/adt-hostname.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Nyní můžete vytvořit nastavení aplikace podle následujících kroků:

* Hledání funkce Azure pomocí názvu funkce na vyhledávacím panelu a výběr funkce ze seznamu
* Vyberte _konfiguraci_ na navigačním panelu vlevo a vytvořte nové nastavení aplikace.
* Na kartě _nastavení aplikace_ vyberte _+ Nastavení nové aplikace_ .

:::image type="content" source="media/how-to-create-azure-function/search-for-azure-function.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

:::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

V okně, které se otevře, použijte hodnotu zkopírovanou z výše k vytvoření nastavení aplikace. \
_Název_  : ADT_SERVICE_URL \
_Hodnota_ : https://{your-Azure-Digital-zdvojené-hostname}

Vyberte _OK_ a vytvořte nastavení aplikace.

:::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Nastavení aplikace můžete zobrazit pomocí názvu aplikace v poli _název_ . Pak nastavení aplikace uložte výběrem tlačítka _Uložit_ .

:::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Všechny změny nastavení aplikace vyžadují restart aplikace. Vyberte _pokračovat_ a restartujte aplikaci.

:::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

Kliknutím na ikonu _oznámení_ můžete zobrazit tato nastavení aplikace. Pokud není nastavení aplikace vytvořeno, můžete znovu přidat nastavení aplikace podle výše uvedeného postupu.

:::image type="content" source="media/how-to-create-azure-function/notifications-update-web-app-settings.png" alt-text="Visual Studio: dialogové okno Nový projekt":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste postupovali podle kroků pro nastavení funkce Azure pro použití s digitálními podmnožinami Azure. V dalším kroku se můžete přihlásit k odběru funkce Azure Functions, abyste Event Grid mohli naslouchat na koncovém bodu. Tento koncový bod může být:
* Event Grid koncový bod připojený k digitálním podprocesům Azure ke zpracování zpráv přicházejících z digitálních vláken Azure (například zpráv o změnách vlastností, zpráv telemetrie generovaných [digitálními](concepts-twins-graph.md) podprocesy ve dvojitých grafech nebo ve zprávách o životním cyklu).
* Témata systému IoT používaná IoT Hub k posílání telemetrie a dalších událostí zařízení
* Event Grid koncový bod přijímající zprávy z jiných služeb

Další informace najdete v tématu postup sestavení na základní funkci Azure pro ingestování IoT Hub dat do digitálních vláken Azure:
* [*Postupy: ingestování telemetrie z IoT Hub*](how-to-ingest-iot-hub-data.md)
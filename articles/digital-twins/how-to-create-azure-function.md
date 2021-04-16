---
title: Nastavení funkce v Azure pro zpracování dat
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit funkci v Azure, která může přistupovat k digitálnímu vlákna a aktivovat je.
author: baanders
ms.author: baanders
ms.date: 8/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5dddaabf47a261f741b3b1cb8d3319d589c4e474
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480723"
---
# <a name="connect-function-apps-in-azure-for-processing-data"></a>Připojení aplikací Function App v Azure pro zpracování dat

Digitální vlákna je možné aktualizovat na základě dat pomocí [tras událostí](concepts-route-events.md) prostřednictvím výpočetních prostředků. Například funkce, která je vytvořena pomocí [Azure Functions](../azure-functions/functions-overview.md) může aktualizovat digitální vlákna v reakci na:
* Data telemetrie zařízení z Azure IoT Hub.
* Změna vlastnosti nebo jiná data z jiného digitálního vlákna v rámci vyzdvojeného grafu.

V tomto článku se dozvíte, jak v Azure vytvořit funkci pro použití s digitálními vlákna Azure. Chcete-li vytvořit funkci, postupujte podle těchto základních kroků:

1. Vytvoření projektu Azure Functions v aplikaci Visual Studio.
2. Napište funkci, která má Trigger [Azure Event Grid](../event-grid/overview.md) .
3. Přidejte ověřovací kód do funkce, abyste měli přístup k digitálním Vlákenám Azure.
4. Publikujte aplikaci Function App do Azure.
5. Nastavte [zabezpečení](concepts-security.md) pro aplikaci Function App.

## <a name="prerequisite-set-up-azure-digital-twins"></a>Předpoklad: nastavení digitálních vláken Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="create-a-function-app-in-visual-studio"></a>Vytvoření aplikace Function App v aplikaci Visual Studio

V aplikaci Visual Studio 2019 vyberte **soubor**  >  **Nový**  >  **projekt**. Vyhledejte šablonu **Azure Functions** . Vyberte **Další**.

:::image type="content" source="media/how-to-create-azure-function/create-azure-function-project.png" alt-text="Snímek obrazovky sady Visual Studio se zobrazeným dialogovým oknem nový projekt Je zvýrazněna šablona projektu Azure Functions.":::

Zadejte název aplikace Function App a pak vyberte __vytvořit__.

:::image type="content" source="media/how-to-create-azure-function/configure-new-project.png" alt-text="Snímek obrazovky sady Visual Studio zobrazující dialogové okno pro konfiguraci nového projektu. Nastavení zahrnuje název projektu, umístění pro uložení, volbu pro vytvoření nového řešení a název řešení.":::

Vyberte typ aplikace Function App **Event Grid Trigger** a pak vyberte __vytvořit__.

:::image type="content" source="media/how-to-create-azure-function/event-grid-trigger-function.png" alt-text="Snímek obrazovky sady Visual Studio zobrazující dialogové okno pro vytvoření nové aplikace Azure Functions. Je zvýrazněna možnost Trigger Event Grid.":::

Po vytvoření aplikace Function App vytvoří Visual Studio ukázku kódu v souboru *function1. cs* ve složce projektu. Tato krátká funkce se používá k protokolování událostí.

:::image type="content" source="media/how-to-create-azure-function/visual-studio-sample-code.png" alt-text="Snímek obrazovky sady Visual Studio. Zobrazí se okno projektu pro nový projekt. Kód pro ukázkovou funkci je zobrazen v souboru s názvem Function1." lightbox="media/how-to-create-azure-function/visual-studio-sample-code.png":::

## <a name="write-a-function-that-has-an-event-grid-trigger"></a>Napsat funkci, která má aktivační událost Event Grid

Funkci můžete napsat přidáním sady SDK do aplikace Function App. Aplikace Function App komunikuje s digitálními interakcemi Azure pomocí [sady Azure Digital SDK pro .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). 

Chcete-li použít sadu SDK, budete muset zahrnout následující balíčky do projektu. Nainstalujte balíčky pomocí Správce balíčků NuGet sady Visual Studio. Nebo přidejte balíčky pomocí `dotnet` nástroje v nástroji příkazového řádku.

* [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core/)
* [Azure. identity](https://www.nuget.org/packages/Azure.Identity/)
* [System .NET. http](https://www.nuget.org/packages/System.Net.Http/)
* [Azure. Core](https://www.nuget.org/packages/Azure.Core/)

Potom v aplikaci Visual Studio Průzkumník řešení otevřete soubor _function1. cs_ , který obsahuje vzorový kód. Přidejte následující `using` příkazy pro balíčky.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="Function_dependencies":::

## <a name="add-authentication-code-to-the-function"></a>Přidání ověřovacího kódu do funkce

Nyní deklarujete proměnné na úrovni třídy a přidejte ověřovací kód, který umožní funkci přístup k digitálním Vlákenám Azure. Přidejte proměnné a kód do funkce v souboru _function1. cs_ .

* **Kód pro čtení adresy URL služby Azure Digital jako proměnné prostředí** Je vhodné načíst adresu URL služby z proměnné prostředí, ale nemusíte ji pevně zakódovat do funkce. [Později v tomto článku](#set-up-security-access-for-the-function-app)nastavíte hodnotu této proměnné prostředí. Další informace o proměnných prostředí najdete v tématu [Správa aplikace Function App](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal).

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ADT_service_URL":::

* **Statická proměnná pro uložení instance HttpClient** HttpClient je poměrně nákladný k vytvoření, takže chceme se vyhnout vytváření pro každé vyvolání funkce.

    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="HTTP_client":::

* **Přihlašovací údaje spravované identity.**
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="ManagedIdentityCredential":::

* **Místní proměnná _DigitalTwinsClient_.** Přidejte proměnnou do funkce, která bude uchovávat vaši instanci klienta služby Azure Digital revariablees. Nevytvářejte *tuto* proměnnou v rámci vaší třídy staticky.
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs" id="DigitalTwinsClient":::

* **Nulová check pro _adtInstanceUrl_.** Přidejte kontrolu null a potom zabalte logiku funkce do bloku try/catch pro zachycení všech výjimek.

Po těchto změnách bude kód vaší funkce vypadat jako v následujícím příkladu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIngestFunctionSample.cs":::

Teď, když je vaše aplikace vytvořená, můžete ji publikovat do Azure.

## <a name="publish-the-function-app-to-azure"></a>Publikování aplikace funkcí do Azure

[!INCLUDE [digital-twins-publish-azure-function.md](../../includes/digital-twins-publish-azure-function.md)]

### <a name="verify-the-publication-of-your-function"></a>Ověření publikace funkce

1. Přihlaste se pomocí svých přihlašovacích údajů v [Azure Portal](https://portal.azure.com/).
2. Do vyhledávacího pole v horní části okna vyhledejte název aplikace Function App a vyberte ji.

    :::image type="content" source="media/how-to-create-azure-function/search-function-app.png" alt-text="Snímek obrazovky znázorňující Azure Portal. Do vyhledávacího pole zadejte název aplikace Function App." lightbox="media/how-to-create-azure-function/search-function-app.png":::

3. Na stránce **Function App** , která se otevře, vyberte v nabídce na levé straně možnost **funkce**. Pokud je vaše funkce úspěšně publikována, zobrazí se její název v seznamu.

    > [!Note] 
    > Možná budete muset několik minut počkat nebo aktualizovat stránku několikrát, než se vaše funkce objeví v seznamu publikovaných funkcí.

    :::image type="content" source="media/how-to-create-azure-function/view-published-functions.png" alt-text="Zobrazení publikovaných funkcí v Azure Portal." lightbox="media/how-to-create-azure-function/view-published-functions.png":::

Pro přístup k digitálním podmnožinám Azure vaše aplikace Function App potřebuje identitu spravovanou systémem s oprávněními pro přístup k instanci digitálních vláken Azure. Nastavíte tuto hodnotu jako další.

## <a name="set-up-security-access-for-the-function-app"></a>Nastavení přístupu zabezpečení pro aplikaci Function App

Přístup k zabezpečení pro aplikaci Function App můžete nastavit buď pomocí rozhraní příkazového řádku Azure nebo Azure Portal. Postupujte podle pokynů pro upřednostňovanou možnost.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli)

Spusťte tyto příkazy v [Azure Cloud Shell](https://shell.azure.com) nebo v [místní instalaci](/cli/azure/install-azure-cli)rozhraní příkazového řádku Azure.
Pomocí spravované systémové identity aplikace Function App můžete dát IT roli **vlastníka dat** k digitálnímu vynechání dat Azure pro instanci digitálních vláken Azure. Role v instanci poskytuje oprávnění aplikace Function App k provádění aktivit roviny dat. Pak nastavte proměnnou prostředí tak, aby byla adresa URL instance pro vaši funkci přístupná.

### <a name="assign-an-access-role"></a>Přiřazení role přístupu

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Kostra funkce v předchozích příkladech vyžaduje předání nosných tokenů. Pokud token nosiče není předaný, aplikace Function App se nemůže ověřit pomocí digitálních vláken Azure. 

Abyste se ujistili, že je předaný token nosiče, nastavte oprávnění [spravovaných identit](../active-directory/managed-identities-azure-resources/overview.md) , aby aplikace Function App měla přístup k digitálním vlákenám Azure. Tato oprávnění nastavíte pro každou aplikaci Function App jenom jednou.


1. Chcete-li zobrazit podrobnosti o identitě spravované systémem pro funkci, použijte následující příkaz. Poznamenejte si `principalId` pole ve výstupu.

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
    > Výstup zobrazí podrobnosti o identitě, včetně `principalId` hodnoty požadované pro další krok. 

1. Pomocí `principalId` hodnoty v následujícím příkazu přiřaďte identitu aplikace funkcí k roli _vlastníka dat digitálních vláken Azure_ pro vaši instanci digitálních vláken Azure.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

### <a name="configure-application-settings"></a>Konfigurace nastavení aplikace

Zpřístupněte adresu URL vaší instance vaší funkci nastavením proměnné prostředí pro ni. Další informace o proměnných prostředí najdete v tématu [Správa aplikace Function App](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

> [!TIP]
> Adresa URL instance digitálních vláken Azure je tvořena přidáním *https://* na začátek názvu hostitele vaší instance. Chcete-li zobrazit název hostitele spolu se všemi vlastnostmi vaší instance, spusťte příkaz `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

```azurecli-interactive 
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Proveďte následující kroky v [Azure Portal](https://portal.azure.com/).

### <a name="assign-an-access-role"></a>Přiřazení role přístupu

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

Spravovaná identita přiřazená systémem umožňuje prostředkům Azure ověřování v cloudových službách (například Azure Key Vault) bez uložení přihlašovacích údajů do kódu. Po povolení spravované identity přiřazené systémem můžete všechna potřebná oprávnění udělit prostřednictvím řízení přístupu na základě role v Azure. 

Životní cyklus tohoto typu spravované identity je vázaný na životní cyklus tohoto prostředku. Každý prostředek navíc může mít pouze jednu spravovanou identitu přiřazenou systémem.

1. V [Azure Portal](https://portal.azure.com/)vyhledejte aplikaci Function App zadáním jejího názvu do vyhledávacího pole. Z výsledků vyberte svou aplikaci. 

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Snímek obrazovky Azure Portal. Název aplikace funkcí je na panelu hledání portálu a výsledek hledání je zvýrazněný.":::

1. Na stránce Function App klikněte v nabídce na levé straně na možnost __Identita__ pro práci se spravovanou identitou funkce. Na stránce __přiřazeno systému__ ověřte, zda je __stav__ nastaven na hodnotu **zapnuto**. Pokud není, nastavte ho hned a pak změnu **uložte** .

    :::image type="content" source="media/how-to-create-azure-function/verify-system-managed-identity.png" alt-text="Snímek obrazovky Azure Portal. Na stránce Identita aplikace Function App je možnost Stav nastavena na hodnotu Zapnuto." lightbox="media/how-to-create-azure-function/verify-system-managed-identity.png":::

1. Vyberte __přiřazení rolí Azure__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-1.png" alt-text="Snímek obrazovky Azure Portal. Na stránce Identita funkce Azure se v části oprávnění zvýrazní tlačítko přiřazení rolí Azure." lightbox="media/how-to-create-azure-function/add-role-assignment-1.png":::

    Vyberte __+ Přidat přiřazení role (Preview)__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-2.png" alt-text="Snímek obrazovky Azure Portal. Na stránce přiřazení rolí Azure se zvýrazní tlačítko Přidat přiřazení role (Preview)." lightbox="media/how-to-create-azure-function/add-role-assignment-2.png":::

1. Na stránce __Přidat přiřazení role (Preview)__ vyberte následující hodnoty:

    * **Obor**: _Skupina prostředků_
    * **Předplatné**: Vyberte své předplatné Azure.
    * **Skupina prostředků**: vyberte skupinu prostředků.
    * **Role**: _vlastník dat digitálních vláken Azure_

    Uložte podrobnosti výběrem možnosti __Uložit__.

    :::image type="content" source="media/how-to-create-azure-function/add-role-assignment-3.png" alt-text="Snímek obrazovky Azure Portal, který ukazuje, jak přidat nové přiřazení role. Dialog zobrazuje pole pro rozsah, předplatné, skupinu prostředků a roli.":::

### <a name="configure-application-settings"></a>Konfigurace nastavení aplikace

Pokud chcete, aby byla adresa URL instance digitálního vlákna Azure pro vaši funkci přístupná, můžete nastavit proměnnou prostředí. Nastavení aplikace jsou vystavena jako proměnné prostředí, aby bylo možné získat přístup k instanci digitálních vláken Azure. Další informace o proměnných prostředí najdete v tématu [Správa aplikace Function App](../azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Pokud chcete nastavit proměnnou prostředí s adresou URL vaší instance, nejdřív najděte název hostitele vaší instance: 

1. Vyhledejte vaši instanci v [Azure Portal](https://portal.azure.com). 
1. V nabídce na levé straně vyberte __Přehled__. 
1. Zkopírujte hodnotu __název hostitele__ .

    :::image type="content" source="media/how-to-create-azure-function/instance-host-name.png" alt-text="Snímek obrazovky Azure Portal. Na stránce Přehled instance je zvýrazněna hodnota název hostitele.":::

Nyní můžete vytvořit nastavení aplikace:

1. Na panelu hledání na portálu vyhledejte aplikaci Function App a vyberte ji z výsledků.

    :::image type="content" source="media/how-to-create-azure-function/portal-search-for-function-app.png" alt-text="Snímek obrazovky Azure Portal. Na panelu hledání na portálu se prohledává název aplikace Function App. Výsledek hledání je zvýrazněný.":::

1. Na levé straně vyberte __Konfigurace__. Pak na kartě __nastavení aplikace__ vyberte __+ nové nastavení aplikace__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting.png" alt-text="Snímek obrazovky Azure Portal. Na kartě Konfigurace aplikace Function App se zvýrazní tlačítko vytvořit nové nastavení aplikace.":::

1. V okně, které se otevře, použijte hodnotu název hostitele, kterou jste zkopírovali k vytvoření nastavení aplikace.
    * **Název**: ADT_SERVICE_URL
    * **Hodnota**: https://{your-Azure-Digital-revláken-Host-Name}
    
    Vyberte __OK__ a vytvořte nastavení aplikace.
    
    :::image type="content" source="media/how-to-create-azure-function/add-application-setting.png" alt-text="Snímek obrazovky Azure Portal. Na stránce Přidat/upravit nastavení aplikace jsou vyplněna pole název a hodnota. Tlačítko O se zvýrazní.":::

1. Po vytvoření nastavení by se mělo zobrazit na kartě __nastavení aplikace__ . Ověřte, že se v seznamu zobrazuje **ADT_SERVICE_URL** . Pak nové nastavení aplikace uložte výběrem možnosti __Uložit__.

    :::image type="content" source="media/how-to-create-azure-function/application-setting-save-details.png" alt-text="Snímek obrazovky Azure Portal. Na kartě nastavení aplikace se zvýrazní nové nastavení služba D T U R L. Tlačítko Uložit je také zvýrazněno.":::

1. Všechny změny nastavení aplikace vyžadují restart aplikace, proto vyberte __pokračovat__ a po zobrazení výzvy restartujte aplikaci.

    :::image type="content" source="media/how-to-create-azure-function/save-application-setting.png" alt-text="Snímek obrazovky Azure Portal. Poznámka uvádí, že všechny změny nastavení aplikace restartují vaši aplikaci. Tlačítko pokračovat je zvýrazněno.":::

---

## <a name="next-steps"></a>Další kroky

V tomto článku jste v Azure nastavili aplikaci Function App pro použití s digitálními ovládacími vlákna Azure. Další informace najdete v tématu postup sestavení na základní funkci pro ingestování [IoT Hub dat do digitálních vláken Azure](how-to-ingest-iot-hub-data.md).

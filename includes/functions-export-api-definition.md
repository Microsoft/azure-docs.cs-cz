## <a name="export-an-api-definition"></a>Export definice rozhraní API.
Máte definici OpenAPI pro funkci, z [vytvořit definici OpenAPI pro funkci](../articles/azure-functions/functions-openapi-definition.md). Další krok v tomto procesu je exportovat definice rozhraní API, tak, aby PowerApps a Flow Microsoft můžete použít v vlastní rozhraní API.

> [!IMPORTANT]
> Mějte na paměti, že vám musí být podepsané do Azure se stejnými pověřeními, které používáte pro vaše PowerApps a Microsoft Flow klienty. To umožňuje Azure a vytvořte vlastní rozhraní API a zpřístupní ji pro PowerApps a Flow společnosti Microsoft.

1. V [portál Azure](https://portal.azure.com), klikněte na název aplikace – funkce (jako **funkce. ukázku energie**) > **funkce** > **definice rozhraní API** .

    ![Definice rozhraní API](media/functions-export-api-definition/api-definition.png)

1. Klikněte na tlačítko **exportovat do PowerApps + toku**.

    ![Zdroj definice rozhraní API](media/functions-export-api-definition/export-api-1.png)

1. V pravém podokně používejte nastavení zadané v tabulce.

    |Nastavení|Popis|
    |--------|------------|
    |**Export režimu**|Vyberte **Express** automaticky generovat vlastní rozhraní API. Výběr **ruční** definice exportuje rozhraní API, ale pak musí ho naimportovat do PowerApps a Microsoft Flow ručně. Další informace najdete v tématu [exportovat do PowerApps a Microsoft Flow](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Prostředí**|Vyberte prostředí, ve kterém by měl být uložen vlastního rozhraní API. Další informace najdete v tématu [Přehled prostředí (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) nebo [Přehled prostředí (Microsoft Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Název vlastního rozhraní API**|Zadejte název, jako je třeba `Turbine Repair`.|
    |**Název klíče rozhraní API**|Zadejte název, který aplikaci a toku počítačů by se měla zobrazit v uživatelském rozhraní pro vlastní rozhraní API. Všimněte si, že v příkladu obsahuje užitečné informace.|
 
    ![Export do PowerApps a Microsoft Flow](media/functions-export-api-definition/export-api-2.png)

1. Klikněte na **OK**. Vlastní rozhraní API je teď vytvořené a přidat do prostředí, které zadáte.
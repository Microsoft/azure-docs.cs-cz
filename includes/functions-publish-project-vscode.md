

## <a name="publish-the-project-to-azure"></a>Publikování projektu do Azure

Visual Studio Code umožňuje publikovat projekt funkcí přímo do Azure. Během postupu vytvoříte aplikaci funkcí a související prostředky ve vašem předplatném Azure. Aplikace funkcí poskytuje kontext spuštění pro vaše funkce. Projekt se zabalí a nasadí do nové aplikace funkcí ve vašem předplatném Azure. 

Tento článek předpokládá, že vytváříte novou aplikaci funkcí. Publikování do existující aplikace funkcí přepíše obsah této aplikace v Azure.

1. V oblasti **Azure: Functions** vyberte ikonu Nasadit do Function App.

    ![Nastavení aplikace funkcí](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Zvolte složku projektu, která je vaším aktuálním pracovním prostorem.

1. Pokud máte víc předplatných, vyberte to, které má hostovat vaši aplikaci funkcí, a pak zvolte **+ Vytvořit novou aplikaci funkcí**.

1. Zadejte globálně jedinečný název, který identifikuje vaši aplikaci funkcí, a stiskněte klávesu Enter. Platné znaky pro název aplikace funkcí jsou `a-z`, `0-9` a `-`.

1. Zvolte **+ Vytvořit novou skupinu prostředků**, zadejte název skupiny prostředků, například `myResourceGroup`, a stiskněte klávesu Enter. Můžete také použít existující skupinu prostředků.

1. Zvolte **+ vytvořit nový účet úložiště**, typ globálně jedinečný název pro nový účet úložiště používá vaše aplikace function app a potom stiskněte klávesu Enter. Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete taky použít existující účet.

1. Zvolte umístění v [oblasti](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají.

    Vytvoření aplikace funkcí se zahájí po zvolení umístění. Po vytvoření aplikace funkcí a použití balíčku nasazení se zobrazí oznámení.

1. V oznámení vyberte **Zobrazit výstup**, abyste zobrazili výsledky vytvoření a nasazení, včetně prostředků Azure, které jste vytvořili.

    ![Výstup vytvoření aplikace funkcí](./media/functions-publish-project-vscode/function-create-notifications.png)

1. Poznamenejte si adresu URL nové aplikace funkcí v Azure. Použijete ji k otestování funkce po publikování projektu do Azure.

    ![Výstup vytvoření aplikace funkcí](./media/functions-publish-project-vscode/function-create-output.png)

1. Zpátky v oblasti **Azure: Functions** uvidíte, že nová aplikace funkcí se zobrazí pod vaším předplatným. Rozbalením tohoto uzlu se zobrazí funkce v aplikaci funkcí, stejně jako nastavení aplikace a proxy servery funkcí.

    ![Nastavení aplikace funkcí](./media/functions-publish-project-vscode/function-app-project-settings.png)

    Z uzlu aplikace funkcí klikněte se stisknutou klávesou Ctrl (klikněte pravým tlačítkem), abyste zvolili provádění z nabídky různých úloh správy a konfigurace pro aplikaci funkcí v Azure. Můžete také zvolit zobrazení aplikace funkcí na webu Azure Portal.

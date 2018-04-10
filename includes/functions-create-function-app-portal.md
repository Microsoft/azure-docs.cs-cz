1. Klikněte na tlačítko **Nový** v levém horním rohu portálu Azure Portal a pak vyberte **Compute** > **Function App**. 

    ![Vytvoření aplikace Function App na portálu Azure Portal](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Použijte nastavení aplikace Function App uvedená v tabulce pod obrázkem.

    ![Určení nastavení nové aplikace Function App](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Nastavení      | Navrhovaná hodnota  | Popis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název aplikace** | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a-z`, `0-9` a `-`.  | 
    | **Předplatné** | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. | 
    | **[Skupina prostředků](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. | 
    | **OS** | Windows | Hostování bez serveru je momentálně dostupné jenom při spuštění ve Windows. Informace k hostování na Linuxu najdete v tématu [Vytvoření první funkce spuštěné v Linuxu pomocí Azure CLI](../articles/azure-functions/functions-create-first-azure-function-azure-cli-linux.md). |
    | **[Plán hostování](../articles/azure-functions/functions-scale.md)** |   Plán Consumption | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím **plánu Consumption** se prostředky přidávají dynamicky podle požadavků příslušných funkcí. U tohoto hostování [bez serveru](https://azure.microsoft.com/overview/serverless-computing/) platíte jenom za dobu, kdy jsou funkce spuštěné.   |
    | **Umístění** | Západní Evropa | Vyberte [oblast](https://azure.microsoft.com/regions/) ve své blízkosti nebo v blízkosti jiných služeb, které vaše funkce využívají. |
    | **[Účet úložiště](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Globálně jedinečný název |  Název nového účtu úložiště, který bude aplikace Function App používat. Názvy účtů úložiště musí mít od 3 do 24 znaků a můžou obsahovat jenom číslice a malá písmena. Můžete taky použít existující účet. |

3. Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**. 

4. Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**. 

    ![Určení nastavení nové aplikace Function App](./media/functions-create-function-app-portal/function-app-create-notification.png)

4. Volbou **Přejít k prostředku** novou aplikaci Function App zobrazíte.

>[!TIP]
>Pokud máte potíže najít na portálu své aplikace funkcí, zkuste [přidat aplikace funkcí mezi oblíbené na webu Azure Portal](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#favorite).   


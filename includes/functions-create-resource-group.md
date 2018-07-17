## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure, jako například aplikace Function App, databáze a účty úložiště.

Následující příklad vytvoří skupinu prostředků s názvem `myResourceGroup`.  
Pokud nepoužíváte službu Cloud Shell, nejprve se přihlaste pomocí `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Obvykle budete svoji skupinu prostředků a prostředky vytvářet v oblasti, kterou máte blízko. Pokud chcete zobrazit všechna podporovaná umístění pro plány služby App Service, spusťte příkaz [az appservice list-locations](/cli/azure/appservice#az_appservice_list_locations).
Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group?view=azure-cli-latest#az_group_create).

[!INCLUDE [resource group intro text](resource-group.md)]

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *westeurope*.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Pokud chcete zobrazit dostupná umístění, spusťte příkaz `az appservice list-locations`. Obvykle budete prostředky vytvářet v oblasti, kterou máte blízko.

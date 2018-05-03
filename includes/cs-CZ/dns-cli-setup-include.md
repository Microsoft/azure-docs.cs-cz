## <a name="set-up-azure-cli-for-azure-dns"></a>Nastavení rozhraní příkazového řádku Azure pro Azure DNS

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure, která je dostupná pro Windows, Linux a Mac. Další informace najdete v tématu [Instalace rozhraní příkazového řádku Azure CLI](../articles/cli-install-nodejs.md).

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Otevřete okno konzoly a proveďte ověření pomocí svých přihlašovacích údajů. Další informace najdete v tématu [Přihlášení k Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Přepnutí režimu rozhraní příkazového řádku

Azure DNS používá Azure Resource Manager. Nezapomeňte přepnout režim rozhraní příkazového řádku tak, aby se používaly příkazy Azure Resource Manageru.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Výběr předplatného

Zkontrolujte předplatná pro příslušný účet.

```azurecli
azure account list
```

Zvolte předplatné Azure, které chcete použít.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Všechny prostředky DNS jsou ale globální, a ne místní, takže volba umístění skupiny prostředků nemá na Azure DNS žádný vliv.

Pokud používáte některou ze stávajících skupin prostředků, můžete tento krok přeskočit.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registrace poskytovatele prostředků

Službu Azure DNS spravuje poskytovatel prostředků Microsoft.Network. Abyste mohli používat Azure DNS, je nutné zaregistrovat předplatné Azure k používání tohoto poskytovatele prostředků. Jedná se o jednorázovou operaci u každého odběru.

```azurecli
azure provider register --namespace Microsoft.Network
```


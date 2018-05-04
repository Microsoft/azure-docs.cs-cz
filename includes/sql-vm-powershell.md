
## <a name="start-your-powershell-session"></a>Spuštění relace prostředí PowerShell
Nejdřív je potřeba mít nejnovější [prostředí Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) nainstalovaná a spuštěná. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> V příkladech v tomto tématu [modelu nasazení Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md), takže příklady používají [rutiny Azure Resource Manager](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Spustit [ **Connect-AzureRmAccount** ](http://msdn.microsoft.com/library/mt619267.aspx) rutiny a zobrazí se přihlašovací obrazovka, zadat přihlašovací údaje. Použijte stejné přihlašovací údaje, pomocí kterých se přihlašujete na portál Azure.

    Connect-AzureRmAccount

Pokud máte více předplatných, pomocí [ **Set-AzureRmContext** ](http://msdn.microsoft.com/library/mt619263.aspx) rutiny vyberte, které předplatné má relace prostředí PowerShell použít. Pokud chcete zjistit, jaké předplatné používá aktuální relace prostředí PowerShell, spusťte rutinu [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx). Pokud chcete zobrazit všechna předplatná, spusťte rutinu [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'


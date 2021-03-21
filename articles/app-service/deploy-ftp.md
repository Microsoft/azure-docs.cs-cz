---
title: Nasazení obsahu pomocí FTP/S
description: Naučte se, jak nasadit aplikaci pro Azure App Service pomocí FTP nebo FTPS. Vylepšete zabezpečení webu zakázáním nešifrovaného serveru FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045798"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Nasazení aplikace pro Azure App Service pomocí FTP/S

V tomto článku se dozvíte, jak pomocí FTP nebo FTPS nasadit webovou aplikaci, back-end mobilní aplikace nebo aplikaci API [Azure App Service](./overview.md).

Koncový bod FTP/S pro vaši aplikaci je už aktivní. Pro povolení nasazení FTP/S není nutná žádná konfigurace.

> [!NOTE]
> Stránka **centra pro vývoj (Classic)** v Azure Portal, což je staré prostředí pro nasazení, bude zastaralá v březnu 2021. Tato změna nebude mít vliv na žádná existující nastavení nasazení v aplikaci a můžete pokračovat ve správě nasazení aplikace na stránce **centra nasazení** .

## <a name="get-deployment-credentials"></a>Získat přihlašovací údaje pro nasazení

1. Postupujte podle pokynů v tématu [konfigurace přihlašovacích údajů nasazení pro Azure App Service](deploy-configure-credentials.md) ke zkopírování přihlašovacích údajů rozsahu aplikace nebo nastavení přihlašovacích údajů uživatele na obor. Můžete se připojit ke koncovému bodu FTP/S aplikace pomocí přihlašovacích údajů.

1. Zadejte uživatelské jméno FTP v následujícím formátu v závislosti na zvoleném oboru přihlašovacích údajů:

    | Rozsah aplikace | Rozsah uživatele |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    V App Service se koncový bod FTP/S sdílí mezi aplikacemi. Vzhledem k tomu, že přihlašovací údaje oboru uživatele nejsou propojené s konkrétním prostředkem, je třeba, abyste předřadíte uživatelské jméno oboru uživatele s názvem aplikace, jak je uvedeno výše.

## <a name="get-ftps-endpoint"></a>Získat koncový bod FTP/S
    
# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Na stejné stránce pro správu aplikace, kam jste zkopírovali přihlašovací údaje nasazení (  >  **přihlašovací údaje FTP** centra nasazení), zkopírujte **koncový bod FTPS**.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Spusťte příkaz [AZ WebApp Deployment list-Publishing-Profiles](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) . Následující příklad používá [cestu JMES](https://jmespath.org/) k extrakci koncových bodů FTP/S z výstupu.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Každá aplikace má dva koncové body FTP/S, jeden je pro čtení i zápis, zatímco druhý je jen pro čtení ( `profileName` obsahuje `ReadOnly` ) a je určený pro scénáře obnovení dat. Chcete-li nasadit soubory s protokolem FTP, zkopírujte adresu URL koncového bodu pro čtení i zápis.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Spusťte příkaz [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) . Následující příklad extrahuje koncový bod FTP/S z výstupu XML.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Nasazení souborů do Azure

1. Z klienta FTP (například sady [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)nebo [WinSCP](https://winscp.net/index.php)) použijte informace o připojení, které jste shromáždili pro připojení k vaší aplikaci.
2. Zkopírujte své soubory a příslušné adresářové struktury do [adresáře **/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) v Azure (nebo adresáře **/site/wwwroot/App_Data/Jobs/** pro WebJobs).
3. Vyhledejte adresu URL vaší aplikace a ověřte, že aplikace funguje správně. 

> [!NOTE] 
> Na rozdíl od [nasazení založeného na Gitu](deploy-local-git.md) a [nasazení zip](deploy-zip.md)nepodporuje nasazení FTP automatizaci sestavení, jako je například: 
>
> - obnovení závislosti (například nástroje NuGet, NPM, PIP a skladatel)
> - kompilace binárních souborů .NET
> - generování web.config ( [ příkladNode.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Vygenerujte tyto potřebné soubory ručně na místním počítači a pak je nasaďte společně s vaší aplikací.
>

## <a name="enforce-ftps"></a>Vyhovět FTPS

Pro zvýšení zabezpečení byste měli použít jenom protokol FTP přes protokol TLS/SSL. Pokud nepoužíváte nasazení FTP, můžete také zakázat protokol FTP i FTPS.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Na stránce prostředků vaší aplikace v [Azure Portal](https://portal.azure.com)v levém navigačním panelu vyberte **Konfigurace**  >  **Obecné nastavení** .

2. Pokud chcete zakázat nešifrované FTP, vyberte **FTPS jenom** ve **stavu FTP**. Pokud chcete úplně vypnout FTP i FTPS, vyberte **disabled (zakázáno**). Jakmile budete hotoví, klikněte na **Uložit**. Pokud používáte **jenom FTPS**, musíte vyhovět TLS 1,2 nebo vyššímu, a to tak, že přejdete do okna **Nastavení TLS/SSL** ve vaší webové aplikaci. TLS 1,0 a 1,1 nejsou podporované jenom pro **FTPS**.

    ![Zakázat FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Spusťte příkaz [AZ WebApp config set](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) s `--ftps-state` argumentem.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Možné hodnoty pro `--ftps-state` jsou `AllAllowed` (FTP a FTPS povoleny), `Disabled` (FTP a FTPS disabled) a `FtpsOnly` (jenom FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Spusťte příkaz [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) s `-FtpsState` parametrem.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Možné hodnoty pro `--ftps-state` jsou `AllAllowed` (FTP a FTPS povoleny), `Disabled` (FTP a FTPS disabled) a `FtpsOnly` (jenom FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Řešení potíží s nasazením FTP

- [Jak můžu řešit potíže s nasazením FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Nedaří se mi FTP a publikovat můj kód. Jak můžu problém vyřešit?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak se můžu připojit k FTP v Azure App Service prostřednictvím pasivního režimu?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak můžu řešit potíže s nasazením FTP?

Prvním krokem pro řešení potíží s nasazením FTP je izolování problému s nasazením z běhového problému s aplikací.

K problému s nasazením obvykle dojde v případě, že do vaší aplikace nejsou nasazené žádné soubory nebo chybné soubory. Můžete řešit potíže pomocí šetření nasazení FTP nebo výběru alternativní cesty nasazení (například správy zdrojového kódu).

Problém běhové aplikace obvykle vede ke správné sadě souborů nasazených do vaší aplikace, ale nesprávnému chování aplikace. Můžete řešit problémy tím, že zaměříte chování kódu za běhu a prozkoumáte konkrétní cesty selhání.

K určení problému s nasazením nebo za běhu najdete informace v tématu [nasazení vs. běhové chyby](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nemůžu se připojit přes protokol FTP a publikovat kód. Jak můžu problém vyřešit?
Ověřte, zda jste zadali správný [název hostitele](#get-ftps-endpoint) a [přihlašovací údaje](#get-deployment-credentials). Ověřte také, že brána firewall neblokuje následující porty FTP v počítači:

- Port připojení ovládacího prvku FTP: 21, 990
- Port pro připojení dat FTP: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak se můžu připojit k FTP v Azure App Service prostřednictvím pasivního režimu?
Azure App Service podporuje připojení prostřednictvím aktivního i pasivního režimu. Pasivní režim je preferovaný, protože počítače nasazení jsou obvykle za bránou firewall (v operačním systému nebo v rámci domácí nebo firemní sítě). Podívejte se na [příklad v dokumentaci k WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Další zdroje informací

* [Místní nasazení Gitu pro Azure App Service](deploy-local-git.md)
* [Přihlašovací údaje pro nasazení Azure App Service](deploy-configure-credentials.md)
* [Ukázka: Vytvoření webové aplikace a nasazení souborů s využitím protokolu FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).
* [Ukázka: nahrání souborů do webové aplikace pomocí FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

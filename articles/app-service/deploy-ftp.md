---
title: Nasazení obsahu pomocí FTP/S
description: Naučte se, jak nasadit aplikaci pro Azure App Service pomocí FTP nebo FTPS. Vylepšete zabezpečení webu zakázáním nešifrovaného serveru FTP.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 9884b109db3f3a34ceb323bef9fba1d5bfc23147
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150265"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Nasazení aplikace pro Azure App Service pomocí FTP/S

V tomto článku se dozvíte, jak pomocí FTP nebo FTPS nasadit webovou aplikaci, back-end mobilní aplikace nebo aplikaci API [Azure App Service](./overview.md).

Koncový bod FTP/S pro vaši aplikaci je už aktivní. Pro povolení nasazení FTP/S není nutná žádná konfigurace.

## <a name="open-ftp-dashboard"></a>Otevření řídicího panelu FTP

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**.

    ![Vyhledejte App Services.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Vyberte webovou aplikaci, kterou chcete nasadit.

    ![Vyberte svou aplikaci.](media/app-service-continuous-deployment/select-your-app.png)

3. Vyberte **Deployment Center**  >  **FTP**  >  **řídicí panel**FTP centra nasazení.

    ![Otevření řídicího panelu FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Získat informace o připojení FTP

Na řídicím panelu FTP vyberte **Kopírovat** a zkopírujte koncový bod FTPS a přihlašovací údaje aplikace.

![Kopírovat informace FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Doporučujeme, abyste pro nasazení do aplikace použili **přihlašovací údaje aplikace** , protože jsou pro každou aplikaci jedinečné. Pokud ale kliknete na **přihlašovací údaje uživatele**, můžete nastavit přihlašovací údaje na úrovni uživatele, které můžete použít pro přihlášení k FTP/S pro všechny App Service aplikace v rámci vašeho předplatného.

> [!NOTE]
> Ověřování u koncového bodu FTP/FTPS pomocí přihlašovacích údajů na úrovni uživatele requirers uživatelské jméno v následujícím formátu: 
>
>`<app-name>\<user-name>`
>
> Vzhledem k tomu, že přihlašovací údaje na úrovni uživatele jsou propojené s uživatelem a ne konkrétním prostředkem, musí být uživatelské jméno v tomto formátu, aby se akce přihlášení nasměrovala do pravého koncového bodu aplikace.
>

## <a name="deploy-files-to-azure"></a>Nasazení souborů do Azure

1. Z klienta FTP (například sady [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)nebo [WinSCP](https://winscp.net/index.php)) použijte informace o připojení, které jste shromáždili pro připojení k vaší aplikaci.
2. Zkopírujte své soubory a příslušné adresářové struktury do [adresáře **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) v Azure (nebo adresáře **/site/wwwroot/App_Data/Jobs/** pro WebJobs).
3. Vyhledejte adresu URL vaší aplikace a ověřte, že aplikace funguje správně. 

> [!NOTE] 
> Na rozdíl od [nasazení na základě Gitu](deploy-local-git.md)nepodporuje nasazení FTP následující automatizace nasazení: 
>
> - obnovení závislosti (například nástroje NuGet, NPM, PIP a skladatel)
> - kompilace binárních souborů .NET
> - generování web.config ( [ příkladNode.js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Vygenerujte tyto potřebné soubory ručně na místním počítači a pak je nasaďte společně s vaší aplikací.
>

## <a name="enforce-ftps"></a>Vyhovět FTPS

Pro zvýšení zabezpečení byste měli použít jenom protokol FTP přes protokol TLS/SSL. Pokud nepoužíváte nasazení FTP, můžete také zakázat protokol FTP i FTPS.

Na stránce prostředků vaší aplikace v [Azure Portal](https://portal.azure.com)v levém navigačním panelu vyberte **Konfigurace**  >  **Obecné nastavení** .

Pokud chcete zakázat nešifrované FTP, vyberte **FTPS jenom** ve **stavu FTP**. Pokud chcete úplně vypnout FTP i FTPS, vyberte **disabled (zakázáno**). Jakmile budete hotoví, klikněte na **Uložit**. Pokud používáte **jenom FTPS**, musíte vyhovět TLS 1,2 nebo vyššímu, a to tak, že přejdete do okna **Nastavení TLS/SSL** ve vaší webové aplikaci. TLS 1,0 a 1,1 nejsou podporované jenom pro **FTPS**.

![Zakázat FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Informace o nasazení FTP pomocí [Azure CLI](/cli/azure)najdete v tématu [Vytvoření webové aplikace a nasazení souborů s využitím protokolu FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

Informace o nasazení FTP pomocí [Azure PowerShell](/cli/azure)najdete v tématu [nahrání souborů do webové aplikace pomocí FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Řešení potíží s nasazením FTP

- [Jak můžu řešit potíže s nasazením FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Nedaří se mi FTP a publikovat můj kód. Jak můžu problém vyřešit?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak se můžu připojit k FTP v Azure App Service prostřednictvím pasivního režimu?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak můžu řešit potíže s nasazením FTP?

Prvním krokem pro řešení potíží s nasazením FTP je izolování problému s nasazením z běhového problému s aplikací.

K problému s nasazením obvykle dojde v případě, že do vaší aplikace nejsou nasazené žádné soubory nebo chybné soubory. Můžete řešit potíže pomocí šetření nasazení FTP nebo výběru alternativní cesty nasazení (například správy zdrojového kódu).

Problém běhové aplikace obvykle vede ke správné sadě souborů nasazených do vaší aplikace, ale nesprávnému chování aplikace. Můžete řešit problémy tím, že zaměříte chování kódu za běhu a prozkoumáte konkrétní cesty selhání.

K určení problému s nasazením nebo za běhu najdete informace v tématu [nasazení vs. běhové chyby](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nemůžu se připojit přes protokol FTP a publikovat kód. Jak můžu problém vyřešit?
Ověřte, zda jste zadali správný název hostitele a [přihlašovací údaje](#open-ftp-dashboard). Ověřte také, že brána firewall neblokuje následující porty FTP v počítači:

- Port připojení řízení FTP: 21
- Port pro připojení dat FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak se můžu připojit k FTP v Azure App Service prostřednictvím pasivního režimu?
Azure App Service podporuje připojení prostřednictvím aktivního i pasivního režimu. Pasivní režim je preferovaný, protože počítače nasazení jsou obvykle za bránou firewall (v operačním systému nebo v rámci domácí nebo firemní sítě). Podívejte se na [příklad v dokumentaci k WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Další kroky

V případě pokročilejších scénářů nasazení zkuste [nasadit do Azure pomocí Gitu](deploy-local-git.md). Nasazení založené na Gitu do Azure umožňuje správu verzí, obnovování balíčků, MSBuild a další.

## <a name="more-resources"></a>Další zdroje informací

* [Přihlašovací údaje pro nasazení Azure App Service](deploy-configure-credentials.md)
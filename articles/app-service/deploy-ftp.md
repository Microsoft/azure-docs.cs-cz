---
title: Nasazení obsahu pomocí FTP/S
description: Zjistěte, jak nasadit aplikaci do služby Azure App Service pomocí FTP nebo FTPS. Zlepšete zabezpečení webových stránek zakázáním nešifrovaného ftp.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 7bc637b5719da3c5f5e5607436aa7da0721f5a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266010"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Nasazení aplikace do služby Azure App Service pomocí FTP/S

V tomto článku se ukazuje, jak pomocí FTP nebo FTPS nasadit webovou aplikaci, back-end mobilní aplikace nebo aplikaci API do [služby Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Koncový bod FTP/S pro vaši aplikaci je již aktivní. Pro povolení nasazení PROTOKOLU FTP/S není nutná žádná konfigurace.

## <a name="open-ftp-dashboard"></a>Otevřít řídicí panel FTP

1. Na [webu Azure Portal](https://portal.azure.com)vyhledejte a vyberte **App Services**.

    ![Vyhledejte služby aplikací.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Vyberte webovou aplikaci, kterou chcete nasadit.

    ![Vyberte aplikaci.](media/app-service-continuous-deployment/select-your-app.png)

3. Vyberte**řídicí panel****FTP** >  **centra** > nasazení .

    ![Otevřít řídicí panel FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Získání informací o připojení FTP

Na řídicím panelu FTP vyberte **Kopírovat,** chcete-li zkopírovat koncový bod FTPS a přihlašovací údaje aplikace.

![Kopírovat informace ftp](./media/app-service-deploy-ftp/ftp-dashboard.png)

K nasazení do aplikace doporučujeme použít **přihlašovací údaje aplikace,** protože je jedinečná pro každou aplikaci. Pokud však kliknete na **pověření uživatele**, můžete nastavit přihlašovací údaje na úrovni uživatele, které můžete použít pro přihlášení FTP/S ke všem aplikacím služby App Service ve vašem předplatném.

> [!NOTE]
> Ověření koncového bodu FTP/FTPS pomocí požadavků pověření na úrovni uživatele pomocí požadavků na uživatelská úroveň uživatelské jméno v následujícím formátu: 
>
>`<app-name>\<user-name>`
>
> Vzhledem k tomu, že pověření na úrovni uživatele jsou propojeny s uživatelem a není konkrétní prostředek, uživatelské jméno musí být v tomto formátu přímé akce přihlášení na koncový bod aplikace.
>

## <a name="deploy-files-to-azure"></a>Nasazení souborů do Azure

1. Z vašeho klienta FTP (například [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)nebo [WinSCP](https://winscp.net/index.php)) použijte informace o připojení, které jste shromáždili pro připojení k aplikaci.
2. Zkopírujte soubory a jejich příslušnou adresářovou strukturu do [adresáře **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) v Azure (nebo do adresáře **/site/wwwroot/App_Data/Jobs/** pro WebJobs).
3. Přejděte na adresu URL aplikace a ověřte, zda aplikace běží správně. 

> [!NOTE] 
> Na rozdíl od [nasazení založených na Gitu](deploy-local-git.md)nasazení FTP nepodporuje následující automatizace nasazení: 
>
> - obnovení závislostí (například Automatizace NuGet, NPM, PIP a Composer)
> - kompilace binárních souborů .NET
> - generace web.config (zde je [příklad Node.js)](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Vygenerujte tyto potřebné soubory ručně v místním počítači a pak je nasaďte společně s aplikací.
>

## <a name="enforce-ftps"></a>Vynutit FTPS

Chcete-li zvýšit zabezpečení, měli byste povolit ftp pouze přes SSL. Pokud nepoužíváte nasazení FTP, můžete také zakázat ftp i FTPS.

Na stránce prostředků aplikace na [webu Azure Portal](https://portal.azure.com)vyberte v levém navigačním panelu nastavení**Obecné** **konfigurace.** > 

Chcete-li zakázat nešifrovaný ftp, vyberte **možnost FTPS Pouze** ve **stavu FTP**. Chcete-li zakázat ftp i ftps úplně, vyberte **zakázáno**. Jakmile budete hotoví, klikněte na **Uložit**. Pokud používáte **pouze FTPS**, musíte vynutit TLS 1.2 nebo vyšší přechodem na okno **nastavení TLS/SSL** vaší webové aplikace. TLS 1.0 a 1.1 nejsou **podporovány pouze ftps**.

![Zakázání ftp/s](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Nasazení FTP pomocí [azure cli](/cli/azure)najdete [v tématu vytvoření webové aplikace a nasazení souborů pomocí FTP (Azure CLI).](./scripts/cli-deploy-ftp.md)

Informace o nasazení FTP pomocí [Azure PowerShellu](/cli/azure)najdete [v tématu Nahrávání souborů do webové aplikace pomocí FTP (PowerShell).](./scripts/powershell-deploy-ftp.md)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Poradce při potížích s nasazením FTP

- [Jak lze vyřešit potíže s nasazením FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Nejsem schopen FTP a publikovat svůj kód. Jak mohu problém vyřešit?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak se můžu připojit k FTP ve službě Azure App Service v pasivním režimu?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak lze vyřešit potíže s nasazením FTP?

Prvním krokem pro řešení potíží s nasazením FTP je izolování problému s nasazením z problému s aplikací runtime.

Problém s nasazením obvykle vede k žádné soubory nebo nesprávné soubory nasazené do vaší aplikace. Řešení potíží můžete prozkoumat nasazení FTP nebo výběrem alternativní cesty nasazení (například správy zdrojového kódu).

Problém s runtime aplikací obvykle vede k správné sadě souborů nasazených do vaší aplikace, ale nesprávné chování aplikace. Řešení potíží můžete zaměřit na chování kódu za běhu a zkoumání konkrétní cesty selhání.

Chcete-li zjistit problém s nasazením nebo za běhu, přečtěte si téma [Problémy s nasazením vs. runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Nemůžu se připojit přes protokol FTP a publikovat kód. Jak mohu problém vyřešit?
Zkontrolujte, zda jste zadali správný název hostitele a [přihlašovací údaje](#open-ftp-dashboard). Zkontrolujte také, zda bránou firewall neblokuje následující porty FTP v počítači:

- Port připojení řízení FTP: 21
- Datový port FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak se můžu připojit k FTP ve službě Azure App Service v pasivním režimu?
Služba Azure App Service podporuje připojení prostřednictvím aktivního i pasivního režimu. Pasivní režim je upřednostňován, protože vaše počítače nasazení jsou obvykle za bránou firewall (v operačním systému nebo jako součást domácí nebo podnikové sítě). Podívejte se na [příklad z dokumentace winscp](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Další kroky

Pokročilejší scénáře nasazení nawebujte nasazení [do Azure pomocí Gitu](deploy-local-git.md). Nasazení na základě Gitu do Azure umožňuje správu verzí, obnovení balíčků, MSBuild a další.

## <a name="more-resources"></a>Další zdroje informací

* [Přihlašovací údaje pro nasazení služby Azure App Service](deploy-configure-credentials.md)

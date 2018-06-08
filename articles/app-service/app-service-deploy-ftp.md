---
title: Nasazení aplikace do Azure App Service pomocí FTP/S | Microsoft Docs
description: Informace o nasazení aplikace do služby Azure App Service pomocí FTP a FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: cephalin;dariac
ms.openlocfilehash: 7e05e06a5abd02dd67f58a8e01bb246e318f51de
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850232"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Nasazení aplikace do Azure App Service pomocí FTP/S

Tento článek ukazuje, jak použít protokol FTP nebo FTPS k nasazení vaší webové aplikace, back-end mobilní aplikace nebo aplikaci API [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Koncový bod FTP/S pro aplikace je již aktivní. Žádná konfigurace je nutná pro povolení nasazení FTP/S.

## <a name="open-ftp-dashboard"></a>Otevřete řídicí panel FTP

V [portál Azure](https://portal.azure.com), otevřete v této aplikaci [prostředků stránky](../azure-resource-manager/resource-group-portal.md#manage-resources).

Chcete-li otevřít řídicí panel FTP, klikněte na tlačítko **nastavené průběžné doručování (Preview)** > **FTP** > **řídicí panel**.

![Otevřete řídicí panel FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Získat informace o připojení FTP

Na řídicím panelu FTP, klikněte na tlačítko **kopie** zkopírovat přihlašovací údaje pro koncový bod a aplikace FTPS.

![Zkopírujte informace o FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Je doporučeno používat **aplikace pověření** nasadit do vaší aplikace, protože je jedinečné pro každou aplikaci. Ale pokud kliknete na tlačítko **přihlašovací údaje uživatele**, můžete nastavit přihlašovací údaje uživatele, které můžete použít pro přihlášení k FTP/S pro všechny aplikace služby App Service v rámci vašeho předplatného.

## <a name="deploy-files-to-azure"></a>Soubory nasadit do Azure

1. Z vašeho klienta FTP (například [Visual Studio](https://www.visualstudio.com/vs/community/) nebo [FileZilla](https://filezilla-project.org/download.php?type=client)), použijte informace o připojení, které jste shromáždili pro připojení k vaší aplikace.
3. Kopírování souborů a jejich odpovídajících adresářovou strukturu pro [ **/web/wwwroot** directory](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) v Azure (nebo **/lokality/wwwroot/App_Data/úlohy/** adresář pro webové úlohy).
4. Přejděte na adresu URL vaší aplikace k ověření, že aplikace běží správně. 

> [!NOTE] 
> Na rozdíl od [nasazení na základě Git](app-service-deploy-local-git.md), FTP nasazení nepodporuje automatizaci následující nasazení: 
>
> - závislost obnoví (například NuGet, NPM, PIP a autora automatizaci)
> - kompilace rozhraní .NET binárních souborů
> - generování souboru Web.config (tady je [Node.js příklad](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Generovat tyto potřebné soubory ručně na místním počítači a pak je nasadit společně s vaší aplikace.
>
>

## <a name="enforce-ftps"></a>Vynutit FTPS

Pro zvýšení zabezpečení byste měli povolit FTP přes protokol SSL jen. Pokud nepoužijete FTP nasazení, lze zakázat i FTP a FTPS.

Na stránce prostředek vaší aplikace v [portál Azure](https://portal.azure.com), vyberte **nastavení aplikace** v levém navigačním panelu.

Zakázat nezašifrované FTP, vyberte **FTPS pouze**. Chcete-li zcela zakázat FTP a FTPS, vyberte **zakázat**. Po dokončení klikněte na tlačítko **Uložit**.

![Zakázat FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizace pomocí skriptů

Pro nasazení pomocí FTP [rozhraní příkazového řádku Azure](/cli/azure), najdete v části [vytvoření webové aplikace a nasazení souborů pomocí protokolu FTP (Azure CLI)](./scripts/app-service-cli-deploy-ftp.md).

Pro nasazení pomocí FTP [prostředí Azure PowerShell](/cli/azure), najdete v části [nahrání souborů do webové aplikace pomocí protokolu FTP (PowerShell)](./scripts/app-service-powershell-deploy-ftp.md).

## <a name="troubleshoot-ftp-deployment"></a>Řešení potíží s FTP nasazení

- [Jak můžete vyřešit potíže s FTP nasazení?](#how-can-i-troubleshoot-ftp-deployment)
- [Není možné FTP a publikování vlastní kód. Jak lze problém vyřešit?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Jak se můžete připojit k serveru FTP ve službě Azure App Service přes pasivní režim?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Jak můžete vyřešit potíže s FTP nasazení?

Prvním krokem pro řešení potíží s nasazením FTP je izolace nasazení problém z problémem aplikace za běhu.

Nasazení problém obvykle výsledkem žádné soubory nebo nesprávné soubory nasadit do vaší aplikace. Řešení potíží s nasazením FTP na odstranění příčin nebo výběrem cestu alternativní nasazení (jako je například Správa zdrojového kódu).

Problému s aplikací runtime obvykle výsledkem správnou sadu soubory nasadit do vaší aplikace, ale chování nesprávný aplikace. Můžete řešit pomocí zaměření na kód chování za běhu a příčin selhání konkrétní cesty.

Nasazení nebo modul runtime problém určit, najdete v tématu [nasazení oproti runtime problémy](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Není možné FTP a publikování vlastní kód. Jak lze problém vyřešit?
Zkontrolujte, zda jste zadali správný název hostitele a [pověření](#step-1--set-deployment-credentials). Zkontrolujte také, že následující porty FTP na počítači nejsou blokována bránou firewall:

- Port připojení řízení FTP: 21
- Port pro připojení FTP data: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Jak se můžete připojit k serveru FTP ve službě Azure App Service přes pasivní režim?
Aplikační služba Azure podporuje připojení prostřednictvím aktivní a pasivní režim. Pasivní režim je preferovaná, protože vaše nasazení počítače jsou obvykle za bránou firewall (v operačním systému nebo jako součást síť doma nebo firemní síť). V tématu [příklad z dokumentace WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Další postup

Pro pokročilejší scénáře nasazení, zkuste [nasazení do Azure s Gitem](app-service-deploy-local-git.md). Na základě Git nasazení do Azure umožňuje verzí, obnovení balíčků, MSBuild a další.

## <a name="more-resources"></a>Další materiály

* [Přihlašovací údaje pro nasazení služby Azure App Service](app-service-deploy-ftp.md)

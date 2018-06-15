---
title: Pomocí prohlížeče sestav na webu | Microsoft Docs
description: Toto téma popisuje postup vytvoření webu služby Microsoft Azure pomocí ovládacího prvku prohlížeče sestav Visual Studio, který se zobrazí zpráva uložené na virtuální počítač Azure Microsoft.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 78b76318-d9bf-48ef-9d9e-d1b7d8cf3042
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: af8a4a9c25005925bed3ddb78ced618e669f7f09
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31424614"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Použití ReportVieweru na webu hostovaném v Azure
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Můžete vytvořit webu služby Microsoft Azure pomocí ovládacího prvku prohlížeče sestav Visual Studio, který se zobrazí zpráva uložené na virtuální počítač Azure Microsoft. Ovládací prvek prohlížeče sestav je ve webové aplikaci vytvořit pomocí šablony aplikace technologie ASP.NET.

> [!IMPORTANT]
> Šablony webové aplikace ASP.NET MVC nepodporují ovládacího prvku prohlížeče sestav.

Zahrnout prohlížeče sestav do vašeho webu Microsoft Azure, budete muset provést následující úlohy.

* **Přidat** sestavení do balíčku nasazení
* **Konfigurace** ověřování a autorizace
* **Publikování** ASP.NET – webové aplikace do Azure

## <a name="prerequisites"></a>Požadavky
Projděte si část "obecné doporučení a osvědčené postupy" v [SQL Server Business Intelligence v Azure Virtual Machines](../classic/ps-sql-bi.md).

> [!NOTE]
> Ovládací prvky prohlížeče sestav jsou dodávané s Visual Studio, Standard Edition nebo vyšší. Pokud používáte Web Developer Express Edition, je nutné nainstalovat [MICROSOFT sestavy VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) používat funkce runtime prohlížeče sestav.
> 
> Prohlížeče sestav nakonfigurovaný v místním zpracování režimu není podporována v Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Přidání sestavení do balíčku nasazení
Pokud je hostitelem ASP.NET aplikace místní, sestavení prohlížeče sestav jsou obvykle nainstalovány přímo v globální mezipaměti sestavení (GAC) serveru služby IIS při instalaci sady Visual Studio a je přístupný přímo z aplikace. Ale pokud je hostitelem vaší aplikace ASP.NET v cloudu, Microsoft Azure nepovoluje položky, které lze nainstalovat do mezipaměti GAC, proto je nutné zajistit, aby že sestavení prohlížeče sestav jsou k dispozici místně pro vaši aplikaci. Můžete to udělat tak, že přidáte odkazy na ně ve vašem projektu a nakonfigurovat je potřeba lokálně zkopírovat.

V režimu vzdálené zpracování ovládacího prvku prohlížeče sestav používá následující sestavení:

* **Microsoft.ReportViewer.WebForms.dll**: obsahuje kód prohlížeče sestav, které budete muset použít prohlížeče sestav v stránku. Referenční dokumentace pro toto sestavení se přidá do projektu při vyřadit ovládacího prvku prohlížeče sestav na stránky ASP.NET ve vašem projektu.
* **Microsoft.ReportViewer.Common.dll**: obsahuje třídy používané nástrojem ovládacího prvku prohlížeče sestav za běhu. Automaticky není přidáno do projektu.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Chcete-li přidat odkaz na Microsoft.ReportViewer.Common
* Klikněte pravým tlačítkem na projekt **odkazy** uzel a vyberte možnost **přidat odkaz na**, vyberte na kartě .NET sestavení a klikněte na **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Chcete-li místně dostupné sestavení aplikací ASP.NET
1. V **odkazy** složku, klikněte na tlačítko Microsoft.ReportViewer.Common sestavení tak, aby jeho vlastnosti se zobrazí v podokně vlastností.
2. V podokně vlastností nastavit **místní kopie** na hodnotu True.
3. Opakujte kroky 1 a 2 pro Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Chcete-li získat jazykovou sadu prohlížeče sestav
1. Nainstalovat odpovídající redistribuovatelný balíček Microsoft sestavy Viewer 2012 Runtime z [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=317386).
2. Z rozevíracího seznamu vyberte jazyk a stránce se přesměruje na stránce odpovídající download center.
3. Klikněte na tlačítko **Stáhnout** zahájíte stahování ReportViewerLP.exe.
4. Po stažení ReportViewerLP.exe, klikněte na tlačítko **spustit** instalovat hned, nebo kliknutím na tlačítko **Uložit** ji uložit do vašeho počítače. Pokud kliknete na tlačítko **Uložit**, mějte na paměti, název složky, kam jste uložili soubor.
5. Vyhledejte složku, kam jste uložili soubor. Klikněte pravým tlačítkem na ReportViewerLP.exe, klikněte na tlačítko **spustit jako správce**a potom klikněte na **Ano**.
6. Po spuštění ReportViewerLP.exe, zobrazí se c:\windows\assembly má soubory prostředků **Microsoft.ReportViewer.Webforms.Resources** a **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Konfigurace pro lokalizované ovládacího prvku prohlížeče sestav
1. Stáhněte a nainstalujte redistribuovatelný balíček Microsoft sestavy Viewer 2012 Runtime podle pokynů uvedených výše zadaný.
2. Vytvoření <language> souborů složku v projektu a zkopírujte sestavení přidružených prostředků existuje. Kopírování souborů sestavení prostředků jsou: **Microsoft.ReportViewer.Webforms.Resources.dll** a **Microsoft.ReportViewer.Common.Resources.dll**. Vyberte soubory sestavení prostředků a v podokně vlastností nastavte **kopírovat do výstupního adresáře** na "**vždy Kopírovat**".
3. Nastavte jazykovou verzi & UICulture pro webový projekt. Další informace o tom, jak nastavit jazykovou verzi a jazyková verze uživatelského rozhraní pro webovou stránku ASP.NET najdete v tématu [postupy: nastavení jazykové verze a jazyková verze uživatelského rozhraní pro globalizaci webové stránky ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurace ověřování a autorizace
ReportViewer potřebuje používat správné přihlašovací údaje k ověřování pro server sestav a přihlašovací údaje musí být autorizovaný serverem sestav, k přístupu k sestavy, které chcete. Informace o ověřování, najdete v dokumentu white paper [sestavy služby Reporting Services ovládací prvek prohlížeče a servery sestav na základě virtuálního počítače Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publikování aplikace technologie ASP.NET do Azure
Pokyny k publikování webové aplikace technologie ASP.NET do Azure, najdete v části [postup: migrace a publikovat webovou aplikaci do Azure ze sady Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) a [Začínáme s webovými aplikacemi a ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Pokud příkaz Přidat projekt nasazení Azure nebo přidat projekt cloudové služby Azure se nezobrazí v místní nabídce v Průzkumníku řešení, budete muset změnit cílový framework projektu na .NET Framework 4.
> 
> Dva příkazy poskytují v podstatě stejné funkce. Jedna nebo jiných příkazu se zobrazí v místní nabídce závislosti na instalované verzi Microsoft Azure SDK jste nainstalovali.
> 
> 

## <a name="resources"></a>Zdroje a prostředky
[Sestavy Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence v Azure Virtual Machines](../classic/ps-sql-bi.md)

[Použití PowerShellu k vytvoření virtuálního počítače Azure se serverem sestav v nativním režimu](../classic/ps-sql-report.md)


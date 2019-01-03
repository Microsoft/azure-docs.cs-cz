---
title: Použití Reportvieweru na webu | Dokumentace Microsoftu
description: Toto téma popisuje, jak vytvořit web Microsoft Azure pomocí ovládacího prvku Visual Studio ReportViewer, která zobrazuje sestavy uloženou na virtuálním počítači Azure společnosti Microsoft.
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
ms.openlocfilehash: b554dc1fa33519d87aa0c9c5ba9130b47cbea142
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971746"
---
# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Použití ReportVieweru na webu hostovaném v Azure
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Můžete vytvořit web Microsoft Azure Web pomocí ovládacího prvku Visual Studio ReportViewer, která zobrazuje sestavy uloženou na virtuálním počítači Azure společnosti Microsoft. Ovládací prvek prohlížeče sestav je ve webové aplikaci vytvoříte pomocí šablony ASP.NET webové aplikace.

> [!IMPORTANT]
> Webová aplikace ASP.NET MVC šablony nepodporují ovládacího prvku ReportViewer.

Zahrnout ReportViewer na webu Microsoft Azure, budete muset provést následující úlohy.

* **Přidat** sestavení do balíčku pro nasazení
* **Konfigurace** ověřování a autorizace
* **Publikování** webovou aplikaci ASP.NET do Azure

## <a name="prerequisites"></a>Požadavky
Projděte si část "Obecná doporučení a osvědčené postupy" v [SQL Server Business Intelligence ve službě Azure Virtual Machines](../classic/ps-sql-bi.md).

> [!NOTE]
> Ovládací prvky prohlížeče sestav se dodávají pomocí sady Visual Studio, edice Standard nebo vyšší. Pokud používáte Web Developer Express Edition, je nutné nainstalovat [MICROSOFT sestavy VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) používat funkce modulu runtime prohlížeče sestav.
>
> ReportViewer nakonfigurované v režimu místního zpracování se nepodporuje v Microsoft Azure.

## <a name="adding-assemblies-to-the-deployment-package"></a>Přidávání sestavení do balíčku pro nasazení
Když hostujete ASP.NET aplikace místní, ReportViewer sestavení jsou obvykle nainstalovány přímo v globální mezipaměti sestavení (GAC) služby IIS serveru během instalace sady Visual Studio a je přístupný přímo aplikací. Nicméně pokud je hostitelem aplikace technologie ASP.NET v cloudu, Microsoft Azure neumožňuje nic, aby nainstalovat do mezipaměti GAC, takže je nutné zajistit, aby že sestavení prohlížeče sestav jsou k dispozici místně pro vaši aplikaci. Můžete to udělat tak, že přidáte odkazy na ně ve vašem projektu a nakonfigurujete je, aby potřeba lokálně zkopírovat.

Ovládací prvek ReportViewer v režimu vzdáleného zpracování, používá následující sestavení:

* **Microsoft.ReportViewer.WebForms.dll**: Obsahuje kód prohlížeče sestav, které jsou nutné k použití Reportvieweru na stránce. Referenční informace pro toto sestavení se přidá do vašeho projektu při umístění ovládacího prvku ReportViewer na stránky ASP.NET ve vašem projektu.
* **Microsoft.ReportViewer.Common.dll**: Obsahuje třídy, které využívá ovládacím prvkem ReportViewer za běhu. Není automaticky přidáno do projektu.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Chcete-li přidat odkaz na Microsoft.ReportViewer.Common
* Klikněte pravým tlačítkem na váš projekt **odkazy** uzel a vyberte možnost **přidat odkaz**, vyberte na kartě .NET sestavení a klikněte na tlačítko **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Chcete-li sestavení místně dostupné v aplikaci ASP.NET
1. V **odkazy** složky, klikněte na tlačítko Microsoft.ReportViewer.Common sestavení tak, aby jeho vlastnosti se zobrazí v podokně vlastností.
2. V podokně vlastností nastavte **Kopírovat místně** na hodnotu True.
3. Opakujte kroky 1 a 2 pro Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Chcete-li získat jazykovou sadu prohlížeče sestav
1. Nainstalujte příslušný redistribuovatelný balíček Microsoft sestavy Viewer 2012 Runtime z [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=317386).
2. Z rozevíracího seznamu vyberte jazyk, a na stránce přesměrován na odpovídající stránce pro stažení softwaru.
3. Klikněte na tlačítko **Stáhnout** zahájíte stahování ReportViewerLP.exe.
4. Po stažení ReportViewerLP.exe, klikněte na tlačítko **spustit** instalovat hned, nebo klikněte na **Uložit** uložte do počítače. Vyberete-li **Uložit**, mějte na paměti název složky, kam jste uložili soubor.
5. Vyhledejte složku, kam jste soubor uložili. Klikněte pravým tlačítkem na ReportViewerLP.exe, klikněte na tlačítko **spustit jako správce**a potom klikněte na tlačítko **Ano**.
6. Po spuštění ReportViewerLP.exe, uvidíte, c:\windows\assembly má soubory prostředků **Microsoft.ReportViewer.Webforms.Resources** a **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Konfigurace pro lokalizované ovládací prvek prohlížeče sestav
1. Stáhněte si a nainstalujte redistribuovatelný balíček Microsoft sestavy Viewer 2012 Runtime podle pokynů uvedených výše zadané.
2. Vytvoření \<jazyk\> soubory složky v projektu a zkopírujte sestavení přidružený prostředek existuje. Kopírování souborů sestavení prostředků jsou: **Microsoft.ReportViewer.Webforms.Resources.dll** a **Microsoft.ReportViewer.Common.Resources.dll**. Vyberte soubory prostředků sestavení a v podokně vlastností nastavte **kopírovat do výstupního adresáře** na "**vždy Kopírovat**".
3. Nastavte Culture a UICulture pro webový projekt. Další informace o tom, jak nastavit jazykovou verzi a jazykové verze uživatelského rozhraní pro webovou stránku ASP.NET najdete v tématu [jak: Nastavení jazykové verze a jazykové verze uživatelského rozhraní pro globalizaci webové stránky ASP.NET](https://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Konfigurace ověřování a autorizace
ReportViewer je potřeba použít správné přihlašovací údaje k ověření na serveru sestav a přihlašovací údaje musí být povoleno na serveru sestav pro přístup k sestavám, které chcete. Informace o ověřování najdete v dokumentu white paper [sestavy služby Reporting Services ovládací prvek prohlížeče a servery sestav založené na virtuálních počítačích Microsoft Azure](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Publikovat webovou aplikaci ASP.NET do Azure
Pokyny k publikování webové aplikace ASP.NET do Azure najdete v tématu [jak: Migrace a publikovat webovou aplikaci do Azure ze sady Visual Studio](../../../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) a [Začínáme s Web Apps a ASP.NET](../../../app-service/app-service-web-get-started-dotnet.md).

> [!IMPORTANT]
> Pokud příkaz Přidat projekt nasazení Azure nebo přidat projekt cloudové služby Azure se nezobrazí v místní nabídce v Průzkumníkovi řešení, budete muset změnit cílový rámec pro projekt rozhraní .NET Framework 4.
>
> Dva příkazy poskytují v podstatě stejné funkce. Jeden nebo jiných příkazu se zobrazí v místní nabídce závislosti na instalované verzi Microsoft Azure SDK je nainstalována.
>
>

## <a name="resources"></a>Zdroje a prostředky
[Sestavy Microsoft](https://go.microsoft.com/fwlink/?LinkId=205399)

[SQL Server Business Intelligence v Azure Virtual Machines](../classic/ps-sql-bi.md)

[Použití PowerShellu k vytvoření virtuálního počítače Azure se serverem sestav v nativním režimu](../classic/ps-sql-report.md)

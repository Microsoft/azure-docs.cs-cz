---
title: Klientské knihovny Služby Azure Analysis Services | Dokumenty společnosti Microsoft
description: Popisuje klientské knihovny potřebné pro klientské aplikace a nástroje pro připojení služby Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5914c7987d5a54a6bcc779231287309517f5121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129220"
---
# <a name="client-libraries-for-connecting-to-analysis-services"></a>Klientské knihovny pro připojení ke službě Analysis Services

Klientské knihovny jsou nezbytné pro klientské aplikace a nástroje pro připojení k serverům Analysis Services. Klientské aplikace Microsoftu, jako je Power BI Desktop, Excel, SQL Server Management Studio (SSMS) a rozšíření projektů Analysis Services pro Visual Studio, nainstalují všechny tři klientské knihovny a aktualizují je spolu s pravidelnými aktualizacemi aplikací. V některých případech může být nutné nainstalovat novější verze klientských knihoven. Vlastní klientské aplikace také vyžadují, aby byly nainstalovány klientské knihovny.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Stažení nejnovějších klientských knihoven (Instalační služba systému Windows)  

|Stáhnout  |Verze produktu  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.29.25    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.29.25       |
|[Amo](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.4.8.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.4.8.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO a ADOMD (Balíčky NuGet)

Objekty správy analysis services (AMO) a klientské knihovny ADOMD jsou k dispozici jako instalovatelné balíčky z [NuGet.org](https://www.nuget.org/). Doporučujeme migrovat na odkazy NuGet namísto použití Instalační služby systému Windows. 

|Balíček  | Verze produktu  | 
|---------|---------|
|[Amo](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.4.8.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.4.8.0      |

Sestavení balíčku NuGet AssemblyVersion postupujte podle sémantické správy verzí: MAJOR. Menší. Oprava. Odkazy NuGet načítají očekávanou verzi i v případě, že je jiná verze v GAC (vyplývající z instalace MSI). PATCH se zintáží pro každé vydání. Verze AMO a ADOMD jsou udržovány v synchronizaci.

## <a name="understanding-client-libraries"></a>Principy klientských knihoven

Analysis Services využívají tři klientské knihovny, známé také jako zprostředkovatelé dat. ADOMD.NET a Analysis Services Management Objects (AMO) jsou spravované klientské knihovny. Zprostředkovatel OLE DB služby Analysis Services (MSOLAP DLL) je nativní klientská knihovna. Obvykle jsou všechny tři nainstalovány současně. **Azure Analysis Services vyžaduje nejnovější verze všech tří knihoven**. 

Klientské aplikace Microsoftu, jako je Power BI Desktop a Excel, nainstalují všechny tři klientské knihovny a aktualizují je, když jsou k dispozici nové verze. V závislosti na verzi nebo četnosti aktualizací nemusí být některé klientské knihovny nejnovější verze vyžadované službou Azure Analysis Services. To samé platí pro vlastní aplikace a další rozhraní, jako jsou AsCmd, Tom nebo ADOMD.NET. Tyto aplikace vyžadují ruční nebo programovou instalaci knihoven. Klientské knihovny pro ruční instalaci jsou zahrnuty v balíčcích funkcí serveru SQL Server jako distribuovatelné balíčky. Tyto klientské knihovny jsou však vázány na verzi serveru SQL Server a nemusí být nejnovější.  

Klientské knihovny pro připojení klientů se liší od poskytovatelů dat, kteří jsou potřeba k připojení ze serveru Azure Analysis Services ke zdroji dat. Další informace o připojeních ke zdroji dat najdete v [tématu Připojení zdrojů dat](analysis-services-datasource.md).

## <a name="client-library-types"></a>Typy klientských knihoven

### <a name="analysis-services-ole-db-provider-msolap"></a>Zprostředkovatel ole db služby Analysis Services (MSOLAP) 

 Analysis Services OLE DB Provider (MSOLAP) je nativní klientská knihovna pro připojení databáze služby Analysis Services. Používá se nepřímo ADOMD.NET a AMO, delegování požadavky na připojení na poskytovatele dat. Zprostředkovatele TECHNOLOGIE OLE DB můžete také volat přímo z kódu aplikace.  
  
 Zprostředkovatel OLE DB služby Analysis Services je nainstalován automaticky většinou nástrojů a klientských aplikací používaných pro přístup k databázím služby Analysis Services. Musí být nainstalován v počítačích používaných pro přístup k datům služby Analysis Services.  
  
 Zprostředkovatelé technologie OLE DB jsou často určeni v připojovacích řetězcích. Připojovací řetězec služby Analysis Services používá jinou názvosloví odkazovat na zprostředkovatele OLE DB: MSOLAP. \<verze>.dll.

### <a name="amo"></a>Amo  

 AMO je spravovaná klientská knihovna používaná pro správu serveru a definici dat. Je nainstalován a používán nástroji a klientskými aplikacemi. Například SQL Server Management Studio (SSMS) používá AMO pro připojení k Analysis Services. Připojení pomocí AMO je obvykle minimální, `"data source=\<servername>"`skládající se z . Po navázání připojení pomocí rozhraní API pracovat s kolekcemi databáze a hlavní objekty. Visual Studio i SSMS používají AMO pro připojení k instanci Analysis Services.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET je klientská knihovna spravovaných dat používaná pro dotazování dat služby Analysis Services. Je nainstalován a používán nástroji a klientskými aplikacemi. 
  
 Při připojování k databázi jsou vlastnosti připojovacího řetězce pro všechny tři knihovny podobné. Téměř jakýkoli připojovací řetězec, který definujete pro ADOMD.NET pomocí [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) funguje také pro AMO a zprostředkovatele OLE DB (Analysis Services OLE DB Provider). Další informace naleznete v [tématu Vlastnosti řetězce připojení &#40;analysis services&#41;](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="how-to-determine-client-library-version"></a><a name="bkmk_LibUpdate"></a>Jak určit verzi klientské knihovny   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Přejděte do části `C:\Program Files\Microsoft Analysis Services\AS OLEDB\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku, zvolte vyšší číslo.
  
2.  Klepněte pravým tlačítkem myši na soubor **msolap.dll** > **Podrobnosti o vlastnostech** > **Details**. Pokud je název souboru msolap140.dll, je starší než nejnovější verze a měl by být inovován.
    
    ![Podrobnosti o knihovně klienta](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>Amo

1. Přejděte do části `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku, zvolte vyšší číslo.
2. Klepněte pravým tlačítkem myši na**položku** >  **Microsoft.AnalysisServices** > Properties**Details**.  

### <a name="adomd"></a>ADOMD

1. Přejděte do části `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku, zvolte vyšší číslo.
2. Klepněte pravým tlačítkem myši na **položku Microsoft.AnalysisServices.AdomdPodrobnosti** > **vlastností klienta** > **Details**.  


## <a name="next-steps"></a>Další kroky
[Připojení k Excelu](analysis-services-connect-excel.md)    
[Propojení s Power BI](analysis-services-connect-pbi.md)

---
title: Klientské knihovny vyžadované pro připojení k Azure Analysis Services | Microsoft Docs
description: Popisuje klientské knihovny vyžadované pro připojení klientských aplikací a nástrojů Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 1ea14f141ed4fa698a21da8030878b04a81f1d08
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298660"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Klientské knihovny pro připojení k Azure Analysis Services

Klientské knihovny jsou nezbytné pro připojení klientských aplikací a nástrojů k Analysis Servicesovým serverům. Klientské aplikace Microsoftu, jako jsou Power BI Desktop, Excel, SQL Server Management Studio (SSMS) a SQL Server Data Tools (SSDT), instalují všechny tři klientské knihovny a aktualizují je spolu s pravidelnými aktualizacemi aplikací. V některých případech může být nutné nainstalovat novější verze klientských knihoven. Vlastní klientské aplikace také vyžadují instalaci klientských knihoven.

## <a name="download-the-latest-client-libraries-windows-installer"></a>Stažení nejnovějších klientských knihoven (Instalační služba systému Windows)  

|Ke stažení  |Verze produktu  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.1.5.15    |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |     15.1.5.15       |
|[AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   18.0.9.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    18.0.9.0     |

## <a name="amo-and-adomd-nuget-packages"></a>AMO a ADOMD (balíčky NuGet)

ADOMDs Management Objects (AMO) a klientské knihovny jsou k dispozici jako Instalovatelné balíčky z [NuGet.org](https://www.nuget.org/). Analysis Services Místo použití Instalační služba systému Windows doporučujeme migrovat na reference NuGet. 

|Balíček  | Verze produktu  | 
|---------|---------|
|[AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    18.0.9     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   18.0.9      |

Sestavení balíčků NuGet AssemblyVersion sledují sémantickou verzi: hlavní. Moll. Použita. Odkazy na NuGet načítají očekávanou verzi i v případě, že v mezipaměti GAC je odlišná verze (výsledná z instalace MSI). Oprava se zvyšuje pro každou verzi. Verze AMO a ADOMD jsou udržovány v synchronizaci.

## <a name="understanding-client-libraries"></a>Principy klientských knihoven

Analysis Services využívat tři klientské knihovny, označované také jako poskytovatelé dat. ADOMD.NET a Analysis Services Management Objects (AMO) jsou spravované klientské knihovny. Zprostředkovatel Analysis Services OLE DB (knihovna DLL MSOLAP) je nativní Klientská knihovna. Obvykle jsou všechny tři nainstalovány ve stejnou dobu. **Azure Analysis Services vyžaduje nejnovější verze všech tří knihoven**. 

Klientské aplikace Microsoftu jako Power BI Desktop a Excel instalují všechny tři klientské knihovny a aktualizují je, když jsou k dispozici nové verze. V závislosti na verzi nebo četnosti aktualizací nemusí některé klientské knihovny být nejnovější verze, které vyžaduje Azure Analysis Services. To samé platí pro vlastní aplikace a další rozhraní, jako jsou AsCmd, Tom nebo ADOMD.NET. Tyto aplikace vyžadují ruční nebo programově instalované knihovny. Klientské knihovny pro ruční instalaci jsou součástí SQL Server balíčků funkcí jako Distribuovatelný balíčky. Tyto klientské knihovny jsou ale vázané na verzi SQL Server a nemusí být nejnovější.  

Klientské knihovny pro připojení klienta se liší od zprostředkovatelů dat požadovaných pro připojení ze serveru Azure Analysis Services ke zdroji dat. Další informace o připojeních DataSource najdete v tématu [připojení DataSource](analysis-services-datasource.md).

## <a name="client-library-types"></a>Typy klientské knihovny

### <a name="analysis-services-ole-db-provider-msolap"></a>Zprostředkovatel Analysis Services OLE DB (MSOLAP) 

 Zprostředkovatel Analysis Services OLE DB (MSOLAP) je nativní Klientská knihovna pro Analysis Services databázová připojení. Používá se nepřímo jak pro ADOMD.NET, tak pro zpracování požadavků na připojení ke zprostředkovateli dat. Můžete také volat poskytovatele OLE DB přímo z kódu aplikace.  
  
 Zprostředkovatel Analysis Services OLE DB se automaticky nainstaluje pomocí většiny nástrojů a klientských aplikací používaných pro přístup k databázím Analysis Services. Musí být nainstalovaný na počítačích, které se používají pro přístup k Analysis Services dat.  
  
 Poskytovatelé OLE DB jsou často určení v připojovacích řetězcích. Připojovací řetězec Analysis Services používá jinou klasifikaci pro odkazování na zprostředkovatele OLE DB: MSOLAP. \<version >. dll.

### <a name="amo"></a>AMO  

 AMO je spravovaná Klientská knihovna, která se používá pro správu serveru a definici dat. Je nainstalována a používána nástroji a klientskými aplikacemi. Například SQL Server Management Studio (SSMS) používá nástroj AMO pro připojení k Analysis Services. Připojení pomocí AMO má obvykle minimální hodnotu, která se skládá z `"data source=\<servername>"`. Po navázání připojení použijete rozhraní API pro práci s kolekcemi databáze a hlavními objekty. SSDT i SSMS používají pro připojení k instanci Analysis Services AMO.  

  
### <a name="adomd"></a>ADOMD

 ADOMD.NET je klientská Knihovna spravovaných dat, která se používá pro dotazování Analysis Services dat. Je nainstalována a používána nástroji a klientskými aplikacemi. 
  
 Při připojování k databázi jsou vlastnosti připojovacího řetězce pro všechny tři knihovny podobné. Skoro jakýkoli připojovací řetězec, který definujete pro ADOMD.NET pomocí [Microsoft. AnalysisServices. klienta AdomdClient. AdomdConnection. ConnectionString](/dotnet/api/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring#Microsoft_AnalysisServices_AdomdClient_AdomdConnection_ConnectionString) , funguje také pro AMO a zprostředkovatel Analysis Services OLE DB (MSOLAP). Další informace najdete v tématu [vlastnosti &#40;připojovacího řetězce&#41;Analysis Services](https://docs.microsoft.com/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a>Jak určit verzi klientské knihovny   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Přejděte do části `C:\Program Files\Microsoft Analysis Services\AS OLEDB\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku, vyberte vyšší číslo.
  
2.  Klikněte pravým tlačítkem na **MSOLAP. dll**@no__t**vlastnosti**-1  > **Podrobnosti**. Pokud název souboru je msolap140. dll, je starší než nejnovější verze a měl by být upgradován.
    
    ![Podrobnosti klientské knihovny](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Přejděte do části `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku, vyberte vyšší číslo.
2. Klikněte pravým tlačítkem na**vlastnosti** **Microsoft. AnalysisServices** > .  > **Podrobnosti**.  

### <a name="adomd"></a>ADOMD

1. Přejděte do části `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku, vyberte vyšší číslo.
2. Klikněte pravým tlačítkem na **Microsoft. AnalysisServices. klienta AdomdClient**–**vlastnosti** >   > **Podrobnosti**.  


## <a name="next-steps"></a>Další kroky
[Připojit k aplikaci Excel](analysis-services-connect-excel.md)    
[Propojení s Power BI](analysis-services-connect-pbi.md)

---
title: Klientské knihovny požadované pro připojení ke službě Azure Analysis Services | Dokumentace Microsoftu
description: Popisuje klientské knihovny požadované pro klientské aplikace a nástroje pro připojení k Azure Analysis Services
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 68f99b71930b507501aa6fb8cf0d3529de2ede5d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310368"
---
# <a name="client-libraries-for-connecting-to-azure-analysis-services"></a>Klientské knihovny pro připojení ke službě Azure Analysis Services

Klientské knihovny jsou nezbytné pro klientské aplikace a nástroje pro připojení k serverům služby Analysis Services. 

## <a name="download-the-latest-client-libraries-windows-installer"></a>Stáhněte si nejnovější klientské knihovny (Instalační program Windows)  

|Ke stažení  |Verze produktu  | 
|---------|---------|
|[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)    |    15.0.8.19     |
|[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)     |    15.0.8.19      |
|[SADA AMO](https://go.microsoft.com/fwlink/?linkid=829578)     |   15.14.1.0    |
|[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)     |    15.14.1.0     |

## <a name="amo-and-adomd-nuget-packages"></a>Sada AMO a ADOMD (balíčky NuGet)

Analysis Services Management Objects (AMO) a ADOMD klientské knihovny jsou k dispozici jako instalovat balíčky ze [NuGet.org](https://www.nuget.org/). Doporučujeme že migraci na NuGet odkazů místo pomocí Instalační služby systému Windows. 

|Balíček  | Verze produktu  | 
|---------|---------|
|[SADA AMO](https://www.nuget.org/packages/Microsoft.AnalysisServices.retail.amd64/)    |    15.14.1.0     |
|[ADOMD](https://www.nuget.org/packages/Microsoft.AnalysisServices.AdomdClient.retail.amd64/)     |   15.14.1.0      |

Sestavení balíčků NuGet AssemblyVersion podle sémantické správy verzí: HLAVNÍ. PODVERZE. OPRAVA. Odkazy na NuGet načíst očekávaná verze i v případě, že je k dispozici různé verze v mezipaměti GAC (vyplývající z instalace MSI). Oprava se zvýší, pro každou vydanou verzí. Synchronizace jsou zachovány AMO a ADOMD verze.

## <a name="understanding-client-libraries"></a>Principy klientské knihovny

Analysis Services využívají tři klientské knihovny, označované také jako poskytovatelé data. Spravované klientské knihovny jsou ADOMD.NET a Analysis Services Management Objects (AMO). Zprostředkovatele Analysis Services OLE DB (MSOLAP knihovny DLL) je nativní klientské knihovny. Všechny tři jsou obvykle nainstalovány ve stejnou dobu. **Služba Azure Analysis Services vyžaduje nejnovější verze knihoven všechny tři**. 

Klientské aplikace Microsoftu jako Power BI Desktopem a Excelem nainstalujte všechny tři klientské knihovny a aktualizovat je, když jsou k dispozici nová verze. V závislosti na verzi nebo četnosti aktualizací nemusí některé klientské knihovny být nejnovější verze, kterou vyžaduje služba Azure Analysis Services. To samé platí pro vlastní aplikace a další rozhraní, jako jsou AsCmd, Tom nebo ADOMD.NET. Tyto aplikace vyžadují, ručně nebo programově instalaci těchto knihoven. Tyto klientské knihovny pro ruční instalaci jsou součástí sad SQL Server feature Pack jako distribuovatelným balíčkům. Ale tyto klientské knihovny jsou vázané na verzi SQL serveru a nemusí být na nejnovější verzi.  

Klientské knihovny pro připojení klientů se liší od zprostředkovatele dat nejde připojit ke zdroji dat ze serveru Azure Analysis Services. Další informace o připojení zdrojů dat, naleznete v tématu [připojení zdrojů dat](analysis-services-datasource.md).

## <a name="client-library-types"></a>Klientské knihovny typů

### <a name="analysis-services-ole-db-provider-msolap"></a>Zprostředkovatele Analysis Services OLE DB (MSOLAP) 

 Analysis Services OLE DB poskytovatele (MSOLAP) je nativní klientské knihovny pro připojení k databázi služby Analysis Services. Používá se nepřímo ADOMD.NET a AMO, delegování žádosti o připojení k poskytovateli data. Zprostředkovatel OLE DB můžete také volat přímo z kódu aplikace.  
  
 Zprostředkovatele Analysis Services OLE DB se nainstaluje automaticky tak, že většina nástrojů a klientské aplikace používají pro přístup k databází služby Analysis Services. Musí být nainstalován v počítačích se používá pro přístup k datům Analysis Services.  
  
 OLE DB – zprostředkovatelé jsou často určené v připojovacích řetězcích. Připojovací řetězec služby Analysis Services používá k odkazování na zprostředkovatele OLE DB různé klasifikace: MSOLAP. \<verze > .dll.

### <a name="amo"></a>AMO  

 Sada AMO je knihovna spravovaných klientů používá pro zajištění správy serverů a definice dat. Má nainstalovat a používat nástroje a klientské aplikace. Například SQL Server Management Studio (SSMS) používá AMO pro připojení ke službě Analysis Services. Připojení pomocí sady AMO je obvykle minimální, který se skládá z `"data source=\<servername>"`. Po vytvoření připojení, je použít rozhraní API pro práci s kolekcí databází a hlavní objekty. Rozšíření SSDT a aplikace SSMS připojit k instanci služby Analysis Services pomocí sady AMO.  

  
### <a name="adomd"></a>ADOMD

 Rozhraní ADOMD.NET je klientská knihovna spravovaných dat používá pro dotazování na data služby Analysis Services. Má nainstalovat a používat nástroje a klientské aplikace. 
  
 Při připojování k databázi, jsou podobné vlastnosti připojovacího řetězce pro všechny tři knihovny. Téměř všechny připojovací řetězec pro rozhraní ADOMD.NET definujete pomocí [Microsoft.AnalysisServices.AdomdClient.AdomdConnection.ConnectionString](https://msdn.microsoft.com/library/microsoft.analysisservices.adomdclient.adomdconnection.connectionstring.aspx) funguje i pro AMO a Analysis Services OLE DB poskytovatele (MSOLAP). Další informace najdete v tématu [vlastnosti připojovacího řetězce &#40;služby Analysis Services&#41;](https://docs.microsoft.com/sql/analysis-services/instances/connection-string-properties-analysis-services).  

  
##  <a name="bkmk_LibUpdate"></a> Jak určit verzi knihovny klienta   
  
### <a name="oleddb-msolap"></a>OLEDDB (MSOLAP)  
  
1.  Přejděte do části `C:\Program Files\Microsoft Analysis Services\AS OLEDB\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku a zvolte vyšší číslo.
  
2.  Klikněte pravým tlačítkem na **msolap.dll** > **vlastnosti** > **podrobnosti**. Pokud je název souboru msolap140.dll, je starší než nejnovější verzi a by měl být upgradovány.
    
    ![Podrobnosti ke knihovně klienta](media/analysis-services-data-providers/aas-msolap-details.png)
    
  
### <a name="amo"></a>AMO

1. Přejděte do části `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku a zvolte vyšší číslo.
2. Klikněte pravým tlačítkem na **Microsoft.AnalysisServices** > **vlastnosti** > **podrobnosti**.  

### <a name="adomd"></a>ADOMD

1. Přejděte do části `C:\Windows\Microsoft.NET\assembly\GAC_MSIL\Microsoft.AnalysisServices.AdomdClient\` (Soubor > Nový > Jiné). Pokud máte více než jednu složku a zvolte vyšší číslo.
2. Klikněte pravým tlačítkem na **Microsoft.AnalysisServices.AdomdClient** > **vlastnosti** > **podrobnosti**.  


## <a name="next-steps"></a>Další postup
[Propojení s Excelem](analysis-services-connect-excel.md)    
[Propojení s Power BI](analysis-services-connect-pbi.md)

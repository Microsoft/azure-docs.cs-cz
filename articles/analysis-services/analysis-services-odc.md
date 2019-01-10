---
title: Vytvoření souboru .odc pro připojení k serveru Azure Analysis Services | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit soubor Office datové připojení a připojte se k získání dat ze serveru služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 37f068be544f964f3aec63d85702098c8f382ab8
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187923"
---
# <a name="create-an-office-data-connection-file"></a>Vytvoření souboru Office Data Connection

Informace v tomto článku popisuje, jak můžete vytvořit soubor Office datové připojení pro připojení k serveru Azure Analysis Services z Excelu 2016 číslo verze 16.0.7369.2117 nebo starší, nebo Excel 2013. Aktualizované [MSOLAP.7 poskytovatele](analysis-services-data-providers.md) je také nutný.


1. Zkopírování ukázkového souboru připojení níže a vložte do textového editoru. 

2. V `odc:ConnectionString`, změňte následující vlastnosti:

    *   V `Data Source=asazure://<region>.asazure.windows.net/<servername>;` změnit `<region>` do oblasti vašeho serveru Analysis Services a `<servername>` na název vašeho serveru.

    *   V `Initial Catalog=<database>;` změnit `<database>` na název vaší databáze.

3. V `<odc:CommandText>Model</odc:CommandText>` změnit `Model` název model nebo perspektivu. 

4. Uložte soubor s `.odc` rozšíření C:\Users\\*uživatelské jméno*\Documents\My zdroje dat složky.

5. Klikněte pravým tlačítkem na soubor a potom klikněte na tlačítko **otevřít v aplikaci Excel**. Nebo v aplikaci Excel na **Data** pásu karet, klikněte na tlačítko **existující připojení**, vyberte soubor a potom klikněte na tlačítko **otevřít**.



**Ukázkový soubor připojení**
```
<html xmlns:o="urn:schemas-microsoft-com:office:office"
xmlns="https://www.w3.org/TR/REC-html40">

<head>
<meta http-equiv=Content-Type content="text/x-ms-odc; charset=utf-8">
<meta name=ProgId content=ODC.Cube>
<meta name=SourceType content=OLEDB>
<meta name=Catalog content="Database">
<meta name=Table content=Model>
<title>AzureAnalysisServicesConnection</title>
<xml id=docprops><o:DocumentProperties
  xmlns:o="urn:schemas-microsoft-com:office:office"
  xmlns="https://www.w3.org/TR/REC-html40">
  <o:Name>SampleAzureAnalysisServices</o:Name>
 </o:DocumentProperties>
</xml><xml id=msodc><odc:OfficeDataConnection
  xmlns:odc="urn:schemas-microsoft-com:office:odc"
  xmlns="https://www.w3.org/TR/REC-html40">
  <odc:Connection odc:Type="OLEDB">
   <odc:ConnectionString>Provider=MSOLAP.7;Data Source=asazure://<region>.asazure.windows.net/<servername>;Initial Catalog=<database>;</odc:ConnectionString>
   <odc:CommandType>Cube</odc:CommandType>
   <odc:CommandText>Model</odc:CommandText>
  </odc:Connection>
 </odc:OfficeDataConnection>
</xml>
<style>
<!--
    .ODCDataSource
    {
    behavior: url(dataconn.htc);
    }
-->
</style>
 
</head>

<body onload='init()' scroll=no leftmargin=0 topmargin=0 rightmargin=0 style='border: 0px'>
<table style='border: solid 1px threedface; height: 100%; width: 100%' cellpadding=0 cellspacing=0 width='100%'> 
  <tr> 
    <td id=tdName style='font-family:arial; font-size:medium; padding: 3px; background-color: threedface'> 
      &nbsp; 
    </td> 
     <td id=tdTableDropdown style='padding: 3px; background-color: threedface; vertical-align: top; padding-bottom: 3px'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td id=tdDesc colspan='2' style='border-bottom: 1px threedshadow solid; font-family: Arial; font-size: 1pt; padding: 2px; background-color: threedface'>

      &nbsp; 
    </td> 
  </tr> 
  <tr> 
    <td colspan='2' style='height: 100%; padding-bottom: 4px; border-top: 1px threedhighlight solid;'> 
      <div id='pt' style='height: 100%' class='ODCDataSource'></div> 
    </td> 
  </tr> 
</table> 

  
<script language='javascript'> 

function init() { 
  var sName, sDescription; 
  var i, j; 
  
  try { 
    sName = unescape(location.href) 
  
    i = sName.lastIndexOf(".") 
    if (i>=0) { sName = sName.substring(1, i); } 
  
    i = sName.lastIndexOf("/") 
    if (i>=0) { sName = sName.substring(i+1, sName.length); } 

    document.title = sName; 
    document.getElementById("tdName").innerText = sName; 

    sDescription = document.getElementById("docprops").innerHTML; 
  
    i = sDescription.indexOf("escription>") 
    if (i>=0) { j = sDescription.indexOf("escription>", i + 11); } 

    if (i>=0 && j >= 0) { 
      j = sDescription.lastIndexOf("</", j); 

      if (j>=0) { 
          sDescription = sDescription.substring(i+11, j); 
        if (sDescription != "") { 
            document.getElementById("tdDesc").style.fontSize="x-small"; 
          document.getElementById("tdDesc").innerHTML = sDescription; 
          } 
        } 
      } 
    } 
  catch(e) { 

    } 
  } 
</script> 

</body> 
 
</html>

```




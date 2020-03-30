---
title: Názvy aliasů služeb Azure Analysis Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit aliasy názvů serverů Služby Azure Analysis Services. Uživatelé se pak mohou připojit k serveru s kratším názvem aliasu namísto názvu serveru.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5e7017fad90e32cb8c4b952987fe248e463e4d03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572290"
---
# <a name="alias-server-names"></a>Alias názvy serverů

Pomocí aliasu názvu serveru se uživatelé mohou připojit k serveru Služby Azure Analysis Services s kratším *aliasem* namísto názvu serveru. Při připojování z klientské aplikace je alias určen jako koncový bod pomocí formátu **protokolu link://.** Koncový bod pak vrátí skutečný název serveru, aby se mohl připojit.

Alias názvy serverů jsou dobré pro:

- Migrace modelů mezi servery bez ovlivnění uživatelů. 
- Popisné názvy serverů jsou pro uživatele snadněji zapamatovatelné. 
- Přímé uživatele na různé servery v různých denních dobách. 
- Přímé uživatele v různých oblastech na instance, které jsou geograficky blíže, jako při použití Azure Traffic Manager. 

Libovolný koncový bod HTTPS, který vrátí platný název serveru Služby Azure Analysis Services, může sloužit jako alias. Koncový bod musí podporovat protokol HTTPS přes port 443 a port nesmí být zadán v identifikátoru URI.

![Alias pomocí formátu odkazu](media/analysis-services-alias/aas-alias-browser.png)

Při připojování z klienta je název aliasového serveru zadán ve formátu **protokolu link://.** Například v Power BI Desktopu:

![Připojení power BI desktopu](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Vytvoření aliasu

Chcete-li vytvořit koncový bod aliasu, můžete použít libovolnou metodu, která vrátí platný název serveru Služby Azure Analysis Services. Například odkaz na soubor v úložišti objektů Blob Azure obsahující skutečný název serveru nebo vytvořit a publikovat aplikaci ASP.NET webových formulářů.

V tomto příkladu je v sadě Visual Studio vytvořena aplikace ASP.NET webových formulářů. Odkaz na stránku předlohy a uživatelský ovládací prvek jsou odebrány ze stránky Default.aspx. Obsah Default.aspx jsou jednoduše následující Page směrnice:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Událost Page_Load v Default.aspx.cs používá metodu Response.Write() k vrácení názvu serveru Služby Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Viz také

[Klientské knihovny](analysis-services-data-providers.md)   
[Připojení z Power BI Desktopu](analysis-services-connect-pbi.md)

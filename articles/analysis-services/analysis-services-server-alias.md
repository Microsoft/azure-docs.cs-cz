---
title: Azure názvy serveru aliasů služby Analysis Services | Dokumentace Microsoftu
description: Popisuje postup vytvoření a použití serveru aliasů názvu.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc649379ffc375282b853687573266ded2bf80eb
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49093761"
---
# <a name="alias-server-names"></a>Názvy serveru aliasů

S použitím aliasu názvu souboru, uživatelé se mohou připojit k serveru Azure Analysis Services pomocí v kratší *alias* místo názvu serveru. Když se připojujete z klientské aplikace, aliasem, který je zadán jako koncový bod pomocí **odkaz: / /** formát protokolu. Koncový bod vrátí název skutečný server aby bylo možné připojit.

Názvy serveru aliasů jsou vhodné pro:

- Migrace modely mezi servery, aniž by to ovlivnilo uživatele. 
- Názvy serverů popisný jsou uživatelé tak mějte na paměti. 
- Uživatelé na různé servery v různých časech dne. 
- Uživatelé v různých oblastech k instancím, které jsou geograficky blíž, stejně jako při použití Azure Traffic Manageru. 

Libovolný koncový bod HTTPS, který vrátí platný název serveru Azure Analysis Services může sloužit jako alias. Koncový bod musí podporovat protokol HTTPS přes port 443 a port, který nesmí být zadaný v identifikátoru URI.

![Alias odkazu formátu](media/analysis-services-alias/aas-alias-browser.png)

Když se připojujete z klienta, je zadán název serveru alias pomocí **odkaz: / /** formát protokolu. Například v Power BI Desktopu:

![Připojení Power BI Desktopu](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Vytvořit alias

Chcete-li vytvořit alias koncový bod, můžete použít libovolnou metodu, která vrací platný název serveru Azure Analysis Services. Například odkaz na soubor v Azure Blob Storage, který obsahuje skutečný server name, nebo vytvořte a publikujte aplikaci webových formulářů ASP.NET.

V tomto příkladu se vytvoří aplikace webových formulářů ASP.NET v sadě Visual Studio. Hlavní stránka odkazu a uživatelský ovládací prvek se odeberou z stránku Default.aspx. Obsah Default.aspx jsou jednoduše následující direktiva stránky:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

V události Page_Load v Default.aspx.cs pomocí metody Response.Write() metody vrátí název serveru Azure Analysis Services.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Další informace najdete v tématech

[Klientské knihovny](analysis-services-data-providers.md)   
[Připojení z Power BI Desktopu](analysis-services-connect-pbi.md)

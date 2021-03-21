---
title: Názvy serverů s aliasem Azure Analysis Services | Microsoft Docs
description: Naučte se vytvářet Azure Analysis Services aliasy názvů serverů. Uživatelé se pak můžou k serveru připojit pomocí kratšího názvu aliasu místo názvu serveru.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7fb52a0f03fa1f9ab9bc0c6a2c27adf70b4bf2ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96491226"
---
# <a name="alias-server-names"></a>Názvy serverů s aliasem

Pomocí aliasu názvu serveru se uživatelé můžou k serveru Azure Analysis Services připojit pomocí kratšího *aliasu* místo názvu serveru. Při připojování z klientské aplikace je alias zadán jako koncový bod pomocí formátu protokolu **Link://** . Koncový bod pak vrátí skutečný název serveru, aby se mohl připojit.

Názvy serverů aliasů jsou vhodné pro:

- Migrace modelů mezi servery bez vlivu na uživatele. 
- Popisné názvy serverů usnadňují uživatelům zapamatování. 
- Přímé nasměrování uživatelů na různé servery v různou denní dobu. 
- Přímé směrování uživatelů v různých oblastech do geograficky podrobnějších instancí, jako při použití Azure Traffic Manager. 

Libovolný koncový bod HTTPS, který vrátí platný název Azure Analysis Services serveru, může sloužit jako alias. Koncový bod musí podporovat HTTPS přes port 443 a port nesmí být zadaný v identifikátoru URI.

![Alias pomocí formátu odkazu](media/analysis-services-alias/aas-alias-browser.png)

Při připojování z klienta se název serveru aliasu zadává pomocí formátu protokolu **Link://** . Například v Power BI Desktop:

![Power BI Desktop připojení](media/analysis-services-alias/aas-alias-connect-pbid.png)

## <a name="create-an-alias"></a>Vytvoření aliasu

Pokud chcete vytvořit koncový bod aliasu, můžete použít libovolnou metodu, která vrátí platný název Azure Analysis Services serveru. Například odkaz na soubor v Azure Blob Storage, který obsahuje skutečný název serveru, nebo vytvořit a publikovat webovou aplikaci ASP.NET Web Forms.

V tomto příkladu je aplikace webových formulářů ASP.NET vytvořena v aplikaci Visual Studio. Odkaz na stránku a uživatelský ovládací prvek jsou odebrány ze stránky default. aspx. Obsah default. aspx je jednoduše následující direktivou stránky:

```
<%@ Page Title="Home Page" Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="FriendlyRedirect._Default" %>
```

Událost Page_Load ve službě default. aspx. cs používá metodu Response. Write () k vrácení Azure Analysis Services názvu serveru.

```
protected void Page_Load(object sender, EventArgs e)
{
    this.Response.Write("asazure://<region>.asazure.windows.net/<servername>");
}
```

## <a name="see-also"></a>Viz také

[Klientské knihovny](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true)   
[Připojení z Power BI Desktopu](analysis-services-connect-pbi.md)
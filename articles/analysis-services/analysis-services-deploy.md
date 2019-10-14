---
title: Nasazení do Azure Analysis Services pomocí sady Visual Studio (SSDT) | Microsoft Docs
description: Naučte se, jak nasadit tabulkový model na Azure Analysis Services Server pomocí SSDT.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 759d85db26ac7370c0b884d2e4839d3045384673
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301131"
---
# <a name="deploy-a-model-from-visual-studio"></a>Nasazení modelu ze sady Visual Studio

Po vytvoření serveru v předplatném Azure jste připraveni nasadit do něj databázi tabelárních modelů. K vytvoření a nasazení projektu s tabelárním modelem, na kterém pracujete, můžete použít nástroje SQL Server Data Tools (SSDT). 

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

* **Analysis Services Server** v Azure. Další informace najdete v tématu [vytvoření serveru Azure Analysis Services](analysis-services-create-server.md).
* **Projekt tabelárního modelu** v SSDT nebo existující tabulkový model na úrovni kompatibility 1200 nebo vyšší. Nikdy jste ho nevytvořili? Vyzkoušejte [kurz tabelárního modelování Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* Místní **Brána** – Pokud je jeden nebo více zdrojů dat v síti vaší organizace v místním prostředí, je nutné nainstalovat [místní bránu dat](analysis-services-gateway.md). Brána je nutná pro váš server v cloudu, který se připojuje k místním zdrojům dat a zpracovává a aktualizuje data v modelu.

> [!TIP]
> Před nasazením se ujistěte, že můžete zpracovat data v tabulkách. V SSDT klikněte na **Model** > **proces** > **zpracovat vše**. Pokud se zpracování nezdaří, nebudete moct úspěšně nasadit.
> 
> 

## <a name="get-the-server-name"></a>Získání názvu serveru

V **Azure Portal** > serveru > **Přehled** > **název serveru**zkopírujte název serveru.
   
![Získat název serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Nasazení z SSDT

1. V SSDT > **Průzkumník řešení**klikněte pravým tlačítkem myši na **vlastnosti**> projektu. Potom v části **nasazení** > **Server** vložte název serveru.   
   
    ![Vložení názvu serveru do vlastnosti serveru nasazení](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. V **Průzkumník řešení**klikněte pravým tlačítkem na **vlastnosti**a pak klikněte na **nasadit**. Může se zobrazit výzva k přihlášení do Azure.
   
    ![Nasadit na server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Stav nasazení se zobrazí v okně výstup i v nasazení.
   
    ![Stav nasazení](./media/analysis-services-deploy/aas-deploy-status.png)

A je to!


## <a name="troubleshooting"></a>Poradce při potížích

Pokud nasazení selhalo při nasazování metadat, je pravděpodobně proto, že SSDT se nemůže připojit k vašemu serveru. Ujistěte se, že se můžete připojit k serveru pomocí SSMS. Pak se ujistěte, že je vlastnost serveru pro nasazení pro projekt správná.

Pokud se nasazení v tabulce nepovede, je pravděpodobně proto, že se server nemohl připojit ke zdroji dat. Pokud je váš zdroj dat místní v síti vaší organizace, nezapomeňte nainstalovat [místní bránu dat](analysis-services-gateway.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte tabulkový model nasazený na váš server, jste připraveni se k němu připojit. Můžete se [k němu připojit pomocí SSMS](analysis-services-manage.md) a spravovat ho. A můžete [se k němu připojit pomocí klientského nástroje](analysis-services-connect.md) , jako je Power BI, Power BI Desktop nebo Excel, a začít vytvářet sestavy.


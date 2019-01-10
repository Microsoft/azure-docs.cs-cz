---
title: Nasazení do služby Azure Analysis Services pomocí Visual Studio (SSDT) | Dokumentace Microsoftu
description: Zjistěte, jak můžete nasadit tabulkový model na server služby Azure Analysis Services pomocí sady SSDT.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a9f3dfba4c79b2369e99b95c13557557ed930c24
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54188585"
---
# <a name="deploy-a-model-from-visual-studio"></a>Nasazení modelu ze sady Visual Studio

Jakmile ve svém předplatném Azure vytvoříte server, můžete na něj nasadit databázi tabulkového modelu. K vytvoření a nasazení projektu tabulkového modelu, na kterém pracujete, můžete použít sadu SSDT (SQL Server Data Tools). 

## <a name="prerequisites"></a>Požadavky

Na začátek budete potřebovat:

* **Server služby Analysis Services** v Azure Další informace najdete v tématu [Vytvoření serveru služby Azure Analysis Services](analysis-services-create-server.md).
* **Projekt tabulkového modelu** v sadě SSDT nebo existující tabulkový model na úrovni kompatibility 1200 nebo vyšší. Nikdy jste ho nevytvářeli? Vyzkoušejte [Kurz tabulkového modelování Adventure Works Internet Sales](https://msdn.microsoft.com/library/hh231691.aspx).
* **Místní brána** – Pokud máte jeden nebo více místních zdrojů dat v síti organizace, budete si muset nainstalovat [místní bránu dat](analysis-services-gateway.md). Brána je nezbytná pro server v cloudu, aby se mohl připojit k místním zdrojům dat a mohl tak zpracovat a aktualizovat data v modelu.

> [!TIP]
> Než začnete provádět nasazení, ujistěte se, že zvládnete zpracovat data v tabulkách. V SSDT klikněte na **Model** > **Zpracovat** > **Zpracovat vše**. Pokud se zpracování nepodaří, nebudete moct provést úspěšné nasazení.
> 
> 

## <a name="get-the-server-name"></a>Získání názvu serveru

Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-ssdt"></a>Nasazení ze sady SSDT

1. V sadě SSDT > **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt > **Vlastnosti**. Potom v **Nasazení** > **Server** vložte název serveru.   
   
    ![Vložení názvu serveru do vlastnosti nasazení serveru](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na **Vlastnosti** a pak klikněte na **Nasadit**. Může se zobrazit výzva k přihlášení do Azure.
   
    ![Nasazení na server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Stav nasazení se zobrazí v okně výstupu a nasazení.
   
    ![Stav nasazení](./media/analysis-services-deploy/aas-deploy-status.png)

A je to!


## <a name="troubleshooting"></a>Řešení potíží

Pokud nasazení selže při nasazování metadat, bude to pravděpodobně tím, že se sada SSDT nemohla připojit k serveru. Ujistěte se, že se k serveru můžete připojit pomocí aplikace SSMS. Potom zkontrolujte, jestli je vlastnost Server nasazení projektu správná.

Pokud nasazení selže u tabulky, bude to pravděpodobně tím, že se server nemohl připojit ke zdroji dat. Pokud je váš zdroj dat místní v síti organizace, nezapomeňte nainstalovat [místní bránu dat](analysis-services-gateway.md).

## <a name="next-steps"></a>Další postup

Teď když jste tabulkový model nasadili na váš server, můžete se k němu připojit. Můžete [se k němu připojit pomocí SSMS](analysis-services-manage.md) a spravovat ho. Můžete [se k němu připojit také pomocí klientského nástroje](analysis-services-connect.md), jako je například Power BI, Power BI Desktop nebo Excel, a začít vytvářet sestavy.


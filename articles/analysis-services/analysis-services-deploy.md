---
title: Nasazení modelu pro Azure Analysis Services pomocí sady Visual Studio | Microsoft Docs
description: Naučte se, jak nasadit tabulkový model na Azure Analysis Services Server pomocí sady Visual Studio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 45ea75b65e75f9bac4d6d4839ffe7fa095e3b25d
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018772"
---
# <a name="deploy-a-model-from-visual-studio"></a>Nasazení modelu ze sady Visual Studio

Jakmile ve svém předplatném Azure vytvoříte server, můžete na něj nasadit databázi tabulkového modelu. Můžete použít Visual Studio s projekty Analysis Services k sestavení a nasazení projektu tabelárního modelu, na kterém pracujete. 

## <a name="prerequisites"></a>Požadavky

Na začátek budete potřebovat:

* **Server služby Analysis Services** v Azure Další informace najdete v tématu [Vytvoření serveru služby Azure Analysis Services](analysis-services-create-server.md).
* **Projekt tabelárního modelu** v aplikaci Visual Studio nebo existující tabulkový model na úrovni kompatibility 1200 nebo vyšší. Nikdy jste ho nevytvářeli? Vyzkoušejte [Kurz tabulkového modelování Adventure Works Internet Sales](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Místní brána** – Pokud máte jeden nebo více místních zdrojů dat v síti organizace, budete si muset nainstalovat [místní bránu dat](analysis-services-gateway.md). Brána je nezbytná pro server v cloudu, aby se mohl připojit k místním zdrojům dat a mohl tak zpracovat a aktualizovat data v modelu.

> [!TIP]
> Než začnete provádět nasazení, ujistěte se, že zvládnete zpracovat data v tabulkách. V aplikaci Visual Studio klikněte na **model**  >  **zpracovat**proces  >  **vše**. Pokud se zpracování nepodaří, nebudete moct provést úspěšné nasazení.
> 
> 

## <a name="get-the-server-name"></a>Získání názvu serveru

Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Nasazení ze sady Visual Studio

1. V aplikaci Visual Studio > **Průzkumník řešení**klikněte pravým tlačítkem myši na **vlastnosti**projektu >. Pak v **nasazení**  >  **serveru** vložte název serveru.   
   
    ![Vložení názvu serveru do vlastnosti nasazení serveru](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na **Vlastnosti** a pak klikněte na **Nasadit**. Může se zobrazit výzva k přihlášení do Azure.
   
    ![Nasazení na server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Stav nasazení se zobrazí v okně výstupu a nasazení.
   
    ![Stav nasazení](./media/analysis-services-deploy/aas-deploy-status.png)

A je to!


## <a name="troubleshooting"></a>Řešení potíží

Pokud nasazení selhalo při nasazování metadat, je pravděpodobně proto, že se Visual Studio nemůže připojit k vašemu serveru. Ujistěte se, že se můžete připojit k serveru pomocí SQL Server Management Studio (SSMS). Potom zkontrolujte, jestli je vlastnost Server nasazení projektu správná.

Pokud nasazení selže u tabulky, bude to pravděpodobně tím, že se server nemohl připojit ke zdroji dat. Pokud je váš zdroj dat místní v síti organizace, nezapomeňte nainstalovat [místní bránu dat](analysis-services-gateway.md).

## <a name="next-steps"></a>Další kroky

Teď když jste tabulkový model nasadili na váš server, můžete se k němu připojit. Můžete se [k němu připojit pomocí SQL Server Management Studio (SSMS)](analysis-services-manage.md) , abyste ho mohli spravovat. Můžete [se k němu připojit také pomocí klientského nástroje](analysis-services-connect.md), jako je například Power BI, Power BI Desktop nebo Excel, a začít vytvářet sestavy.   

Další informace o pokročilých metodách nasazení najdete v tématu věnovaném [nasazení řešení pro tabelární model](/analysis-services/deployment/tabular-model-solution-deployment?view=azure-analysis-services-current).
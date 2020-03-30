---
title: Nasazení modelu do služby Azure Analysis Services pomocí Sady Visual Studio | Dokumenty společnosti Microsoft
description: Zjistěte, jak nasadit tabulkový model na server Služby Azure Analysis Services pomocí Visual Studia.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 71b3b7815d2a4b0b4de3afdca9db93156f505445
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572884"
---
# <a name="deploy-a-model-from-visual-studio"></a>Nasazení modelu ze sady Visual Studio

Jakmile ve svém předplatném Azure vytvoříte server, můžete na něj nasadit databázi tabulkového modelu. Pomocí sady Visual Studio s projekty Analysis Services můžete vytvořit a nasadit projekt tabulkového modelu, na kterém pracujete. 

## <a name="prerequisites"></a>Požadavky

Na začátek budete potřebovat:

* **Server služby Analysis Services** v Azure Další informace najdete v tématu [Vytvoření serveru služby Azure Analysis Services](analysis-services-create-server.md).
* **Tabulkový model projektu** v sadě Visual Studio nebo existující tabulkový model na úrovni kompatibility 1200 nebo vyšší. Nikdy jste ho nevytvářeli? Vyzkoušejte [Kurz tabulkového modelování Adventure Works Internet Sales](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial).
* **Místní brána** – Pokud máte jeden nebo více místních zdrojů dat v síti organizace, budete si muset nainstalovat [místní bránu dat](analysis-services-gateway.md). Brána je nezbytná pro server v cloudu, aby se mohl připojit k místním zdrojům dat a mohl tak zpracovat a aktualizovat data v modelu.

> [!TIP]
> Než začnete provádět nasazení, ujistěte se, že zvládnete zpracovat data v tabulkách. V sadě Visual Studio klikněte na **položku Proces** > **procesu** > **modelu vše**. Pokud se zpracování nepodaří, nebudete moct provést úspěšné nasazení.
> 
> 

## <a name="get-the-server-name"></a>Získání názvu serveru

Na portálu **Azure Portal** > Server > **Přehled** > **Název serveru** zkopírujte název serveru.
   
![Získání názvu serveru v Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="to-deploy-from-visual-studio"></a>Nasazení z Visual Studia

1. V **Průzkumníku řešení**visual studio > klepněte pravým tlačítkem myši na > **vlastnosti**projektu . Potom v**serveru** **pro nasazení** > vložte název serveru.   
   
    ![Vložení názvu serveru do vlastnosti nasazení serveru](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
2. V **Průzkumníku řešení** klikněte pravým tlačítkem na **Vlastnosti** a pak klikněte na **Nasadit**. Může se zobrazit výzva k přihlášení do Azure.
   
    ![Nasazení na server](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    Stav nasazení se zobrazí v okně výstupu a nasazení.
   
    ![Stav nasazení](./media/analysis-services-deploy/aas-deploy-status.png)

A je to!


## <a name="troubleshooting"></a>Řešení potíží

Pokud se nasazení nezdaří při nasazování metadat, je to pravděpodobně proto, že se Visual Studio nemůže připojit k vašemu serveru. Ujistěte se, že se k serveru můžete připojit pomocí aplikace SSMS. Potom zkontrolujte, jestli je vlastnost Server nasazení projektu správná.

Pokud nasazení selže u tabulky, bude to pravděpodobně tím, že se server nemohl připojit ke zdroji dat. Pokud je váš zdroj dat místní v síti organizace, nezapomeňte nainstalovat [místní bránu dat](analysis-services-gateway.md).

## <a name="next-steps"></a>Další kroky

Teď když jste tabulkový model nasadili na váš server, můžete se k němu připojit. Můžete [se k němu připojit pomocí SQL Server Management Studio (SSMS)](analysis-services-manage.md) pro jeho správu. Můžete [se k němu připojit také pomocí klientského nástroje](analysis-services-connect.md), jako je například Power BI, Power BI Desktop nebo Excel, a začít vytvářet sestavy.


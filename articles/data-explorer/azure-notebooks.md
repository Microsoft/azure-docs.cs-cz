---
title: Analýza dat v Azure Data Exploreru pomocí poznámkových bloků Azure
description: Toto téma ukazuje, jak vytvořit dotaz pomocí poznámkového bloku Azure
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522404"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Analýza dat v Azure Data Exploreru pomocí poznámkových bloků Azure

[Poznámkové bloky Azure](https://notebooks.azure.com/) je cloudová služba Azure, která zjednodušuje vytváření a sdílení [poznámkových bloků Jupyter](https://jupyter.org/). Poznámkové bloky Azure usnadňuje kombinovat dokumentaci, kód a výsledky spuštění kódu.

> [!Note]
> * Následující postup používá klienta Pythonu v prostředí Poznámkových bloků Azure k dotazování Průzkumníka dat Azure. [KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) však můžete také použít k dotazování aplikace Azure Data Explorer.
> * Někteří uživatelé hlásili problémy s ověřováním pomocí edge; dokud tyto problémy nebudou vyřešeny, použijte jiný prohlížeč.

## <a name="create-a-project"></a>Vytvoření projektu

1. Otevřete [poznámkové bloky Azure](https://notebooks.azure.com/) ve svém prohlížeči a přihlaste se.

1. V záhlaví vyberte kartu **Moje projekty.** 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Vyberte **+ Nové projekty**.
    
1. V dialogovém okně **Vytvořit nový projekt:**
    1. Zadejte **název projektu**.
    1. Zrušte zaškrtnutí políčka **Veřejné.**
        >[!Important]
        > Pokud nezaškrtnete políčko Veřejné, bude projekt vystaven na otevřeném Internetu.
    1. Vyberte **Vytvořit**.
    
    ![Vytvoření nového projektu](media/azurenotebooks/an-create-new-project-blank.png)

    ID projektu se vytvoří automaticky.

## <a name="create-a-notebook"></a>Vytvoření poznámkového bloku

1. V novém projektu vytvořte poznámkový blok. Poznámkový blok by měl používat [podporovaný jazyk](https://github.com/Azure/azure-kusto-python#minimum-requirements).
Chcete-li vytvořit poznámkový blok, vyberte **+ Nový** a vyberte **Poznámkový blok**.

    ![Vytvoření nového poznámkového bloku](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. V dialogovém okně **Vytvořit nový poznámkový blok** zadejte název poznámkového bloku.

1. Vyberte **Python 3.6** a vyberte **Nový**.
    
    ![Vytvoření nového poznámkového bloku](media/azurenotebooks/an-create-new-notebook.png) 
    
1. V projektu vyberte nový poznámkový blok.

    ![Výběr nového poznámkového bloku](media/azurenotebooks/an-select-notebook.png)

    Počkejte, až se jádro Pythonu inicializuje. Když se ikona vyplněného kruhu změní na ikonu prázdného kruhu, můžete pokračovat.

    ![Inicializuje jádro](media/azurenotebooks/an-python-init-icon.png)

1. Chcete-li systémový modul importovat, zadejte následující příkazy a vyberte **Spustit**:
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Chcete-li spustit buňku, můžete také stisknout kombinaci kláves Shift+Enter.

1.  Chcete-li importovat třídy sady SDK, zadejte následující příkazy a vyberte **Spustit**:
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Chcete-li vytvořit připojovací řetězec a spustit klienta Kusto, zadejte následující příkazy a vyberte **spustit**:  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. Na výzvu otevřete novou [https://aka.ms/devicelogin](https://aka.ms/devicelogin)kartu prohlížeče . 
   
1. Zadejte autorizační kód a přihlaste@microsoft.comse ke svému účtu AAD ( ). Klient `kc` Kusto teď může ověřit do Průzkumníka dat Azure pomocí vaší identity.

1. Pokud se vrátíte do poznámkového bloku, zobrazí se výsledek ověření. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Spuštění dotazu Kusto

1. Zadejte kusto dotaz a vyberte **Spustit**. Například:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Další kroky

* [Kusto-python GitHub úložiště](https://github.com/Azure/azure-kusto-python)

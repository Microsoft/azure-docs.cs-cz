---
title: 'Kurz: Nasazení modelu strojového učení s návrhářem'
titleSuffix: Azure Machine Learning
description: Tento kurz ukazuje, jak vytvořit řešení prediktivní analýzy v návrháři Azure Machine Learning (preview). Trénování, skóre a nasazení modelu strojového učení pomocí modulů přetažení myší.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: c3ca37fd47b6551a95f9a491053ec7863acd1eeb
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389388"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer-preview"></a>Kurz: Nasazení modelu strojového učení s návrhářem (náhled)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Prediktivní model vyvinutý v [první části kurzu](tutorial-designer-automobile-price-train-score.md) můžete nasadit, abyste měli ostatním možnost ho použít. V první části jste vycvičili svůj model. Nyní je čas generovat nové předpovědi na základě vstupu uživatele. V této části tutoriálu budete:

> [!div class="checklist"]
> * Vytvořte kanál odvození v reálném čase.
> * Vytvořte odvozovací cluster.
> * Nasaďte koncový bod v reálném čase.
> * Otestujte koncový bod v reálném čase.

## <a name="prerequisites"></a>Požadavky

Dokončení [první části kurzu](tutorial-designer-automobile-price-train-score.md) se dozvíte, jak trénovat a skóre model strojového učení v návrháři.

## <a name="create-a-real-time-inference-pipeline"></a>Vytvoření kanálu odvození v reálném čase

Chcete-li nasadit kanál, musíte nejprve převést kanál školení do kanálu odvození v reálném čase. Tento proces odebere školicí moduly a přidá vstupy a výstupy webové služby pro zpracování požadavků.

### <a name="create-a-real-time-inference-pipeline"></a>Vytvoření kanálu odvození v reálném čase

1. Nad plátnem kanálu vyberte **Vytvořit odvození kanálu** > **v reálném čase odvození potrubí**.

    ![Snímek obrazovky s informacemi o tom, kde najít tlačítko vytvořit kanál](./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png)

    Váš kanál by teď měl vypadat takto: 

   ![Snímek obrazovky zobrazující očekávanou konfiguraci kanálu po jeho přípravě k nasazení](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Když vyberete **Vytvořit kanál odvození**, stane se několik věcí:
    
    * Trénovaný model je uložen jako modul **datové sady** v paletě modulů. Najdete ji v části **Moje datové sady**.
    * Tréninkové moduly jako **Model vlaku** a **Split data** jsou odebrány.
    * Uložený trénovaný model je přidán zpět do kanálu.
    * Jsou přidány vstupní moduly **webové služby** a **výstupní webové služby.** Tyto moduly ukazují, kde uživatelská data zadá do kanálu a kde jsou vrácena data.

    > [!NOTE]
    > Ve výchozím nastavení bude **vstup webové služby** očekávat stejné schéma dat jako trénovací data použitá k vytvoření prediktivního kanálu. V tomto scénáři cena je zahrnuta ve schématu. Cena se však nepoužívá jako faktor během predikce.
    >

1. Vyberte **Odeslat**a použijte stejný výpočetní cíl a experiment, který jste použili v první části.

    Pokud je první spuštění, může trvat až 20 minut pro spuštění kanálu. Výchozí nastavení výpočetních prostředků mají minimální velikost uzlu 0, což znamená, že návrhář musí přidělit prostředky po nečinnosti. Opakované spuštění kanálu bude trvat kratší dobu, protože výpočetní prostředky jsou již přiděleny. Kromě toho návrhář používá výsledky uložené v mezipaměti pro každý modul k dalšímu zlepšení efektivity.

1. Vyberte **Nasadit**.

## <a name="create-an-inferencing-cluster"></a>Vytvoření odvozovavého clusteru

V dialogovém okně, které se zobrazí, můžete vybrat z libovolné existující clustery služby Azure Kubernetes Service (AKS), do kterých chcete nasadit model. Pokud cluster AKS nemáte, vytvořte ho pomocí následujících kroků.

1. V dialogovém **okně,** které se zobrazí a přejde na stránku Výpočetní výkon, vyberte **Vypočítat.**

1. Na navigačním pásu karet vyberte **Inference Clusters** > **+ New**.

    ![Snímek obrazovky znázorňující, jak se dostat do nového podokna odvození clusteru](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)

1. V podokně odvozeného clusteru nakonfigurujte novou službu Kubernetes.

1. Zadejte *aks-compute* pro **název Compute**.
    
1. Vyberte oblast v okolí, která je k dispozici pro **region**.

1. Vyberte **Vytvořit**.

    > [!NOTE]
    > Vytvoření nové služby AKS trvá přibližně 15 minut. Stav zřizování můžete zkontrolovat na stránce **Odvozené clustery.**
    >

## <a name="deploy-the-real-time-endpoint"></a>Nasazení koncového bodu v reálném čase

Po dokončení zřizování služby AKS se vraťte k kanálu pro odvození v reálném čase a dokončete nasazení.

1. Nad plátnem vyberte **Nasadit.**

1. Vyberte **Nasadit nový koncový bod v reálném čase**. 

1. Vyberte cluster AKS, který jste vytvořili.

1. Vyberte **Nasadit**.

    ![Snímek obrazovky s postupem nastavení nového koncového bodu v reálném čase](./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png)

    Po dokončení nasazení se zobrazí oznámení o úspěchu nad plátnem. Může to trvat pár minut.

## <a name="test-the-real-time-endpoint"></a>Testování koncového bodu v reálném čase

Po dokončení nasazení můžete otestovat koncový bod v reálném čase tak, že přejdete na stránku **Koncové body.**

1. Na stránce **Koncové body** vyberte cílový bod, který jste nasadili.

    ![Snímek obrazovky se zvýrazněnou záložkou koncových bodů v reálném čase se zvýrazněným nedávno vytvořeným koncovým bodem](./media/tutorial-designer-automobile-price-deploy/endpoints.png)

1. Vyberte **Test**.

1. Můžete ručně zadat testovací data nebo použít automaticky vyplněná ukázková data a vybrat **možnost Testovat**.

    Portál odešle požadavek na test do koncového bodu a zobrazí výsledky. Přestože je pro vstupní data generována hodnota ceny, nepoužívá se ke generování hodnoty předpověď.

    ![Snímek obrazovky ukazující, jak otestovat koncový bod v reálném čase se zvýrazněným popiskem s vyznačeným skóre pro cenu](./media/tutorial-designer-automobile-price-deploy/test-endpoint.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili klíčové kroky v tom, jak vytvořit, nasadit a využívat model strojového učení v návrháři. Další informace o tom, jak můžete pomocí návrháře řešit jiné typy problémů, najdete v dalších ukázkových kanálech.

> [!div class="nextstepaction"]
> [Ukázky návrháře](samples-designer.md)

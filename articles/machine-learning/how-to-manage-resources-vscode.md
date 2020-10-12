---
title: Vytvoření a Správa prostředků VS Code rozšíření (Preview)
titleSuffix: Azure Machine Learning
description: Vytváření a Správa prostředků pomocí rozšíření VS Code
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 09/30/2020
ms.openlocfilehash: 1120a3636b7ce24dde0d33c213f9063f910123c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91530625"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Správa prostředků Azure Machine Learning s rozšířením VS Code (Preview)

Naučte se spravovat Azure Machine Learning prostředky s rozšířením VS Code.

![Rozšíření Azure Machine Learning VS Code](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho ještě nemáte, zaregistrujte se a vyzkoušejte si [bezplatnou nebo placená verzi Azure Machine Learning](https://aka.ms/AMLFree).
- Visual Studio Code. Pokud ho nemáte, [nainstalujte ho](https://code.visualstudio.com/docs/setup/setup-overview).
- Rozšíření VS Code Azure Machine Learning. Pokud chcete nainstalovat rozšíření, postupujte podle pokynů [Průvodce instalací rozšíření Azure Machine Learning vs Code](tutorial-setup-vscode-extension.md#install-the-extension) .

Všechny procesy níže předpokládají, že jste v zobrazení Azure Machine Learning v Visual Studio Code. Rozšíření spustíte tak, že na řádku VS Code aktivity vyberete ikonu **Azure** .

## <a name="workspaces"></a>Pracovní prostory

Další informace najdete v tématu [pracovní prostory](concept-workspace.md).

### <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

1. V zobrazení Azure Machine Learning klikněte pravým tlačítkem myši na uzel předplatného a vyberte **vytvořit pracovní prostor**.
1. Na příkazovém řádku:
    1. Zadejte název pracovního prostoru.
    1. Zvolte svoje předplatné Azure.
    1. Vyberte nebo vytvořte novou skupinu prostředků, ve které se má pracovní prostor zřídit.
    1. Vyberte umístění, kam chcete zřídit pracovní prostor.

Mezi alternativní metody vytvoření pracovního prostoru patří:

- Otevřete zobrazení palety příkazů **> paleta příkazů** a zadejte text do příkazového řádku **Azure ml: vytvořit pracovní prostor**.
- Klikněte na `+` ikonu v horní části zobrazení Azure Machine Learning.
- Po zobrazení výzvy k výběru pracovního prostoru během zřizování jiných prostředků vytvořte nový pracovní prostor.

### <a name="remove-a-workspace"></a>Odebrat pracovní prostor

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Klikněte pravým tlačítkem na pracovní prostor, který chcete odebrat.
1. Vyberte, zda chcete odebrat:
    - *Pouze pracovní prostor*: Tato možnost odstraní **pouze** prostředek pracovního prostoru Azure. Skupina prostředků, účty úložiště a všechny další prostředky, ke kterým byl pracovní prostor připojen, jsou stále v Azure.
    - *S přidruženými prostředky*: Tato možnost odstraní pracovní prostor **a** všechny přidružené prostředky.

## <a name="datastores"></a>Úložiště dat

Rozšíření VS Code aktuálně podporuje úložiště dat následujících typů:

- Sdílená složka Azure
- Azure Blob Storage

Když vytváříte pracovní prostor, vytvoří se úložiště dat pro každý z těchto typů.

Další informace najdete v tématu [úložiště dat](concept-data.md#datastores).

### <a name="create-a-datastore"></a>Vytvoření úložiště dat

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru, pod kterým chcete vytvořit úložiště dat.
1. Klikněte pravým tlačítkem na uzel **úložiště dat** a vyberte **Registrovat úložiště dat**.
1. Na příkazovém řádku:
    1. Zadejte název úložiště dat.
    1. Vyberte typ úložiště dat.
    1. Vyberte prostředek úložiště. Můžete buď zvolit prostředek úložiště, který je přidružený k vašemu pracovnímu prostoru, nebo ho vybrat z libovolného platného prostředku úložiště v předplatných Azure.
    1. Vyberte kontejner, ve kterém jsou vaše data uvnitř dříve vybraného prostředku úložiště.
1. V VS Code se zobrazí konfigurační soubor. Pokud jste se souborem konfigurace spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

### <a name="manage-a-datastore"></a>Správa úložiště dat

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru.
1. Rozbalte uzel **úložiště dat** ve vašem pracovním prostoru.
1. Vyberte úložiště dat, které chcete:
    - *Nastavit jako výchozí*. Při každém spuštění experimentů se jedná o úložiště dat, které bude použito.
    - *Zkontrolujte nastavení jen pro čtení*.
    - *Upravit*. Změňte typ ověřování a přihlašovací údaje. Mezi podporované typy ověřování patří klíč účtu a token SAS.

## <a name="datasets"></a>Datové sady

Rozšíření aktuálně podporuje následující typy datových sad:

- *Tabulková*: umožňuje vyhodnotit data do datového rámce (PANDAS nebo PySpark).
- *Soubor*: soubor nebo kolekce souborů. Umožňuje stahovat nebo připojovat soubory do výpočetních prostředků.

Další informace najdete v tématu [datové sady](concept-data.md#datasets) .

### <a name="create-dataset"></a>Vytvoření datové sady

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru, pod kterým chcete vytvořit úložiště dat.
1. Klikněte pravým tlačítkem na uzel **datové sady** a vyberte **vytvořit datovou sadu**.
1. Na příkazovém řádku:
    1. Zvolit typ datové sady
    1. Definujte, jestli jsou data umístěná na vašem počítači nebo na webu.
    1. Zadejte umístění vašich dat. Může to být buď jeden soubor, nebo adresář obsahující vaše datové soubory.
    1. Vyberte úložiště dat, do kterého chcete data nahrát.
    1. Zadejte předponu, která pomůže identifikovat datovou sadu v úložišti dat.

### <a name="version-datasets"></a>Datové sady verze

Při sestavování modelů strojového učení se může stát, že budete chtít datovou sadu upravovat. Postup v rozšíření VS Code:

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru.
1. Rozbalte uzel **datové sady** .
1. Klikněte pravým tlačítkem na datovou sadu, kterou chcete vybrat, a vyberte **vytvořit novou verzi**.
1. Na příkazovém řádku:
    1. Vybrat typ datové sady
    1. Definujte, jestli jsou data uložená ve vašem počítači nebo na webu.
    1. Zadejte umístění vašich dat. Může to být buď jeden soubor, nebo adresář obsahující vaše datové soubory.
    1. Vyberte úložiště dat, do kterého chcete data nahrát.
    1. Zadejte předponu, která pomůže identifikovat datovou sadu v úložišti dat.

### <a name="view-dataset-properties"></a>Zobrazit vlastnosti datové sady

Tato možnost umožňuje zobrazit metadata přidružená k konkrétní datové sadě. Postup v rozšíření VS Code:

1. Rozbalte uzel pracovního prostoru.
1. Rozbalte uzel **datové sady** .
1. Klikněte pravým tlačítkem na datovou sadu, kterou chcete zkontrolovat, a vyberte **Zobrazit vlastnosti datové sady**. Zobrazí se konfigurační soubor s vlastnostmi nejnovější verze sady dat.

> [!NOTE]
> Máte-li více verzí datové sady, můžete zvolit, zda chcete zobrazit pouze vlastnosti datové sady konkrétní verze rozbalením uzlu DataSet a provedením stejných kroků popsaných v této části v rámci verze zájmu.

### <a name="unregister-datasets"></a>Zrušit registraci datových sad

Chcete-li odebrat datovou sadu a všechny její verze, zrušte její registraci. Postup v rozšíření VS Code:

1. Rozbalte uzel pracovního prostoru.
1. Rozbalte uzel **datové sady** .
1. Klikněte pravým tlačítkem na datovou sadu, kterou chcete zrušit registraci, a vyberte zrušit **registraci datové sady**.

## <a name="environments"></a>Prostředí

Další informace najdete v tématu [prostředí](concept-environments.md).

### <a name="create-environment"></a>Vytvořit prostředí

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru, pod kterým chcete vytvořit úložiště dat.
1. Klikněte pravým tlačítkem na uzel **prostředí** a vyberte **vytvořit prostředí**.
1. Na příkazovém řádku:
    1. Zadejte název vašeho prostředí.
    1. Definujte konfiguraci prostředí:
        - Podaná *prostředí*: předkonfigurovaná prostředí v Azure Machine Learning. Prostředí můžete dále upravit úpravou `dependencies` vlastnosti v souboru JSON. Přečtěte si další informace o spravovaných [prostředích](resource-curated-environments.md).
        - *Soubor závislostí conda*: pro prostředí Anaconda je možné zadat soubor obsahující definici prostředí.
        - *Soubor požadavků PIP*: pro prostředí PIP se dá zadat soubor obsahující definici vašeho prostředí.
        - *Existující prostředí conda*: Tato možnost vyhledá prostředí conda v místním počítači a pokusí se vytvořit prostředí z vybraného prostředí.
        - *Vlastní*: definování vlastních kanálů a závislostí
    1. V editoru se otevře konfigurační soubor. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

### <a name="view-environment-configurations"></a>Zobrazit konfigurace prostředí

Zobrazení závislostí a konfigurací pro konkrétní prostředí v rozšíření:

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru.
1. Rozbalte uzel **prostředí** .
1. Klikněte pravým tlačítkem na prostředí, které chcete zobrazit, a vyberte **Zobrazit prostředí**.

### <a name="edit-environment-configurations"></a>Upravit konfigurace prostředí

Postup úpravy závislostí a konfigurací pro konkrétní prostředí v rozšíření:

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **prostředí** do svého pracovního prostoru.
1. Klikněte pravým tlačítkem na prostředí, které chcete zobrazit, a vyberte **Upravit prostředí**.
1. Pokud jste se změnou vaší konfigurace spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

## <a name="experiments"></a>Experimenty

Další informace najdete v tématu [experimenty](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-experiment"></a>Vytvoření experimentu

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru.
1. Klikněte pravým tlačítkem na uzel **experimenty** v pracovním prostoru a vyberte **vytvořit experiment**.
1. Do příkazového řádku zadejte název experimentu.

### <a name="run-experiment"></a>Spustit experiment

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **experimenty** v pracovním prostoru.
1. Klikněte pravým tlačítkem na experiment, který chcete spustit.
1. Na řádku aktivity vyberte ikonu pro **Spuštění experimentu** .
1. Vyberte, zda chcete experiment spustit místně nebo vzdáleně. Další informace o místním spouštění a ladění experimentů najdete v [Průvodci laděním](how-to-debug-visual-studio-code.md) .
1. Zvolte vaše předplatné.
1. Vyberte Azure pracovní prostor ML ke spuštění experimentu v rámci.
1. Vyberte experiment.
1. Vyberte nebo vytvořte výpočetní prostředky pro spuštění experimentu na.
1. Vyberte nebo vytvořte konfiguraci spuštění pro svůj experiment.

Alternativně můžete vybrat tlačítko pro **Spuštění experimentu** v horní části rozšíření a nakonfigurovat svůj experiment na příkazovém řádku.

### <a name="view-experiment"></a>Zobrazit experiment

Postup zobrazení experimentu v Azure Machine Learning Studio:

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **experimenty** v pracovním prostoru.
1. Klikněte pravým tlačítkem na experiment, který chcete zobrazit, a vyberte **Zobrazit experiment**. 
1. Zobrazí se výzva s výzvou k otevření adresy URL experimentu v Azure Machine Learning Studiu. Vyberte **Otevřít**.

### <a name="track-run-progress"></a>Sledovat průběh běhu

Při spouštění experimentu můžete chtít zobrazit jeho průběh. Postup sledování průběhu spuštění v Azure Machine Learning Studiu z rozšíření:

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **experimenty** v pracovním prostoru.
1. Rozbalte uzel experiment, pro který chcete sledovat průběh.
1. Klikněte pravým tlačítkem na spustit a vyberte **Zobrazit spustit v Azure Portal**.
1. Zobrazí se výzva s výzvou k otevření adresy URL pro spuštění v Azure Machine Learning Studiu. Vyberte **Otevřít**.

### <a name="download-run-logs--outputs"></a>Stáhnout protokoly spuštění & výstupy

Po dokončení běhu budete možná chtít stáhnout protokoly a prostředky, jako je model vygenerovaný jako součást experimentového běhu.

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **experimenty** v pracovním prostoru.
1. Rozbalte uzel experiment, pro který chcete sledovat průběh.
1. Klikněte pravým tlačítkem na spuštění:
    - Výstupy stáhnete tak, že vyberete **Stáhnout výstupy**.
    - Protokoly stáhnete tak, že vyberete **Stáhnout protokoly**.

### <a name="view-run-metadata"></a>Zobrazit metadata spuštění

V rozšíření můžete zkontrolovat metadata, jako je například konfigurace spuštění používaná pro běh a také podrobnosti o spuštění.

## <a name="compute-instances"></a>Instance služby Compute

Další informace najdete v tématu [výpočetní instance](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Vytvořit výpočetní instanci

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru, ve kterém chcete vytvořit výpočetní instanci.
1. Klikněte pravým tlačítkem na uzel **výpočetní instance** a vyberte **vytvořit výpočetní instanci**.
1. Na příkazovém řádku:
    1. Zadejte název instance služby Compute.
    1. V seznamu vyberte velikost virtuálního počítače.
    1. Vyberte, jestli chcete povolit přístup přes SSH.
        1. Pokud povolíte přístup přes SSH, budete muset zadat také veřejný klíč SSH nebo soubor obsahující klíč. Další informace najdete v příručce k [vytváření a používání klíčů ssh v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys).

### <a name="stop-or-restart-compute-instance"></a>Zastavení nebo restartování výpočetní instance

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **výpočetní instance** do svého pracovního prostoru.
1. Pravým tlačítkem myši klikněte na výpočetní instanci, kterou chcete zastavit nebo restartovat, a vyberte **zastavit výpočetní instanci** nebo **restartovat výpočetní instanci** v uvedeném pořadí.

### <a name="view-compute-instance-configuration"></a>Zobrazit konfiguraci výpočetní instance

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **výpočetní instance** do svého pracovního prostoru.
1. Klikněte pravým tlačítkem na výpočetní instanci, kterou chcete zkontrolovat, a vyberte **Zobrazit vlastnosti instance COMPUTE**.

### <a name="delete-compute-instance"></a>Odstranit výpočetní instanci

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **výpočetní instance** do svého pracovního prostoru.
1. Klikněte pravým tlačítkem na výpočetní instanci, kterou chcete odstranit, a vyberte **Odstranit výpočetní instanci**.

## <a name="compute-clusters"></a>Výpočetní clustery

Rozšíření podporuje následující výpočetní typy:

- Azure Machine Learning výpočetní cluster
- Azure Kubernetes Service

Další informace najdete v tématu [výpočetní cíle](concept-compute-target.md#train).

### <a name="create-compute"></a>Vytvořit výpočetní prostředky

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru, ve kterém chcete vytvořit výpočetní cluster.
1. Klikněte pravým tlačítkem na uzel **COMPUTE clustery** a vyberte **vytvořit výpočetní**prostředky.
1. Na příkazovém řádku:
    1. Zvolit výpočetní typ
    1. Vyberte velikost virtuálního počítače. Přečtěte si další informace o [velikostech virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).
    1. Zadejte název pro výpočetní výkon.

### <a name="view-compute-configuration"></a>Zobrazit konfiguraci výpočtů

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **výpočetní clustery** do vašeho pracovního prostoru.
1. Klikněte pravým tlačítkem na výpočetní výkon, který chcete zobrazit, a vyberte **Zobrazit výpočetní vlastnosti**.

### <a name="edit-compute-scale-settings"></a>Upravit nastavení škály výpočtů

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **výpočetní clustery** do vašeho pracovního prostoru.
1. Klikněte pravým tlačítkem na výpočetní výkon, který chcete upravit, a vyberte **Upravit výpočetní**prostředky.
1. V editoru se otevře konfigurační soubor pro výpočetní výkon. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

### <a name="delete-compute"></a>Odstranit výpočetní prostředky

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **výpočetní clustery** do vašeho pracovního prostoru.
1. Klikněte pravým tlačítkem na výpočet, který chcete odstranit, a vyberte **Odstranit výpočetní**prostředky.

### <a name="create-run-configuration"></a>Vytvořit konfiguraci spuštění

Postup vytvoření konfigurace spuštění v rozšíření:

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **výpočetní clustery** do vašeho pracovního prostoru.
1. Klikněte pravým tlačítkem na cíl výpočtů, na kterém chcete vytvořit konfiguraci spuštění, a vyberte **vytvořit konfiguraci spuštění**.
1. Na příkazovém řádku:
    1. Zadejte název pro svůj cíl služby Compute.
    1. Vyberte nebo vytvořte nové prostředí.
    1. Zadejte název skriptu, který chcete spustit, nebo stiskněte klávesu **ENTER** do prohlížeče pro skript na místním počítači.
    1. Volitelné Zvolte, zda chcete vytvořit odkaz na data pro váš školicí běh. V takovém případě se zobrazí výzva k definování datové sady v konfiguraci spuštění.
        1. Vyberte jednu z registrovaných datových sad, které chcete propojit s konfigurací spuštění. otevře se konfigurační soubor pro datovou sadu v editoru. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.
    1. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

### <a name="edit-run-configuration"></a>Upravit konfiguraci spuštění

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. V uzlu **výpočetní clustery** pracovního prostoru rozbalte uzel Compute Cluster.
1. Klikněte pravým tlačítkem na konfiguraci spuštění, kterou chcete upravit, a vyberte **Upravit konfiguraci spuštění**.
1. V editoru se otevře konfigurační soubor pro vaši konfiguraci spuštění. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

### <a name="delete-run-configuration"></a>Odstranit konfiguraci spuštění

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru.
1. Rozbalte uzel výpočetní cluster v zájmu v uzlu **výpočetní clustery** .
1. Klikněte pravým tlačítkem na konfiguraci spuštění, kterou chcete upravit, a vyberte **Odstranit konfiguraci spuštění**.

## <a name="models"></a>Modely

Další informace najdete v tématu [modely](concept-azure-machine-learning-architecture.md#models) .

### <a name="register-model"></a>Registrace modelu

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru.
1. Klikněte pravým tlačítkem na uzel **modely** a vyberte **Registrovat model**.
1. Na příkazovém řádku:
    1. Zadejte název pro svůj model.
    1. Vyberte, jestli je váš model soubor nebo složka.
    1. Najděte model na svém místním počítači.
    1. Konfigurační soubor pro model v editoru. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

### <a name="view-model-properties"></a>Zobrazit vlastnosti modelu

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **modely** do svého pracovního prostoru.
1. Klikněte pravým tlačítkem na model, jehož vlastnosti chcete zobrazit, a vyberte **Zobrazit vlastnosti modelu**. Soubor se otevře v editoru, který obsahuje vlastnosti vašeho modelu.

### <a name="download-model"></a>Stáhnout model

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **modely** do svého pracovního prostoru.
1. Klikněte pravým tlačítkem na model, který chcete stáhnout, a vyberte **Stáhnout soubor modelu**.

### <a name="delete-a-model"></a>Odstranění modelu

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **modely** do svého pracovního prostoru.
1. Klikněte pravým tlačítkem na model, který chcete odstranit, a vyberte **Odebrat model**.

## <a name="endpoints"></a>Koncové body

Rozšíření VS Code podporuje následující cíle nasazení:

- Azure Container Instances
- Azure Kubernetes Service

Další informace najdete v tématu [koncové body webové služby](concept-azure-machine-learning-architecture.md#web-service-endpoint).

### <a name="create-deployments"></a>Vytvoření nasazení

> [!NOTE]
> Vytváření nasazení v tuto chvíli funguje jenom s conda prostředími.

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel pracovního prostoru.
1. Klikněte pravým tlačítkem myši na uzel **koncové body** a vyberte **nasadit službu**.
1. Na příkazovém řádku:
    1. Vyberte, zda chcete použít již registrovaný model nebo místní soubor modelu.
    1. Vyberte svůj model.
    1. Vyberte cíl nasazení, do kterého chcete model nasadit.
    1. Zadejte název pro svůj model.
    1. Zadejte skript, který se má spustit při bodování modelu.
    1. Zadejte soubor conda závislostí.
    1. V editoru se zobrazí konfigurační soubor pro vaše nasazení. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.

> [!NOTE]
> Případně můžete kliknout pravým tlačítkem na registrovaný model v uzlu *modely* a vybrat **nasadit službu z registrovaného modelu**.

### <a name="delete-deployments"></a>Odstranit nasazení

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **koncové body** v pracovním prostoru.
1. Klikněte pravým tlačítkem na nasazení, které chcete odebrat, a vyberte **Odebrat službu**.
1. Zobrazí se výzva, která potvrzuje, že chcete službu odebrat. Vyberte **OK**.

### <a name="manage-deployments"></a>Správa nasazení

Kromě vytváření a odstraňování nasazení můžete zobrazit a upravit nastavení přidružená k nasazení.

1. Rozbalte uzel předplatné, který obsahuje váš pracovní prostor.
1. Rozbalte uzel **koncové body** v pracovním prostoru.
1. Klikněte pravým tlačítkem na nasazení, které chcete spravovat:
    - Pokud chcete upravit nastavení, vyberte **Upravit službu**.
        - V editoru se zobrazí konfigurační soubor pro vaše nasazení. Pokud jste s konfigurací spokojeni, vyberte **Uložit a pokračovat** nebo otevřete vs Code paletu příkazů (**zobrazení > paleta příkazů**) a zadejte **Azure ml: Uložit a pokračovat**.
    - Chcete-li zobrazit nastavení konfigurace nasazení, vyberte možnost **Zobrazit vlastnosti služby**.

## <a name="next-steps"></a>Další kroky

[Zaškolení modelu klasifikace obrázků](tutorial-train-deploy-image-classification-model-vscode.md) pomocí rozšíření vs Code.

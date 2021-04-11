---
title: Nastavení nabízených oznámení do konfigurace aplikace pomocí Azure Pipelines
description: Naučte se používat Azure Pipelines k odesílání hodnot klíč-hodnota do úložiště konfigurace aplikace.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: e1a4fb52a5f9622758e9ed805bf9380f5f608870
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068242"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Nastavení nabízených oznámení do konfigurace aplikace pomocí Azure Pipelines

[Nabízená úloha konfigurace aplikace Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) přenáší hodnoty klíč-hodnota z konfiguračního souboru do úložiště konfigurace aplikace. Tato úloha v rámci kanálu umožňuje úplné funkce kruhů, protože teď můžete získat nastavení z úložiště konfigurace aplikace a nabízet nastavení do úložiště konfigurace aplikace.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- Prostředek konfigurace aplikace – vytvořte si ho zdarma ve [Azure Portal](https://portal.azure.com).
- Projekt Azure DevOps – [Vytvořte si ho zdarma](https://go.microsoft.com/fwlink/?LinkId=2014881) .
- Úloha nabízených oznámení konfigurace Azure App – stažení z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push)zdarma

## <a name="create-a-service-connection"></a>Vytvoření připojení služby

[Připojení služby](/azure/devops/pipelines/library/service-endpoints) umožňuje přístup k prostředkům ve vašem předplatném Azure z vašeho projektu Azure DevOps.

1. V Azure DevOps se podívejte na projekt obsahující váš cílový kanál a otevřete **nastavení projektu** v levém dolním rohu.
1. V části **kanály** vyberte **připojení služby** a v pravém horním rohu vyberte **nové připojení služby** .
1. Vyberte **Azure Resource Manager**.
![Snímek obrazovky zobrazuje výběr Azure Resource Manager v rozevíracím seznamu nové připojení služby.](./media/new-service-connection.png)
1. V dialogovém okně **metoda ověřování** vyberte **instanční objekt (automatický)**.
    > [!NOTE]
    > **Spravované ověřování identity** není aktuálně pro úlohu konfigurace aplikace podporováno.
1. Vyplňte své předplatné a prostředek. Dejte vašemu připojení služby název.

Teď, když se vytvoří připojení ke službě, vyhledejte název přiřazeného objektu služby. V dalším kroku přidáte nové přiřazení role k tomuto instančnímu objektu.

1. Přejít na **nastavení projektu**  >  **připojení služby**.
1. Vyberte připojení služby, které jste vytvořili v předchozí části.
1. Vyberte **Spravovat instanční objekt**.
1. Všimněte si **zobrazovaného názvu** .
![Snímek obrazovky se zobrazeným názvem objektu služby.](./media/service-principal-display-name.png)

## <a name="add-role-assignment"></a>Přidat přiřazení role

Přiřaďte správné přiřazení role konfigurace aplikace k přihlašovacím údajům používaným v rámci úlohy, aby úloha mohla získat přístup k úložišti konfigurace aplikace.

1. Přejděte do cílového úložiště konfigurace aplikace. 
1. Na levé straně vyberte **řízení přístupu (IAM)**.
1. Na pravé straně klikněte na tlačítko **Přidat přiřazení rolí** .
![Snímek obrazovky se zobrazí na tlačítku Přidat přiřazení role.](./media/add-role-assignment-button.png)
1. V části **role** vyberte možnost **vlastník dat konfigurace aplikace**. Tato role umožňuje, aby úloha četla do úložiště konfigurace aplikace a zapisovala do něj. 
1. Vyberte objekt služby přidružený k připojení služby, které jste vytvořili v předchozí části.
![Snímek obrazovky se zobrazí v dialogovém okně Přidat přiřazení role.](./media/add-role-assignment.png)

  
## <a name="use-in-builds"></a>Použít v sestaveních

V této části se dozvíte, jak používat úlohu Azure App Configuration Push v kanálu sestavení Azure DevOps.

1. Přejděte na stránku kanálu **sestavení kliknutím na kanály kanály**  >  . Dokumentaci k kanálům sestavení najdete [tady](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Pokud vytváříte nový kanál sestavení, v posledním kroku procesu na kartě **Revize** vyberte **Zobrazit pomocníka** na pravé straně kanálu.
      ![Snímek obrazovky se zobrazí tlačítko Zobrazit pomocníka pro nový kanál.](./media/new-pipeline-show-assistant.png)
      - Pokud používáte existující kanál sestavení, klikněte na tlačítko **Upravit** v pravém horním rohu.
      ![Snímek obrazovky zobrazující tlačítko Upravit pro existující kanál.](./media/existing-pipeline-show-assistant.png)
1. Vyhledejte úlohu **nabízení konfigurace Azure App** .
![Snímek obrazovky se zobrazí dialogové okno Přidat úlohu s možností Azure App Configuration push do vyhledávacího pole.](./media/add-azure-app-configuration-push-task.png)
1. Proveďte konfiguraci nezbytných parametrů pro úlohu, aby se navložily hodnoty klíče z konfiguračního souboru do úložiště konfigurace aplikace. Vysvětlení parametrů jsou k dispozici v níže uvedené části **parametry** a v popisech tlačítek vedle jednotlivých parametrů.
![Snímek obrazovky se zobrazí parametry nabízené úlohy konfigurace aplikace.](./media/azure-app-configuration-push-parameters.png)
1. Uložit a zařadit sestavení do fronty V protokolu sestavení se zobrazí všechny chyby, ke kterým došlo během provádění úlohy.

## <a name="use-in-releases"></a>Použít ve verzích

V této části se dozvíte, jak používat úlohu Azure App Configuration Push v kanálech pro vydávání verzí Azure DevOps.

1. Vyberte vydaná vydání **kanálů** a přejděte na stránku kanály vydání  >  . Dokumentaci k kanálům pro vydávání verzí najdete [tady](/azure/devops/pipelines/release).
1. Vyberte existující kanál verze. Pokud ji nemáte, vyberte **+ Nová** a vytvořte novou.
1. Kliknutím na tlačítko **Upravit** v pravém horním rohu upravte kanál verze.
1. V rozevíracím seznamu **úlohy** vyberte **fázi** , do které chcete úkol přidat. Další informace o fázích najdete [tady](/azure/devops/pipelines/release/environments).
![Snímek obrazovky se zobrazí vybraná fáze v rozevíracím seznamu úkoly.](./media/pipeline-stage-tasks.png)
1. Klikněte na tlačítko **+** Další do úlohy, do které chcete přidat nový úkol.
![Snímek obrazovky se zobrazeným tlačítkem plus vedle úlohy.](./media/add-task-to-job.png)
1. V dialogovém okně **Přidat úlohy** zadejte do vyhledávacího pole položku **Azure App Configuration push** a vyberte ji.
1. Nakonfigurujte potřebné parametry v rámci úlohy, aby se do úložiště konfigurace aplikace navložily hodnoty klíče z konfiguračního souboru. Vysvětlení parametrů jsou k dispozici v níže uvedené části **parametry** a v popisech tlačítek vedle jednotlivých parametrů.
1. Uložte a zařadíte do fronty verzi. V protokolu vydaných verzí se zobrazí všechny chyby zjištěné při spuštění úlohy.

## <a name="parameters"></a>Parametry

Následující parametry používá úloha nabízení konfigurace aplikace:

- **Předplatné Azure**: rozevírací seznam obsahující dostupná připojení služby Azure. Pokud chcete aktualizovat a aktualizovat seznam dostupných připojení služby Azure, klikněte na tlačítko **Aktualizovat předplatné Azure** napravo od textového pole.
- **Název konfigurace aplikace**: rozevírací seznam, který načte vaše dostupná úložiště konfigurace pod vybraným předplatným. Pokud chcete aktualizovat a aktualizovat seznam dostupných úložišť konfigurací, stiskněte tlačítko **aktualizovat název konfigurace aplikace** napravo od textového pole.
- **Cesta ke konfiguračnímu souboru**: cesta ke konfiguračnímu souboru. Parametr **cesty ke konfiguračnímu souboru** začíná v kořenovém adresáři úložiště souborů. Můžete procházet artefaktem sestavení a vybrat konfigurační soubor. ( `...` tlačítko napravo od textového pole). Podporované formáty souborů jsou: YAML, JSON, Properties. Následuje příklad konfiguračního souboru ve formátu JSON.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **Oddělovač**: oddělovač, který se používá k sloučení souborů. JSON a. yml.
- **Hloubka**: Hloubka, na kterou budou soubory. JSON a. yml shrnuté.
- **Prefix**: řetězec, který je připojený k začátku každého klíče, který se odeslal do úložiště konfigurace aplikace.
- **Label**: řetězec, který se přidá do každé hodnoty klíč-hodnota jako popisek v rámci úložiště konfigurace aplikace.
- **Typ obsahu**: řetězec, který se přidá do každého klíč-hodnota jako typ obsahu v rámci úložiště konfigurace aplikace.
- **Tags**: objekt JSON ve formátu `{"tag1":"val1", "tag2":"val2"}` , který definuje značky přidané do každé hodnoty klíč-hodnota vložené do úložiště konfigurace aplikace.
- **Odstraní všechny ostatní Key-Values ve Storu se zadaným prefixem a popiskem**: výchozí hodnota je **nezaškrtnutá**.
  - **Checked**: Odebere všechny klíčové hodnoty v úložišti konfigurace aplikace, které odpovídají zadané předponě a popisku před vložením nových klíčových hodnot z konfiguračního souboru.
  - **Nezaškrtnuto**: vloží všechny klíčové hodnoty z konfiguračního souboru do úložiště konfigurace aplikace a ponechá všechno ostatní v úložišti konfigurace aplikace beze změny.



## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k neočekávané chybě, můžete povolit protokoly ladění nastavením proměnné kanálu `system.debug` na `true` .

## <a name="faq"></a>Časté otázky

**Jak můžu nahrát víc konfiguračních souborů?**

Vytvořte víc instancí úlohy push App Configuration pro Azure v rámci stejného kanálu, aby se do úložiště konfigurace aplikace vložily víc konfiguračních souborů.

**Proč při pokusu o vložení klíč-hodnoty do úložiště konfigurace dochází k chybě 409?**

Pokud se úloha pokusí odebrat nebo přepsat klíčovou hodnotu, která je uzamčena v úložišti konfigurace aplikace, dojde k chybě 409 zprávy o konfliktu.

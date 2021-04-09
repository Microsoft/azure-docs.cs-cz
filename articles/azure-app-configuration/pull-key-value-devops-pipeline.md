---
title: Získání konfigurace aplikace settingsfromo pomocí Azure Pipelines
description: Naučte se používat Azure Pipelines k získání hodnot klíč-hodnota do úložiště konfigurace aplikace.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 1c01984f6a359c0fd1f5d06d26d97d4a84973f57
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056741"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Nastavení vyžádání do konfigurace aplikace pomocí Azure Pipelines

Úloha [Konfigurace aplikace Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) přebírá klíčové hodnoty z úložiště konfigurace aplikace a nastavuje je jako proměnné kanálu Azure, které mohou být spotřebovány následujícími úlohami. Tato úloha doplňuje úlohu [nabízení konfigurace aplikace Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) , která z konfiguračního souboru vloží hodnoty klíč-to do úložiště konfigurace aplikace. Další informace najdete v tématu [nastavení nabízených oznámení do konfigurace aplikací pomocí Azure Pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- Úložiště konfigurace aplikace – vytvořte si ho zdarma ve [Azure Portal](https://portal.azure.com).
- Projekt Azure DevOps – [Vytvořte si ho zdarma](https://go.microsoft.com/fwlink/?LinkId=2014881) .
- Úloha konfigurace Azure App – stažení z [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)zdarma  

## <a name="create-a-service-connection"></a>Vytvoření připojení služby

[Připojení služby](/azure/devops/pipelines/library/service-endpoints) umožňuje přístup k prostředkům ve vašem předplatném Azure z vašeho projektu Azure DevOps.

1. V Azure DevOps se podívejte na projekt obsahující váš cílový kanál a otevřete **nastavení projektu** v levém dolním rohu.
1. V části **kanály** vyberte **připojení služby**.
1. Pokud nemáte žádná existující připojení služby, klikněte na tlačítko **vytvořit připojení služby** uprostřed obrazovky. V opačném případě klikněte v pravém horním rohu stránky na **nové připojení služby** .
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

## <a name="add-role-assignment"></a>Přidat přiřazení role

Přiřaďte správnou roli konfigurace aplikace k připojení služby, které se používá v rámci úlohy, aby mohl úkol získat přístup k úložišti konfigurace aplikace.

1. Přejděte do cílového úložiště konfigurace aplikace. Návod k nastavení úložiště konfigurace aplikace najdete v tématu [Vytvoření úložiště konfigurace aplikace](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) v některém z rychlých startů konfigurace aplikace Azure.
1. Na levé straně vyberte **řízení přístupu (IAM)**.
1. Na pravé straně klikněte na tlačítko **Přidat přiřazení rolí** .
![Na snímku obrazovky se zobrazí tlačítko Přidat přiřazení rolí. ](./media/add-role-assignment-button.png) .
1. V části **role** vyberte **čtečka konfiguračních dat aplikace**. Tato role umožňuje, aby úkol četl z úložiště konfigurace aplikace. 
1. Vyberte objekt služby přidružený k připojení služby, které jste vytvořili v předchozí části.
![Snímek obrazovky se zobrazí v dialogovém okně Přidat přiřazení role.](./media/add-role-assignment-reader.png)

> [!NOTE]
> Aby bylo možné vyřešit Azure Key Vault odkazy v rámci konfigurace aplikace, musí být připojení služby také uděleno oprávnění ke čtení tajných kódů v odkazovaných trezorech klíčů Azure.
  
## <a name="use-in-builds"></a>Použít v sestaveních

V této části se dozvíte, jak používat úlohu konfigurace aplikace Azure v kanálu sestavení Azure DevOps.

1. Přejděte na stránku kanálu **sestavení kliknutím na kanály kanály**  >  . Dokumentaci k kanálu sestavení najdete v tématu  [Vytvoření prvního kanálu](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Pokud vytváříte nový kanál sestavení, v posledním kroku procesu na kartě **Revize** vyberte **Zobrazit pomocníka** na pravé straně kanálu.
      ![Snímek obrazovky se zobrazí tlačítko Zobrazit pomocníka pro nový kanál.](./media/new-pipeline-show-assistant.png)
      - Pokud používáte existující kanál sestavení, klikněte na tlačítko **Upravit** v pravém horním rohu.
      ![Snímek obrazovky zobrazující tlačítko Upravit pro existující kanál.](./media/existing-pipeline-show-assistant.png)
1. Vyhledejte úlohu **Konfigurace aplikace Azure** .
![Snímek obrazovky se zobrazí dialogové okno Přidat úlohu s konfigurací aplikace Azure do vyhledávacího pole.](./media/add-azure-app-configuration-task.png)
1. Nakonfigurujte potřebné parametry pro úlohu, aby vyčetly klíčové hodnoty z úložiště konfigurace aplikace. Popisy parametrů jsou k dispozici v části **parametry** níže a v popiscích tlačítek vedle jednotlivých parametrů.
      - Nastavte parametr **předplatné Azure** na název připojení služby, které jste vytvořili v předchozím kroku.
      - Nastavte **název konfigurace aplikace** na název prostředku vašeho úložiště konfigurace aplikace.
      - Pro zbývající parametry ponechte výchozí hodnoty.
![Snímek obrazovky se zobrazí parametry úlohy konfigurace aplikace.](./media/azure-app-configuration-parameters.png)
1. Uložit a zařadit sestavení do fronty V protokolu sestavení se zobrazí všechny chyby, ke kterým došlo během provádění úlohy.

## <a name="use-in-releases"></a>Použít ve verzích

V této části se dozvíte, jak používat úlohu konfigurace aplikace Azure v kanálu vydaných verzí Azure DevOps.

1. Vyberte vydaná vydání **kanálů** a přejděte na stránku kanály vydání  >  . Dokumentaci k vydaným kanálům vydaných verzí najdete v tématu [kanály verzí](/azure/devops/pipelines/release).
1. Vyberte existující kanál verze. Pokud ho nemáte, klikněte na **Nový kanál** a vytvořte nový.
1. Kliknutím na tlačítko **Upravit** v pravém horním rohu upravte kanál verze.
1. V rozevíracím seznamu **úlohy** vyberte **fázi** , do které chcete úkol přidat. Další informace o fázích najdete [tady](/azure/devops/pipelines/release/environments).
![Snímek obrazovky se zobrazí vybraná fáze v rozevíracím seznamu úkoly.](./media/pipeline-stage-tasks.png)
1. Klikněte na tlačítko **+** Další do úlohy, do které chcete přidat nový úkol.
![Snímek obrazovky se zobrazeným tlačítkem plus vedle úlohy.](./media/add-task-to-job.png)
1. Vyhledejte úlohu **Konfigurace aplikace Azure** .
![Snímek obrazovky se zobrazí dialogové okno Přidat úlohu s konfigurací aplikace Azure do vyhledávacího pole.](./media/add-azure-app-configuration-task.png)
1. Nakonfigurujte potřebné parametry v rámci úlohy, aby vyčetly klíčové hodnoty z úložiště konfigurace aplikace. Popisy parametrů jsou k dispozici v části **parametry** níže a v popiscích tlačítek vedle jednotlivých parametrů.
      - Nastavte parametr **předplatné Azure** na název připojení služby, které jste vytvořili v předchozím kroku.
      - Nastavte **název konfigurace aplikace** na název prostředku vašeho úložiště konfigurace aplikace.
      - Pro zbývající parametry ponechte výchozí hodnoty.
1. Uložte a zařadíte do fronty verzi. V protokolu vydaných verzí se zobrazí všechny chyby zjištěné při spuštění úlohy.

## <a name="parameters"></a>Parametry

Úkol konfigurace aplikace Azure používá následující parametry:

- **Předplatné Azure**: rozevírací seznam obsahující dostupná připojení služby Azure. Pokud chcete aktualizovat a aktualizovat seznam dostupných připojení služby Azure, klikněte na tlačítko **Aktualizovat předplatné Azure** napravo od textového pole.
- **Název konfigurace aplikace**: rozevírací seznam, který načte vaše dostupná úložiště konfigurace pod vybraným předplatným. Pokud chcete aktualizovat a aktualizovat seznam dostupných úložišť konfigurací, stiskněte tlačítko **aktualizovat název konfigurace aplikace** napravo od textového pole.
- **Filtr klíčů**: pomocí filtru můžete vybrat, které klíčové hodnoty se z konfigurace aplikace Azure vyžadují. Hodnota * vybere všechny klíčové hodnoty. Další informace o najdete v tématu [hodnoty klíčů dotazů](concept-key-value.md#query-key-values).
- **Label**: Určuje, který popisek se má použít při výběru hodnot klíče z úložiště konfigurace aplikace. Pokud není zadaný žádný popisek, načtou se hodnoty klíč-hodnota bez popisku. Následující znaky nejsou povoleny:, *.
- **Řezací předpona klíče**: Určuje jednu nebo více předpon, které by měly být z konfiguračních klíčů aplikace oříznuty, než je nastavíte jako proměnné. Více předpon lze oddělit znakem nového řádku.

## <a name="use-key-values-in-subsequent-tasks"></a>Použití hodnot klíč-hodnota v následujících úlohách

Hodnoty klíč-hodnota načtené z konfigurace aplikace se nastaví jako proměnné kanálu, které jsou přístupné jako proměnné prostředí. Klíč proměnné prostředí je klíč hodnoty klíče, který je načten z konfigurace aplikace po vystřihování předpony, je-li zadána.

Pokud třeba následná úloha spustí skript PowerShellu, může to spotřebovat klíčovou hodnotu s klíčem "myBuildSetting", například:
```powershell
echo "$env:myBuildSetting"
```
A hodnota bude vytištěna do konzoly.

> [!NOTE]
> Azure Key Vault odkazy v konfiguraci aplikace budou vyřešeny a nastaveny jako [tajné proměnné](/azure/devops/pipelines/process/variables#secret-variables). V kanálech Azure jsou tajné proměnné z protokolu maskované. Nejsou předávány do úkolů jako proměnné prostředí a musí být předány jako vstupy. 

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k neočekávané chybě, můžete povolit protokoly ladění nastavením proměnné kanálu `system.debug` na `true` .

## <a name="faq"></a>Časté otázky

**Návody vytvořit konfiguraci z více klíčů a popisků?**

Existují situace, kdy může být nutné, aby konfigurace byla složena z více popisků, například jako výchozí a vývoj. K implementaci tohoto scénáře se dá v jednom kanálu použít víc úloh konfigurace aplikace. Klíčové hodnoty načtené úlohou v pozdějším kroku nahradí všechny hodnoty z předchozích kroků. Ve výše uvedeném příkladu se dá úkol použít k výběru hodnot klíče s výchozím popiskem, zatímco druhý úkol může vybírat klíčové hodnoty pomocí popisku pro vývoj. Klíče s popisem pro vývoj budou přepisovat stejné klíče s výchozím popiskem.

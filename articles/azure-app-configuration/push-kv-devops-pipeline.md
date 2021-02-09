---
title: Nastavení nabízených oznámení do konfigurace aplikace pomocí Azure Pipelines
description: Naučte se používat Azure Pipelines k odesílání hodnot klíč-hodnota do úložiště konfigurace aplikace.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkemper
ms.openlocfilehash: c5e0cc3eb29fb612460b16d8de9dee62949b5bd2
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979602"
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
1. Vyberte **instanční objekt (automatický)**.
1. Vyplňte své předplatné a prostředek. Dejte vašemu připojení služby název.

Teď, když se vytvoří připojení ke službě, vyhledejte název přiřazeného objektu služby. V dalším kroku přidáte nové přiřazení role k tomuto instančnímu objektu.

1. Přejít na **nastavení projektu**  >  **připojení služby**.
1. Vyberte připojení služby, které jste vytvořili v předchozí části.
1. Vyberte **Spravovat instanční objekt**.
1. Všimněte si **zobrazovaného názvu** .

## <a name="add-role-assignment"></a>Přidat přiřazení role

Přiřaďte správné přiřazení role konfigurace aplikace k přihlašovacím údajům používaným v rámci úlohy, aby úloha mohla získat přístup k úložišti konfigurace aplikace.

1. Přejděte do cílového úložiště konfigurace aplikace. 
1. Na levé straně vyberte **řízení přístupu (IAM)**.
1. V horní části vyberte **+ Přidat** a vyberte **Přidat přiřazení role**.
1. V části **role** vyberte možnost **vlastník dat konfigurace aplikace**. Tato role umožňuje, aby úloha četla do úložiště konfigurace aplikace a zapisovala do něj. 
1. Vyberte objekt služby přidružený k připojení služby, které jste vytvořili v předchozí části.
  
## <a name="use-in-builds"></a>Použít v sestaveních

V této části se dozvíte, jak používat úlohu Azure App Configuration Push v kanálu sestavení Azure DevOps.

1. Přejděte na stránku kanálu **sestavení kliknutím na kanály kanály**  >  . Dokumentaci k kanálům sestavení najdete [tady](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Pokud vytváříte nový kanál sestavení, vyberte **Zobrazit pomocníka** na pravé straně kanálu a vyhledejte úlohu **nabízených oznámení konfigurace Azure App** .
      - Pokud používáte existující kanál sestavení, přejděte na kartu **úlohy** při úpravách kanálu a vyhledejte úlohu **nabízených oznámení konfigurace Azure App** .
2. Proveďte konfiguraci nezbytných parametrů pro úlohu, aby se navložily hodnoty klíče z konfiguračního souboru do úložiště konfigurace aplikace. Parametr **cesty ke konfiguračnímu souboru** začíná v kořenovém adresáři úložiště souborů.
3. Uložit a zařadit sestavení do fronty V protokolu sestavení se zobrazí všechny chyby, ke kterým došlo během provádění úlohy.

## <a name="use-in-releases"></a>Použít ve verzích

V této části se dozvíte, jak používat úlohu Azure App Configuration Push v kanálech pro vydávání verzí Azure DevOps.

1. Vyberte vydaná vydání **kanálů** a přejděte na stránku kanály vydání  >  . Dokumentaci k kanálům pro vydávání verzí najdete [tady](/azure/devops/pipelines/release).
1. Vyberte existující kanál verze. Pokud ji nemáte, vyberte **+ Nová** a vytvořte novou.
1. Kliknutím na tlačítko **Upravit** v pravém horním rohu upravte kanál verze.
1. Vyberte **fázi** pro přidání úlohy. Další informace o fázích najdete [tady](/azure/devops/pipelines/release/environments).
1. **+** Pro tuto úlohu vyberte a pak na kartě **nasazení** přidejte úlohu **push pro konfiguraci aplikace Azure** .
1. Nakonfigurujte potřebné parametry v rámci úlohy, aby se do úložiště konfigurace aplikace navložily hodnoty klíče z konfiguračního souboru. Vysvětlení parametrů jsou k dispozici v níže uvedené části **parametry** a v popisech tlačítek vedle jednotlivých parametrů.
1. Uložte a zařadíte do fronty verzi. V protokolu vydaných verzí se zobrazí všechny chyby zjištěné při spuštění úlohy.

## <a name="parameters"></a>Parametry

Následující parametry používá úloha nabízení konfigurace aplikace:

- **Předplatné Azure**: rozevírací seznam obsahující dostupná připojení služby Azure. Pokud chcete aktualizovat a aktualizovat seznam dostupných připojení služby Azure, klikněte na tlačítko **Aktualizovat předplatné Azure** napravo od textového pole.
- **Název konfigurace aplikace**: rozevírací seznam, který načte vaše dostupná úložiště konfigurace pod vybraným předplatným. Pokud chcete aktualizovat a aktualizovat seznam dostupných úložišť konfigurací, stiskněte tlačítko **aktualizovat název konfigurace aplikace** napravo od textového pole.
- **Cesta ke konfiguračnímu souboru**: cesta ke konfiguračnímu souboru. Můžete procházet artefaktem sestavení a vybrat konfigurační soubor. ( `...` tlačítko napravo od textového pole).
- **Oddělovač**: oddělovač, který se používá k sloučení souborů. JSON a. yml.
- **Hloubka**: Hloubka, na kterou budou soubory. JSON a. yml shrnuté.
- **Prefix**: řetězec, který je připojený k začátku každého klíče, který se odeslal do úložiště konfigurace aplikace.
- **Label**: řetězec, který se přidá do každé hodnoty klíč-hodnota jako popisek v rámci úložiště konfigurace aplikace.
- **Typ obsahu**: řetězec, který se přidá do každého klíč-hodnota jako typ obsahu v rámci úložiště konfigurace aplikace.
- **Tags**: objekt JSON ve formátu `{"tag1":"val1", "tag2":"val2"}` , který definuje značky přidané do každé hodnoty klíč-hodnota vložené do úložiště konfigurace aplikace.
- **Odstraní všechny ostatní Key-Values ve Storu se zadaným prefixem a popiskem**: výchozí hodnota je **nezaškrtnutá**.
  - **Checked**: Odebere všechny klíčové hodnoty v úložišti konfigurace aplikace, které odpovídají zadané předponě a popisku před vložením nových klíčových hodnot z konfiguračního souboru.
  - **Nezaškrtnuto**: vloží všechny klíčové hodnoty z konfiguračního souboru do úložiště konfigurace aplikace a ponechá všechno ostatní v úložišti konfigurace aplikace beze změny.

Po vyplnění požadovaných parametrů spusťte kanál. Všechny klíčové hodnoty v zadaném konfiguračním souboru se nahrají do konfigurace aplikace.

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k neočekávané chybě, můžete povolit protokoly ladění nastavením proměnné kanálu `system.debug` na `true` .

## <a name="faq"></a>Časté otázky

**Jak můžu nahrát víc konfiguračních souborů?**

Vytvořte víc instancí úlohy push App Configuration pro Azure v rámci stejného kanálu, aby se do úložiště konfigurace aplikace vložily víc konfiguračních souborů.

**Proč při pokusu o vložení klíč-hodnoty do úložiště konfigurace dochází k chybě 409?**

Pokud se úloha pokusí odebrat nebo přepsat klíčovou hodnotu, která je uzamčena v úložišti konfigurace aplikace, dojde k chybě 409 zprávy o konfliktu.

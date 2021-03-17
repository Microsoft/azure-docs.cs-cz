---
title: Kurz aktualizace zařízení pro Azure IoT Hub pomocí agenta pro reference na simulátor Ubuntu (18,04 x64) | Microsoft Docs
description: Začínáme s aktualizací zařízení pro Azure IoT Hub pomocí referenčního agenta simulátoru Ubuntu (18,04 x64).
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 4740bf02c33314dd7c887356f2ef1ed12bea44cf
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443807"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Kurz aktualizace zařízení pro Azure IoT Hub pomocí agenta pro reference na simulátor Ubuntu (18,04 x64)

Aktualizace zařízení pro IoT Hub podporuje dvě formy aktualizací – založené na bitových kopiích a na balíčcích.

Aktualizace obrázků poskytují v konečném stavu zařízení vyšší úroveň spolehlivosti. Obvykle je snazší replikovat výsledky aktualizace image mezi předprodukčním prostředím a produkčním prostředím, protože nepředstavuje stejné výzvy jako balíčky a jejich závislosti. Z důvodu atomické povahy může jeden model převzetí služeb při selhání A/B také snadno přijmout.

Tento kurz vás provede kroky k dokončení kompletní aktualizace na základě bitové kopie pomocí aktualizace zařízení IoT Hub. 

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Stáhnout a nainstalovat image
> * Přidání značky do zařízení IoT
> * Importovat aktualizaci
> * Vytvoření skupiny zařízení
> * Nasazení aktualizace image
> * Monitorování nasazení aktualizace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* Přístup k IoT Hub. Doporučuje se použít úroveň S1 (Standard) nebo vyšší.

### <a name="download-and-install"></a>Stažení a instalace

* Rutiny AZ (Azure CLI) pro PowerShell:
  * Otevřete PowerShell > nainstalujte rozhraní příkazového řádku Azure CLI ("Y", kde se zobrazí výzvy k instalaci ze zdroje "nedůvěryhodné").

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>Povolení WSL na zařízení s Windows (subsystém Windows pro Linux)

1. Otevřete na svém počítači PowerShell jako správce a spusťte následující příkaz (může se po každém kroku zobrazit výzva, abyste počítač restartovali; po zobrazení výzvy restartujte):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(Po *dokončení tohoto kroku se může zobrazit výzva k restartování*)

2. Na webu otevřete Microsoft Store a nainstalujte [Ubuntu 18,04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`).

3. Spusťte "Ubuntu 18,04 LTS" a nainstalujte.

4. Po instalaci se zobrazí výzva k nastavení názvu kořenového adresáře (Username) a hesla. Nezapomeňte použít heslo k zapamatovatelné kořenovému názvu.

5. V prostředí PowerShell spusťte následující příkaz, který nastaví Ubuntu jako výchozí distribuci systému Linux:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Vypíše všechny distribuce systému Linux, ujistěte se, že Ubuntu je výchozí.

```powershell
PS> wsl --list
```

7. Měli byste vidět: **Ubuntu-18,04 (výchozí)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Stáhnout referenčního agenta simulátoru Ubuntu aktualizace zařízení (18,04 x64)

Image aktualizace Ubuntu se dá stáhnout z části *assets (prostředky* ) z [poznámky k](https://github.com/Azure/iot-hub-device-update/releases)verzi.

Existují dvě verze agenta. Pokud používáte scénář na základě bitové kopie, použijte AducIotAgentSim-Microsoft-SWUpdate a pokud vykonáváte scénář založený na balíčku, použijte AducIotAgentSim-Microsoft-apt.

## <a name="install-device-update-agent-simulator"></a>Nainstalovat simulátor agenta aktualizace zařízení

1. Spusťte Ubuntu WSL a zadejte následující příkaz (Všimněte si, že je mezera a tečka na konci).

  ```shell
  explorer.exe .
  ```

2. Zkopírujte AducIotAgentSim-Microsoft-SWUpdate (nebo AducIotAgentSim-Microsoft-apt) z místní složky, kde byla stažena v/mnt, do domovské složky v WSL.

3. Spusťte následující příkaz, který nastaví spustitelný soubor binárních souborů.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  nebo

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Aktualizace zařízení pro software Azure IoT Hub podléhá následujícím licenčním podmínkám:
   * [Aktualizace zařízení pro IoT Hub licenci](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licence klienta Optimalizace doručení](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Přečtěte si licenčních podmínek před použitím agenta. Vaše instalace a použití představují Vaše přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte aktualizaci zařízení pro agenta IoT Hub.

## <a name="add-device-to-azure-iot-hub"></a>Přidat zařízení do Azure IoT Hub

Jakmile je na zařízení IoT spuštěný agent aktualizace zařízení, musí se zařízení přidat do IoT Hub Azure.  V rámci Azure IoT Hub se připojovací řetězec vygeneruje pro konkrétní zařízení.

1. Z Azure Portal spusťte IoT Hub aktualizace zařízení.
2. Vytvoří nové zařízení.
3. Na levé straně stránky přejděte do ' Explorers ' > ' zařízení IoT ' > vyberte možnost "nové".
4. Zadejte název zařízení v části ID zařízení – Ujistěte se, že je zaškrtnuté políčko vygenerovat klíče.
5. Vyberte Save (Uložit).
6. Teď se vrátíte na stránku zařízení a zařízení, které jste vytvořili, by mělo být v seznamu. Vyberte toto zařízení.
7. V zobrazení zařízení vyberte ikonu Kopírovat vedle řetězce primární připojovací řetězec.
8. Zkopírované znaky vložte někam pro pozdější použití v následujících krocích. **Tento zkopírovaný řetězec je připojovací řetězec zařízení**.

## <a name="add-connection-string-to-simulator"></a>Přidat připojovací řetězec do simulátoru

Spusťte na nových softwarových zařízeních agenta aktualizace zařízení.

1. Spusťte Ubuntu.
2. Spusťte agenta aktualizace zařízení a zadejte připojovací řetězec zařízení z předchozí části zabalený pomocí apostrofů:

Nahraďte `<device connection string>` připojovacím řetězcem.
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

nebo

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Posuňte se nahoru a vyhledejte řetězec označující, že zařízení je ve stavu nečinnosti. Stav "nečinný" znamená, že zařízení je připravené pro příkazy služby:

```markdown
Agent running. [main]
```

## <a name="add-a-tag-to-your-device"></a>Přidání značky do zařízení

1. Přihlaste se [Azure Portal](https://portal.azure.com) a přejděte do IoT Hub.

2. V levém navigačním podokně v části zařízení IoT nebo IoT Edge Najděte své zařízení IoT a přejděte k zařízení s dvojitou platností.

3. V případě, že je zařízení s dvojitou hodnotou, odstraňte všechny existující hodnoty značky aktualizace zařízení nastavením na hodnotu null.

4. Přidejte novou hodnotu značky aktualizace zařízení, jak je uvedeno níže.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importovat aktualizaci

1. Podle těchto [pokynů](import-update.md)vytvořte manifest importu.
2. V levém navigačním panelu vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

3. Vyberte kartu aktualizace.

4. Vyberte + Importovat novou aktualizaci.

5. V části vyberte soubor manifestu pro import vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte manifest importu, který jste vytvořili výše.  Potom v části vyberte jeden nebo více souborů aktualizace vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte bitovou kopii aktualizace Ubuntu, kterou jste si stáhli dříve. 

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Snímek obrazovky znázorňující výběr aktualizačního souboru" lightbox="media/import-update/select-update-files.png":::

6. V části vyberte kontejner úložiště vyberte ikonu složky nebo textové pole. Pak vyberte příslušný účet úložiště.

7. Pokud jste už kontejner vytvořili, můžete ho znovu použít. (Jinak vyberte + kontejner a vytvořte nový kontejner úložiště pro aktualizace.).  Vyberte kontejner, který chcete použít, a klikněte na vybrat.
  
  :::image type="content" source="media/import-update/container.png" alt-text="Snímek obrazovky znázorňující výběr kontejneru" lightbox="media/import-update/container.png":::

8. Kliknutím na Odeslat spusťte proces importu.

9. Spustí se proces importu a obrazovka se změní v části Historie importu. Pokud chcete zobrazit průběh až do dokončení procesu importu, vyberte aktualizovat. V závislosti na velikosti aktualizace to může trvat několik minut, ale může trvat delší dobu.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Snímek obrazovky znázorňující sekvenci importu aktualizace" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Když sloupec status (stav) indikuje, že import proběhl úspěšně, vyberte hlavičku připraveno k nasazení. V seznamu by se teď měla zobrazit vaše importovaná aktualizace.

[Přečtěte si další informace](import-update.md) o importu aktualizací.

## <a name="create-update-group"></a>Vytvořit skupinu aktualizací

1. Přejít na IoT Hub, kterou jste dříve připojili k instanci aktualizace zařízení.

2. V levém navigačním panelu vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

3. V horní části stránky vyberte kartu skupiny. 

4. Kliknutím na tlačítko Přidat vytvořte novou skupinu.

5. V seznamu vyberte značku IoT Hub, kterou jste vytvořili v předchozím kroku. Vyberte vytvořit skupinu aktualizací.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Snímek obrazovky znázorňující výběr značky" lightbox="media/create-update-group/select-tag.PNG":::

[Další informace](create-update-group.md) o přidávání značek a vytváření skupin aktualizací


## <a name="deploy-update"></a>Nasazení aktualizace

1. Po vytvoření skupiny byste měli vidět novou aktualizaci dostupnou pro skupinu zařízení s odkazem na aktualizaci v části čekají na aktualizace. Možná budete muset aktualizaci jednou aktualizovat. 

2. Klikněte na dostupnou aktualizaci.

3. Potvrďte, že je jako cílová skupina vybraná správná skupina. Naplánujte nasazení a pak vyberte nasadit aktualizaci.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Vybrat aktualizaci" lightbox="media/deploy-update/select-update.png":::

4. Zobrazení grafu dodržování předpisů. Měla by se zobrazit, že aktualizace probíhá. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Probíhá aktualizace." lightbox="media/deploy-update/update-in-progress.png":::

5. Po úspěšné aktualizaci zařízení by se měl zobrazit graf dodržování předpisů a aktualizace podrobností nasazení, aby odrážely stejnou hodnotu. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Úspěšná aktualizace" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorování nasazení aktualizací

1. V horní části stránky vyberte kartu nasazení.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta nasazení" lightbox="media/deploy-update/deployments-tab.png":::

2. Vyberte nasazení, které jste vytvořili pro zobrazení podrobností o nasazení.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Podrobnosti o nasazení" lightbox="media/deploy-update/deployment-details.png":::

3. Pokud chcete zobrazit nejnovější podrobnosti o stavu, vyberte aktualizovat. Pokračujte v tomto procesu, dokud se stav nezmění na úspěšné.

Nyní jste dokončili úspěšnou aktualizaci kompletních imagí na konci pomocí aktualizace zařízení IoT Hub pomocí referenčního agenta simulátoru pro Ubuntu (18,04 x64).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, vyčistěte účet aktualizace zařízení, instanci IoT Hub a zařízení IoT. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Řešení potíží](troubleshoot-device-update.md)


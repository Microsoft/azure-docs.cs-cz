---
title: Kurz aktualizace zařízení pro Azure IoT Hub pomocí agenta balíčku Ubuntu Server 18,04 x64 | Microsoft Docs
description: Začínáme s aktualizací zařízení pro Azure IoT Hub pomocí agenta balíčku Ubuntu Server 18,04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 9468b3b53e0f7c435bf84b6ef99eb1e0f85d0c8e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560263"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Kurz aktualizace zařízení pro Azure IoT Hub pomocí agenta balíčku na serveru Ubuntu 18,04 x64

Aktualizace zařízení pro IoT Hub podporuje dvě formy aktualizací – založené na bitových kopiích a na balíčcích.

Aktualizace založené na balíčku jsou cílené aktualizace, které mění pouze konkrétní součást nebo aplikaci na zařízení. To vede ke snížení spotřeby šířky pásma a pomáhá zkrátit dobu potřebné ke stažení a instalaci aktualizace. Aktualizace balíčků obvykle umožňují méně výpadkům zařízení při použití aktualizace a vyhněte se režii při vytváření imagí.

Tento kurz vás provede kroky k dokončení kompletní aktualizace založené na balíčku prostřednictvím aktualizace zařízení pro IoT Hub. V tomto kurzu použijeme Ubuntu Server 18,04 x64 se spuštěným Azure IoT Edge a agentem balíčku aktualizace zařízení. V tomto kurzu se dozvíte, jak aktualizovat ukázkový balíček, ale s podobnými kroky jste mohli aktualizovat další balíčky, jako je Azure IoT Edge nebo kontejnerový modul, který používá.

Nástroje a koncepty v tomto kurzu se pořád použijí i v případě, že plánujete použít jinou konfiguraci platformy operačního systému. Dokončete tento Úvod do kompletního procesu aktualizace a pak zvolte preferovaný způsob aktualizace a platformy operačního systému tak, aby se podrobně na podrobnosti.

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Stažení a instalace agenta aktualizace zařízení a jeho závislostí
> * Přidání značky do zařízení
> * Importovat aktualizaci
> * Vytvoření skupiny zařízení
> * Nasazení aktualizace balíčku
> * Monitorování nasazení aktualizace

## <a name="prerequisites"></a>Požadavky

* Pokud jste to ještě neudělali, vytvořte [účet a instanci aktualizace zařízení](create-device-update-account.md), včetně konfigurace IoT Hub.
* [Připojovací řetězec pro zařízení IoT Edge](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Příprava zařízení
### <a name="using-the-automated-deploy-to-azure-button"></a>Použití tlačítka pro automatizované nasazení do Azure

Pro usnadnění práce v tomto kurzu se používá [šablona Azure Resource Manager](../azure-resource-manager/templates/overview.md) založená na [cloudu](../virtual-machines/linux/using-cloud-init.md), která vám pomůže rychle nastavit virtuální počítač s Ubuntu 18,04 LTS. Nainstaluje Azure IoT Edge runtime i agenta balíčku aktualizace zařízení a pak automaticky nakonfiguruje zařízení pomocí informací o zřizování pomocí připojovacího řetězce zařízení pro IoT Edge zařízení (předpoklad), které zadáte. Tím předejdete nutnosti spustit relaci SSH, aby bylo možné instalaci dokončit.

1. Začněte tím, že kliknete na tlačítko níže:

   [![Tlačítko Nasazení do Azure pro iotedge-vm-deploy](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2F1.2.0-rc4%2FedgeDeploy.json)

1. V nově otevřeném okně vyplňte dostupná pole formuláře:

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky zobrazující šablonu iotedge-vm-deploy](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Předplatné**: aktivní předplatné Azure, do kterého se má virtuální počítač nasadit.

    **Skupina prostředků**: existující nebo nově vytvořená skupina prostředků, která bude obsahovat virtuální počítač a jeho přidružené prostředky.

    **Předpona popisku DNS**: požadovaná hodnota, která se používá k vytvoření předpony názvu hostitele virtuálního počítače.

    **Uživatelské jméno správce**: uživatelské jméno, které bude poskytovat oprávnění root pro nasazení.

    **Připojovací řetězec zařízení**: [připojovací řetězec zařízení](../iot-edge/how-to-register-device.md) pro zařízení, které bylo vytvořeno v rámci zamýšleného [IoT Hub](../iot-hub/about-iot-hub.md).

    **Velikost virtuálního** počítače: [Velikost](../cloud-services/cloud-services-sizes-specs.md) virtuálního počítače, který se má nasadit.

    **Verze OS Ubuntu**: verze Ubuntu operačního systému, která se má nainstalovat na základní virtuální počítač. Ponechte výchozí hodnotu beze změny, protože bude nastavená na Ubuntu 18,04-LTS už.

    **Umístění**: [geografická oblast](https://azure.microsoft.com/global-infrastructure/locations/) , do které se má virtuální počítač nasadit, tato hodnota se nastaví jako výchozí umístění vybrané skupiny prostředků.

    **Typ ověřování**: v závislosti na vaší preferenci vyberte **sshPublicKey** nebo **Password** .

    **Heslo nebo klíč správce**: hodnota veřejného klíče SSH nebo hodnota hesla v závislosti na zvoleném typu ověřování.

    Po vyplnění všech polí zaškrtněte políčko v dolní části stránky, aby se podmínky přijaly, a výběrem **koupit** zahájíte nasazení.

1. Ověřte, že se nasazení úspěšně dokončilo. Po dokončení nasazení počkejte několik minut, než se dokončí instalace a konfigurace, aby se dokončila instalace IoT Edge a Agent aktualizace balíčku zařízení.

   Do vybrané skupiny prostředků by měl být nasazený prostředek virtuálního počítače.  Poznamenejte si název počítače, který by měl být ve formátu `vm-0000000000000` . Dále si poznamenejte související **název DNS**, který by měl být ve formátu `<dnsLabelPrefix>`.`<location>`. cloudapp.azure.com.

    **Název DNS** můžete získat v části **Přehled** nově nasazeného virtuálního počítače na webu Azure Portal.

    > [!div class="mx-imgBorder"]
    > [![Snímek obrazovky zobrazující název DNS virtuálního počítače s iotedge](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Pokud chcete k tomuto virtuálnímu počítači po instalaci použít SSH, použijte k tomu přidružený **název DNS** pomocí příkazu: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Volitelné Ruční příprava zařízení
Následující ruční kroky pro instalaci a konfiguraci zařízení jsou stejné jako ty, které byly automatizovány tímto [skriptem Cloud-init](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt). Dají se použít k přípravě fyzického zařízení.

1. Podle pokynů [nainstalujte modul runtime Azure IoT Edge](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true).
   > [!NOTE]
   > Agent balíčku aktualizace zařízení nezávisí na IoT Edge. Ale spoléhá na to, že démon služby IoT identity, který je nainstalovaný s IoT Edge (1.2.0 a novější), získá identitu a připojí se k IoT Hub.
   >
   > I když tento kurz není popsaný v tomto kurzu, je [možné démona služby IoT identity nainstalovat samostatně na zařízeních IoT](https://azure.github.io/iot-identity-service/packaging.html)se systémem Linux. Pořadí instalace. Agent balíčku aktualizace zařízení musí být nainstalovaný _po_ službě IoT identity. V opačném případě agent balíčku nebude registrován jako autorizovaná komponenta pro navázání připojení k IoT Hub.

1. Pak nainstalujte balíčky Device Update Agent. deb.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Aktualizace zařízení pro softwarové balíčky Azure IoT Hub podléhají následujícím licenčním podmínkám:
  * [Aktualizace zařízení pro IoT Hub licenci](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Licence klienta Optimalizace doručení](https://github.com/microsoft/do-client/blob/main/LICENSE)

Přečtěte si licenčních podmínek před použitím balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte tento balíček.

## <a name="add-a-tag-to-your-device"></a>Přidání značky do zařízení

1. Přihlaste se [Azure Portal](https://portal.azure.com) a přejděte do IoT Hub.

2. Z ' IoT Edge ' v levém navigačním podokně Najděte své zařízení IoT Edge a přejděte do vlákna zařízení.

3. V případě, že je zařízení s dvojitou hodnotou, odstraňte všechny existující hodnoty značky aktualizace zařízení nastavením na hodnotu null.

4. Přidejte novou hodnotu značky aktualizace zařízení, jak je uvedeno níže.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Importovat aktualizaci

1. Přejděte na vydaná [vydání aktualizací zařízení](https://github.com/Azure/iot-hub-device-update/releases) na GitHubu a klikněte na rozevírací seznam "prostředky".

3. `apt-update-import-samples.zip`Kliknutím na něj si stáhněte.

5. Extrahujte obsah složky, abyste zjistili různé ukázky aktualizací a jejich odpovídající manifesty importu. 

2. V Azure Portal na levém navigačním panelu v IoT Hub vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

3. Vyberte kartu aktualizace.

4. Vyberte + Importovat novou aktualizaci.

5. V části vyberte soubor manifestu pro import vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte `sample-package-update-1.0.1-importManifest.json` importovat manifest ze složky, kterou jste si dříve stáhli. Potom v části vyberte jeden nebo více souborů aktualizace vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte `sample-1.0.1-libcurl4-doc-apt-manifest.json` soubor aktualizace manifestu apt ze složky, kterou jste si dříve stáhli.
Tato aktualizace nainstaluje nejnovější dostupnou verzi nástroje `libcurl4-doc package` do zařízení.

   Alternativně můžete vybrat soubor `sample-package-update-2-2.0.1-importManifest.json` importu manifestu a `sample-2.0.1-libcurl4-doc-7.58-apt-manifest.json` soubor aktualizace manifestu apt ze složky, kterou jste předtím stáhli. Tím se do zařízení nainstaluje konkrétní verze v 7.58.0 `libcurl4-doc package` .

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

1. V levém navigačním panelu vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

1. V horní části stránky vyberte kartu skupiny.

1. Kliknutím na tlačítko Přidat vytvořte novou skupinu.

1. V seznamu vyberte značku IoT Hub, kterou jste vytvořili v předchozím kroku. Vyberte vytvořit skupinu aktualizací.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Snímek obrazovky znázorňující výběr značky" lightbox="media/create-update-group/select-tag.PNG":::

[Další informace](create-update-group.md) o přidávání značek a vytváření skupin aktualizací

## <a name="deploy-update"></a>Nasazení aktualizace

1. Po vytvoření skupiny byste měli vidět novou aktualizaci dostupnou pro skupinu zařízení s odkazem na aktualizaci ve sloupci _dostupné aktualizace_ . Možná budete muset aktualizaci jednou aktualizovat.

1. Klikněte na odkaz k dostupné aktualizaci.

1. Potvrďte, že je vybrána správná skupina jako cílová skupina a naplánujte nasazení.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Vybrat aktualizaci" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Ve výchozím nastavení je datum/čas spuštění 24 hodin od aktuálního času. Pokud chcete, aby nasazení začalo dříve, nezapomeňte vybrat jiné datum a čas.

1. Vyberte nasadit aktualizaci.

1. Zobrazení grafu dodržování předpisů. Měla by se zobrazit, že aktualizace probíhá. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Probíhá aktualizace." lightbox="media/deploy-update/update-in-progress.png":::

1. Po úspěšné aktualizaci zařízení by se měl zobrazit graf dodržování předpisů a aktualizace podrobností nasazení, aby odrážely stejnou hodnotu. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Úspěšná aktualizace" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorování nasazení aktualizací

1. V horní části stránky vyberte kartu nasazení.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta nasazení" lightbox="media/deploy-update/deployments-tab.png":::

1. Vyberte nasazení, které jste vytvořili pro zobrazení podrobností o nasazení.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Podrobnosti o nasazení" lightbox="media/deploy-update/deployment-details.png":::

1. Pokud chcete zobrazit nejnovější podrobnosti o stavu, vyberte aktualizovat. Pokračujte v tomto procesu, dokud se stav nezmění na úspěšné.

Nyní jste dokončili úspěšnou kompletní aktualizaci balíčku pomocí aktualizace zařízení pro IoT Hub na zařízení s platformou Ubuntu Server 18,04 x64. 

## <a name="bonus-steps"></a>Bonusové kroky

1. Opakujte oddíly import aktualizace a nasadit aktualizaci.

3. Během kroku import aktualizace vyberte `sample-package-update-1.0.2-importManifest.json` importovat soubor manifestu a `sample-1.0.2-libcurl4-doc-remove-apt-manifest.json` soubor aktualizace manifestu apt ze složky, kterou jste si dříve stáhli. Tato aktualizace odebere `libcurl4-doc package` z vašeho zařízení instalaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, vyčistěte účet aktualizace zařízení, instanci IoT Hub a zařízení IoT Edge (Pokud jste virtuální počítač vytvořili přes tlačítko nasadit do Azure). Můžete to udělat tak, že na každý jednotlivý prostředek kliknete a vyberete odstranit. Nezapomeňte, že před vymazáním účtu aktualizace zařízení je třeba vyčistit instanci aktualizace zařízení.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz aktualizace image na Maline 3 B +](device-update-raspberry-pi.md)

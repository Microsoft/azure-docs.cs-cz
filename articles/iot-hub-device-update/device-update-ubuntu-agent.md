---
title: Kurz aktualizace zařízení pro Azure IoT Hub pomocí agenta balíčku Ubuntu Server 18,04 x64 | Microsoft Docs
description: Začínáme s aktualizací zařízení pro Azure IoT Hub pomocí agenta balíčku Ubuntu Server 18,04 x64.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664696"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Kurz aktualizace zařízení pro Azure IoT Hub pomocí agenta balíčku Ubuntu Server 18,04 x64

Aktualizace zařízení pro IoT Hub podporuje dvě formy aktualizací – založené na bitových kopiích a na balíčcích.

Aktualizace založené na balíčku jsou cílené aktualizace, které mění pouze konkrétní součást nebo aplikaci na zařízení. To vede ke snížení spotřeby šířky pásma a pomáhá zkrátit dobu potřebné ke stažení a instalaci aktualizace. Aktualizace balíčků obvykle umožňují méně výpadkům zařízení při použití aktualizace a vyhněte se režii při vytváření imagí.

Tento kurz vás provede kroky k dokončení kompletní aktualizace založené na balíčku prostřednictvím aktualizace zařízení pro IoT Hub. Pro tento kurz použijeme ukázkového agenta balíčku pro Ubuntu Server 18,04 x64. I když plánujete použití jiné konfigurace platformy operačního systému, v tomto kurzu se dozvíte i informace o nástrojích a konceptech v části aktualizace zařízení pro IoT Hub. Dokončete tento Úvod do kompletního procesu aktualizace a pak zvolte preferovaný způsob aktualizace a platformy operačního systému tak, aby se podrobně na podrobnosti. Pomocí aktualizace zařízení IoT Hub můžete v tomto kurzu aktualizovat zařízení Azure IoT nebo Azure IoT Edge. 

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Konfigurovat úložiště balíčků aktualizací zařízení
> * Stažení a instalace agenta aktualizací zařízení a jeho závislostí
> * Přidání značky do zařízení IoT
> * Importovat aktualizaci
> * Vytvoření skupiny zařízení
> * Nasazení aktualizace balíčku
> * Monitorování nasazení aktualizace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* Přístup k IoT Hub. Doporučuje se použít úroveň S1 (Standard) nebo vyšší.
* Zařízení Azure IoT nebo Azure IoT Edge s Ubuntu serverem 18,04 x64, které je připojené k IoT Hub.
   * Pokud používáte zařízení Azure IoT Edge, ujistěte se, že je v 1.2.0u na hraničním modulu runtime nebo vyšším. 
* Pokud nepoužíváte zařízení Azure IoT Edge, [nainstalujte si nejnovější `aziot-identity-service` balíček (Preview) do zařízení IoT](https://github.com/Azure/iot-identity-service/actions/runs/575919358) . 
* [Účet aktualizace zařízení a instance propojená se stejným IoT Hub, jak je uvedeno výše.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Konfigurovat úložiště balíčků aktualizací zařízení

1. Nainstalujte konfiguraci úložiště, která odpovídá operačnímu systému vašeho zařízení. V tomto kurzu to bude Ubuntu Server 18,04. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Zkopírujte vygenerovaný seznam do adresáře sources. list. d.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Nainstalujte veřejný klíč Microsoft GPG.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Nainstalovat balíčky agenta Update. deb

1. Aktualizace seznamů balíčků na zařízení

   ```shell
      sudo apt-get update
   ```

2. Instalace balíčku deviceupdate-agent a jeho závislostí

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Aktualizace zařízení pro softwarové balíčky Azure IoT Hub podléhají následujícím licenčním podmínkám:
   * [Aktualizace zařízení pro IoT Hub licenci](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licence klienta Optimalizace doručení](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Přečtěte si licenčních podmínek před použitím balíčku. Vaše instalace a používání balíčku znamená přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte tento balíček.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Konfigurace agenta aktualizace zařízení pomocí služby Azure IoT identity Service (Preview)

Po instalaci požadovaných balíčků musíte zařízení zřídit s jeho identitou v cloudu a ověřovacími informacemi.

1. Otevření konfiguračního souboru

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. Vyhledejte část konfigurace zřizování souboru. Odkomentujte oddíl ručního zřizování pomocí připojovacího řetězce. Aktualizujte hodnotu connection_string připojovacím řetězcem pro vaše zařízení IoT (Edge). Zajistěte, aby byly všechny ostatní oddíly pro zřizování zakomentovány.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Uložte a zavřete soubor pomocí kláves CTRL + X, Y.

4. Použijte konfiguraci. 

   Pokud používáte zařízení IoT Edge, použijte následující příkaz. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Pokud používáte zařízení IoT s `aziot-identity-service` nainstalovaným balíčkem, použijte následující příkaz. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Volitelně můžete ověřit, že služby běží na

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    Výstup by měl číst:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
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

1. Stáhněte si následující [soubor manifestu apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) a [importujte soubor manifestu](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). Tento manifest apt nainstaluje nejnovější dostupnou verzi nástroje `libcurl4-doc package` do zařízení IoT. 

   Alternativně můžete stáhnout tento [soubor manifestu apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) a [importovat soubor manifestu](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Tím se `libcurl4-doc package` do vašeho zařízení IoT nainstaluje konkrétní verze v 7.58.0. 

2. V Azure Portal na levém navigačním panelu v IoT Hub vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

3. Vyberte kartu aktualizace.

4. Vyberte + Importovat novou aktualizaci.

5. V části vyberte soubor manifestu pro import vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte importovaný manifest, který jste si dříve stáhli. Potom v části vyberte jeden nebo více souborů aktualizace vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte soubor aktualizace manifestu apt, který jste předtím stáhli.
   
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

Nyní jste dokončili úspěšnou kompletní aktualizaci balíčku pomocí aktualizace zařízení pro IoT Hub na zařízení s platformou Ubuntu Server 18,04 x64. 

## <a name="bonus-steps"></a>Bonusové kroky

1. Stáhněte si následující [soubor manifestu apt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) a [importujte soubor manifestu](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Tento manifest apt odebere instalaci `libcurl4-doc package` ze zařízení IoT. 

2. Opakujte oddíly import aktualizace a nasadit aktualizaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, vyčistěte účet aktualizace zařízení, instanci IoT Hub a zařízení IoT. Můžete to udělat tak, že na každý jednotlivý prostředek kliknete a vyberete odstranit. Nezapomeňte, že před vymazáním účtu aktualizace zařízení je třeba vyčistit instanci aktualizace zařízení. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz aktualizace image na Maline 3 B +](device-update-raspberry-pi.md)


---
title: Import nové aktualizace | Microsoft Docs
description: Průvodce How-To pro import nové aktualizace do IoT Hub aktualizace zařízení pro IoT Hub.
author: andbrown
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: d8757f3076f784576f95bbdfc30abf578446c776
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662517"
---
# <a name="import-new-update"></a>Importovat novou aktualizaci
Přečtěte si, jak naimportovat novou aktualizaci do aktualizace zařízení pro IoT Hub.

## <a name="prerequisites"></a>Požadavky

* [Pro IoT Hub povolený přístup k IoT Hub s aktualizací zařízení](create-device-update-account.md). Doporučujeme pro IoT Hub použít úroveň S1 (Standard) nebo vyšší. 
* Zařízení IoT (nebo simulátor) zřízené pro aktualizaci zařízení v IoT Hub.
   * Pokud používáte reálné zařízení, budete potřebovat soubor s obrázkem aktualizace pro aktualizaci Image nebo [soubor manifestu apt](device-update-apt-manifest.md) pro aktualizaci balíčku.
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) nebo novější.
* Podporované prohlížeče:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Některá data odeslaná do této služby mohou být zpracována v oblasti mimo oblast, ve které byla tato instance vytvořena.

## <a name="create-device-update-import-manifest"></a>Vytvořit manifest pro import aktualizace zařízení

1. Ujistěte se, že soubor s obrázkem aktualizace nebo soubor manifestu APT je umístěný v adresáři přístupném z PowerShellu.

2. Naklonujte [aktualizaci zařízení pro úložiště IoT Hub](https://github.com/azure/iot-hub-device-update)nebo si ho stáhněte jako soubor. zip do umístění přístupného z PowerShellu (po stažení souboru ZIP klikněte pravým tlačítkem na `Properties`  >  `General` kartu > zaškrtněte `Unblock` v `Security` části, abyste se vyhnuli zobrazování výzev pro upozornění na zabezpečení PowerShellu).

3. V PowerShellu přejděte do `tools/AduCmdlets` adresáře a spusťte:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Spusťte následující příkazy nahrazením ukázkových hodnot parametrů pro vygenerování manifestu importu, souboru JSON, který popisuje aktualizaci:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Tady jsou některé příklady hodnot výše uvedených parametrů pro rychlé odkazy. Úplnou dokumentaci najdete v úplném schématu importu manifestu níže.

    | Parametr | Popis |
    | --------- | ----------- |
    | deviceManufacturer | Výrobce zařízení, ke kterému je aktualizace kompatibilní, například contoso
    | deviceModel | Model zařízení, ke kterému je aktualizace kompatibilní, například informační zpráva
    | updateProvider | Část poskytovatele identity aktualizace, například Fabrikam
    | aktualizovat | Název součásti identity aktualizace, například ImageUpdate
    | updateVersion | Verze aktualizace, například 2,0
    | Typ aktualizace | <ul><li>Zadat `microsoft/swupdate:1` pro aktualizaci obrázku</li><li>Zadat `microsoft/apt:1` pro aktualizaci balíčku</li></ul>
    | installedCriteria | <ul><li>Zadejte hodnotu SWVersion pro `microsoft/swupdate:1` typ aktualizace.</li><li>Zadejte doporučenou hodnotu pro `microsoft/apt:1` typ aktualizace.
    | updateFilePath (celkem) | Cesta k souborům aktualizací v počítači

    Schéma manifestu úplného importu

    | Název | Typ | Popis | Omezení |
    | --------- | --------- | --------- | --------- |
    | UpdateID naformátovat | `UpdateId` předmětů | Aktualizujte identitu. |
    | Typ aktualizace | řetězec | Typ aktualizace: <ul><li>Určete `microsoft/apt:1` , kdy se má při provádění aktualizace na základě balíčku použít Agent pro reference.</li><li>Určete `microsoft/swupdate:1` , kdy se provádí aktualizace na základě bitové kopie pomocí referenčního agenta.</li><li>Určete `microsoft/simulator:1` , kdy se má používat simulátor ukázkového agenta.</li><li>Při vývoji vlastního agenta zadejte vlastní typ.</li></ul> | <ul><li>Formátovat `{provider}/{type}:{typeVersion}`</li><li>Maximálně 32 znaků celkem</li></ul> |
    | InstalledCriteria | řetězec | Řetězec, který je interpretován agentem k určení, jestli se aktualizace úspěšně použila:  <ul><li>Zadejte **hodnotu** SWVersion pro typ aktualizace `microsoft/swupdate:1` .</li><li>Zadejte `{name}-{version}` pro typ aktualizace `microsoft/apt:1` , ze kterého se získá název a verze ze souboru apt.</li><li>Zadejte hodnotu hash souboru aktualizace pro typ aktualizace `microsoft/simulator:1` .</li><li>Při vývoji vlastního agenta zadejte vlastní řetězec.</li></ul> | Maximálně 64 znaků |
    | Kompatibilita | Pole `CompatibilityInfo` objektů | Informace o kompatibilitě zařízení kompatibilního s touto aktualizací. | Maximálně 10 položek |
    | CreatedDateTime | Datum a čas | Datum a čas, kdy byla aktualizace vytvořena. | Formát data a času s oddělovači ISO 8601, v UTC |
    | ManifestVersion | řetězec | Importovat verzi schématu manifestu. Zadejte `2.0` , který bude kompatibilní s `urn:azureiot:AzureDeviceUpdateCore:1` rozhraním a rozhraním `urn:azureiot:AzureDeviceUpdateCore:4` .</li></ul> | Musí být `2.0` |
    | Soubory | Pole `File` objektů | Aktualizovat soubory datové části | Maximálně 5 souborů |

Poznámka: všechna pole jsou povinná.

## <a name="review-generated-import-manifest"></a>Zkontrolovat vygenerovaný manifest importu

Příklad:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Importovat aktualizaci

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte k IoT Hub pomocí aktualizace zařízení.

2. Na levé straně stránky vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importovat aktualizace" lightbox="media/import-update/import-updates-3.png":::

3. V horní části obrazovky se zobrazí několik karet. Vyberte kartu aktualizace.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Aktualizace" lightbox="media/import-update/updates-tab.png":::

4. Pod hlavičkou "připraveno k nasazení" vyberte + Importovat novou aktualizaci.

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importovat novou aktualizaci" lightbox="media/import-update/import-new-update-2.png":::

5. V části vyberte soubor manifestu pro import vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte importovaný manifest, který jste dříve vytvořili pomocí rutiny prostředí PowerShell. Potom v části vyberte jeden nebo více souborů aktualizace vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte soubory aktualizace.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Vybrat soubory aktualizace" lightbox="media/import-update/select-update-files.png":::

6. V části vyberte kontejner úložiště vyberte ikonu složky nebo textové pole. Pak vyberte příslušný účet úložiště. Kontejner úložiště se používá k dočasnému přípravě souborů aktualizace.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Účet úložiště" lightbox="media/import-update/storage-account.png":::

7. Pokud jste už kontejner vytvořili, můžete ho znovu použít. (Jinak vyberte + kontejner a vytvořte nový kontejner úložiště pro aktualizace.).  Vyberte kontejner, který chcete použít, a klikněte na vybrat.

   :::image type="content" source="media/import-update/container.png" alt-text="Vybrat kontejner" lightbox="media/import-update/container.png":::

8. Kliknutím na Odeslat spusťte proces importu.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publikování aktualizace" lightbox="media/import-update/publish-update.png":::

9. Spustí se proces importu a obrazovka se přepne do části Historie importu. Pokud chcete zobrazit průběh až do dokončení procesu importu (v závislosti na velikosti aktualizace, může to trvat několik minut, ale může to trvat déle), vyberte aktualizovat.

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Aktualizovat pořadí importu" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Když sloupec status (stav) indikuje, že import proběhl úspěšně, vyberte hlavičku připraveno k nasazení. V seznamu by se teď měla zobrazit vaše importovaná aktualizace.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Stav úlohy" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Další kroky

[Vytváření skupin](create-update-group.md)

[Další informace o konceptech importu](import-concepts.md)

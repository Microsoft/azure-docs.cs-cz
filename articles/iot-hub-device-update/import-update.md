---
title: Postup přidání nové aktualizace | Microsoft Docs
description: Průvodce How-To pro přidání nové aktualizace do aktualizace zařízení pro IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: ebfeee2828b3a36f9cf47891f8aea6d889db85bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763572"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>Přidat aktualizaci do aktualizace zařízení pro IoT Hub
Přečtěte si, jak přidat novou aktualizaci do aktualizace zařízení pro IoT Hub.

## <a name="prerequisites"></a>Požadavky

* [Pro IoT Hub povolený přístup k IoT Hub s aktualizací zařízení](create-device-update-account.md). 
* Zařízení IoT (nebo simulátor) [zřízené pro aktualizaci zařízení](device-update-agent-provisioning.md) v IoT Hub.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) nebo novější (zahrnuje instalace pro Linux, MacOS a Windows)
* Podporované prohlížeče:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Některá data odeslaná do této služby mohou být zpracována v oblasti mimo oblast, ve které byla tato instance vytvořena.

## <a name="obtain-an-update-for-your-devices"></a>Získání aktualizace pro vaše zařízení

Teď, když jste nastavili aktualizace zařízení a zřídili vaše zařízení, budete potřebovat aktualizační soubory, které budete do těchto zařízení nasazovat.

Pokud jste si koupili zařízení od integrátoru OEM nebo některého z integrátoru řešení, bude tato organizace pravděpodobně poskytovat soubory aktualizace za vás, aniž byste museli vytvářet aktualizace. Pokud chcete zjistit, jak jsou aktualizace k dispozici, obraťte se na integrátora OEM nebo řešení.

Pokud už vaše organizace vytvořila software pro zařízení, která používáte, tato skupina bude vytvářet aktualizace pro daný software. Při vytváření aktualizace, která se má nasadit pomocí aktualizace zařízení pro IoT Hub, zahajte buď [přístup na základě bitové kopie, nebo na základě balíčku](understand-device-update.md#support-for-a-wide-range-of-update-artifacts) podle vašeho scénáře. Poznámka: Pokud chcete vytvořit vlastní aktualizace, ale teprve začínáte, je GitHub vhodný způsob, jak spravovat vývoj. Svůj zdrojový kód můžete ukládat a spravovat a provádět průběžnou integraci (CI) a průběžné nasazování (CD) pomocí [akcí GitHubu](https://docs.github.com/en/actions/guides/about-continuous-integration).

## <a name="create-a-device-update-import-manifest"></a>Vytvoření manifestu pro import aktualizace zařízení

Pokud jste to ještě neudělali, nezapomeňte se seznámit se základními [koncepcemi importu](import-concepts.md).

1. Ujistěte se, že vaše soubory aktualizace jsou umístěné v adresáři přístupném z prostředí PowerShell.

2. Vytvořte textový soubor s názvem **AduUpdate. psm1** v adresáři, ve kterém se nachází soubor s obrázkem aktualizace nebo soubor manifestu apt. Pak otevřete rutinu prostředí PowerShell [AduUpdate. psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) , zkopírujte obsah do textového souboru a uložte textový soubor.

3. V PowerShellu přejděte do adresáře, kde jste vytvořili rutinu PowerShellu z kroku 2. Použijte níže uvedenou možnost kopírování a potom vložte do PowerShellu příkazy pro spuštění příkazů:

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

    Tady jsou některé příklady hodnot výše uvedených parametrů pro rychlé odkazy. Další podrobnosti můžete zobrazit také v úplném [schématu importu manifestu](import-schema.md) .

    | Parametr | Popis |
    | --------- | ----------- |
    | deviceManufacturer | Výrobce zařízení: aktualizace je kompatibilní, například contoso. Musí se shodovat s [vlastností zařízení](./device-update-plug-and-play.md#device-properties) _výrobce_ .
    | deviceModel | Model zařízení, ke kterému je aktualizace kompatibilní, například informační zprávy. Musí odpovídat  [vlastnosti zařízení](./device-update-plug-and-play.md#device-properties)modelu.
    | updateProvider | Entita, která vytváří nebo přímo zodpovídá za aktualizaci. Často se jedná o název společnosti.
    | aktualizovat | Identifikátor pro třídu aktualizací. Třída může být cokoliv, co zvolíte. Často se jedná o název zařízení nebo modelu.
    | updateVersion | Číslo verze, které rozlišuje tuto aktualizaci od ostatních, kteří mají stejného poskytovatele a název. Neodpovídá verzi jednotlivého softwarové komponenty v zařízení (ale může se rozhodnout, jestli si zvolíte).
    | Typ aktualizace | <ul><li>Zadat `microsoft/swupdate:1` pro aktualizaci obrázku</li><li>Zadat `microsoft/apt:1` pro aktualizaci balíčku</li></ul>
    | installedCriteria | <ul><li>Zadejte hodnotu SWVersion pro `microsoft/swupdate:1` typ aktualizace.</li><li>Zadejte **název-Version**, kde _název_ je název manifestu apt a _verze_ je verze manifestu apt. Například contoso-IoT-Edge-1.0.0.0.
    | updateFilePath (celkem) | Cesta k souborům aktualizací v počítači


## <a name="review-the-generated-import-manifest"></a>Zkontrolujte vygenerovaný manifest importu.

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

## <a name="import-an-update"></a>Importovat aktualizaci

> [!NOTE]
> Následující pokyny ukazují, jak naimportovat aktualizaci prostřednictvím uživatelského rozhraní Azure Portal. K importu aktualizace můžete použít taky [aktualizaci zařízení IoT Hub API](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) . 

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
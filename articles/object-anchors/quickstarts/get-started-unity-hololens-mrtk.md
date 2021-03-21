---
title: 'Rychlý Start: Vytvoření aplikace pro HoloLens pomocí Unity a MRTK'
description: V tomto rychlém startu se dozvíte, jak pomocí MRTK a ukotvení objektů vytvořit aplikaci Unity pro HoloLens.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049690"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Rychlý Start: Vytvoření aplikace HoloLens pomocí kotev objektů Azure v Unity s MRTK

V tomto rychlém startu vytvoříte aplikaci Unity HoloLens, která používá [kotvy objektů Azure](../overview.md). Kotvy objektů Azure je spravovaná cloudová služba, která převádí 3D prostředky na modely AI, které umožňují objektově pracujícím prostředí HoloLens. Až budete hotovi, budete mít aplikaci HoloLens vytvořenou v Unity, která dokáže detekovat objekty ve fyzickém světě.

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Připravte nastavení sestavení Unity.
> * Exportujte projekt HoloLens sady Visual Studio.
> * Nasaďte aplikaci a spusťte ji na zařízení HoloLens 2.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

V Unity otevřete `quickstarts/apps/unity/mrtk` projekt.

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

Když se v dialogovém okně Import nástroje TMP zobrazí výzva k importu prostředků TextMesh pro, vyberte k tomu možnost importovat základy TMP.
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Import prostředků TextMesh pro":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Zapněte zařízení, vyberte **všechny aplikace** a pak vyhledejte a spusťte aplikaci. Po úvodní obrazovce Unity by se mělo zobrazit bílé ohraničující pole. Můžete použít ruku pro přesun, škálování nebo otočení ohraničovacího rámečku. Umístěte pole k pokrytí objektu, který chcete detekovat.

Otevřete <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">nabídku ruka</a> a vyberte **Zamknout SearchArea** , abyste zabránili dalšímu přesunu ohraničovacího rámečku. Výběrem **možností zahájit hledání** spusťte rozpoznávání objektů. Při zjištění objektu se na objekt vykreslí mřížka. Podrobnosti zjištěné instance se zobrazí na obrazovce, jako je například aktualizované časové razítko a poměr pokrytí povrchu. Vyberte **Zastavit hledání** , aby se sledování zastavilo a odebraly se všechny zjištěné instance.

#### <a name="the-app-menus"></a>Nabídky aplikace

Pomocí <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">nabídky ruka</a>můžete také provádět další akce.

##### <a name="primary-menu"></a>Primární nabídka

* **Zahájit hledání nebo zastavit hledání** – spustí nebo zastaví proces detekce objektu.
* **Přepnout mapování prostorů** – Zobrazit/skrýt vykreslování prostorového mapování Tuto možnost lze použít k ladění, pokud je kontrola dokončena nebo ne.
* **Nastavení nástroje pro sledování** – přepne aktivaci v nabídce nastavení sledování.
* **Nastavení oblasti hledání** – přepne aktivaci v nabídce nastavení oblasti hledání.
* **Spustit trasování** – Zachyťte diagnostická data a uložte je do zařízení. Další podrobnosti najdete v části **problémy s detekcí ladění a zachytit diagnostiku**.
* **Nahrání trasování** – odeslání diagnostických dat do služby ukotvení objektů. Uživatel musí zadat svůj účet `subscription.json` pro předplatné a odeslat ho do `LocalState` složky. Ukázkový `subscription.json` soubor najdete níže.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Primární nabídka Unity":::

##### <a name="tracker-settings-menu"></a>Nabídka nastavení sledování

* **Vysoká přesnost** – experimentální funkce, která slouží k získání přesnější pozice. Povolení této možnosti bude během detekce objektu vyžadovat víc systémových prostředků. Mřížka objektu bude v tomto režimu vykreslena růžovou. Opětovným výběrem tohoto tlačítka přepnete zpět do režimu normálního sledování.
* **Odlehčené svislé zarovnání** – Pokud je povoleno, umožňuje objekt, který se má detekovat v nesvislém úhlu. Užitečné pro detekci objektů na rampách.
* **Povolit změnu měřítka** – umožňuje sledování změnit velikost zjištěného objektu na základě informací o prostředí.
* **Posuvník poměru pokrytí** – upraví poměr povrchových bodů, které se musí shodovat, aby bylo možné zjistit objekt.  Nižší hodnoty umožňují sledování lépe detekovat objekty, které jsou náročné pro senzory HoloLens ke zjištění, jako jsou tmavé objekty nebo vysoce reflektované objekty. Vyšší hodnoty omezí četnost detekce v hodnotě false.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Nabídka pro sledování Unity":::

##### <a name="search-area-settings-menu"></a>Nabídka nastavení oblasti hledání

* **Zamknout oblast vyhledávání** – ohraničovací rámeček zamykací oblasti, aby se zabránilo nechtěnému přesunu rukou.
* **Automaticky upravit oblast vyhledávání** – umožní, aby se oblast hledání při detekci objektu mohla změnit.
* **Cyklická síť** – cykly prostřednictvím vizualizace načtených sítí v oblasti hledání.  Tato možnost může uživatelům pomáhat při zarovnávání vyhledávacího pole, aby bylo obtížné detekovat objekty.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="Nabídka oblasti hledání Unity":::

Příklad `subscription.json` :

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Koncepty: Přehled sady SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy](../faq.md)

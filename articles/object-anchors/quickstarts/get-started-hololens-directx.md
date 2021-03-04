---
title: 'Rychlý Start: Vytvoření aplikace pro HoloLens pomocí rozhraní DirectX'
description: V tomto rychlém startu se dozvíte, jak vytvořit aplikaci HoloLens pomocí kotev objektů.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 7e71719d83426a3444435ed78d0d63a2599a9157
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748550"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Rychlý Start: Vytvoření aplikace HoloLens pomocí kotev objektů Azure v C++/WinRT a DirectX

Tento rychlý Start popisuje, jak vytvořit aplikaci HoloLens pomocí [kotev objektů Azure](../overview.md) v C++/WinRT a DirectX. Kotvy objektů Azure je spravovaná cloudová služba, která převádí 3D prostředky na modely AI, které umožňují objektově pracujícím prostředí HoloLens. Až budete hotovi, budete mít aplikaci HoloLens, která dokáže detekovat objekt a jeho pozice v holografické aplikaci DirectX 11 (univerzální pro Windows).

Dozvíte se, jak provést tyto akce:

> [!div class="checklist"]
> * Vytvoření a navýšení zátěže aplikace HoloLens
> * Zjištění objektu a vizualizace jeho modelu
> * Zachytit diagnostiku modulu runtime

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, ujistěte se, že máte následující:

* Fyzický objekt ve vašem prostředí a jeho 3D model (buď CAD, nebo naskenovaný).
* Počítač se systémem Windows s následujícím nainstalovaným:
  * <a href="https://git-scm.com" target="_blank">Git pro Windows</a>
  * Sada <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> s úlohou **vývoje Univerzální platforma Windows** a komponentou **Windows 10 SDK (10.0.18362.0 nebo novější)**
* Zařízení HoloLens 2, které je aktuální a má povolený [režim pro vývojáře](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio#enabling-developer-mode) .
  * Chcete-li provést aktualizaci na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení** , pokračujte na **aktualizace & zabezpečení** a pak vyberte **Vyhledat aktualizace**.

## <a name="open-the-sample-project"></a>Otevřete vzorový projekt

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Otevřete `quickstarts/apps/directx/DirectXAoaSampleApp.sln` v aplikaci Visual Studio.

Změňte **konfiguraci řešení** na **release**, změňte **platformu řešení** na **ARM64** a vyberte **zařízení** z možností cíle nasazení. Pak Sestavte projekt **AoaSampleApp** kliknutím pravým tlačítkem myši na projekt a výběrem možnosti **sestavit**.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Konfigurace projektu sady Visual Studio pro nasazení":::

## <a name="deploy-the-app-to-hololens"></a>Nasazení aplikace do HoloLens

Po úspěšném kompilování ukázkového projektu můžete aplikaci nasadit do HoloLens.

Zapněte zařízení HoloLens, přihlaste se a připojte ho k počítači pomocí kabelu USB. Ujistěte se, že je **zařízení** vybraným cílem nasazení (viz výše).

Klikněte pravým tlačítkem na projekt **AoaSampleApp** a potom v místní nabídce klikněte na **nasadit** a nainstalujte aplikaci. Pokud se v **okno výstup** sady Visual Studio nezobrazí žádná chyba, aplikace se nainstaluje na HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Nasazení aplikace do HoloLens":::

Před spuštěním aplikace je potřeba nahrát objektový model. Postupujte podle pokynů v části **objektového modelu** ingestování a zjistěte níže uvedený oddíl instance.

Pokud chcete aplikaci spustit a ladit, vyberte **ladit > spustit ladění**. Chcete-li aplikaci zastavit, vyberte možnost **Zastavit ladění** nebo stiskněte klávesy **SHIFT + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Ingestování objektového modelu a zjištění jeho instance

Abyste mohli spustit ukázkovou aplikaci, budete muset vytvořit objektový model. Předpokládejme, že už máte ve svém prostoru buď model CAD, nebo naskenovaný model 3D mřížky objektu. Informace o tom, jak vytvořit model, najdete v tématu [rychlý Start:](./get-started-model-ingestion.md) ingestování 3D modelu.

Stáhněte si tento model, **židli. ou** v našem případě do vašeho počítače. Pak na portálu zařízení HoloLens vyberte **systém > Průzkumník souborů > LocalAppData > AoaSampleApp > LocalState** a vyberte **Procházet...**. Pak vyberte soubor modelu, **židli. ou** a vyberte **nahrát**. Pak byste měli vidět soubor modelu v místní mezipaměti.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Model odeslání portálu":::

Z HoloLens spusťte aplikaci **AoaSampleApp** (Pokud už je otevřená, zavřete ji a znovu otevřete). Procházení se blíží k cílovému objektu (křeslo) a prohledá ho tím, že se na něj podívá z více perspektiv. Mělo by se zobrazit růžový ohraničovací rámeček kolem objektu s některými žlutými body vykreslenými blízko k povrchu objektu, což znamená, že byl zjištěn.

:::image type="content" source="./media/chair-detection.png" alt-text="Detekce křesla":::

Obrázek: zjištěná křesla vykreslená s jeho ohraničujícím rámečkem (růžová), Point Cloud (žlutá) a oblast hledání (velký žlutý rámeček).

Můžete definovat prostor pro hledání objektu v aplikaci tak, že prstem kliknete na Air buď přímo, nebo vlevo. Místo pro hledání se bude přepínat mezi koule poloměru 2 měřičů, ohraničovacím polem 4 m ^ 3 a frustum zobrazení. U větších objektů, jako jsou automobily, je nejlepší volbou obvykle použití zobrazení frustum výběru, zatímco se nacházíte na horním okraji objektu na vzdálenosti 2 měřiče.
Pokaždé, když se oblast hledání změní, aplikace odstraní aktuálně sledované instance a pak se znovu pokusí o jejich vyhledání v nové oblasti hledání.

Tato aplikace může sledovat více objektů najednou. Provedete to tak, že nahrajete více modelů do složky **LocalState** a nastavíte oblast hledání, která pokrývá všechny cílové objekty. Zjišťování a sledování více objektů může trvat delší dobu.

Aplikace úzce zarovnává 3D model fyzickému protějšku. Uživatel může klepnout na své levé straně a zapnout režim sledování s vysokou přesností, který počítá přesnější pozici. Toto je stále experimentální funkce, která spotřebovává více systémových prostředků a může způsobit vyšší kolísání odhadované pozice. Klepnutím na Air znovu klepněte na levou stranu a přepněte zpátky do režimu normálního sledování.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý Start: ingestování 3D modelu](./get-started-model-ingestion.md)

> [!div class="nextstepaction"]
> [Koncepty: Přehled sady SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Nejčastější dotazy](../faq.md)

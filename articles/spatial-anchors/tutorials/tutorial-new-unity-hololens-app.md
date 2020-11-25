---
title: 'Kurz: vytvoření nové aplikace Unity pro HoloLens'
description: V tomto kurzu se naučíte, jak vytvořit novou aplikaci pro HoloLens s využitím prostorových kotev Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: ee0bf9b4ce009f37dd1931d4ed030defa24e7d38
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95996252"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Kurz: podrobné pokyny k vytvoření nové aplikace Unity pro HoloLens pomocí prostorových kotev Azure

V tomto kurzu se dozvíte, jak vytvořit novou aplikaci Unity pro HoloLens pomocí prostorových kotev Azure.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

1. Počítač se systémem Windows se sadou <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> nainstalovaný s úlohou **vývoje Univerzální platforma Windows** a komponentou **Windows 10 SDK (10.0.18362.0 nebo novější)** a <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
2. [Rozšíření Visual Studio/WinRT v jazyce C++](https://aka.ms/cppwinrt/vsix) pro Visual Studio by se mělo nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Zařízení HoloLens s povoleným [vývojářským režimem](/windows/mixed-reality/using-visual-studio) . Tento článek vyžaduje zařízení HoloLens s [Windows 10 květen 2020 Update](/windows/mixed-reality/whats-new/release-notes-may-2020). Chcete-li provést aktualizaci na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení** , pokračujte na **aktualizace & zabezpečení** a pak vyberte tlačítko **Vyhledat aktualizace** .

## <a name="getting-started"></a>Začínáme

Nejdřív nastavíme náš projekt a scénu Unity:
1. Spusťte Unity.
2. Vyberte možnost pro **novou** položku.
4. Ujistěte se, že je vybraná možnost **3D** .
5. Pojmenujte projekt a zadejte **umístění** pro uložení.
6. Vyberte **Vytvořit projekt**.
7. Uložit prázdnou výchozí scénu do nového souboru pomocí: **soubor**  >  **Uložit jako**.
8. Pojmenujte novou **scénu** a stiskněte tlačítko **Uložit** .

**Nastavení projektu**

Teď nastavíme některá nastavení projektů Unity, která nám pomůžou zaměřit se na vývoj pro Windows holografické sady Windows.

Nejdřív nastavíme nastavení kvality pro naši aplikaci.
1. Výběr možnosti **Upravit**  >  **nastavení projektu**  >  **kvalita**
2. Ve sloupci loga **Windows Store** klikněte na šipku na **výchozím** řádku a vyberte **velmi nízká**. Víte, že nastavení se použije správně, když je pole ve sloupci **Windows Store** a **velmi málo** řádku zelené.

Musíme nakonfigurovat naši aplikaci Unity pomocí moderního zobrazení, nikoli 2D zobrazení. Moderní zobrazení můžeme vytvořit tak, že povolíte podporu virtuální reality pro Unity cílící na sadu Windows 10 SDK.
1. Přejít na **Upravit**  >  **nastavení projektu**  >  **Player**.
2. Na **panelu Inspektor** pro **nastavení přehrávače** vyberte ikonu **Windows** .
3. Rozbalte skupinu **Nastavení XR** .
4. V části **vykreslování** zaškrtněte políčko **podporované virtuálními realitami** a přidejte nový seznam **sad SDK pro virtuální realitu** .
5. Ověřte, že se v seznamu zobrazuje **Windows Mixed reality** . Pokud ne, vyberte **+** tlačítko v dolní části seznamu a zvolte **Windows Mixed reality**.

> [!NOTE]
> Pokud nevidíte ikonu Windows, před instalací se ujistěte, že jste vybrali back-end Windows .NET Scripting. V takovém případě možná budete muset znovu nainstalovat Unity se správnou instalací Windows.

**Ověřit konfiguraci skriptovacího back-endu**
1. Přejít na **Upravit**  >  **nastavení projektu**  >  **přehrávač** (můžete mít stále otevřený **přehrávač** z předchozího kroku).
2. Na **panelu Inspektor** pro **nastavení přehrávače** vyberte ikonu **Windows Store** .
3. V části **Další konfigurace nastavení** se ujistěte, že je pro **skriptování back-end** nastavená možnost **IL2CPP**.

**Nastavit možnosti**
1. Přejít na **Upravit**  >  **nastavení projektu**  >  **přehrávač** (můžete mít stále otevřený **přehrávač** z předchozího kroku).
2. Na **panelu Inspektor** pro **nastavení přehrávače** vyberte ikonu **Windows Store** .
3. V části konfigurace **Nastavení publikování** ověřte **InternetClientServer** a **SpatialPerception**.

**Nastavení hlavní virtuální kamery**
1. Na **panelu hierarchie** vyberte **hlavní kamera**.
2. V **inspektoru** nastavte jeho pozici transformace na **0, 0, 0**.
3. Vyhledejte vlastnost **Vymazat příznaky** a změňte rozevírací seznam z **Skybox** na **Solid Color**.
4. Kliknutím na tlačítko v poli **pozadí** otevřete výběr barvy.
5. Nastavte **R, G, B a** a na **0**.
6. Vyberte **Přidat součást** a vyhledejte a přidejte **kolizi prostorového mapování**.

**Vytvoření našeho skriptu**
1. V podokně **projekt** vytvořte ve složce **assets (prostředky** ) novou složku a **skripty**.
2. Klikněte pravým tlačítkem na složku a vyberte **vytvořit >**, **skript C#**. **AzureSpatialAnchorsScript** název.
3. Přejít na **GameObject**  ->  **vytvořit prázdné**
4. Vyberte ho a v **inspektoru** ho přejmenujte z **GameObject** na **MixedRealityCloud**. Vyberte **Přidat komponentu** a vyhledejte a přidejte **AzureSpatialAnchorsScript**.

**Vytvoření Prefab koule**
1. Přejít na **GameObject**  ->  **3D objekt**  ->  **sphere**.
2. V **inspektoru** nastavte jeho měřítko na **0,25, 0,25, 0,25**.
3. V podokně **hierarchie** Najděte objekt **sphere** . Klikněte na něj a přetáhněte ho do složky **assets (prostředky** ) v podokně **projekt** .
4. Klikněte pravým tlačítkem a **odstraňte** původní oblast, kterou jste vytvořili v podokně **hierarchie** .

Nyní byste měli mít v podokně **projektu** Prefab sphere.

## <a name="trying-it-out"></a>Vyzkoušení
Chcete-li otestovat, že vše funguje, sestavte aplikaci v **Unity** a nasaďte ji ze sady **Visual Studio**. Postupujte podle kapitoly 6 ze [ **základních základů pro Mr 100: Začínáme s** kurzem Unity](/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) . Měli byste vidět úvodní obrazovku Unity a pak Vymazat displej.

## <a name="place-an-object-in-the-real-world"></a>Umístit objekt do reálného světa
Pojďme vytvořit & umístit objekt pomocí vaší aplikace. Otevřete řešení sady Visual Studio, které jsme vytvořili při [nasazení naší aplikace](#trying-it-out).

Nejdřív přidejte následující importy do `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Pak do své třídy přidejte následující proměnné členů `AzureSpatialAnchorsScript` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

Než budeme pokračovat, musíme nastavit sphere Prefab, kterou jsme vytvořili v naší členské proměnné spherePrefab. Vraťte se zpět do **Unity**.
1. V části **Unity** vyberte objekt **MixedRealityCloud** v podokně **hierarchie** .
2. Klikněte na Prefab **koule** , kterou jste uložili v podokně **projektu** . Přetáhněte **koulí** , na kterou jste klikli, do oblasti **sphere Prefab** v části **skriptové kotvy Azure (skript)** v podokně **inspektor** .

Nyní byste měli mít nastavenou **koule** jako Prefab na vašem skriptu. Sestavte z **Unity** a pak znovu otevřete výsledné řešení sady **Visual Studio** , stejně jako jste [to právě zkoušeli](#trying-it-out).

V **aplikaci Visual Studio** otevřete `AzureSpatialAnchorsScript.cs` znovu. Do své metody přidejte následující kód `Start()` . Tento kód se zachytí `GestureRecognizer` , který se zavolá, `HandleTap` když rozpozná vzduchový klepnutí.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

Nyní je nutné přidat následující `HandleTap()` metodu níže `Update()` . Provede přetypování do ray a získá bod přístupů, na který se má koule umístit.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

Teď je potřeba vytvořit sphere. Koule bude zpočátku bílá, ale tato hodnota se upraví později. Přidejte následující `CreateAndSaveSphere()` metodu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

Spusťte aplikaci ze sady **Visual Studio** a znovu ji ověřte. Tentokrát můžete klepnutím na obrazovku vytvořit & umístit svou bílou plochu na plochu podle vašeho výběru.

## <a name="set-up-the-dispatcher-pattern"></a>Nastavení modelu dispečera

Při práci s Unity se musí v hlavním vlákně vyskytnout všechna rozhraní API Unity (například rozhraní API, která používáte k aktualizaci uživatelského rozhraní). V kódu, který zapíšeme, ale budeme zpětně nakládat zpětná volání na jiných vláknech. Chceme aktualizovat uživatelské rozhraní v těchto zpětných voláních, takže potřebujeme způsob, jak přejít z vlákna z boku do hlavního vlákna. Chcete-li spustit kód v hlavním vlákně z vedlejšího vlákna, použijeme vzor dispečera.

Pojďme přidat členskou proměnnou, `dispatchQueue` , která je frontou akcí. Do fronty přiřadíme akce a pak se vyřadí a spustí akce v hlavním vlákně.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

Nyní přidáme způsob, jak do fronty přidat akci. Přidat `QueueOnUpdate()` práva po `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

Pomocí smyčky Update () můžeme zjistit, jestli je nějaká akce zařazená do fronty. Pokud ano, vyřadíme akci a spustíme ji.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Získat sadu SDK prostorových kotev Azure

## <a name="via-unity-package-manager-upm-package"></a>[Prostřednictvím balíčku UPM (Unity Package Manager)](#tab/UPMPackage)

Tato metoda je kompatibilní s verzemi Unity 2019.1 +.

### <a name="add-the-registry-to-your-unity-project"></a>Přidání registru do projektu Unity

1. V Průzkumníku souborů přejděte do složky vašeho projektu Unity `Packages` . Otevřete soubor manifestu projektu, `manifest.json` v textovém editoru.
2. V horní části souboru na stejné úrovni jako v `dependencies` části přidejte následující položku pro zahrnutí registru prostorových kotev Azure do vašeho projektu. Tato `scopedRegistries` položka obsahuje informace o Unity, kde hledat balíčky sady SDK prostorových kotev Azure.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

### <a name="add-the-sdk-package-to-your-unity-project"></a>Přidat balíček sady SDK do projektu Unity

1. Přidejte položku s prostorovými kotvami Azure Windows SDK název balíčku ( `com.microsoft.azure.spatial-anchors-sdk.windows` ) a verzi balíčku do `dependencies` oddílu v manifestu projektu. Níže najdete příklad.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-20&highlight=12)]

2. Soubor uložte a zavřete `manifest.json` . Když se vrátíte do Unity, Unity by měl automaticky detekovat změnu manifestu projektu a načíst zadané balíčky. Můžete rozbalit `Packages` složku v zobrazení projektu a ověřit, zda byly naimportovány správné balíčky.

## <a name="via-unity-asset-package"></a>[Prostřednictvím balíčku assetů Unity](#tab/UnityAssetPackage)

> [!WARNING]
> Po instalování sady SDK verze 2.5.0 bude distribuována sada prostředků služby Unity v sadě SDK prostorových kotev Azure.

Pojďme si stáhnout sadu SDK prostorových kotev Azure. Přejít na [stránku vydání GitHubu prostorových kotev Azure](https://github.com/Azure/azure-spatial-anchors-samples/releases). V části **assety (prostředky**) stáhněte **AzureSpatialAnchors. unitypackage**. V Unity, přejít na **prostředky**, vybrat **importovat balíček**  >  **vlastní balíček..**. Přejděte k balíčku a vyberte **otevřít**.

V okně Nový **balíček Unity balíčku** , které se zobrazí, zrušte výběr **modulů plug-in** a v pravém dolním rohu vyberte **importovat** .

---

Do řešení sady **Visual Studio** přidejte následující import do `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Pak do své třídy přidejte následující proměnné členů `AzureSpatialAnchorsScript` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Připojte k místnímu ukotvení místní prostorové ukotvení Azure

Pojďme nastavit CloudSpatialAnchorSession prostorového ukotvení Azure. Začneme přidáním následující `InitializeSession()` metody do vaší `AzureSpatialAnchorsScript` třídy. Po volání se zajistí, že se vytvoří relace prostorových kotev Azure, která se při spuštění vaší aplikace správně inicializuje.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

Nyní potřebujeme napsat kód pro zpracování volání delegátů. Až budeme pokračovat, přidáme k nim další.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

Nyní zapojte `initializeSession()` metodu do vaší `Start()` metody.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

Nakonec do své metody přidejte následující kód `CreateAndSaveSphere()` . Připojí se k oblasti, kterou umísťujeme do reálného světa, místní prostor Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

Než budete pokračovat, budete muset vytvořit účet prostorových kotev Azure, který získá identifikátor účtu, klíč a doménu. Pokud tyto hodnoty ještě nemáte, Získejte je podle pokynů v další části.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Nahrání místního kotvy do cloudu

Jakmile budete mít identifikátor účtu prostorových kotev Azure, klíč a doménu, přejdete do části a vložíte `Account Id` do `SpatialAnchorsAccountId` `Account Key` `SpatialAnchorsAccountKey` `Account Domain` `SpatialAnchorsAccountDomain` .

Nakonec připojovat všechno dohromady. Do `CreateAndSaveSphere()` metody přidejte následující kód. Vyvolá `CreateAnchorAsync()` metodu hned po vytvoření vaší koule. Jakmile se metoda vrátí, kód níže aktualizuje vaši sféru o jednu poslední, změna barvy na modrou.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

Spusťte aplikaci ze sady **Visual Studio** , a to ještě jednou. Pohybujte kolem své hlavy a potom klepněte v duchu na umístění své koule. Jakmile máme dostatek snímků, koule se změní žlutě a spustí se nahrávání do cloudu. Až se nahrávání dokončí, vaše koule se změní na modrou. V případě potřeby můžete také použít [okno výstup](/visualstudio/ide/reference/output-window) při ladění v **aplikaci Visual Studio** a monitorovat zprávy protokolu, které vaše aplikace posílá. Ujistěte se, že jste nasadili `Debug` konfiguraci aplikace ze sady Visual Studio, abyste viděli zprávy protokolu. Můžete sledovat `RecommendedForCreateProgress` , a až se nahrávání dokončí, budete moct zobrazit identifikátor kotvy vrácený z cloudu.

> [!NOTE]
> Pokud se zobrazí "DllNotFoundException –: nejde načíst knihovnu DLL" AzureSpatialAnchors ": zadaný modul se nepovedlo najít.", měli byste řešení **vyčistit** a znovu **sestavit** .

## <a name="locate-your-cloud-spatial-anchor"></a>Najděte své cloudové kotvy

Jedna vaše kotva se nahraje do cloudu, můžeme se pokusit o jejich vyhledání znovu. Pojďme do vaší metody přidat následující kód `HandleTap()` . Tento kód bude:

* Volání `ResetSession()` , které zastaví `CloudSpatialAnchorSession` a odebere z obrazovky existující modrou koule.
* `CloudSpatialAnchorSession`Znovu inicializovat. To provedeme, abychom si zajistili, že kotva, kterou vyhledáme, pochází z cloudu, a ne jako místní kotvu, kterou jsme vytvořili.
* Vytvořte **sledovací** proces, který bude hledat kotvu, kterou jsme nahráli do prostorových kotev Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

Pojďme teď přidat naše `ResetSession()` a `CleanupObjects()` metody. Můžete je umístit níže. `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

Teď je potřeba, abyste připojili kód, který se vyvolá, když se na něj nachází kotva, na které se dotazuje. Do `InitializeSession()` přidejte následující zpětná volání:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


Nyní umožňuje přidat kód, který vytvoří & umístit zelenou koule po umístění CloudSpatialAnchor. Také se znovu aktivuje obrazovka, takže můžete celý scénář zopakovat, a to ještě jednou: vytvořte další místní kotvu, nahrajte ho a znovu ho vyhledejte.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

A to je vše! Spusťte aplikaci ze sady **Visual Studio** jednou, abyste si vyzkoušeli celý scénář od konce do konce. Pohybujte kolem zařízení a umístěte svou bílou plochu. Pak pokračujte v přesouvání vašeho hlavního prostředí, dokud se koule nezmění žlutě. Vaše místní kotva se nahraje a vaše koule se zachová modře. Nakonec klepněte na obrazovku, abyste mohli odebrat místní kotvu a začít dotaz na jeho protějšek cloudu. Pokračujte v přesouvání zařízení, dokud se neumístí cloudové ukotvení. Zelená koule by se měla zobrazit ve správném umístění a celý scénář můžete opakovat.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]
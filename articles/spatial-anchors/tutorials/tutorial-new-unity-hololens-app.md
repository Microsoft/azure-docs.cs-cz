---
title: 'Kurz: Vytvoření nové aplikace HoloLens Unity'
description: V tomto kurzu se dozvíte, jak vytvořit novou aplikaci HoloLens Unity pomocí azure prostorových kotev.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e1abb759c80e770f1e650c232b6b2e21232b7e6f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75457724"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Kurz: Podrobné pokyny k vytvoření nové aplikace HoloLens Unity pomocí prostorových ukotvení Azure

Tento kurz vám ukáže, jak vytvořit novou aplikaci HoloLens Unity s prostorovými kotvami Azure.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

1. Počítač s Windows s <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> nainstalovaným s **úlohou vývoje univerzální platformy Windows** a součástí Windows **10 SDK (10.0.18362.0 nebo novější)** a <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
2. Rozšíření Visual Studio (VSIX) pro Visual Studio [(C++/WinRT)](https://aka.ms/cppwinrt/vsix) pro Visual Studio by mělo být nainstalováno z [webu Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Zařízení HoloLens s [povoleným režimem pro vývojáře.](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) Tento článek vyžaduje zařízení HoloLens s [aktualizací windows 10 října 2018](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (označované také jako RS5). Chcete-li aktualizovat na nejnovější verzi na HoloLens, otevřete aplikaci **Nastavení,** přejděte na **Aktualizovat & zabezpečení**a pak vyberte tlačítko Vyhledat **aktualizace.**

## <a name="getting-started"></a>Začínáme

Nejprve nastavíme náš projekt a scénu Unity:
1. Zahajte jednotu.
2. Vyberte **nový**.
4. Ujistěte se, že je **vybráno 3D.**
5. Pojmenujte projekt a zadejte uložit **umístění**.
6. Klepněte na **tlačítko Vytvořit projekt**.
7. Uložte prázdnou výchozí scénu do nového souboru pomocí: **Soubor** > **uložit jako**.
8. Pojmenujte novou scénu **Main** a stiskněte tlačítko **Uložit.**

**Nastavení projektu**

Nyní nastavíme některá nastavení projektu Unity, která nám pomohou zaměřit se na sadu Windows Holographic SDK pro vývoj.

Za prvé, umožňuje nastavit nastavení kvality pro naši aplikaci.
1. Vybrat **možnost Upravit** > **kvalitu** **nastavení** > projektu
2. Ve sloupci pod logem **Windows Storu** klikněte na šipku na **řádku Výchozí** a vyberte **Velmi nízká**. Budete vědět, že nastavení se použije správně, když je pole ve **sloupci Windows Store** a **řádku Velmi nízké** zelené.

Musíme dát Unity vědět, že aplikace, kterou se snažíme exportovat, by měla vytvořit pohlcující zobrazení namísto 2D zobrazení. Vytváříme pohlcující zobrazení tím, že umožňujeme podporu virtuální reality v Unity zaměřené na Windows 10 SDK.

1. Přejděte na **upravit** > **přehrávač****nastavení** > projektu .
2. V **panelu Inspektor** pro **nastavení přehrávače**vyberte ikonu **Windows Store.**
3. Rozbalte skupinu **Nastavení XR.**
4. V části **Vykreslování** zaškrtněte políčko **Podporovaná virtuální realita** a přidejte seznam nové sady **SDK virtuální reality.**
5. Ověřte, zda se v seznamu zobrazí **hybridní realita systému Windows.** Pokud ne, **+** vyberte tlačítko v dolní části seznamu a zvolte **Windows Mixed Reality**.

> [!NOTE]
> Pokud ikonu Windows Store nevidíte, zkontrolujte, jestli jste před instalací vybrali back-end skriptování ve Windows Storu .NET. Pokud ne, bude pravděpodobně nutné přeinstalovat Unity se správnou instalací systému Windows.

**Ověření konfigurace back-endu skriptování**
1. Přejděte na **Upravit** > **přehrávač** **nastavení** > projektu (v předchozím kroku je přehrávač **stále** otevřený).
2. V **panelu Inspektor** pro **nastavení přehrávače**vyberte ikonu **Windows Store.**
3. V části **Další konfigurace nastavení** zkontrolujte, zda je **skriptovací back-end** nastaven na **IL2CPP**.

**Nastavit možnosti**
1. Přejděte na **Upravit** > **přehrávač** **nastavení** > projektu (v předchozím kroku je přehrávač **stále** otevřený).
2. V **panelu Inspektor** pro **nastavení přehrávače**vyberte ikonu **Windows Store.**
3. V části **Konfigurace nastavení publikování** zaškrtněte položky **InternetClientServer** a **SpatialPerception**.

**Nastavení hlavní virtuální kamery**
1. V **panelu Hierarchie**vyberte **Hlavní fotoaparát**.
2. V **inspektoru**nastavte jeho pozici transformace na **0,0,0**.
3. Najděte vlastnost **Clear Flags** a změňte rozevírací soubor ze **Skyboxu** na **Plná barva**.
4. Kliknutím na pole **Pozadí** otevřete výběr barvy.
5. Nastavte **R, G, B a A** na **0**.
6. Vyberte **Přidat komponentu** a vyhledejte a přidejte **urychlovač prostorového mapování**.

**Vytvořte náš skript**
1. V podokně **Projekt** vytvořte novou složku **Skripty**ve složce **Datové zdroje.**
2. Klikněte pravým tlačítkem myši na složku a pak vyberte **příkaz Vytvořit >**, **C# Script**. Název je **AzureSpatialAnchorsScript**.
3. Přejděte na **GameObject** -> **Create Empty**.
4. Vyberte ji a v **inspektoru** přejmenujte jej z **GameObject** na **MixedRealityCloud**. Vyberte **Přidat komponentu** a vyhledejte a přidejte **AzureSpatialAnchorsScript**.

**Vytvoření panelové koule**
1. Přejděte na **gameobject** -> **3D objektová** -> **koule**.
2. V **inspektoru**nastavte jeho měřítko na **0,25, 0,25, 0,25**.
3. Najděte objekt **Koule** v podokně **Hierarchie.** Klikněte na něj a přetáhněte ji do složky **Datové zdroje** v podokně **Projekt.**
4. Klikněte pravým tlačítkem myši a **odstraňte** původní oblast, kterou jste vytvořili v podokně **Hierarchie.**

Nyní byste měli mít panelovou fámu v podokně **projektu.**

## <a name="trying-it-out"></a>Vyzkoušet to
Chcete-li otestovat, že vše funguje, vytvořte aplikaci v **Unity** a nasaďte ji z **Visual Studia**. Postupujte podle kapitoly 6 z [ **MR Základy 100: Začínáme s Unity** kurz,](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) aby tak učinily. Měli byste vidět úvodní obrazovku Unity a pak jasný displej.

## <a name="place-an-object-in-the-real-world"></a>Umístěte objekt do reálného světa
Pojďme vytvořit & umístit objekt pomocí aplikace. Otevřete řešení Visual Studia, které jsme [vytvořili](#trying-it-out)při nasazení naší aplikace .

Nejprve do aplikace `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`přidejte následující importy :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Potom do třídy `AzureSpatialAnchorsScript` přidejte následující proměnné členů:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Než budeme pokračovat, musíme nastavit koule prefabrikované jsme vytvořili na naší proměnné spherePrefab člen. Vraťte se k **Jednotě**.
1. V **unity**vyberte objekt **MixedRealityCloud** v podokně **Hierarchie.**
2. Klikněte na **panel Sphere,** který jste uložili v podokně **Projekt.** Přetáhněte **sphere,** na kterou jste klikli, do oblasti **Panelové plochy koule** v části Skript **prostorových ukotvení Azure (Skript)** v podokně **Inspektor.**

Nyní byste měli mít **sphere** nastavit jako panelový soubor na skriptu. Build z **Unity** a pak znovu otevřete výsledné řešení **Sady Visual Studio,** jako jste to právě udělali v [vyzkoušení](#trying-it-out).

V **sadě Visual** `AzureSpatialAnchorsScript.cs` Studio otevřete znovu. Přidejte do `Start()` metody následující kód. Tento kód se `GestureRecognizer`připojí , který detekuje, kdy `HandleTap`je vzduchové kohoutku a volání .

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Nyní musíme přidat `HandleTap()` následující `Update()`metodu níže . To bude dělat ray obsazení a získat místo zásahu, na které umístit kouli.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Nyní musíme vytvořit kouli. Koule bude zpočátku bílá, ale tato hodnota bude později upravena. Přidejte `CreateAndSaveSphere()` následující metodu:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Spusťte aplikaci z **Visual Studia** a znovu ji ověřte. Tentokrát klepnutím na obrazovku vytvořte & umístěte bílou kouli na povrch podle vašeho výběru.

## <a name="set-up-the-dispatcher-pattern"></a>Nastavení způsobu dispečera

Při práci s Unity, všechny rozhraní API Unity, například rozhraní API, které používáte k aktualizaci uživatelského rozhraní, musí dojít v hlavním vlákně. V kódu budeme psát však dostaneme zpětná volání na jiných vláknech. Chceme aktualizovat uživatelské rozhraní v těchto zpětná volání, takže potřebujeme způsob, jak přejít z boční vlákno na hlavní vlákno. Chcete-li spustit kód v hlavním vlákně z bočního vlákna, použijeme vzor dispečera.

Přidáme proměnnou člena dispatchQueue, což je fronta akcí. Budeme tlačit akce do fronty a potom dequeue a spustit akce v hlavním vlákně.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

Dále přidáme způsob, jak přidat akci do fronty. Přidat `QueueOnUpdate()` hned `Update()` za :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Nyní použijeme smyčku Update() ke kontrole, zda je zařazena do fronty akce. Pokud ano, zfrontneme akci do fronty a spustíme ji.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Získání sady Azure Spatial Anchors SDK

## <a name="via-unity-package"></a>[Prostřednictvím balíčku Unity](#tab/UnityPackage)

Teď si stáhneme sdk prostorové kotvy Azure. Přejděte na [stránku githubu Azure Spatial Anchors GitHub](https://github.com/Azure/azure-spatial-anchors-samples/releases). V části Datové zdroje stáhněte **balíček AzureSpatialAnchors.unitypackage**. V unity přejděte na **Datové zdroje**, klikněte na **Importovat vlastní** > **balíček balíčku...**. Přejděte na balíček a vyberte **Otevřít**.

V novém okně **Import unity package,** které se objeví, zrušte výběr **modulů plug-in** a v pravém dolním rohu klikněte na **Importovat.**

Teď potřebujeme obnovit balíčky Nuget, abychom získali azure spatial anchors SDK. Sestavení z **Unity** a pak otevřít a znovu sestavit výsledné řešení **sady Visual Studio,** jak je podrobně popsáno v [vyzkoušení](#trying-it-out).

## <a name="via-nugetforunity"></a>[Přes NugetForUnity](#tab/NuGetForUnity)

Nejprve musíme nainstalovat NuGetForUnity. Přejděte na [stránku vydání GitHubu NuGetForUnity](https://github.com/GlitchEnzo/NuGetForUnity/releases). V části Prostředky stáhněte poslední **balíček NuGetForUnity.unitypackage**. V unity přejděte na **Datové zdroje**, klikněte na **Importovat vlastní** > **balíček balíčku...**. Přejděte na balíček a vyberte **Otevřít**. Unity nyní nainstaluje NugetForUnity. Pokud nevidíte nový **seznam NuGet** v Unity, možná budete muset kliknout pravým tlačítkem myši v části **Projekty** > **prostředky**. Pak vyberte **Znovu importovat vše**.

Jakmile máte nainstalovanou NuGetForUnity, vyberte **NuGet** > **Spravovat NuGet balíčky**. Pak vyhledejte Microsoft.Azure.SpatialAnchors.Unity a vyberte **Nainstalovat**.

Teď musíme vytvořit získat skutečné Azure prostorové kotvy SDK, jako balíček NuGet, který jsme právě stáhli obsahuje pouze pomocné skripty. Sestavení z **Unity** a pak otevřít a znovu sestavit výsledné řešení **sady Visual Studio,** jak je podrobně popsáno v [vyzkoušení](#trying-it-out).

---

V řešení **Sady Visual Studio** přidejte `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`do aplikace :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Potom přidejte do `AzureSpatialAnchorsScript` třídy následující členské proměnné:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Připojení místní prostorové kotvy Azure k místní kotvě

Pojďme nastavit Azure prostorové kotvy CloudSpatialAnchorSession. Začneme přidáním následující `InitializeSession()` metody do `AzureSpatialAnchorsScript` vaší třídy. Po volání zajistí, že relace Azure Spatial Anchors se vytvoří a správně inicializuje během spuštění aplikace.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Nyní potřebujeme napsat kód pro zpracování volání delegáta. Budeme přidávat další k nim, jak budeme pokračovat.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Nyní pojďme připojit `initializeSession()` svou metodu do vaší `Start()` metody.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Nakonec přidejte následující kód `CreateAndSaveSphere()` do metody. Připojí místní Azure Spatial Anchor do koule, kterou umisťujeme do reálného světa.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Než budete pokračovat dál, budete muset vytvořit identifikátor účtu Azure Spatial Anchors a klíč, pokud je ještě nemáte. Postupujte podle následující části, abyste je získali.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Nahrání místní kotvy do cloudu

Jakmile budete mít svůj účet Azure Spatial Anchors `Account Id` identifikátor `SpatialAnchorsAccountId` a `Account Key` `SpatialAnchorsAccountKey`klíč, přejděte a vložte do a do .

Konečně, pojďme připojit všechno dohromady. Do `SpawnNewAnchoredObject()` metody přidejte následující kód. Vyvolá metodu, `CreateAnchorAsync()` jakmile bude vytvořena vaše koule. Jakmile se metoda vrátí, níže uvedený kód provede jednu konečnou aktualizaci vaší koule a změní její barvu na modrou.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Spusťte aplikaci z **Visual Studia** ještě jednou. Pohybovat kolem hlavy a pak vzduchem kohoutek umístit svou kouli. Jakmile budeme mít dostatek rámů, koule se změní na žlutou a nahrávání mraků začne. Jakmile nahrávání skončí, vaše koule zmodrá. Volitelně můžete také použít okno Výstup uvnitř **sady Visual Studio** ke sledování zpráv protokolu, které aplikace odesílá. Budete moci sledovat doporučené pro průběh vytváření, stejně jako identifikátor kotvy, který cloud vrátí po dokončení nahrávání.

> [!NOTE]
> Pokud se zobrazí "DllNotFoundException: Nelze načíst dll 'AzureSpatialAnchors': Zadaný modul nebyl nalezen.", měli byste **vyčistit** a **znovu vytvořit** řešení.

## <a name="locate-your-cloud-spatial-anchor"></a>Vyhledání prostorové kotvy cloudu

Jedna z vašich kotvy je nahrána do cloudu, jsme připraveni se ji pokusit znovu najít. Pojďme přidat následující kód `HandleTap()` do metody. Tento kód bude:

* Volejte `ResetSession()`, který `CloudSpatialAnchorSession` zastaví a odstraní naši stávající modrou kouli z obrazovky.
* Inicializovat `CloudSpatialAnchorSession` znovu. Děláme to tak, že jsme si jisti, že kotva, kterou najdeme, pochází z cloudu místo toho, aby byla místní kotvou, kterou jsme vytvořili.
* Vytvořte **sledovací proces,** který bude hledat kotvu, kterou jsme nahráli do azure prostorových kotvy.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Nyní přidáme naše `ResetSession()` `CleanupObjects()` a metody. Můžete je umístit pod`QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Nyní potřebujeme připojit kód, který bude vyvolán, když je umístěna kotva, na kterou se dotazujeme. Uvnitř `InitializeSession()`písmenka k tomu přidejte následující zpětná volání:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]


Nyní umožňuje přidat kód, který vytvoří & umístit zelenou kouli, jakmile CloudSpatialAnchor je umístěn. To také umožní nahrávání na obrazovce znovu, takže můžete opakovat celý scénář ještě jednou: vytvořit další místní kotvu, nahrát ji a najít ji znovu.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

A to je vše! Spusťte aplikaci z **Visual Studia** naposledy vyzkoušet celý scénář od začátku do konce. Pohybujte se kolem zařízení a umístěte bílou kouli. Pak pokračujte v pohybu hlavy, abyste zachytili data prostředí, dokud koule nezežloutne. Vaše místní kotva bude nahrána a vaše koule zmodrá. Nakonec klepněte na obrazovku ještě jednou, aby se odebrala místní kotva, a pak se podíváme na jeho protějšek v cloudu. Pokračujte v pohybu zařízení, dokud nebude umístěna prostorová kotva cloudu. Zelená koule by se měla zobrazit ve správném umístění a můžete znovu opláchnut& opakovat celý scénář.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]
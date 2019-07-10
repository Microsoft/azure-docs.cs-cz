---
title: Kurz – podrobné pokyny k vytvoření nové aplikace HoloLens Unity pomocí prostorových kotvy Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit novou aplikaci HoloLens Unity pomocí prostorových kotvy Azure.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 07/05/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 57244dd9f3365b3899bcc1dde6382cc3b51719d9
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722928"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Kurz: Podrobné pokyny k vytvoření nové aplikace HoloLens Unity pomocí prostorových kotev vztahů Azure

Tomto kurzu se dozvíte, jak vytvořit novou aplikaci HoloLens Unity s Azure prostorových kotvy vztahů.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

1. Počítače s Windows s <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017 +</a> součástí **vývoj pro univerzální platformu Windows** pracovního vytížení a **Windows 10 SDK (10.0.17763.0 nebo novější)** komponenty, a <a href="https://git-scm.com/download/win" target="_blank">Git pro Windows</a>.
2. [ C++WinRT rozšíření aplikace Visual Studio (VSIX)](https://aka.ms/cppwinrt/vsix) pro Visual Studio musí nainstalovat z [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Zařízení HoloLens s [vývojářský režim](https://docs.microsoft.com/windows/mixed-reality/using-visual-studio) povolena. Tento článek vyžaduje zařízení HoloLens se [Windows 10. října 2018 Update](https://docs.microsoft.com/windows/mixed-reality/release-notes-october-2018 ) (označované také jako RS5). Chcete-li aktualizovat na nejnovější verzi na HoloLens, otevřete **nastavení** aplikaci, přejděte na **aktualizace a zabezpečení**, vyberte **vyhledat aktualizace** tlačítko.

## <a name="getting-started"></a>Začínáme

Nejprve se nastavíme naše projektu a scénu Unity:
1. Začněte Unity.
2. Vyberte **Nový**.
4. Zajištění **3D** zaškrtnuto.
5. Pojmenujte svůj projekt a zadejte uložení **umístění**.
6. Klikněte na tlačítko **vytvořit projekt**.
7. Prázdný výchozí scény uložte do nového souboru pomocí: **Soubor** > **uložit jako**.
8. Pojmenujte novou scénu **hlavní** a stiskněte klávesu **Uložit** tlačítko.

**Nastavení projektu**

Nyní nastavíme některá nastavení projektu Unity, které nám pomáhají cíl Windows Holographic SDK pro vývoj. 

Nejprve umožňuje nastavení kvality pro naši aplikaci. 
1. Vyberte **upravit** > **nastavení projektu** > **kvality**
2. V sloupci **Windows Store** logo, klikněte na šipku v **výchozí** řádku a vyberte **velmi nízká**. Budete vědět, nastavení se použije, správně když pole v **Windows Store** sloupce a **velmi nízká** řádek je zeleně.

Je potřeba nechat Unity vědí, by měl vytvořit aplikaci, kterou jsme se pokusu o export atraktivní zobrazení namísto 2D zobrazení. Vytvoříme atraktivní zobrazení tím, že podpora virtuální realita na Unity cílení na Windows 10 SDK.

1. Přejděte na **upravit** > **nastavení projektu** > **Player**.
2. V **Inspector panelů** pro **nastavení přehrávače**, vyberte **Windows Store** ikonu.
3. Rozbalte **XR nastavení** skupiny.
4. V **vykreslování** oddílu, zkontrolujte **podporované virtuální realitu** zaškrtávací políčko a přidáním nové **virtuální realitu SDK** seznamu.
5. Ověřte, že **Windows Mixed Reality** se zobrazí v seznamu. Pokud ne, vyberte **+** tlačítko v dolní části seznamu a zvolte **Windows Mixed Reality**.
 
> [!NOTE]
> Pokud nevidíte ikonu Windows Store, pečlivě zkontrolujte, že jste vybrali Windows Store skriptování back-Endem .NET před instalací. V opačném případě budete muset znovu nainstalovat Unity s správnou instalaci Windows.

**Ověření konfigurace .NET**
1. Přejděte na **upravit** > **nastavení projektu** > **Player** (mohou mít i nadále **Player** otevřít z předchozí krok).
2. V **Inspector panelů** pro **nastavení přehrávače**, vyberte **Windows Store** ikonu.
3. V **další nastavení** konfigurace části, ujistěte se, že **skriptování back-endu** je nastavena na **.NET**.

**Sada možností**
1. Přejděte na **upravit** > **nastavení projektu** > **Player** (mohou mít i nadále **Player** otevřít z předchozí krok).
2. V **Inspector panelů** pro **nastavení přehrávače**, vyberte **Windows Store** ikonu.
3. V **nastavení publikování** konfiguračního oddílu, zkontrolujte **InternetClientServer** a **SpatialPerception**.

**Nastavte hlavní virtuální kamera**
1. V **panelu hierarchie**vyberte **hlavní fotoaparát**.
2. V **inspektoru**, nastavte jeho pozice transformace na **0,0,0**.
3. Najít **Vymazat příznaky** vlastnost a změnit z rozevíracího seznamu **Skybox** k **plnou barvu**.
4. Klikněte na **pozadí** pole k otevření ovládacího prvku pro výběr barvy.
5. Nastavte **R, G, B a A** k **0**.
6. Vyberte **přidat součást** a vyhledat a přidat **prostorových kolidující objekt mapování**.

**Vytvoření skriptu**
1. V **projektu** podokno, vytvořte novou složku **skripty**v části **prostředky** složky. 
2. Klikněte pravým tlačítkem na složku a potom vyberte **vytvořit >** ,  **C# skript**. Název je **AzureSpatialAnchorsScript**. 
3. Přejděte na **GameObject** -> **vytvořit prázdné**. 
4. Vyberte ho a v **inspektoru** přejmenovat z **GameObject** k **MixedRealityCloud**. Vyberte **přidat součást** a vyhledat a přidat **AzureSpatialAnchorsScript**.

**Vytvořit kouli prefab**
1. Přejděte na **GameObject** -> **3D objekt** -> **Sphere**.
2. V **inspektoru**, nastavte jeho škálování na **0,25, 0,25, 0,25**.
3. Najít **Sphere** objekt **hierarchie** podokně. Klikněte na něj a přetáhněte ho do **prostředky** složky **projektu** podokně.
4. Klikněte pravým tlačítkem myši a **odstranit** původní sphere, kterou jste vytvořili v **hierarchie** podokně.

Teď byste měli mít koule prefab ve vašich **projektu** podokně.

## <a name="trying-it-out"></a>Vyzkoušejte si
K otestování, zda vše funguje, vytvořte svoji aplikaci **Unity** a nasadit ho z **sady Visual Studio**. Postupujte podle kapitola 6 ze [ **MR základy 100: Začínáme s Unity** kurzu](https://docs.microsoft.com/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) Uděláte to tak. Měli byste vidět Unity spusťte obrazovku a potom zrušte zaškrtnutí zobrazení.

## <a name="place-an-object-in-the-real-world"></a>Umístit objekt v reálném světě
Pojďme vytvořit & umístit objekt pomocí vaší aplikace. Otevřete řešení sady Visual Studio, kterou jsme vytvořili, když jsme [nasadit naši aplikaci](#trying-it-out). 

Nejprve přidejte následující importy do vaší `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Pak přidejte následující proměnné členů do vaší `AzureSpatialAnchorsScript` třídy: 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-42,48-52,60-79)]

Abychom mohli pokračovat, potřebujeme nastavit na tvar koule prefab že jsme vytvořili v našich spherePrefab členské proměnné. Přejděte zpět na **Unity**.
1. V **Unity**, vyberte **MixedRealityCloud** objekt **hierarchie** podokně.
2. Klikněte na **Sphere** prefab, který jste uložili **projektu** podokně. Přetáhněte **Sphere** jste kliknuli na **Sphere Prefab** oblasti **Azure prostorových kotvy skript (skript)** v **inspektoru** podokno .

Teď byste měli mít **Sphere** nastavit jako prefab na skript. Sestavení z **Unity** a pak otevřete výsledná **sady Visual Studio** řešení, jako je třeba pouze u [zkoušíte](#trying-it-out). 

V **sady Visual Studio**, otevřete `AzureSpatialAnchorsScript.cs` znovu. Přidejte následující kód do vašeho `Start()` metody. Tento kód bude připojení `GestureRecognizer`, která zjistí, když dojde klepnutí ve vzduchu a volání `HandleTap`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-90,93&highlight=4-10)]

Teď máme přidat následující `HandleTap()` metodu `Update()`. Bude dělat ray přetypování a získat přístupů bod, ve kterém se má umístit koule. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-277,299-300,304-312)]

Nyní potřebujeme vytvořit oblasti. Oblasti budou zpočátku bílé, ale tato hodnota se upraví později. Přidejte následující `CreateAndSaveSphere()` metody:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-325,390)]

Spuštění aplikace z **sady Visual Studio** abyste ověřili, že ještě jednou. Tentokrát, klepněte na obrazovku k vytváření a umístěte bílé oblasti na plochu podle vašeho výběru.

## <a name="set-up-the-dispatcher-pattern"></a>Nastavit dispečer vzor

Při práci s Unity, všechna rozhraní API Unity, například rozhraní API můžete provést aktualizace uživatelského rozhraní, třeba provádělo na hlavním vlákně. V kódu, které budeme psát však získáme zpětná volání na jiných vláken. Chcete aktualizovat uživatelské rozhraní v tato zpětná volání, takže potřebujeme způsob, jak přejít z vlákna na straně do hlavního vlákna. Ke spouštění kódu vytvořeného v hlavním vlákně z vlákna na straně, použijeme vzor dispečera. 

Pojďme přidat členskou proměnnou, dispatchQueue, což je fronty akce. Bude odsuneme akce do fronty a pak odstranění z fronty a spouštět akce na hlavním vlákně. 

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=38-51&highlight=6-9)]

V dalším kroku přidejme způsob, jak přidat akci do fronty. Přidat `QueueOnUpdate()` hned po `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=107-117)]

Pojďme teď používá smyčku Update() a zkontrolujte, zda je akce zařadí do fronty. Pokud ano, budeme odstranění z fronty akce a spustíme ji.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=95-105&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Získat Azure prostorových kotvy SDK

Nyní jsme budete stažení sady SDK Azure prostorových ukotvení. Přejděte na [stránky vydaných verzí Azure prostorových kotvy Githubu](https://github.com/Azure/azure-spatial-anchors-samples/releases). V části prostředky, stáhněte si **AzureSpatialAnchors.unitypackage** souboru. 

V Unity, přejděte na **prostředky**, klikněte na tlačítko **importovat balíček** > **vlastní balíček...** . Přejděte do balíčku a vyberte **otevřít**.

Na novém **Import balíčku Unity** okno, které bodů POP, vyberte **žádný** vlevo dole. Pak v oblasti **AzureSpatialAnchorsPlugin** > **moduly plug-in**vyberte **běžné**, **Editor**, a  **HoloLens**. Klikněte na tlačítko **Import** v pravém dolním rohu.

Nyní potřebujeme pro obnovování balíčků Nuget, pokud chcete získat Azure prostorových kotvy SDK. Sestavení z **Unity** a pak otevřít a sestavit výsledná **sady Visual Studio** řešení znovu, jak [zkoušíte](#trying-it-out). 

Ve vaší **sady Visual Studio** řešení, přidejte následující import do vaší `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=23-26&highlight=1)]

Pak přidejte následující proměnné členů do vaší `AzureSpatialAnchorsScript` třídy:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=48-63&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Připojení místní prostorových ukotvení Azure k místní ukotvení

Nastavíme CloudSpatialAnchorSession Azure prostorových ukotvení. Začneme tak, že přidáte následující `InitializeSession()` metoda uvnitř vaší `AzureSpatialAnchorsScript` třídy. Po zavolání zajistí relaci prostorových kotvy Azure se vytvoří a správně inicializován během spuštění aplikace.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=174-202,205-209)]

Teď musíme napsat kód pro zpracování volání delegáta. Přidáme další k nim, jak budeme dál.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=211-226)]

Teď můžeme zapojit vaše `initializeSession()` metodu do vaší `Start()` – metoda.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=81-93&highlight=12)]

Nakonec přidejte následující kód do vašeho `CreateAndSaveSphere()` metody. Místní prostorových ukotvení Azure ho připojí k oblasti, které jsme umísťování v reálném světě.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-338,390&highlight=14-25)]

Než budete pokračovat dále, budete muset vytvořit prostorových ukotvení Azure účtu identifikátor a klíč, pokud ještě nemáte je. Pomocí postupu v následující části je můžete získat.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Nahrání místního ukotvení do cloudu

Jakmile váš účet Azure prostorových kotvy identifikátor a klíče go a vložte `Account Id` do `SpatialAnchorsAccountId` a `Account Key` do `SpatialAnchorsAccountKey`.

Nakonec Pojďme integrovat všechno dohromady. Ve vaší `SpawnNewAnchoredObject()` metodu, přidejte následující kód. Se vyvolá `CreateAnchorAsync()` metoda co nejdříve po vytvoření vaší oblasti. Jakmile se metoda vrátí hodnotu, následující kód provede jednu poslední aktualizaci do vaší oblasti, změníte jeho barvu na modrou.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=314-391&highlight=26-77)]

Spuštění aplikace z **sady Visual Studio** ještě jednou. Pohyb hlavě a potom klepnutím ve vzduchu můžete umístit vaší oblasti. Jakmile budeme mít dostatek snímků, oblasti se změní na žlutou barvou a spustí se nahrávání cloudu. Po dokončení nahrávání se modře vaší oblasti. Volitelně můžete také použít v okně výstupu uvnitř **sady Visual Studio** sledovat vaše aplikace odesílá zprávy protokolu. Budete moct sledovat doporučenou pro vytvoření průběh, stejně jako identifikátor anchor, který vrací cloudu po dokončení nahrávání.

> [!NOTE]
> Pokud se zobrazí "dllnotfoundexception –: Nelze načíst knihovnu DLL "AzureSpatialAnchors": Zadaný modul nebyl nalezen. ", měli byste **Vyčistit** a **sestavení** řešení znovu.

## <a name="locate-your-cloud-spatial-anchor"></a>Vyhledejte své cloudové prostorových kotvy

Jeden vaše ukotvení je nahraná na cloud, jsme připraveni pokusu o vyhledání znovu. Přidejte následující kód do vašeho `HandleTap()` metody. Tento kód vám:

* Volání `ResetSession()`, tím se zastaví `CloudSpatialAnchorSession` a naší stávající modré sphere odebrat z obrazovky.
* Inicializovat `CloudSpatialAnchorSession` znovu. To provedeme tak jsme si jisti, že anchor, které chceme vyhledejte pochází z cloudu namísto místní anchor, který jsme vytvořili.
* Vytvoření **sledovacích procesů** , který bude hledat ukotvení jsme nahráli do Azure prostorových ukotvení.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=267-305&highlight=13-31,35-36)]

Teď přidáme náš `ResetSession()` a `CleanupObjects()` metody. Můžete vložit níže `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=119-172)]

Nyní potřebujeme kód, který bude vyvolán při nachází anchor, který jsme už dotazování pro připojení. Uvnitř `InitializeSession()`, přidejte následující zpětná volání:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=200-206&highlight=4-5)]

 
Pojďme přidat kód, který bude vytvoření & na místě kouli zelené po CloudSpatialAnchor se nachází. Také umožní obrazovky klepněte znovu, takže můžete opakovat celý scénář ještě jednou: vytvořit jiného místního ukotvení, nahrajte ho a znovu ho najít.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=228-265)]

A to je vše! Spuštění aplikace z **sady Visual Studio** jednou vyzkoušet si celý scénář začátku do konce. Pohyb zařízení a umístěte bílé oblasti. Potom přesouvat hlavě k zaznamenání dat o prostředí, dokud oblasti se změní na žlutou. Nahraje místní ukotvení a bude modře vaší oblasti. A konečně klepněte na obrazovku ještě jednou klikněte tak, aby místní ukotvení se odebere a pak jsme dotaz pro jeho protějšek cloudu. Pokračujte, dokud se nachází vaše cloudové prostorových ukotvení přesouvat zařízení. Zelená koule by se měla objevit ve správném umístění a může zasažení vodou a znovu opakujte celý scénář.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]
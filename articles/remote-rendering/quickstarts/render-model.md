---
title: Vykreslení modelu pomocí Unity
description: Rychlý start, který uživatele provede kroky k vykreslení modelu
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b0af45ba4a6b1ca7f9e751af082ff0db80776ec0
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679783"
---
# <a name="quickstart-render-a-model-with-unity"></a>Úvodní příručka: Vykreslení modelu pomocí Unity

Tento rychlý start popisuje, jak spustit ukázku Unity, která na dálku vykreslí vestavěný model pomocí služby Azure Remote Rendering (ARR).

Nebudeme zacházet do podrobností o samotném Api ARR ani o tom, jak nastavit nový projekt Unity. Tato témata jsou popsána v [kurzu: Nastavení projektu Unity od nuly](../tutorials/unity/project-setup.md).

V tomto rychlém startu se dozvíte, jak:
> [!div class="checklist"]
>
>* Nastavení místního vývojového prostředí
>* Získání a sestavení ukázkové aplikace ARR Quickstart pro Unity
>* Vykreslení modelu v ukázkové aplikaci ARR Quickstart

## <a name="prerequisites"></a>Požadavky

Chcete-li získat přístup ke službě Vzdálené vykreslování Azure, musíte nejprve [vytvořit účet](../how-tos/create-an-account.md).

Musí být nainstalován následující software:

* Sada Windows SDK 10.0.18362.0 [(stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Nejnovější verze Visual Studia 2019 [(ke stažení)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(stáhnout)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(ke stažení)](https://unity3d.com/get-unity/download)
  * Nainstalujte tyto moduly v Unity:
    * **UPWP** – podpora sestavení univerzální platformy Windows
    * **IL2CPP** - Podpora sestavení systému Windows (IL2CPP)

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Otevřete příkazový `cmd` řádek (zadejte nabídku Start systému Windows) a změňte na adresář, do kterého chcete uložit ukázkový projekt ARR.

Spusťte následující příkazy:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Poslední příkaz vytvoří podadresář v adresáři ARR obsahující různé ukázkové projekty pro vzdálené vykreslování Azure.

Ukázková aplikace quickstart pro Unity se nachází v podadresáři *Unity/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Vykreslení modelu pomocí ukázkového projektu Unity

Otevřete centrum Unity a přidejte ukázkový projekt, což je složka *ARR\azure-remote-rendering\Unity\Quickstart.*
Otevřete projekt. V případě potřeby povolte Unity upgradovat projekt na nainstalovanou verzi.

Výchozí model, který vykreslujeme, je [vestavěný ukázkový model](../samples/sample-model.md). Ukážeme, jak převést vlastní model pomocí služby převodu ARR v [příštím rychlém startu](convert-model.md).

### <a name="enter-your-account-info"></a>Zadejte informace o svém účtu.

1. V prohlížeči datových zdrojů Unity přejděte do složky *Scény* a otevřete scénu **rychlého startu.**
1. V *hierarchii*vyberte objekt hry **RemoteRendering.**
1. V *inspektoru*zadejte [přihlašovací údaje svého účtu](../how-tos/create-an-account.md).

![Informace o účtu ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure Portal zobrazuje doménu vašeho účtu jenom jako *mixedreality.azure.com*. To je nedostatečné pro úspěšné připojení.
> Nastavte **accountdomain** `<region>.mixedreality.azure.com`na `<region>` , kde je [jedna z dostupných oblastí ve vašem okolí](../reference/regions.md).

Později chceme nasadit tento projekt na HoloLens a připojit se ke službě vzdáleného vykreslování z tohoto zařízení. Vzhledem k tomu, že nemáme snadný způsob, jak zadat přihlašovací údaje v zařízení, ukázka rychlého startu **uloží pověření ve scéně Unity**.

> [!WARNING]
> Ujistěte se, že projekt s uloženými přihlašovacími údaji nezkontrolujete do nějakého úložiště, kde by unikly tajné přihlašovací údaje!

### <a name="create-a-session-and-view-the-default-model"></a>Vytvoření relace a zobrazení výchozího modelu

Stisknutím tlačítka Unity **Play** spusťte relaci. V dolní části výřezu v panelu *Hra* by se mělo zobrazit překryvné překrytí se stavovým textem. Relace projde řadou přechodů stavu. Ve stavu **Spuštění** se vzdálený virtuální virtuální ms otáčí, což trvá několik minut. Po úspěchu přejde do stavu **Připraveno.** Nyní relace přejde do stavu **Připojení,** kde se pokusí dosáhnout vykreslování runtime na tomto virtuálním počítači. V případě úspěchu se ukázka přejde do stavu **Připojeno.** V tomto okamžiku začne stahovat model pro vykreslování. Vzhledem k velikosti modelu může stahování trvat ještě několik minut. Pak se zobrazí vzdáleně vykreslený model.

![Výstup ze vzorku](media/arr-sample-output.png)

Blahopřejeme! Nyní si prohlížíte vzdáleně vykreslený model!

## <a name="inspecting-the-scene"></a>Kontrola scény

Jakmile je spuštěno připojení vzdáleného vykreslování, panel Inspektor se aktualizuje o další informace o stavu:

![Unity ukázka přehrávání](./media/arr-sample-configure-session-running.png)

Nyní můžete prozkoumat graf scény výběrem nového uzlu a kliknutím na **Zobrazit podřízené položky** v inspektoru.

![Hierarchie jednoty](./media/unity-hierarchy.png)

Ve scéně je objekt [řezané roviny.](../overview/features/cut-planes.md) Zkuste ji povolit ve svých vlastnostech a přesunout ji:

![Změna roviny řezu](media/arr-sample-unity-cutplane.png)

Chcete-li synchronizovat transformace, klepněte buď na **tlačítko Synchronizovat nyní,** nebo zaškrtněte možnost **Synchronizovat každý snímek.** Pro vlastnosti komponenty stačí jejich změna.

## <a name="next-steps"></a>Další kroky

V dalším rychlém startu nasadíme ukázku do HoloLens, abychom zobrazili vzdáleně vykreslený model v původní velikosti.

> [!div class="nextstepaction"]
> [Úvodní příručka: Nasazení ukázky Unity do HoloLens](deploy-to-hololens.md)

Alternativně ukázka může být také nasazena do stolního počítače.

> [!div class="nextstepaction"]
> [Úvodní příručka: Nasazení ukázky Unity na plochu](deploy-to-desktop.md)
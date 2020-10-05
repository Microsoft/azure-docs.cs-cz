---
title: Nasazení nativního kurzu v jazyce C++ na HoloLens
description: Rychlý Start, který ukazuje spuštění nativního kurzu v jazyce C++ na HoloLens
author: florianborn71
ms.author: flborn
ms.date: 06/08/2020
ms.topic: quickstart
ms.openlocfilehash: 71760e9b54ff3a520f0784ecda4484bb3ea047e3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88892656"
---
# <a name="quickstart-deploy-native-c-sample-to-hololens"></a>Rychlý Start: nasazení nativní ukázky C++ na HoloLens

Tento rychlý Start popisuje, jak nasadit a spustit nativní výukovou aplikaci C++ na HoloLens 2.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
>
>* Sestavte aplikaci kurzu pro HoloLens.
>* Změňte přihlašovací údaje ARR ve zdrojovém kódu.
>* Nasaďte a spusťte na zařízení ukázku.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete získat přístup ke službě vzdáleného vykreslování Azure, musíte nejdřív [vytvořit účet](../../../how-tos/create-an-account.md).

Musí být nainstalovaný následující software:

* Windows SDK 10.0.18362.0 [(Stáhnout)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Nejnovější verzi sady Visual Studio 2019 [(Stáhnout)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools pro Mixed reality](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Konkrétně jsou nutné následující instalace *úloh* :
  * **Vývoj desktopových aplikací v C++**
  * **Vývoj Univerzální platforma Windows (UWP)**
* GIT [(Stáhnout)](https://git-scm.com/downloads)

## <a name="clone-the-arr-samples-repository"></a>Naklonujte úložiště ukázek ARR.

Jako první krok naklonujte úložiště Git, které zveřejňuje veřejné ukázky pro vzdálené vykreslování Azure. Otevřete příkazový řádek (typ `cmd` v nabídce Start systému Windows) a přejděte do adresáře, kam chcete uložit vzorový projekt Arr.

Spusťte následující příkazy:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Poslední příkaz vytvoří podadresář v adresáři ARR, který obsahuje různé ukázkové projekty pro vzdálené vykreslování Azure.

Kurz pro HoloLens v jazyce C++ najdete v podadresáři *NativeCpp/HoloLens*.

## <a name="build-the-project"></a>Sestavení projektu

Otevřete soubor řešení *HolographicApp. sln* umístěný v podadresáři *NativeCpp/HoloLens* v rámci sady Visual Studio 2019.

Přepněte konfiguraci buildu na *Debug* (nebo *release*) a *ARM64*. Také se ujistěte, že je režim ladicího programu nastavený na *zařízení* na rozdíl od *vzdáleného počítače*:

![Konfigurace sady Visual Studio](media/vs-config-native-cpp-tutorial.png)

Vzhledem k tomu, že přihlašovací údaje účtu jsou pevně zakódované ve zdrojovém kódu kurzu, změňte je na platné přihlašovací údaje. V takovém případě otevřete soubor `HolographicAppMain.cpp` v aplikaci Visual Studio a změňte část, kde se vytvoří front-end uvnitř konstruktoru třídy `HolographicAppMain` :

```cpp
// 2. Create front end
{
    // Users need to fill out the following with their account data and model
    RR::AzureFrontendAccountInfo init;
    init.AccountId = "00000000-0000-0000-0000-000000000000";
    init.AccountKey = "<account key>";
    init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
    m_modelURI = "builtin://Engine";
    m_sessionOverride = ""; // If there is a valid session ID to re-use, put it here. Otherwise a new one is created
    m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
}
```

Konkrétně změňte následující hodnoty:
* `init.AccountId` a `init.AccountKey` k používání dat účtu. Přečtěte si článek o tom, jak [načíst informace o účtu](../../../how-tos/create-an-account.md#retrieve-the-account-information).
* Část `init.AccountDomain` řetězce pro jiné oblasti `westus2` , než například, např. `"westeurope.mixedreality.azure.com"`
* Kromě toho se `m_sessionOverride` dá změnit na existující ID relace. Relace je možné vytvořit mimo tuto ukázku, například pomocí [skriptu PowerShellu](../../../samples/powershell-example-scripts.md#script-renderingsessionps1) nebo přímo pomocí [REST API relace](../../../how-tos/session-rest-api.md#create-a-session) .
Vytvoření relace mimo ukázku se doporučuje, když by se vzorek spouštěl víckrát. Pokud není předána žádná relace, ukázka při každém spuštění vytvoří novou relaci, která může trvat několik minut.

Nyní může být aplikace zkompilována.

## <a name="launch-the-application"></a>Spuštění aplikace

1. Připojte HoloLens k počítači pomocí kabelu USB.
1. Zapněte HoloLens a počkejte, než se zobrazí nabídka Start.
1. Spusťte ladicí program v aplikaci Visual Studio (F5). Aplikace se automaticky nasadí do zařízení.

Ukázková aplikace by se měla spustit a zobrazí se textový panel, který vás informuje o aktuálním stavu aplikace. Stav při spuštění je buď spuštění nové relace, nebo připojení k existující relaci. Po dokončení načítání modelu se vestavěný model motoru zobrazí přímo na pozici hlavního panelu. Překrytí model modulu pracuje správně s otáčející se datovou krychlí, která je vykreslena místně.

 Pokud chcete ukázku spustit později později, můžete ji také najít v nabídce Start HoloLens, ale Všimněte si, že do ní může být zkompilováno ID relace s vypršenou platností.

## <a name="next-steps"></a>Další kroky

Tento rychlý Start je založený na výsledku kurzu, který vysvětluje, jak integrovat všechny části související se vzdáleným vykreslováním do burzovní *holografické aplikace*. Postup, který je potřeba, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Kurz: integrace vzdáleného vykreslování do holografické aplikace HoloLens](../../../tutorials/native-cpp/hololens/integrate-remote-rendering-into-holographic-app.md)

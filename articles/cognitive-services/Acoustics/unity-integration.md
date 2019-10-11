---
title: Nasazení a integrace Unity v projektu
titlesuffix: Azure Cognitive Services
description: Tento článek popisuje, jak do vašeho projektu Unity integrovat modul plug-in Unity s akustickými projekty.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243000"
---
# <a name="project-acoustics-unity-integration"></a>Integrace Unity v projektu
Tento článek popisuje, jak do vašeho projektu Unity integrovat modul plug-in Unity s akustickými projekty.

Požadavky na software:
* [Unity 2018.2 +](https://unity3d.com) pro Windows
* [Balíček Unity s akustickými projekty](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Import modulu plug-in
1. Importujte akustické UnityPackagey do projektu. 
 V Unity jděte do **assets** > **Import balíčku** > **vlastní balíček**.

    ![Nabídka balíčku importu Unity](media/import-package.png)  

1. Vyberte **ProjectAcoustics. unitypackage**.

1. Vyberte tlačítko **importovat** a integrujte balíček Unity do svého projektu.

    ![Dialogové okno pro Import balíčku Unity](media/import-dialog.png)  

Pokud importujete modul plug-in do existujícího projektu, projekt již může mít v kořenu projektu soubor *MCS. rsp* . Tento soubor určuje možnosti pro C# kompilátor. Slučte obsah tohoto souboru se souborem MCS. rsp, který je součástí modulu plug-in projekt akustického množství.

## <a name="enable-the-plug-in"></a>Povolit modul plug-in
Zanesli část akustického navýšení sady nástrojů vyžaduje rozhraní .NET 4. verze modulu runtime skriptování *x* Import balíčku aktualizuje nastavení přehrávače Unity. Restartujte Unity, aby se toto nastavení projevilo.

![Panel nastavení přehrávače Unity](media/player-settings.png)

![Panel nastavení přehrávače Unity s vybraným .NET 4,5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Nastavení DSP zvukového zařízení
Akustické projekty zahrnují rozhraní DSP pro modul runtime zvuku, které se integruje do spatializer architektury zvukového modulu Unity. Zahrnuje jak na základě HRTF, tak pro posouvání. Pokud chcete povolit **, aby se**projekt mohl nacházet z akustického přenosu, otevřete nastavení projektu  > **nastavení projektu** > **zvuk** a otevřete nastavení zvuk Unity. Jako **modul plug-in Spatializer** pro váš projekt vyberte **akustické projekty** . Ujistěte se, že je **Velikost vyrovnávací paměti DSP** nastavena na *nejlepší výkon*.

![Nabídka nastavení projektu Unity](media/project-settings.png)  

![Panel nastavení Spatializer Unity s možnostmi akustického projektu Spatializer vybrané](media/choose-spatializer.png)

V dalším kroku otevřete směšovač zvuku (**okno** > **směšovač zvuku**). Ujistěte se, že máte alespoň jednu směšovač s jednou skupinou. Pokud ho nemáte, vyberte tlačítko **+** napravo od **směšovačů**. Pravým tlačítkem myši klikněte na dolní část pruhu kanálu v části efekty a přidejte efekt **směšovače akustického množství Microsoftu** . V jednom okamžiku se podporuje jenom jeden směšovač akustického projektu.

![Směšovač zvuku v Unity, který hostuje směšovač akustického projektu](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Povolit akustické zvuky na zdrojích zvuku
Vytvořit zdroj zvuku: zaškrtněte políčko **Spatialize** v dolní části panelu inspektora AudioSource. Ujistěte se, že je **prostorový Blend** nastavený na úplný *3D*.  

![Panel zdroje zvuk Unity](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Povolit akustický návrh
Připojte skript *AcousticsAdjust* ke zdroji zvuku ve scéně, abyste mohli povolit další parametry pro návrh zdrojového kódu: vyberte **Přidat komponentu** a zvolte **skripty** > **upravitelné akustické hlasitost**.

![Skript Unity AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Další kroky
* [Zanesli své scény díky akustickým projektům pro Unity](unity-baking.md).
* [Vytvořte účet Azure Batch](create-azure-account.md) , abyste zanesli svou scénu v cloudu.
* Prozkoumejte [proces návrhu Unity s akustickými projekty](unity-workflow.md).

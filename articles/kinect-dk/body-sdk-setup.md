---
title: Rychlý Start – Nastavení sledování textu v Azure Kinect
description: V tomto rychlém startu vytvoříte sadu SDK pro sledování těla pro Azure Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, snímač, Access, Depth, SDK, tělo, sledování, společné, nastavení, CUDA, NVIDIA
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277945"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Rychlý Start: nastavení sledování těla Kinect Azure

Tento rychlý Start vás provede procesem získání sledování těla, které běží na vaší službě Azure Kinect DK.

## <a name="system-requirements"></a>Požadavky na systém

Sada SDK pro sledování textu vyžaduje, aby byl na hostitelském počítači nainstalovaný grafický procesor NVIDIA. Doporučený požadavek na počítač pro sledování textu hostitele je popsaný na stránce [požadavky na systém](system-requirements.md) .

## <a name="install-software"></a>Instalovat software

### <a name="install-the-latest-nvidia-driver"></a>[Nainstalovat nejnovější ovladač NVIDIA](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Stáhněte si a nainstalujte nejnovější ovladač NVIDIA pro grafickou kartu. Starší ovladače nemusí být kompatibilní s CUDA binárními soubory, které jsou předistribuované pomocí sady SDK pro sledování textu.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Visual C++ Redistributable pro Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Stáhněte a nainstalujte Distribuovatelné součásti Visual C++ pro Visual Studio 2015. 

## <a name="set-up-hardware"></a>Nastavení hardwaru

### <a name="set-up-azure-kinect-dk"></a>[Nastavení sady Azure Kinect DK](set-up-azure-kinect-dk.md)

Spusťte [prohlížeč Azure Kinect](azure-kinect-viewer.md) a ověřte, jestli je správně nastavená vaše Azure Kinect DK.

## <a name="download-the-body-tracking-sdk"></a>Stažení sady Body Tracking SDK
 
1. Vyberte odkaz pro [Stažení sady SDK pro sledování textu](body-sdk-download.md)
2. Nainstalujte do svého počítače sadu SDK pro sledování textu.

## <a name="verify-body-tracking"></a>Ověřit sledování textu

Spusťte **prohlížeč pro sledování textu v Azure Kinect** a ověřte, jestli je správně nastavená sada SDK pro sledování textu. Prohlížeč se instaluje s instalačním programem sady SDK MSI. Můžete ji najít v nabídce Start nebo v `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` .

Pokud nemáte dostatečně výkonný grafický procesor a přesto chcete výsledek otestovat, můžete spustit **prohlížeč sledování textu v Azure Kinect** na příkazovém řádku pomocí následujícího příkazu: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Pokud je všechno správně nastavené, mělo by se zobrazit okno s cloudem a sledovanými subjekty v 3D bodě.


![Sledování obsahu – prohlížeč 3D](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Příklady

Příklady, jak používat sadu SDK pro sledování textu, najdete [tady](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Vytvoření první aplikace pro sledování pohybu těla](build-first-body-app.md)


---
title: O sadě Azure Kinect snímače SDK
description: Přehled služby Azure Kinect senzor Software Development Kit (SDK), jejích funkcí a nástrojů.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, Kinect, RGB, IR, nahrávání, senzor, SDK, přístup, Hloubka, video, fotoaparát, imu, pohyb, senzor, zvuk, mikrofon, Matroska, sada SDK pro sadu senzorů, stažení
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277167"
---
# <a name="about-azure-kinect-sensor-sdk"></a>O sadě Azure Kinect snímače SDK

Tento článek obsahuje přehled služby Azure Kinect senzor Software Development Kit (SDK), jejích funkcí a nástrojů.

## <a name="features"></a>Funkce

SADA Kinect snímače pro Azure poskytuje přístup na nízké úrovni pro datové proudy zařízení Azure Kinect a hardwarové senzory, včetně těchto:

- Hloubkový ovládací prvek přístupu a režim kamery (pasivní režim IR, plus celé a zúžené hloubkové režimy zobrazení) 
- Přístup a ovládání kamery RGB (například vystavení a vyvážení bílé) 
- Přístup snímače pohybu (vybavený gyroskopem and akcelerometr) 
- Synchronizovaná hloubka – streamování kamery RGB s konfigurovatelným zpožděním mezi fotoaparáty 
- Řízení synchronizace externích zařízení s konfigurovatelným posunem zpoždění mezi zařízeními 
- Přístup k metadatům snímku kamery pro rozlišení obrazu, časové razítko atd. 
- Přístup k datům kalibrace zařízení 

## <a name="tools"></a>Nástroje

- [Prohlížeč Azure Kinect](azure-kinect-viewer.md) , který umožňuje monitorovat datové proudy zařízení a konfigurovat různé režimy.
- Rozhraní API pro nahrávání a přehrávání ve [službě Azure Kinect](azure-kinect-recorder.md) , které používá [Formát kontejneru Matroska](record-file-format.md).
- [Nástroj pro aktualizaci firmwaru](azure-kinect-firmware-tool.md)Azure Kinect DK.

## <a name="sensor-sdk"></a>SADA senzorů

- [Stáhněte si sadu SDK pro senzory](sensor-sdk-download.md).
- Sada senzor SDK je k dispozici v [Open source na GitHubu](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).
- Další informace o využití najdete v [dokumentaci k rozhraní API sady senzor SDK](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html).

## <a name="next-steps"></a>Další kroky

Nyní jste se dozvěděli o sadě Azure Kinect senzor SDK, můžete také:
>[!div class="nextstepaction"]
>[Stáhnout kód sady SDK pro senzory](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Najít a otevřít zařízení](find-then-open-device.md)

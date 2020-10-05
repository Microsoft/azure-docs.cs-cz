---
title: Informace o Azure Kinect DK
description: Přehled nástrojů a integrovaných služeb Azure Kinect Developer Kit (DK).
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: overview
ms.date: 06/26/2019
keywords: Azure, Kinect, přehled, dev Kit, DK, zařízení, Hloubka, sledování textu, rozpoznávání řeči, rozpoznávání služeb, sady SDK, sada SDK, firmware
ms.openlocfilehash: 6b3215ba6761521214ebffa2a100c5a1a030ab4c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277948"
---
# <a name="about-azure-kinect-dk"></a>Informace o Azure Kinect DK

 ![Azure Kinect DK](./media/index/device-image.jpg)

Azure Kinect DK je vývojářská sada s pokročilými senzory AI, které poskytují sofistikované modely pro počítačové zpracování obrazu a řeči.  Kinect obsahuje senzor hloubky, prostorové mikrofonní pole s videokamerou a senzor orientace ve formě malého komplexního zařízení s několika režimy, možnostmi a sadami SDK (Software Development Kit). Je k dispozici pro nákup v [Microsoft Online Storu](https://www.microsoft.com/p/azure-kinect-dk/8pp5vxmd9nhq).

Vývojové prostředí Azure Kinect DK se skládá z následujících sad SDK:

- Sada snímač SDK pro senzor nízké úrovně a přístup k zařízení.
- Sada SDK pro sledování těla pro sledování subjektů v 3D.
- Speech Cognitive Services SDK pro povolení přístupu k mikrofonu a cloudových služeb Azure.

Kromě toho je možné použít službu rozpoznávání zraku s fotoaparátem RGB zařízení.

   ![Diagram sady Azure Kinect SDK](./media/quickstarts/sdk-diagram.jpg)

## <a name="azure-kinect-sensor-sdk"></a>Sada Azure Kinect Sensor SDK

SADA Kinect senzorů Azure poskytuje přístup snímače nízké úrovně pro hardwarové senzory Azure Kinect DK a konfiguraci zařízení.

Další informace o sadě Kinect sady SDK pro Azure najdete v tématu [použití sady snímač SDK](about-sensor-sdk.md).

### <a name="azure-kinect-sensor-sdk-features"></a>Funkce sady Kinect pro senzory Azure

Sada snímač SDK má následující funkce, které fungují po instalaci a spuštění v Azure Kinect DK:

- Hloubkový ovládací prvek přístupu a režim kamery (pasivní režim IR, plus celé a zúžené hloubkové režimy zobrazení) 
- Přístup a ovládání kamery RGB (například vystavení a vyvážení bílé) 
- Přístup snímače pohybu (vybavený gyroskopem and akcelerometr) 
- Synchronizovaná hloubka – streamování kamery RGB s konfigurovatelným zpožděním mezi fotoaparáty 
- Řízení synchronizace externích zařízení s konfigurovatelným posunem zpoždění mezi zařízeními 
- Přístup k metadatům snímku kamery pro rozlišení obrazu, časové razítko atd. 
- Přístup k datům kalibrace zařízení 

### <a name="azure-kinect-sensor-sdk-tools"></a>Nástroje sady SDK pro Azure Kinect

V sadě snímač SDK jsou k dispozici následující nástroje:

- Nástroj pro prohlížeč, který umožňuje monitorovat datové proudy zařízení a konfigurovat různé režimy.
- Nástroj pro záznam snímačů a rozhraní API pro čtečku přehrávání, který používá formát kontejneru Matroska.
- Nástroj pro aktualizaci firmwaru Azure Kinect DK.

## <a name="azure-kinect-body-tracking-sdk"></a>Sada Azure Kinect Body Tracking SDK

Sada SDK pro sledování těla zahrnuje knihovnu Windows a modul runtime pro sledování institucí v 3D při použití s hardwarem Azure Kinect DK.

### <a name="azure-kinect-body-tracking-features"></a>Funkce pro sledování textu v Azure Kinect

V doprovodné sadě SDK jsou k dispozici následující funkce pro sledování textu:

- Poskytuje segmentaci těla.
- Obsahuje anatomicky správný kostru pro každý částečný nebo úplný text v FOV.
- Nabízí jedinečnou identitu každého těla.
- Může sledovat subjekty v průběhu času.

### <a name="azure-kinect-body-tracking-tools"></a>Nástroje pro sledování textu v Azure Kinect

- Nástroj pro sledování textu má nástroj pro prohlížení, který umožňuje sledovat texty v 3D.

## <a name="speech-cognitive-services-sdk"></a>Sada Speech vnímání Services SDK

Sada Speech SDK umožňuje služby řeči připojené ke službám Azure.

### <a name="speech-services"></a>Hlasové služby

- Převod řeči na text
- Překlad řeči
- Převod textu na řeč

>[!NOTE]
>Azure Kinect DK nemá reproduktory.

Další podrobnosti a informace najdete v [dokumentaci ke službě Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="vision-services"></a>Služby Vision

Níže uvedené [služby Azure vnímání Vision](https://azure.microsoft.com/services/cognitive-services/directory/vision/) poskytují služby Azure, které mohou identifikovat a analyzovat obsah v rámci obrázků a videí.

- [Počítačové zpracování obrazu](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
- [Rozpoznávání tváře](https://azure.microsoft.com/services/cognitive-services/face/)
- [Video indexer](https://azure.microsoft.com/services/media-services/video-indexer/)
- [Moderátor obsahu](https://azure.microsoft.com/services/cognitive-services/content-moderator/)
- [Vlastní vize](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)

Služby se neustále víjejí a zlepšují, takže si nezapomeňte pravidelně kontrolovat nové nebo další [služby pro rozpoznávání](https://azure.microsoft.com/services/cognitive-services/) , které vám pomůžou aplikaci zlepšit. Chcete-li se podívat na nově vznikající nové služby, podívejte se na [cvičení pro rozpoznávání služeb](https://labs.cognitive.microsoft.com/).

## <a name="azure-kinect-hardware-requirements"></a>Požadavky na hardware pro Azure Kinect

Azure Kinect DK integruje nejnovější technologii snímače Microsoftu do jediného příslušenství připojeného přes USB. Další informace najdete v tématu [specifikace hardwaru v Azure Kinect DK](hardware-specification.md).

## <a name="next-steps"></a>Další kroky

Teď máte přehled o Azure Kinect DK. Dalším krokem je podrobně a jeho nastavení.

> [!div class="nextstepaction"]
>[Rychlý Start: nastavení Azure Kinect DK](set-up-azure-kinect-dk.md)

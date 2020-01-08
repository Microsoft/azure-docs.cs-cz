---
title: Certifikace zařízení IoT technologie Plug and Play Preview | Microsoft Docs
description: V tomto kurzu se dozvíte, jak přidat informace o produktu do katalogu zařízení Azure Certified for IoT, jak zařízení připojit k certifikační službě Azure IoT a pak spustit testy technologie Plug and Play IoT.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550158"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Kurz: certifikace zařízení IoT technologie Plug and Play Preview

Pokud chcete publikovat zařízení IoT technologie Plug and Play ve verzi Preview v [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat), musí předat sadu ověřovacích testů. K odeslání vašeho zařízení k certifikaci použijte portál [Azure Certified for IoT](https://aka.ms/ACFI) . [Certifikační služba Azure IoT](https://aka.ms/azure-iot-aics) spouští testy certifikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Jaké jsou požadavky na certifikaci technologie Plug and Play IoT.
> * Jak na portál přidat název a informace o produktu.
> * Jak připojit a zjišťovat technologie Plug and Play rozhraní IoT
> * Jak zkontrolovat technologie Plug and Play rozhraní IoT a spustit testy certifikace.
> * Jak publikovat certifikované zařízení IoT technologie Plug and Play do katalogu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Účet partnerského centra Microsoftu.
* ID Microsoft Partner Network.

Další informace najdete v tématu [Postup při připojování k portálu Azure Certified for IoT](howto-onboard-portal.md) .

## <a name="certification-requirements"></a>Požadavky na certifikaci

K certifikaci zařízení technologie Plug and Play IoT musí zařízení splňovat následující požadavky:

* Váš kód zařízení IoT technologie Plug and Play musí být nainstalovaný na vašem zařízení.
* Váš kód zařízení technologie Plug and Play IoT je sestavený pomocí sady Azure IoT SDK.
* Váš kód zařízení musí podporovat [IoT Hub Device Provisioning Service Azure](../iot-dps/about-iot-dps.md).
* Váš kód zařízení musí implementovat [rozhraní informací o zařízení](concepts-common-interfaces.md).
* Model schopností a kód zařízení fungují s IoT Central.

Všechna zařízení, která se v katalogu aktuálně nacházejí, se považují za zařízení, která jsou předem certifikována technologie Plug and Play IoT. Nezaručuje to kvalitu a dodržování předpisů konečnému produktu IoT technologie Plug and Play softwarové komponenty, jako je například sada SDK a jazyk digitálního vlákna s definicemi.

Všechna předběžně certifikovaná zařízení IoT technologie Plug and Play musí být znovu certifikována při obecné dostupnosti technologie Plug and Play IoT na základě konečné verze požadavků certifikace a softwarových součástí poskytovaných společností Microsoft.

## <a name="add-product-name"></a>Přidat název produktu

**Produkt** je popisný název modelu produktu, který si zákazník může koupit. Přidání produktu:

1. Přihlaste se k portálu [Azure Certified for IoT](https://aka.ms/ACFI) .
1. Na portálu v nabídce vlevo přejděte na stránku **produkty** a vyberte **+ Nový**.
1. Zadejte popisný název produktu a vyberte **vytvořit**. Název, který se tady zadal, se liší od názvu zobrazeného v katalogu zařízení.

## <a name="add-product-information"></a>Přidat informace o produktu

Po úspěšném vytvoření produktu na portálu se produkt zobrazí na stránce **produkty** . Přidání informací o produktu:

1. Vyberte odkaz vytvořená na produkt, který najdete na stránce **produktu** ve sloupci produkt. Stav by měl být ve stavu konceptu.
1. Vyberte odkaz **Upravit** vedle položky **produkt informace o produktu** . Na stránce informace o produktu zadejte informace o produktu a ujistěte se, že jste dokončili všechna povinná pole.
1. Vyberte **Uložit**. Tlačítko **Uložit** se zobrazí pouze v případě, že jste dokončili všechna požadovaná pole. Pokud jsou pole neúplná, tlačítko oznámí **pozdější uložení a dokončení**.
1. Zkontrolujte obsah, který jste zadali. Dokončete všechna povinná pole pro publikování zařízení do katalogu zařízení. Na stránce s podrobnostmi o produktu se můžete kdykoli vrátit a upravit informace o produktu kliknutím na odkaz **Upravit** vedle položky **produkt informace** .

## <a name="connect-and-discover-interfaces"></a>Připojení a vyhledání rozhraní

Pokud chcete spustit testy certifikace, připojte zařízení k [certifikační službě Azure IoT](https://aka.ms/azure-iot-aics) (AICS), která je k dispozici na portálu.

Tyto kroky jsou jednorázovým krokem pro spouštění certifikačních testů a není nutné měnit kód zařízení produktu. Začněte tím, že se připojíte k AICS pomocí těchto kroků:

1. Přihlaste se k portálu pomocí účtu partnerského centra.
1. Kliknutím na **připojit + test** spustíte tok certifikace.
1. Vyberte [metodu ověřování](../iot-dps/concepts-security.md#attestation-mechanism) , která zřídí vaše zařízení AICS pomocí [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
   * Pokud používáte [certifikát x. 509](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites), nahrajte vygenerovaný certifikát x. 509. Možná budete chtít zkontrolovat vzorový kód, který ukazuje, jak používat certifikáty X. 509: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Pokud používáte [symetrický klíč](../iot-dps/concepts-symmetric-key-attestation.md), zkopírujte a vložte symetrický klíč do kódu zařízení.
   * V tuto chvíli není podporována metoda ověřování TPM.
1. Zkopírujte a vložte následující vygenerovaná ID do kódu zařízení.
   * [ID registrace](../iot-dps/use-hsm-with-sdk.md)
   * [ID DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Koncový bod DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Když se váš kód zařízení sestaví a nasadí na zařízení, vyberte **připojit** a vyhledejte rozhraní IoT technologie Plug and Play.
1. Po úspěšném připojení k AICS vyberte **Další** a zkontrolujte zjištěná rozhraní IoT technologie Plug and Play.

## <a name="run-tests-and-publish-the-device"></a>Spuštění testů a publikování zařízení

Na stránce Kontrola si můžete prohlédnout zjištěná rozhraní IoT technologie Plug and Play. Pomocí této stránky lze kontrolovat, zda jsou primitivní prvky implementované v rozhraní správně zobrazovány. Můžete také ověřit umístění rozhraní.

1. Zajistěte, aby byly pro požadovaná pole zadány vstupy datové části. Tato pole obsahují informace o datové části pro primitivní příkazy pro zadané rozhraní.
1. Až zadáte všechny požadované informace, vyberte **Další**.
1. Chcete-li spustit testy pro implementovaná rozhraní IoT technologie Plug and Play, vyberte možnost **Spustit testy**.
1. Všechny testy se spustí automaticky. Pokud se některý test nezdařil, vyberte **Zobrazit protokoly** a zobrazte tak chybové zprávy z AICS a nezpracovaná telemetrie odeslání do Azure IoT Hub.
1. Pokud chcete dokončit testy certifikátů, vyberte **Dokončit**.
1. Publikujte certifikované zařízení IoT technologie Plug and Play do katalogu. Chcete-li přidat certifikované zařízení do katalogu, vyberte možnost **Přidat do katalogu** na panelu nástrojů. Pokud je možnost **Přidat do katalogu** šedá, znamená to, že informace o produktu jsou neúplné nebo testy selhaly. 
1. Kliknutím na odkaz CERTIFIKOVÁNo a v katalogu zobrazíte publikované zařízení v katalogu zařízení.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o certifikaci zařízení IoT technologie Plug and Play, je navržený další krok, kde najdete další informace o správě modelů schopností:

> [!div class="nextstepaction"]
> [Správa modelů](./howto-manage-models.md)

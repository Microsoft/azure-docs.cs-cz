---
title: Certifikace zařízení IoT Plug and Play Preview | Dokumenty společnosti Microsoft
description: Tento kurz popisuje, jak přidat informace o produktech do katalogu zařízení Azure Certified for IoT, připojit zařízení k certifikační službě Azure IoT a pak spustit certifikační testy IoT Plug and Play.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75550158"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Kurz: Certifikace zařízení IoT Plug and Play Preview

Chcete-li publikovat zařízení IoT Plug and Play Preview v [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat), musí projít sadou certifikačních testů. K odeslání zařízení k certifikaci použijte portál [Azure Certified for IoT.](https://aka.ms/ACFI) [Certifikační služba Azure IoT](https://aka.ms/azure-iot-aics) spouští certifikační testy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Jaké jsou požadavky na certifikaci IoT Plug and Play.
> * Jak přidat název produktu a informace na portál.
> * Jak se připojit a objevit rozhraní IoT Plug and Play.
> * Jak zkontrolovat rozhraní IoT Plug and Play a spustit certifikační testy.
> * Jak publikovat certifikované zařízení IoT Plug and Play do katalogu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Účet Centra partnerů společnosti Microsoft.
* ID sítě partnerů společnosti Microsoft.

Další informace najdete [v tématu Onboard na azure certified for IoT](howto-onboard-portal.md) portal.

## <a name="certification-requirements"></a>Požadavky na certifikaci

Chcete-li certifikovat zařízení IoT Plug and Play, musí vaše zařízení splňovat následující požadavky:

* Kód zařízení IoT Plug and Play musí být na instalovaný v zařízení.
* Kód zařízení IoT Plug and Play je vytvořený pomocí sady Azure IoT SDK.
* Kód vašeho zařízení musí podporovat [službu Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
* Kód zařízení musí implementovat [informační rozhraní zařízení](concepts-common-interfaces.md).
* Model schopností a kód zařízení pracují s IoT Central.

Všechna zařízení, která jsou aktuálně v katalogu, jsou považována za předem certifikovaná zařízení IoT Plug and Play. Nezaručuje kvalitu a shodu konečného produktu softwarových komponent IoT Plug and Play, jako je sada SDK a jazyk Digital Twin Definition Language.

Všechna předem certifikovaná zařízení IoT Plug and Play musí být recertifikována podle obecné dostupnosti technologie IoT Plug and Play na základě konečné verze požadavků na certifikaci a softwarových komponent poskytovaných společností Microsoft.

## <a name="add-product-name"></a>Přidání názvu produktu

**Produkt** je popisný název modelu výrobku, který si zákazník může zakoupit. Přidání produktu:

1. Přihlaste se k portálu [Azure Certified for IoT.](https://aka.ms/ACFI)
1. Přejděte na stránku **Produkty** na portálu v levé nabídce a vyberte **+ Nový**.
1. Zadejte popisný název produktu a vyberte **Vytvořit**. Zde zadaný název se liší od názvu zobrazeného v katalogu zařízení.

## <a name="add-product-information"></a>Přidání informací o produktu

Po úspěšném vytvoření produktu na portálu se produkt zobrazí na stránce **Produkty.** Přidání informací o produktu:

1. Vyberte odkaz na vytvořený produkt, který se nachází na stránce **Produkt** ve sloupci produktu. Stát by měl být ve stavu konceptu.
1. Vyberte odkaz **Upravit** vedle **nadpisu Informace o produktu.** Zadejte informace o produktu na stránce s informacemi o produktu a vyplňte všechna požadovaná pole.
1. Vyberte **Uložit**. Tlačítko **Uložit** se zobrazí pouze po dokončení všech požadovaných polí. Pokud jsou pole neúplná, na tlačítku je **uvedeno Uložit a dokončit později**.
1. Zkontrolujte obsah, který jste zadali. Vyplňte všechna požadovaná pole pro publikování zařízení do katalogu zařízení. Chcete-li provést úpravy informací o produktu na stránce s podrobnostmi o produktu, můžete se vždy vrátit k úpravám informací o produktu kliknutím na odkaz **pro úpravy** vedle **nadpisu Informace o produktu.**

## <a name="connect-and-discover-interfaces"></a>Připojení a zjišťovací rozhraní

Chcete-li spustit certifikační testy, připojte zařízení k [certifikační službě Azure IoT](https://aka.ms/azure-iot-aics) (AICS), která je dostupná na portálu.

Tyto kroky jsou jedním časovým krokem pro spuštění certifikačních testů a není nutné měnit kód zařízení produktu. Chcete-li začít, připojte se k AICS následujícím způsobem:

1. Přihlaste se k portálu pomocí účtu Partnerského centra.
1. Klikněte na **Připojit + test** pro spuštění certifikačního toku.
1. Zvolte [metodu ověřování](../iot-dps/concepts-security.md#attestation-mechanism) pro zřizování zařízení do AICS pomocí [služby Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).
   * Pokud používáte [certifikát X.509](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites), nahrajte vygenerovaný certifikát X.509. Můžete zkontrolovat ukázkový kód, který ukazuje, jak používat certifikáty X.509: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Pokud používáte [symetrický klíč](../iot-dps/concepts-symmetric-key-attestation.md), zkopírujte a vložte symetrický klíč do kódu zařízení.
   * Metoda ověřování čipu TPM není v tuto chvíli podporována.
1. Zkopírujte a vložte následující vygenerovaná ID do kódu zařízení.
   * [ID registrace](../iot-dps/use-hsm-with-sdk.md)
   * [DPS ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Koncový bod DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Když je kód zařízení vytvořený a nasazený do zařízení, vyberte **Připojit** a zjišťujete rozhraní IoT Plug and Play.
1. Když je připojení k AICS úspěšné, vyberte **další** a zkontrolujte zjištěná rozhraní IoT Plug and Play.

## <a name="run-tests-and-publish-the-device"></a>Spuštění testů a publikování zařízení

Na stránce recenze si můžete prohlédnout zjištěná rozhraní IoT Plug and Play. Na této stránce můžete zkontrolovat primitiva implementovaná v zobrazení rozhraní správně. Můžete také zkontrolovat umístění rozhraní.

1. Ujistěte se, že jsou zadány datové části pro požadovaná pole. Tato pole obsahují informace o datové části pro základní příkaz pro zadané rozhraní.
1. Po zadání všech požadovaných informací vyberte **Další**.
1. Chcete-li spustit testy implementovaných rozhraní IoT Plug and Play, vyberte **spustit testy**.
1. Všechny testy spustit automaticky. Pokud se některý test nezdaří, vyberte **zobrazit protokoly** pro zobrazení chybových zpráv z AICS a nezpracované telemetrie odeslané do služby Azure IoT Hub.
1. Chcete-li dokončit certifikační testy, vyberte **možnost Dokončit**.
1. Publikujte certifikované zařízení IoT Plug and Play do katalogu. Pokud chcete přidat certifikované zařízení do katalogu, vyberte Přidat **do katalogu** na panelu nástrojů. Pokud je **katalog Přidat do** šedě, znamená to, že informace o produktu jsou neúplné nebo testy selhaly. 
1. Chcete-li zobrazit publikované zařízení v katalogu zařízení, vyberte odkaz "CERTIFIKOVÁNO A V KATALOGU".

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o certifikaci zařízení IoT Plug and Play, je dalším doporučeným krokem další informace o správě modelů schopností:

> [!div class="nextstepaction"]
> [Správa modelů](./howto-manage-models.md)

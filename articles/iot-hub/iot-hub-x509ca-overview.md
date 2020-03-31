---
title: Přehled zabezpečení Azure IoT Hub X.509 CA | Dokumenty společnosti Microsoft
description: Přehled – jak ověřit zařízení do služby IoT Hub pomocí certifikačních úřadů X.509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 3d02d3573902964a8549fa0eeb1f4f1471de1752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284509"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Ověřování zařízení pomocí certifikátů webu X.509

Tento článek popisuje použití certifikátů Certifikační autority X.509 (CA) k ověření zařízení připojujících službu IoT Hub.  V tomto článku se dozvíte:

* Jak získat certifikát certifikační autority X.509
* Jak zaregistrovat certifikát Certifikační autority X.509 do služby IoT Hub
* Jak podepsat zařízení pomocí certifikátů Certifikační autority X.509
* Ověřování zařízení podepsaných pomocí certifikační autority X.509

## <a name="overview"></a>Přehled

Funkce Certifikační autority X.509 umožňuje ověřování zařízení v centru IoT Hub pomocí certifikační autority ( CA). Výrazně zjednodušuje počáteční proces registrace zařízení a logistiku dodavatelského řetězce během výroby zařízení. [Další informace v tomto scénáři článku o hodnotě pomocí X.509 certifikáty certifikační autority](iot-hub-x509ca-concept.md) pro ověřování zařízení.  Doporučujeme vám přečíst si tento scénář článku před pokračováním, protože vysvětluje, proč existují následující kroky.

## <a name="prerequisite"></a>Požadavek

Použití funkce Certifikační autority X.509 vyžaduje, abyste měli účet služby IoT Hub.  [Přečtěte si, jak vytvořit instanci služby IoT Hub,](quickstart-send-telemetry-dotnet.md) pokud ji ještě nemáte.

## <a name="how-to-get-an-x509-ca-certificate"></a>Jak získat certifikát certifikační autority X.509

Certifikát certifikační autority X.509 je v horní části řetězce certifikátů pro každé z vašich zařízení.  Můžete si ji zakoupit nebo vytvořit v závislosti na tom, jak ji hodláte používat.

V produkčním prostředí doporučujeme zakoupit certifikát certifikační autority X.509 od veřejné kořenové certifikační autority. Zakoupení certifikátu certifikační autority má výhodu, že kořenová certifikační autorita funguje jako důvěryhodná třetí strana, která se zaručuje za legitimitu vašich zařízení. Zvažte tuto možnost, pokud chcete, aby vaše zařízení byla součástí otevřené sítě IoT, kde se očekává, že budou komunikovat s produkty nebo službami třetích stran.

Můžete také vytvořit certifikační autoritu X.509 podepsanou svým držitelem pro experimentování nebo pro použití v uzavřených sítích IoT.

Bez ohledu na to, jak získáte certifikát certifikační autority X.509, nezapomeňte zachovat jeho odpovídající soukromý klíč v tajnosti a vždy je chránit.  To je nezbytné pro vytváření důvěryhodnosti v ověřování certifikační autority X.509.

Přečtěte si, jak [vytvořit certifikát certifikační autority podepsaný svým držitelem](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), který můžete použít pro experimentování v celém popisu této funkce.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Přihlášení zařízení do řetězu důvěryhodnosti certifikátů

Vlastník certifikátu certifikační autority X.509 může kryptograficky podepsat zprostředkující certifikační autoritu, která může následně podepsat další zprostředkující certifikační autoritu a tak dále, dokud poslední zprostředkující certifikační autorita neukončí tento proces podepsáním zařízení. Výsledkem je kaskádový řetězec certifikátů známý jako řetěz důvěryhodnosti certifikátů. V reálném životě se to odehrává jako delegování důvěry směrem k podpisu zařízení. Toto delegování je důležité, protože vytváří kryptograficky variabilní řetězec úschovy a zabraňuje sdílení podpisových klíčů.

![img-generic-cert-chain-of-trust](./media/generic-cert-chain-of-trust.png)

Certifikát zařízení (nazývaný také listový certifikát) musí mít *název subjektu* nastavený na **ID zařízení,** které se používalo při registraci zařízení IoT v centru Azure IoT Hub. Toto nastavení je vyžadováno pro ověřování.

Tady se dozvíte, jak [vytvořit řetěz certifikátů,](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) jak je tomu bylo při podepisování zařízení.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Jak zaregistrovat certifikát Certifikační autority X.509 do služby IoT Hub

Zaregistrujte svůj certifikát Certifikační autority X.509 do služby IoT Hub, kde bude použit k ověření vašich zařízení během registrace a připojení.  Registrace certifikátu Certifikační autority X.509 je dvoustupňový proces, který zahrnuje nahrání souboru certifikátu a doklad o vlastnictví.

Proces nahrávání vyžaduje nahrání souboru, který obsahuje váš certifikát.  Tento soubor by nikdy neměl obsahovat žádné soukromé klíče.

Důkaz vlastnictví krok zahrnuje kryptografické výzvy a proces odezvy mezi vámi a IoT Hub.  Vzhledem k tomu, že obsah digitálního certifikátu je veřejný, a proto je náchylný k odposlouchávání, ioT Hub by rád zjistil, že certifikát certifikační autority skutečně vlastníte.  Učiní tak tak, že vygeneruje náhodnou výzvu, kterou musíte podepsat odpovídajícím soukromým klíčem certifikátu certifikační autority.  Pokud jste soukromý klíč uchovávali v tajnosti a chránili, jak bylo doporučeno dříve, pak pouze vy budete mít znalosti k dokončení tohoto kroku. Utajení soukromých klíčů je zdrojem důvěryhodnosti v této metodě.  Po podepsání výzvy proveďte tento krok nahráním souboru obsahujícího výsledky.

Zde se dozvíte, jak [zaregistrovat certifikát certifikační autority.](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub)

## <a name="how-to-create-a-device-on-iot-hub"></a>Jak vytvořit zařízení ve službě IoT Hub

Chcete-li zabránit zosobnění zařízení, IoT Hub vyžaduje, abyste mu vědět, jaká zařízení očekávat.  To provést vytvořením položky zařízení v registru zařízení služby IoT Hub.  Tento proces je automatizovaný při použití [služby Zřizování zařízení služby](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/)IoT Hub . 

Tady se dozvíte, jak [ručně vytvořit zařízení v centru IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Vytvoření zařízení X.509 pro centrum IoT hub

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Ověřovací zařízení podepsaná certifikáty Certifikační autority X.509

Díky registraci certifikátu Certifikační autority X.509 a zařízením přihlášeným do řetězu důvěryhodnosti certifikátů zůstává ověřování zařízení, když se zařízení připojí, a to i poprvé.  Když se připojí zařízení podepsané certifikační autoritou X.509, nahraje svůj řetězec certifikátů k ověření. Řetězec obsahuje všechny zprostředkující certifikáty certifikační autority a zařízení.  Pomocí těchto informací služby IoT Hub ověřuje zařízení ve dvou krocích.  IoT Hub kryptograficky ověří řetěz certifikátů pro vnitřní konzistenci a pak vydá ověření vlastnictví výzvu k zařízení.  IoT Hub deklaruje zařízení autentické na úspěšné ověření vlastnictví odpověď ze zařízení.  Toto prohlášení předpokládá, že soukromý klíč zařízení je chráněn a že pouze zařízení může úspěšně reagovat na tuto výzvu.  Doporučujeme používat zabezpečené čipy, jako jsou hardwarové zabezpečené moduly (HSM) v zařízeních k ochraně soukromých klíčů.

Úspěšné připojení zařízení k centru IoT Hub dokončí proces ověřování a také svědčí o správné nastavení.

Zde se dozvíte, jak [tento krok připojení zařízení dokončit](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Další kroky

Přečtěte si [o hodnotě ověřování certifikační autority X.509](iot-hub-x509ca-concept.md) v IoT.

Začínáme se [službou Zřizování zařízení služby](https://docs.microsoft.com/azure/iot-dps/)IoT Hub .

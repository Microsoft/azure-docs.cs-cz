---
title: Přehled zabezpečení Azure IoT Hub X.509 CA | Dokumentace Microsoftu
description: Přehled – jak ověřovat zařízení do IoT Hubu pomocí X.509 certifikační autority.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 6a9b4fc5479dda58dd024cdf93cbdf4853f9c965
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054637"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Zařízení ověřování pomocí certifikátů X.509 certifikační Autority

Tento článek popisuje, jak používat certifikáty X.509 certifikační autoritu (CA) k ověřování zařízení připojující se IoT Hub.  V tomto článku se dozvíte:

* Získání certifikátu webu X.509
* Postup při registraci certifikátu webu X.509 do služby IoT Hub
* Jak zaregistrovat zařízení pomocí certifikátů webu X.509
* Jak se zařízení s podmíněným Přístupem X.509 podepsaný ověření

## <a name="overview"></a>Přehled

Funkce X.509 certifikační Autority umožňuje ověřování zařízení k IoT Hubu pomocí certifikační autority (CA). Během zařízení výrobní výrazně zjednodušuje proces registrace počáteční zařízení a logistiky dodavatelského řetězce. [Další informace najdete v tomto článku scénář o hodnotě pomocí certifikátů webu X.509](iot-hub-x509ca-concept.md) pro ověřování zařízení.  Doporučujeme vám přečíst si téma tento scénář článek, než budete pokračovat, jak vysvětluje, proč existují následující kroky.

## <a name="prerequisite"></a>Požadavek

Použití certifikační Autority X.509 funkce vyžaduje, že máte účet služby IoT Hub.  [Zjistěte, jak vytvořit instanci služby IoT Hub](quickstart-send-telemetry-dotnet.md) pokud ho ještě nemáte.

## <a name="how-to-get-an-x509-ca-certificate"></a>Získání certifikátu webu X.509

Certifikát X.509 certifikační Autority je v horní části řetěz certifikátů pro každé z vašich zařízení.  Mohou zakoupit nebo vytvořit novou v závislosti na způsobu jeho použití.

Pro produkční prostředí doporučujeme vám zakoupit od certifikační autority veřejného kořenového certifikátu webu X.509. Nákup certifikátu certifikační Autority má výhodu kořenové certifikační Autority jako důvěryhodné třetích stran k ručit pro legitimitu z vašich zařízení. Tuto možnost zvažte, pokud chcete zařízení jako součást open IoT network, kde se očekává pro interakci s produkty třetích stran nebo služby.

Můžete také vytvořit podepsaný svým držitelem CA X.509 pro experimentování ve službě nebo pro použití v uzavřené sítím IoT.

Bez ohledu na to toho, jak získat certifikát X.509 CA, zajistěte její odpovídající privátní klíč tajný kód a chráněný neustále.  To je nezbytné pro důvěryhodnost budování důvěry ověřování X.509 certifikační Autority. 

Zjistěte, jak [vytvořit certifikát podepsaný svým držitelem certifikační Autority](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), který můžete použít pro služby experimentování ve službě v průběhu tohoto popisu funkce.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Přihlášení zařízení do řetězu certifikátů vztahu důvěryhodnosti

Vlastník certifikátu webu X.509 se můžete přihlásit kryptograficky zprostředkující certifikační Autority, který můžete následně podepsat jinou zprostředkující certifikační Autority, a tak dále, dokud poslední zprostředkující certifikační Autority ukončí tento proces po přihlášení zařízení. Výsledkem je kaskádovým příkazem řetěz certifikátů se označuje jako řetěz certifikátů. V reálném životě to hraje jako delegování vztah důvěryhodnosti k podepisování zařízení. Toto delegování je důležité, protože vytváří kryptograficky proměnné dodavatelského řetězce a zabraňuje sdílení podpisových klíčů.

![img-Generic-CERT-chain-of-Trust](./media/generic-cert-chain-of-trust.png)

Přečtěte si zde jak [vytvořit řetěz certifikátů](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) dělalo při přihlašování k zařízení.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Postup při registraci certifikátu webu X.509 do služby IoT Hub

Zaregistrujte vašeho certifikátu webu X.509 do služby IoT Hub, ve kterém se použije k ověření zařízení při registraci a připojení.  Registrace certifikátu webu X.509 je dvoustupňový proces, který se skládá z nahrání souboru certifikátu a dokladu o vlastnictví.

Proces nahrávání zahrnuje nahrát soubor, který obsahuje váš certifikát.  Tento soubor by měl obsahovat nikdy všem privátním klíčům.

Důkaz vlastnictví krok zahrnuje kryptografických výzvy a procesu reakce na mezi vámi a IoT Hub.  Vzhledem k tomu, že digitální certifikát obsah jsou veřejné a proto náchylný k odposlouchávání, IoT Hub chcete zjistit, že ve skutečnosti vlastníte certifikátu certifikační Autority.  Učiní tak vygenerováním náhodné výzva, která s odpovídající privátní klíč certifikátu certifikační Autority, musíte se přihlásit.  Pokud jste ponechali privátní klíč tajného kódu a chráněné jako předtím nedoporučuje, pak pouze bude mít znalosti k dokončení tohoto kroku. Tajemství privátního klíče je zdrojem vztahu důvěryhodnosti v této metodě.  Po přihlášení na výzvu, tento krok proveďte tak, že nahrajete soubor, který obsahuje výsledky.

Přečtěte si zde jak [zaregistrovat certifikát CA](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Vytvoření zařízení v IoT Hubu

Vylučovat zosobnění zařízení služby IoT Hub vyžaduje, abyste ho vědět, jaká zařízení mají očekávat.  To provedete tak, že vytvoříte položky zařízení v registru zařízení služby IoT Hub.  Tento proces je automatické, při použití služby IoT Hub [služby Device Provisioning](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Přečtěte si zde jak [ručně vytvořit zařízení ve službě IoT Hub](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Ověřování zařízení podepsané certifikáty webu X.509

Pomocí certifikátu webu X.509 zaregistrované a zařízení přihlásil řetěz certifikátů je ověření zařízení při připojení zařízení i pro první použití.  Když Certifikační autoritu X.509 podepsaný připojení zařízení, odešle její řetěz certifikátů pro ověření. Řetězec obsahuje všechny zprostředkující certifikáty certifikační Autority a zařízení.  Pomocí těchto informací IoT Hub provádí ověřování zařízení v dvoustupňový proces.  IoT Hub kryptograficky ověří řetěz certifikátů pro interní konzistence a pak vydá challenge důkaz vlastnictví zařízení.  IoT Hub deklaruje zařízení platná v případě úspěšné odpovědi důkaz vlastnictví ze zařízení.  Tato deklarace předpokládá, že privátní klíč zařízení je chráněný a že pouze zařízení může reagovat úspěšně těchto problémů.  Doporučujeme používat zabezpečené čipy jako zabezpečené modulů HSM (Hardware) v zařízení, která chrání privátní klíče.

Zařízení úspěšné připojení ke službě IoT Hub je indikátorem správné nastavení a dokončení procesu ověřování.

Přečtěte si zde jak [provedení tohoto kroku připojení zařízení](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Další kroky

Další informace o [hodnotu ověřování X.509 certifikační Autority](iot-hub-x509ca-concept.md) ve službě IoT.

Začínáme se službou IoT Hub [služby Device Provisioning](https://docs.microsoft.com/azure/iot-dps/).

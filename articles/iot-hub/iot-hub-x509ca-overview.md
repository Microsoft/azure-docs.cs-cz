---
title: Přehled zabezpečení certifikační Autority X.509 Azure IoT Hub | Microsoft Docs
description: Přehled – jak ověřovat zařízení do služby IoT Hub pomocí X.509 certifikačních autorit.
services: iot-hub
documentationcenter: .net
author: eustacea
manager: arjmands
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 11f340ebe1719c680cce5f6ad34150d3717c57d8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Zařízení ověřování pomocí certifikátů X.509 certifikační Autority

Tento článek popisuje, jak používat certifikáty X.509 certifikační autoritou (CA) k ověřování připojení služby IoT Hub zařízení.  V tomto článku se dozvíte:

* Jak získat certifikát X.509 certifikační Autority.
* Postup registrace certifikátu X.509 certifikační Autority do služby IoT Hub
* Tom, jak podepsat zařízení pomocí certifikátů X.509 certifikační Autority
* Jak se ověřují zařízení podepsané certifikační Autority X.509

## <a name="overview"></a>Přehled

Funkce X.509 certifikační Autority umožňuje ověřování zařízení do služby IoT Hub pomocí certifikační autoritou (CA). Během zařízení výrobní výrazně zjednodušuje proces registrace počáteční zařízení a zadejte řetězec logistiky. [Další informace v tomto scénáři článku o hodnotě pomocí certifikátů X.509 CA](iot-hub-x509ca-concept.md) pro ověřování zařízení.  Doporučujeme vám umožní číst tento scénář článek, než budete pokračovat, jak vysvětluje, proč kroky, které následují neexistuje.

## <a name="prerequisite"></a>Požadavek

Pomocí funkce X.509 certifikační Autority vyžaduje, že máte účet služby IoT Hub.  [Naučte se vytvořit instanci služby IoT Hub](iot-hub-csharp-csharp-getstarted.md) Pokud jste ještě nemáte.

## <a name="how-to-get-an-x509-ca-certificate"></a>Jak získat certifikát X.509 certifikační Autority.

Certifikát X.509 certifikační Autority je v horní části řetězu certifikátů pro každý z vašich zařízení.  Můžete zakoupit nebo vytvořit v závislosti na tom, jak máte v úmyslu použít.

Pro produkční prostředí doporučujeme vám zakoupit certifikát X.509 certifikační Autority od veřejné kořenové certifikační autority. Zakoupení certifikátu certifikační Autority má výhodu kořenové certifikační Autority funguje jako důvěryhodná třetí strana ručit za legitimitu zařízení. Zvažte tuto možnost, pokud chcete zařízení jako součást otevřené síti IoT, kde je očekávána pro interakci s produkty třetích stran nebo služby.

Můžete také vytvořit podepsaný certifikační Autority X.509 pro experimentování nebo pro použití v sítích uzavřené IoT.

Bez ohledu na to jak získat váš certifikát certifikační Autority X.509, ujistěte se, aby jeho odpovídající privátní klíče tajný klíč a chráněné kdykoli.  To je nezbytné pro vytvoření vztahu důvěryhodnosti v certifikační Autority X.509 ověřování vztahu důvěryhodnosti. 

Zjistěte, jak [vytvořit certifikát podepsaný svým držitelem certifikační Autority](iot-hub-security-x509-create-certificates.md#createcerts), který můžete použít pro experimenty v rámci popis této funkce.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Přihlašování zařízení v řetězu certifikátů vztahu důvěryhodnosti

Vlastník certifikát X.509 certifikační Autority se můžete přihlásit kryptograficky zprostředkující certifikační Autority, který můžete zase přihlásit jinou zprostředkující certifikační Autority a atd., dokud poslední zprostředkující certifikační Autority ukončuje tento proces po přihlášení zařízení. Výsledkem je kaskádě řetěz certifikátů, které jsou známé jako řetěz certifikátů. Ve skutečnosti to hraje jako delegování pro vztah důvěryhodnosti k podepisování zařízení. Toto delegování je důležité, protože vytváří kryptograficky proměnné předávací protokol a zabraňuje sdílení podpisových klíčů.

![img-Generic-CERT-chain-of-Trust](./media/generic-cert-chain-of-trust.png)

Přečtěte si zde postup [vytvořit řetěz certifikátů](iot-hub-security-x509-create-certificates.md#createcertchain) stejně jako při přihlašování zařízení.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Postup registrace certifikátu X.509 certifikační Autority do služby IoT Hub

Zaregistrujte svůj certifikát X.509 certifikační Autority do služby IoT Hub, kde se bude používat k ověřování během registrace a připojení svých zařízení.  Registrace certifikátu X.509 certifikační Autority je dvoustupňový proces, který se skládá z nahrávání souboru certifikátu a ověření u sebe.

Proces odesílání zahrnuje nahrání souboru, který obsahuje certifikát.  Tento soubor by měl obsahovat nikdy všem privátním klíčům.

Ověření u sebe krok zahrnuje kryptografických výzvy a odezvy proces mezi vámi a IoT Hub.  Vzhledem k tomu, že obsah digitální certifikát se veřejné a proto ohrožena útoky založenými na odposlechu, IoT Hub chcete zjistit, že vlastníte skutečně certifikátu certifikační Autority.  Použije k tomu tak, že generuje náhodné výzva, která s odpovídající privátní klíč certifikátu certifikační Autority, musíte se odhlásit.  Pokud jste u privátního klíče tajný a chráněný jako dříve nedoporučuje, pak pouze bude mít znalosti k dokončení tohoto kroku. Tajemství privátní klíče je zdrojem vztahu důvěryhodnosti v této metodě.  Po podepsání výzvy, provedení tohoto kroku tím, že nahrajete soubor s výsledky.

Přečtěte si zde postup [zaregistrujte svůj certifikát certifikační Autority](iot-hub-security-x509-get-started.md#registercerts).

## <a name="how-to-create-a-device-on-iot-hub"></a>Postup vytvoření zařízení v IoT Hub

Vylučovat zařízení zosobnění, IoT Hub vyžaduje, abyste mohli ho vědět, jaká zařízení se očekává.  To uděláte tak, že vytvoříte záznam zařízení v registru zařízení IoT Hub.  Tento proces je automatizováno při používání služby IoT Hub [služba zřizování zařízení](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/) (distribučních bodů). 

Přečtěte si zde postup [ruční vytvoření zařízení IoT hub](iot-hub-security-x509-get-started.md#createdevice).

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Ověřování zařízení podepsané certifikáty X.509 certifikační Autority

Certifikát X.509 certifikační Autority, které jsou zaregistrované a zařízení přihlášeni řetěz certifikátů co je ještě je ověřování zařízení, když se zařízení připojí, i pro první.  Když podepsané Certifikační autoritu X.509 zařízení připojuje, odešle svůj řetěz certifikátů pro ověření. Řetězu zahrnuje všechny zprostředkující certifikáty certifikační Autoritu a zařízení.  Pomocí těchto informací ověřuje IoT Hub zařízení ve dvou krocích.  IoT Hub kryptograficky ověří řetěz certifikátů pro interní konzistence a následně vydá důkaz vlastnictví výzvy k zařízení.  IoT Hub deklaruje zařízení platná na úspěšné ověření vlastnictví odpovědi ze zařízení.  Toto prohlášení se předpokládá, že je zařízení privátní klíč je chráněný a že pouze zařízení může úspěšně reagovat na tento problém.  Doporučujeme použít čipy zabezpečení jako zabezpečené moduly HSM (Hardware) v zařízení chrání privátní klíče.

Zařízení úspěšné připojení ke službě IoT Hub proces ověření dokončí a je také naznačuje výslednou správné instalačního programu.

Přečtěte si zde postup [provedení tohoto kroku připojení zařízení](iot-hub-security-x509-get-started.md#authenticatedevice).

## <a name="next-steps"></a>Další kroky

Další informace o [hodnotu X.509 CA ověřování](iot-hub-x509ca-concept.md) v IoT.

Začínáme se službou IoT Hub [služba zřizování zařízení](https://docs.microsoft.com/azure/iot-dps/).

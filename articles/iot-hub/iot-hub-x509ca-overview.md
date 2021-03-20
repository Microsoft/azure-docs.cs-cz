---
title: Přehled zabezpečení certifikační autority Azure IoT Hub X. 509 | Microsoft Docs
description: Přehled – jak ověřit zařízení IoT Hub pomocí certifikačních autorit X. 509.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 455f1f3e1c8181646fbe165d0f1aea6a2fb2fb13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92150710"
---
# <a name="device-authentication-using-x509-ca-certificates"></a>Ověřování zařízení pomocí certifikátů webu X.509

Tento článek popisuje, jak používat certifikáty certifikační autority (CA) X. 509 k ověřování zařízení připojujících se IoT Hub.  V tomto článku se dozvíte, jak:

* Jak získat certifikát certifikační autority X. 509
* Jak zaregistrovat certifikát CA X. 509 pro IoT Hub
* Jak podepisovat zařízení pomocí certifikátů certifikační autority X. 509
* Způsob ověřování zařízení podepsaných pomocí certifikační autority X. 509

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Přehled

Funkce CA X. 509 umožňuje ověřování zařízení IoT Hub pomocí certifikační autority (CA). Značně zjednodušuje proces prvotního registrace zařízení a poskytuje logistické řetězce při výrobě zařízení. Další [informace najdete v tomto článku o tomto scénáři o hodnotě použití certifikátů CA X. 509](iot-hub-x509ca-concept.md) pro ověřování zařízení.  Doporučujeme, abyste před pokračováním přečetli tento článek s scénářem, protože vysvětluje, proč existují níže uvedené kroky.

## <a name="prerequisite"></a>Požadavek

Použití funkce CA X. 509 vyžaduje, abyste měli účet IoT Hub.  [Naučte se, jak vytvořit instanci IoT Hub](quickstart-send-telemetry-dotnet.md) , pokud ji ještě nemáte.

## <a name="how-to-get-an-x509-ca-certificate"></a>Jak získat certifikát certifikační autority X. 509

Certifikát CA X. 509 je na začátku řetězce certifikátů pro každé ze svých zařízení.  Můžete si ho koupit nebo vytvořit v závislosti na tom, jak ho máte v úmyslu použít.

V produkčním prostředí doporučujeme koupit certifikát CA X. 509 od veřejné kořenové certifikační autority. Zakoupení certifikátu certifikační autority má za to, že kořenová certifikační autorita slouží jako důvěryhodná třetí strana k přijetí nároků na legitimitu vašich zařízení. Tuto možnost zvažte, pokud chcete, aby vaše zařízení byla součástí otevřené sítě IoT, kde se očekává, že budou pracovat s produkty nebo službami třetích stran.

Můžete také vytvořit certifikační autoritu X. 509 podepsané svým držitelem pro experimentování nebo pro použití v uzavřených sítích IoT.

Bez ohledu na to, jak získáte certifikát certifikační autority X. 509, zajistěte, aby měl odpovídající tajný klíč privátního klíče a byl neustále chráněný.  To je nezbytné pro důvěryhodnost vytváření vztahů důvěryhodnosti v ověřování CA X. 509.

Naučte se, jak [vytvořit certifikát certifikační autority podepsané svým držitelem](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md), který můžete použít k experimentování v rámci tohoto popisu funkce.

## <a name="sign-devices-into-the-certificate-chain-of-trust"></a>Podepisování zařízení v řetězu certifikátů vztahu důvěryhodnosti

Vlastník certifikátu certifikační autority X. 509 může kryptograficky podepsat zprostředkující certifikační autoritu, která může zase podepsat další zprostředkující certifikační autoritu, a tak dále, dokud poslední zprostředkující certifikační autorita tento proces neukončí podepsáním zařízení. Výsledkem je kaskádový řetěz certifikátů označovaných jako řetěz certifikátů s důvěryhodností. V reálném čase se to pro podepisování zařízení vyhrává jako delegování důvěryhodnosti. Toto delegování je důležité, protože vytváří kryptograficky variabilní řetězec pro úschovu a zabraňuje sdílení podpisových klíčů.

![IMG-Generic-CERT-Chain-of-Trust](./media/generic-cert-chain-of-trust.png)

Certifikát zařízení (označovaný také jako listový certifikát) musí mít *název subjektu* nastavený na **ID zařízení** ( `CN=deviceId` ), které se použilo při registraci zařízení IoT v IoT Hub Azure. Toto nastavení se vyžaduje pro ověřování.

Tady se dozvíte, jak [vytvořit řetěz certifikátů](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) , který se má provést při podepisování zařízení.

## <a name="how-to-register-the-x509-ca-certificate-to-iot-hub"></a>Jak zaregistrovat certifikát CA X. 509 pro IoT Hub

Zaregistrujte svůj certifikát CA X. 509, abyste IoT Hub, kde se bude používat k ověřování vašich zařízení během registrace a připojení.  Registrace certifikátu certifikační autority X. 509 je proces se dvěma kroky, který obsahuje odeslání souboru certifikátu a důkaz o jeho vlastnictví.

Proces nahrávání zahrnuje odeslání souboru, který obsahuje váš certifikát.  Tento soubor by nikdy neměl obsahovat žádné privátní klíče.

Krok ověření přístupnosti zahrnuje kryptografickou výzvu a proces reakce mezi vámi a IoT Hub.  Vzhledem k tomu, že obsah digitálního certifikátu je veřejný a tudíž náchylný k odposlouchávání, IoT Hub by chtěl zjistit, že skutečně vlastníte certifikát certifikační autority.  Je to udělat tak, že vygeneruje náhodnou výzvu, kterou musíte podepsat pomocí odpovídajícího privátního klíče certifikátu certifikační autority.  Pokud jste si zanechali tajný klíč privátního klíče a chránili ho jako dříve, měli byste mít k dokončení tohoto kroku jenom tyto znalosti. Tajemství privátních klíčů je zdrojem důvěryhodnosti v této metodě.  Po podepsání výzvy dokončete tento krok nahráním souboru, který obsahuje výsledky.

Tady se dozvíte, jak [zaregistrovat certifikát certifikační autority](iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) .

## <a name="how-to-create-a-device-on-iot-hub"></a>Postup vytvoření zařízení v IoT Hub

Aby se zamezilo zosobnění zařízení, IoT Hub vyžaduje, abyste věděli, co zařízení mají očekávat.  Provedete to tak, že vytvoříte záznam zařízení v registru zařízení IoT Hub.  Tento proces je automatizovaný při používání [služby IoT Hub Device Provisioning](https://azure.microsoft.com/blog/azure-iot-hub-device-provisioning-service-preview-automates-device-connection-configuration/). 

Tady se dozvíte, jak [ručně vytvořit zařízení v IoT Hub](iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub).

Vytvoření zařízení X. 509 pro Centrum IoT

## <a name="authenticating-devices-signed-with-x509-ca-certificates"></a>Ověřování zařízení podepsaných pomocí certifikátů certifikační autority X. 509

Se zaregistrovaným certifikátem certifikační autority X. 509 a zařízeními přihlášenými do řetězu certifikátů důvěřuje, co zůstává k ověřování zařízení, když se zařízení připojuje, a to i poprvé.  Když se zařízení podepsaný certifikační autoritou (CA) X. 509 připojí, nahraje svůj řetěz certifikátů k ověření. Řetěz zahrnuje všechny zprostředkující certifikační autority a certifikáty zařízení.  Pomocí těchto informací IoT Hub ověří zařízení v procesu se dvěma kroky.  IoT Hub kryptograficky ověří řetěz certifikátů pro zajištění Interní konzistence a pak vydá výzvu k ověření přístupnosti zařízení.  IoT Hub deklaruje platnost zařízení po úspěšném potvrzení odezvy ze zařízení.  Tato deklarace předpokládá, že privátní klíč zařízení je chráněný a že jenom zařízení může na tuto výzvu úspěšně reagovat.  Pro ochranu privátních klíčů doporučujeme použít v zařízeních zabezpečené čipy, jako jsou hardwarové zabezpečené moduly (HSM).

Úspěšné připojení zařízení k IoT Hub dokončí proces ověřování a je také indikativní pro správné nastavení.

Tady se dozvíte, jak [Dokončit tento krok připojení zařízení](iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates).

## <a name="next-steps"></a>Další kroky

Přečtěte si o [hodnotě ověřování CA X. 509](iot-hub-x509ca-concept.md) v IoT.

Začínáme se službou IoT Hub [Device Provisioning Service](../iot-dps/index.yml).
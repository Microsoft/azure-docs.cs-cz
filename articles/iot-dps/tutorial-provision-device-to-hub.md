---
title: Zřízení zařízení pomocí služby Azure IoT Hub Device Provisioning | Microsoft Docs
description: Zřízení zařízení pro jedno centrum IoT pomocí služby Azure IoT Hub Device Provisioning
services: iot-dps
keywords: ''
author: dsk-2015
ms.author: dkshir
ms.date: 04/12/2018
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 9f151a8fbcdc20124467a1db290f6a05f574e4fe
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Zřízení zařízení pro centrum IoT pomocí služby Azure IoT Hub Device Provisioning

V předchozím kurzu jste se naučili nastavit zařízení pro připojení ke službě Device Provisioning. V tomto kurzu zjistíte, jak pomocí této služby zřídit zařízení pro jedno centrum IoT s využitím automatického zřizování a **_seznamů registrací_**. V tomto kurzu získáte informace o následujících postupech:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, nezapomeňte své zařízení nakonfigurovat, jak je popsáno v kurzu [Nastavení zařízení pro zřízení pomocí služby Azure IoT Hub Device Provisioning](./tutorial-set-up-device.md).

Pokud neznáte proces automatického zřizování, nezapomeňte si přečíst o [konceptech automatického zřizování](concepts-auto-provisioning.md), než budete pokračovat.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrace zařízení

Tento krok zahrnuje přidání jedinečných artefaktů zabezpečení zařízení do služby Device Provisioning. Tyto artefakty zabezpečení jsou založené na [mechanismu ověřování](concepts-device.md#attestation-mechanism) zařízení následujícím způsobem:

- Pro zařízení založená na TPM potřebujete:
    - *Ověřovací klíč*, který je jedinečný pro každou simulaci nebo čip TPM a který získáte od výrobce čipu TPM.  Další informace najdete v tématu [Vysvětlení ověřovacího klíče TPM](https://technet.microsoft.com/library/cc770443.aspx).
    - *ID registrace*, které slouží k jednoznačné identifikaci zařízení v oboru názvů nebo oboru. Toto ID může, ale nemusí být stejné jako ID zařízení. ID je povinné pro každé zařízení. U zařízení založených na TPM můžete ID registrace získat přímo z TMP, například jako hodnotu hash SHA-256 ověřovacího klíče TPM.

    [![Informace o registraci TPM na portálu](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Pro zařízení založená na X.509 potřebujete:
    - [Certifikát vydaný pro simulaci nebo čip X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx) ve formě souboru *.pem* nebo *.cer*. K jednotlivé registraci musíte použít *certifikát podpisovatele* daného zařízení pro váš systém X.509, zatímco pro skupiny registrací musíte použít *kořenový certifikát*. 

    [![Přidání jednotlivé registrace pro ověření X.509 na portálu](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Zařízení můžete do služby Device Provisioning zaregistrovat dvěma způsoby:

- **Skupiny registrací** představují skupinu zařízení, která sdílí konkrétní mechanismus ověřování. Skupinu registrací doporučujeme použít pro velké množství zařízení, která sdílí požadovanou počáteční konfiguraci, nebo pro zařízení, která budou patřit do stejného tenanta.

    [![Přidání skupinové registrace pro ověření X.509 na portálu](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Jednotlivé registrace** představují záznamy jednotlivých zařízení, která se můžou zaregistrovat do služby Device Provisioning. Jednotlivé registrace můžou jako mechanismus ověřování využívat certifikáty X.509 nebo tokeny SAS (na skutečném nebo virtuálním zařízení TPM). Jednotlivé registrace doporučujeme použít pro zařízení, která vyžadují jedinečnou počáteční konfiguraci, a zařízení, která jako mechanismus ověřování můžou využívat pouze tokeny SAS prostřednictvím skutečného nebo virtuálního zařízení TPM. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

Teď zařízení zaregistrujete do své instance služby Device Provisioning s použitím požadovaných artefaktů zabezpečení na základě mechanismu ověřování zařízení: 

1. Přihlaste se k webu Azure Portal, v nabídce vlevo klikněte na tlačítko **Všechny prostředky** a otevřete svou službu Device Provisioning.

2. V okně s přehledem služby Device Provisioning vyberte **Správa registrací**. V závislosti na nastavení svého zařízení vyberte kartu **Jednotlivé registrace** nebo **Skupiny registrací**. Klikněte na tlačítko **Přidat** v horní části. Jako *Mechanismus* ověření identity vyberte **TPM** nebo **X.509** a zadejte odpovídající artefakty zabezpečení, jak je popsáno výše. Můžete zadat nové **ID zařízení služby IoT Hub**. Jakmile budete hotovi, klikněte na tlačítko **Uložit**. 

3. Po úspěšné registraci by se zařízení mělo zobrazit na portálu, jak je znázorněno níže:

    ![Úspěšná registrace TPM na portálu](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Po registraci zřizovací služba počká na budoucí spuštění a připojení zařízení. Při prvním spuštění zařízení klientská knihovna SDK ve spolupráci s čipem extrahuje ze zařízení artefakty zabezpečení a ověří registraci ve službě Device Provisioning. 

## <a name="start-the-device"></a>Spuštění zařízení

V tuto chvíli je následující nastavení připravené k registraci zařízení:

1. Vaše zařízení nebo skupina zařízení jsou zaregistrované do služby Device Provisioning. 
2. Vaše zařízení je připravené s nakonfigurovaným mechanismem ověřování a přístupné přes aplikaci pomocí klientské sady SDK služby Device Provisioning.

Spusťte aplikaci, aby klientská aplikace mohla zahájit registraci do vaší služby Device Provisioning.  

## <a name="verify-the-device-is-registered"></a>Ověření registrace zařízení

Po spuštění zařízení by se měly provést následující akce. Další podrobnosti najdete v ukázkové aplikaci simulátoru TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c). 

1. Zařízení odešle žádost o registraci do vaší služby Device Provisioning.
2. Pro zařízení TPM služba Device Provisioning odešle zpět výzvu k registraci, na kterou zařízení zareaguje. 
3. Po úspěšné registraci služba Device Provisioning odešle zpět do zařízení identifikátor URI centra IoT, ID zařízení a zašifrovaný klíč. 
4. Klientská aplikace IoT Hub na zařízení se pak připojí k vašemu centru. 
5. Po úspěšném připojení k centru by se zařízení mělo zobrazit v nástroji **Device Explorer** centra IoT. 

    ![Úspěšné připojení k centru na portálu](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Registrace zařízení
> * Spuštění zařízení
> * Ověření registrace zařízení

V dalším kurzu se dozvíte, jak zřídit několik zařízení napříč centry s vyrovnáváním zatížení. 

> [!div class="nextstepaction"]
> [Zřízení zařízení v několika centrech IoT s vyrovnáváním zatížení](./tutorial-provision-multiple-hubs.md)

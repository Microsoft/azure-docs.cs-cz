---
title: Navrácení certifikátů X. 509 v Azure IoT Central
description: Postup nasazení certifikátů X. 509 pomocí aplikace IoT Central
author: v-krghan
ms.author: v-krghan
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 76e2b9542d20b5788a2875dec89d447ce38276a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88122019"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>Postup při navrácení certifikátů zařízení X. 509 v aplikaci IoT Central

Během životního cyklu řešení IoT budete muset certifikáty navýšit. Dvěma hlavními důvody pro zavedení certifikátů je porušení zabezpečení a vypršení platnosti certifikátů. 

Průběžné certifikáty jsou osvědčenými postupy zabezpečení, které vám pomůžou zabezpečit systém v případě porušení. V rámci této [metodiky předpokládáme](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), že společnost Microsoft vyžaduje, aby byly v zavedeny reaktivní procesy zabezpečení spolu s preventivními opatřeními. Zavedení certifikátů zařízení by mělo být součástí těchto procesů zabezpečení. Četnost změn certifikátů bude záviset na potřebách zabezpečení vašeho řešení. Zákazníci s řešeními, které zahrnují velmi citlivá data, můžou každý den nastavovat certifikát, zatímco ostatní si své certifikáty každých pár let.


## <a name="obtain-new-x509-certificates"></a>Získání nových certifikátů X. 509

Pomocí nástroje, jako je OpenSSL, můžete vytvořit vlastní certifikáty X. 509. Tento přístup je skvělý pro testování certifikátů X. 509, ale poskytuje několik záruk týkajících se zabezpečení. Tento přístup používejte jenom pro testování, pokud jste se připravili jako poskytovatel vaší certifikační autority.


## <a name="enrollment-groups-and-security-breaches"></a>Skupiny registrací a porušení zabezpečení

Pokud chcete aktualizovat registraci skupiny v reakci na porušení zabezpečení, měli byste použít následující postup, který okamžitě aktualizuje aktuální certifikát:

1. V levém podokně přejděte do části **Správa**  a klikněte na **připojení zařízení**.

2. Klikněte na **skupiny**registrací a v seznamu klikněte na název skupiny.

    ![Připojení zařízení](./media/how-to-roll-x509-certificates/device-connection.png)


3. V případě aktualizace certifikátu klikněte na možnost **Spravovat primární** nebo **spravovat sekundární**.

    ![Správa certifikátů](./media/how-to-roll-x509-certificates/certificates.png)


4. Přidejte a ověřte kořenový certifikát X. 509 ve skupině pro registraci.

   Pokud dojde k ohrožení zabezpečení, proveďte tyto kroky u primárních a sekundárních certifikátů.



## <a name="enrollment-groups-and-certificate-expiration"></a>Registrace skupin a vypršení platnosti certifikátu

Pokud vytváříte certifikáty pro zpracování vypršení platnosti certifikátů, použijte následující postup, který okamžitě aktualizuje aktuální certifikát:

1. V levém podokně přejděte do části **Správa**  a klikněte na **připojení zařízení**. 

2. Klikněte na **skupiny**registrací a v seznamu klikněte na název skupiny.

    ![Připojení zařízení](./media/how-to-roll-x509-certificates/device-connection.png)


3. V případě aktualizace certifikátu klikněte na možnost **Spravovat primární**.

    ![Připojení zařízení](./media/how-to-roll-x509-certificates/manage-certs.png)

4. Přidejte a ověřte kořenový certifikát X. 509 ve skupině pro registraci.

5. Později po vypršení platnosti sekundárního certifikátu se vraťte a aktualizujte tento sekundární certifikát.



## <a name="individual-enrollments-and-security-breaches"></a>Jednotlivé registrace a porušení zabezpečení

Pokud během reakce na porušení zabezpečení provádíte certifikáty, použijte následující postup, který okamžitě aktualizuje aktuální certifikát:


1. Klikněte na **zařízení**a vyberte zařízení. 

2. Klikněte na **připojit**a jako **jednotlivou registraci** vyberte metoda připojit.

3. Jako mechanismus vyberte **certifikáty (X. 509)** .

    ![Spravovat jednotlivé registrace](./media/how-to-roll-x509-certificates/certificate-update.png)

4. V případě aktualizace certifikátu klikněte na ikonu složky a vyberte nový certifikát, který se má nahrát pro položku registrace. Klikněte na **Uložit**.

    U primárních a sekundárních certifikátů proveďte tyto kroky, pokud dojde k ohrožení obou z nich.



## <a name="individual-enrollments-and-certificate-expiration"></a>Jednotlivé registrace a vypršení platnosti certifikátu

Pokud vytváříte certifikáty pro zpracování vypršení platnosti certifikátů, měli byste pomocí sekundární konfigurace certifikátu zkrátit dobu výpadku zařízení, která se snaží zřídit.

Později, když se sekundární certifikát taky blíží vypršení platnosti a je potřeba ho navrátit, můžete ho použít k primární konfiguraci. Otáčení mezi primárním a sekundárním certifikátem tímto způsobem zkracuje výpadky zařízení, která se snaží zřídit.

1. Klikněte na **zařízení**a vyberte zařízení.

2. Klikněte na **připojit**a jako **jednotlivou registraci** vyberte metoda připojit.

3. Jako mechanismus vyberte **certifikáty (X. 509)** .

    ![Spravovat jednotlivé registrace](./media/how-to-roll-x509-certificates/certificate-update.png)

4. V případě aktualizace sekundárního certifikátu klikněte na ikonu složky a vyberte nový certifikát, který se má nahrát pro položku registrace. Klikněte na **Uložit**.


5. Později po vypršení platnosti primárního certifikátu se vraťte a aktualizujte tento primární certifikát.


## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak v aplikaci IoT Central Azure navrátit certifikáty X. 509, můžete se [připojit k Azure IoT Central](concepts-get-connected.md).



---
title: Správa registrací zařízení pomocí webu Azure portal | Dokumentace Microsoftu
description: Správa registrací zařízení pro svou službu Device Provisioning na webu Azure Portal
author: dsk-2015
ms.author: dkshir
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: ff28d9730cc95d934c78163534aed08271c6b98c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439814"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Správa registrací zařízení pomocí webu Azure Portal

A *registrace zařízení* vytvoří záznam o jedno zařízení nebo skupině zařízení, která může v určitém okamžiku zaregistrovat s Azure IoT Hub Device Provisioning Service. Záznam registrace obsahuje počáteční požadovanou konfiguraci zařízení jako součást registrace, včetně požadované centrum IoT hub. Tento článek ukazuje, jak Správa registrací zařízení pro vaši službu zřizování.


## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

Existují dva způsoby, jak můžete zaregistrovat svoje zařízení pomocí zřizovací služby:

* **Skupinu registrací** je záznam pro skupinu zařízení, které sdílejí společné mechanismus ověřování certifikátů X.509, podepsané stejným podpisový certifikát, který může být [kořenový certifikát](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkující certifikát](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), která se používá k vytvoření certifikátu zařízení na fyzickém zařízení. Doporučujeme použít skupinu registrací pro velký počet zařízení, která sdílí požadovanou počáteční konfiguraci, nebo pro zařízení budou patřit do stejného tenanta. Všimněte si, že můžete registrovat jenom zařízení, která používají mechanismus ověřování X.509 jako *skupiny registrací*. 

    Vytvořit skupinu registrací na portálu pro skupinu zařízení pomocí následujících kroků:

    1. Přihlaste se k webu Azure portal a klikněte na tlačítko **všechny prostředky** z nabídky na levé straně.  
    1. Klikněte na službu Device Provisioning chcete zaregistrovat zařízení v seznamu prostředků.  
    1. Ve vaší službě zřizování:  
       a. Klikněte na tlačítko **Správa registrací**a pak **skupiny registrací** kartu.  
       b. Klikněte na tlačítko **Přidat** v horní části.  
       c. Jakmile se zobrazí na panelu "Přidat skupinu registrací", zadejte informace pro položku seznamu registrací.  **Název skupiny** je povinný. Také vyberte "CA nebo zprostředkující" pro **typ certifikátu**a nahrajte kořenový adresář **primární certifikát** pro skupinu zařízení.  
       d. Klikněte na **Uložit**. Po úspěšném vytvoření skupiny pro registraci, zobrazí se název skupiny se zobrazí v rámci **skupiny registrací** kartu.  

       [![Skupiny pro registraci na portálu](./media/how-to-manage-enrollments/group-enrollment.png)] (. / media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Jednotlivou registraci** je záznam pro jedno zařízení, který může zaregistrovat. Jednotlivé registrace můžou používat x509 certifikáty nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM) jako mechanismus ověřování. Doporučujeme používat jednotlivé registrace pro zařízení, která vyžadují jedinečnou počáteční konfiguraci, nebo zařízení, které můžou využívat pouze tokeny SAS prostřednictvím skutečného nebo virtuálního čipu TPM jako mechanismus ověřování. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

    Vytvoření jednotlivé registrace na portálu, pomocí následujících kroků:

    1. Přihlaste se k webu Azure portal a klikněte na tlačítko **všechny prostředky** z nabídky na levé straně.
    1. Klikněte na službu Device Provisioning chcete zaregistrovat zařízení v seznamu prostředků.
    1. Ve vaší službě zřizování:  
       a. Klikněte na tlačítko **Správa registrací**a pak **jednotlivé registrace** kartu.  
       b. Klikněte na tlačítko **Přidat** v horní části.   
       c. Jakmile se zobrazí na panelu "Přidat registrace", zadejte informace pro položku seznamu registrací. Nejprve vyberte potvrzení **mechanismus** pro zařízení (X.509 nebo TPM). Ověření X.509 je potřeba nahrát listu **primární certifikát** pro zařízení. Čip TPM je potřeba zadat **ověření identity klíče** a **ID registrace** pro zařízení.  
       d. Klikněte na **Uložit**. Po úspěšném vytvoření skupiny pro registraci, měli byste vidět vaše zařízení se zobrazí v rámci **jednotlivé registrace** kartu.  

       [![Jednotlivé registrace na portálu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aktualizovat položku registrace
Můžete aktualizovat existující položka registrace na portálu, pomocí následujících kroků:

1. Otevřete svou službu Device Provisioning na webu Azure Portal a klikněte na tlačítko **Správa registrací**. 
1. Přejděte na položku registrace, který chcete upravit. Klikněte na položku, otevře se souhrnné informace o registraci zařízení. 
1. Na této stránce můžete upravit položky než je typ zabezpečení a přihlašovací údaje, jako jsou služby IoT hub zařízení by měly být propojeny, a také ID zařízení. Můžete také upravit počáteční stav dvojčete zařízení. 
1. Po dokončení klikněte na tlačítko **Uložit** aktualizovat registraci zařízení. 

    ![Aktualizace registrace na portálu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Odebrat registraci zařízení
V případech, kdy vaše zařízení není potřeba zřídí do jakékoli služby IoT hub můžete odebrat položky související registrace na portálu, pomocí následujících kroků:

1. Otevřete svou službu Device Provisioning na webu Azure Portal a klikněte na tlačítko **Správa registrací**. 
1. Přejděte do a vyberte položku registrace, který chcete odebrat. 
1. Klikněte na tlačítko **odstranit** tlačítko v horní části a pak vyberte **Ano** po zobrazení výzvy k potvrzení. 
1. Po dokončení akce se zobrazí vaše položky odebrat ze seznamu registrací zařízení. 
 
    ![Odebrat registraci na portálu](./media/how-to-manage-enrollments/remove-enrollment.png)



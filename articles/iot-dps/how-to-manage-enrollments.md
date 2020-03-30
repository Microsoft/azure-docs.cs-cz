---
title: Správa registrací zařízení pro službu Azure IoT Hub Device Provisioning service na webu Azure Portal
description: Jak spravovat registrace zařízení pro službu Zřizování zařízení (DPS) na webu Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8bd896573dbd0b24df11c0d66009098de19654ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974934"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Jak spravovat registrace zařízení pomocí Portálu Azure

*Registrace zařízení* vytvoří záznam jednoho zařízení nebo skupiny zařízení, které se mohou v určitém okamžiku zaregistrovat pomocí služby Azure IoT Hub Device Provisioning Service. Záznam registrace obsahuje počáteční požadovanou konfiguraci pro zařízení jako součást této registrace, včetně požadovaného centra IoT Hub. Tento článek ukazuje, jak spravovat registrace zařízení pro službu zřizování.


## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

Existují dva způsoby, jak můžete zaregistrovat zařízení pomocí zřizovací služby:

* **Skupina Zápisje** položka pro skupinu zařízení, která sdílejí společný mechanismus ověřování certifikátů X.509 podepsaných stejným podpisovým certifikátem, kterým může být [kořenový certifikát](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkující certifikát](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate)používaný k výrobě certifikátu zařízení na fyzickém zařízení. Doporučujeme používat skupinu registrace pro velký počet zařízení, která sdílejí požadovanou počáteční konfiguraci, nebo pro zařízení, která přejdou do stejného tenanta. Všimněte si, že můžete zaregistrovat pouze zařízení, která používají mechanismus ověřování X.509 jako *skupiny zápisu*. 

    Skupinu pro zápis na portálu můžete vytvořit pro skupinu zařízení pomocí následujících kroků:

  1. Přihlaste se k portálu Azure a klikněte na **všechny prostředky** z levé nabídky.  
  1. Klikněte na službu Zřizování zařízení, do které chcete zařízení zaregistrovat ze seznamu prostředků.  
  1. Ve službě zřizování:  
     a. Klikněte na **Spravovat registrace**a vyberte kartu **Skupiny zápisů.**  
     b. Klikněte na tlačítko **Přidat** v horní části.  
     c. Po zobrazení panelu Přidat skupinu zápisů zadejte informace o položce seznamu zápisů.  Je vyžadován **název skupiny.** Pro **typ certifikátu**také vyberte "CA nebo Intermediate" a nahrajte **kořenový primární certifikát** pro skupinu zařízení.  
     d. Klikněte na **Uložit**. Při úspěšném vytvoření skupiny pro zápis byste měli vidět název skupiny na kartě **Skupiny zápisů.**  

     [![Skupina zápisu na portálu](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Individuální registrace** je položka pro jedno zařízení, které se může zaregistrovat. Jednotlivé zápisy mohou jako mechanismy ověřování používat certifikáty x509 nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM). Doporučujeme používat jednotlivé registrace pro zařízení, která vyžadují jedinečné počáteční konfigurace, nebo pro zařízení, která mohou používat pouze tokeny SAS prostřednictvím čipu TPM nebo virtuálního čipu TPM jako mechanismus ověřování. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

    Individuální registraci na portálu můžete vytvořit pomocí následujících kroků:

    1. Přihlaste se k portálu Azure a klikněte na **všechny prostředky** z levé nabídky.
    1. Klikněte na službu Zřizování zařízení, do které chcete zařízení zaregistrovat ze seznamu prostředků.
    1. Ve službě zřizování:  
       a. Klikněte na **Spravovat registrace**a vyberte kartu **Jednotlivé registrace.**  
       b. Klikněte na tlačítko **Přidat** v horní části.   
       c. Po zobrazení panelu "Přidat zápis" zadejte informace o položce seznamu zápisu. Nejprve vyberte **mechanismus atestace** pro zařízení (X.509 nebo TPM). Atestace X.509 vyžaduje, abyste pro zařízení nahráli **primární certifikát** listu. Čip TPM vyžaduje zadání **atestačního klíče** a **ID registrace** zařízení.  
       d. Klikněte na **Uložit**. Při úspěšném vytvoření skupiny registrace byste měli vidět, že se vaše zařízení zobrazí na kartě **Individuální registrace.**  

       [![Individuální registrace na portálu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aktualizace položky registrace
Existující položku registrace na portálu můžete aktualizovat pomocí následujících kroků:

1. Otevřete službu Zřizování zařízení na webu Azure Portal a klikněte na **Spravovat registrace**. 
1. Přejděte na položku zápisu, kterou chcete upravit. Klikněte na položku, která otevře souhrnné informace o registraci zařízení. 
1. Na této stránce můžete upravit jiné položky než typ zabezpečení a přihlašovací údaje, jako je například služba IoT hub, se kterou by mělo být zařízení propojeno, a ID zařízení. Můžete také upravit počáteční stav dvojčete zařízení. 
1. Po dokončení klikněte na **Uložit** a aktualizujte registraci zařízení. 

    ![Aktualizovat registraci na portálu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Odebrání registrace zařízení
V případech, kdy vaše zařízení nemusí být zřízena do žádného centra IoT hub, můžete odebrat související položku registrace na portálu pomocí následujících kroků:

1. Otevřete službu Zřizování zařízení na webu Azure Portal a klikněte na **Spravovat registrace**. 
1. Přejděte na položku zápisu, kterou chcete odebrat, a vyberte ji. 
1. Klikněte na tlačítko **Odstranit** nahoře a po zobrazení výzvy k potvrzení vyberte **Ano.** 
1. Po dokončení akce se zobrazí vaše položka odebrána ze seznamu registrací zařízení. 
 
    ![Odebrání registrace na portálu](./media/how-to-manage-enrollments/remove-enrollment.png)



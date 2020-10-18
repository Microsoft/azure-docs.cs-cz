---
title: Správa registrace zařízení pro Azure IoT Hub Device Provisioning Service v Azure Portal
description: Správa registrace zařízení pro službu Device Provisioning Service (DPS) na webu Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 2fbcacd7f2094f9b0b9dcea3fea4d804fd96923e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165311"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Jak spravovat registrace zařízení pomocí webu Azure Portal

*Registrace zařízení* vytvoří záznam o jednom zařízení nebo skupině zařízení, která se můžou v některých bodech zaregistrovat u služby Azure IoT Hub Device Provisioning Service. Záznam zápisu obsahuje počáteční požadovanou konfiguraci pro zařízení v rámci této registrace, včetně požadovaného centra IoT Hub. V tomto článku se dozvíte, jak spravovat registraci zařízení pro vaši službu zřizování.


## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

Existují dva způsoby, jak můžete zařízení zaregistrovat pomocí služby zřizování:

* **Skupina** registrací je položka pro skupinu zařízení, která sdílí běžný mechanismus ověřování certifikátů X. 509, podepsaný stejným podpisovým certifikátem, který může být [kořenovým certifikátem](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) nebo [zprostředkujícím certifikátem](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), který se používá k vytvoření certifikátu zařízení na fyzickém zařízení. Doporučujeme používat skupinu registrací pro velký počet zařízení, která sdílejí požadovanou počáteční konfiguraci, nebo pro zařízení, která se budou napojovat do stejného tenanta. 

    Skupinu zařízení můžete na portálu vytvořit pomocí následujících kroků:

  1. Přihlaste se k Azure Portal a v nabídce na levé straně klikněte na **všechny prostředky** .  
  1. V seznamu prostředků klikněte na službu Device Provisioning, ze které chcete zařízení zaregistrovat.  
  1. Ve vaší službě zřizování:  
     a. Klikněte na **spravovat registrace**a pak vyberte kartu **skupiny** registrací.  
     b. Klikněte na tlačítko **Přidat** v horní části.  
     c. Jakmile se zobrazí panel Přidat skupinu registrací, zadejte informace pro položku seznamu registrace.  **Název skupiny** je povinný. Jako **typ certifikátu**vyberte taky CA nebo zprostředkující a nahrajte kořenový **primární certifikát** pro skupinu zařízení.  
     d. Klikněte na **Uložit**. Po úspěšném vytvoření skupiny registrací by se na kartě **skupiny** registrací měl zobrazit název skupiny.  

     [![Skupina registrace na portálu](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* **Jednotlivá registrace** je záznam pro jedno zařízení, které se může zaregistrovat. Jednotlivé registrace můžou použít buď certifikáty x509, nebo tokeny SAS (z fyzického nebo virtuálního čipu TPM) jako mechanismy ověřování. Pro zařízení, která vyžadují jedinečné počáteční konfigurace, doporučujeme používat jednotlivé registrace nebo pro zařízení, která jako mechanismus ověřování můžou jako mechanismus ověřování používat jenom tokeny SAS přes TPM nebo virtuální čip TPM. Jednotlivé registrace můžou mít zadané požadované ID zařízení centra IoT.

    Jednotlivé registrace můžete vytvořit na portálu pomocí následujících kroků:

    1. Přihlaste se k Azure Portal a v nabídce na levé straně klikněte na **všechny prostředky** .
    1. V seznamu prostředků klikněte na službu Device Provisioning, ze které chcete zařízení zaregistrovat.
    1. Ve vaší službě zřizování:  
       a. Klikněte na **spravovat registrace**a pak vyberte kartu **jednotlivé registrace** .  
       b. Klikněte na tlačítko **Přidat** v horní části.   
       c. Jakmile se zobrazí panel Přidat registraci, zadejte informace pro položku seznamu registrace. Nejdřív vyberte **mechanismus** ověření identity pro zařízení (X. 509 nebo TPM). X. 509 Attestation vyžaduje, abyste nahráli **primární certifikát** na úrovni zařízení. ČIP TPM vyžaduje, abyste zadali **ověřovací klíč** a **ID registrace** pro zařízení.  
       d. Klikněte na **Uložit**. Po úspěšném vytvoření skupiny registrací by se zařízení mělo zobrazit na kartě **jednotlivé registrace** .  

       [![Jednotlivé registrace na portálu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aktualizace položky registrace
Existující položku registrace můžete aktualizovat na portálu pomocí následujících kroků:

1. Otevřete službu Device Provisioning v Azure Portal a klikněte na **spravovat registrace**. 
1. Přejděte na položku registrace, kterou chcete upravit. Klikněte na položku, která otevře souhrnné informace o registraci zařízení. 
1. Na této stránce můžete upravit jiné položky než typ zabezpečení a přihlašovací údaje, jako je třeba Centrum IoT, se kterým se má zařízení propojit, a také ID zařízení. Můžete také změnit počáteční stav vlákna zařízení. 
1. Po dokončení klikněte na **Uložit** a aktualizujte registraci zařízení. 

    ![Aktualizace registrace na portálu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Odebrání registrace zařízení
V případech, kdy vaše zařízení není nutné zřídit do služby IoT Hub, můžete související položku registrace odebrat na portálu pomocí následujících kroků:

1. Otevřete službu Device Provisioning v Azure Portal a klikněte na **spravovat registrace**. 
1. Přejděte na adresu a vyberte položku registrace, kterou chcete odebrat. 
1. Klikněte na tlačítko **Odstranit** v horní části a po zobrazení výzvy k potvrzení vyberte **Ano** . 
1. Až se akce dokončí, uvidíte, že se vaše položka odebrala ze seznamu registrací zařízení. 
 
    ![Odebrání registrace na portálu](./media/how-to-manage-enrollments/remove-enrollment.png)



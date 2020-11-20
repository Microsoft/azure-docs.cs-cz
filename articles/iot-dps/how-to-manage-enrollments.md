---
title: Správa registrace zařízení pro Azure IoT Hub Device Provisioning Service v Azure Portal
description: Správa registrace zařízení pro službu Device Provisioning Service (DPS) v Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/17/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: 6ec146a05df1b896f8ca594d29cf13341b70765a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954558"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Správa registrace zařízení pomocí Azure Portal

*Registrace zařízení* vytvoří záznam o jednom zařízení nebo skupině zařízení, která se můžou v některých bodech zaregistrovat u služby Azure IoT Hub Device Provisioning Service. Záznam zápisu obsahuje počáteční konfiguraci zařízení v rámci této registrace. Součástí konfigurace je centrum IoT, ke kterému se zařízení přiřadí nebo zásadu přidělení, která nakonfiguruje centrum ze sady Center. V tomto článku se dozvíte, jak spravovat registraci zařízení pro vaši službu zřizování.


## <a name="create-a-device-enrollment"></a>Vytvoření registrace zařízení

Existují dva způsoby, jak můžete zařízení zaregistrovat pomocí služby zřizování:

* **Skupina** registrací je položka pro skupinu zařízení, která sdílí společný mechanismus ověřování. Doporučujeme používat skupinu registrací pro velký počet zařízení, která sdílejí počáteční konfiguraci, nebo pro zařízení, která se budou napojovat do stejného tenanta. Podporují se zařízení s využitím [symetrického klíče](concepts-symmetric-key-attestation.md) nebo [certifikátů X. 509](concepts-x509-attestation.md) . 

    Podrobné pokyny k vytváření a používání skupin registrací s symetrickými klíči najdete v kurzu [zřízení zařízení pomocí symetrických klíčů](how-to-legacy-device-symm-key.md) .

    Skupinu zařízení můžete na portálu vytvořit pomocí následujících kroků:

    1. Přihlaste se k Azure Portal a v nabídce na levé straně klikněte na **všechny prostředky** .  
    1. V seznamu prostředků klikněte na službu Device Provisioning, ze které chcete zařízení zaregistrovat.  
    1. Ve vaší službě zřizování klikněte na **spravovat registrace** a pak klikněte na tlačítko **Přidat skupinu** registrací v horní části.  
     
        ![Skupina registrace na portálu](./media/how-to-manage-enrollments/add-group-enrollment.png)
        
    1. Jakmile se zobrazí panel Přidat skupinu registrací, zadejte informace o registraci a klikněte na **Uložit**.  
     
        [![Přidání skupiny registrací k portálu](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
        
        | Pole | Popis |
        | :--- | :--- |
        | **Název skupiny** | Požadovaný název vaší skupiny zařízení. |
        | **Typ ověření identity** | Klikněte na **certifikát** nebo **symetrický klíč** pro typ ověření identity v závislosti na metodě ověření, kterou vaše zařízení bude používat. |
        | **Typ certifikátu** | K dispozici, pokud používáte ověření identity certifikátu. Vyberte **certifikát certifikační autority** nebo **zprostředkující** na základě certifikátu podepsaného certifikáty zařízení. |
        | **Primární certifikát** | Pokud podepisujete certifikáty zařízení s kořenovým certifikátem certifikační autority, musí mít certifikát od kořenové certifikační autority dokončené [vlastnictví](how-to-verify-certificates.md) . Pak ho můžete vybrat jako **primární certifikát** pro skupinu zařízení.<br><br>Pokud podepisujete certifikáty zařízení pomocí zprostředkujícího certifikátu, bude k dispozici tlačítko pro nahrání, které vám umožní nahrát svůj zprostředkující certifikát. Certifikát, který podepsal zprostředkující, musí také mít k [dispozici důkaz o jejich](how-to-verify-certificates.md) vyhotovení. |

        
    

* **Jednotlivá registrace** je záznam pro jedno zařízení, které může být přiřazeno ke IoT Hub. Podporují se zařízení s využitím [symetrického klíče](concepts-symmetric-key-attestation.md), [certifikátů X. 509](concepts-x509-attestation.md)a [ověření identity čipem TPM](concepts-tpm-attestation.md) . 

    Jednotlivé registrace můžete vytvořit na portálu pomocí následujících kroků:

    1. Přihlaste se k Azure Portal a v nabídce na levé straně klikněte na **všechny prostředky** .
    1. V seznamu prostředků klikněte na službu Device Provisioning, ze které chcete zařízení zaregistrovat.
    1. Ve vaší službě zřizování klikněte na **spravovat registrace** a pak klikněte na tlačítko **přidat jednotlivou registraci** v horní části.   

       [![Přidání jednotlivé registrace na portálu](./media/how-to-manage-enrollments/add-individual-enrollment.png)](./media/how-to-manage-enrollments/add-individual-enrollment.png#lightbox)

    1. Jakmile se zobrazí panel Přidat registraci, zadejte informace o registraci jednotlivých zařízení a klikněte na **Uložit**. 
     
        [![Jednotlivé registrace na portálu](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)
    
        | Pole | Popis |
        | :--- | :--- |
        | **Mechanismus** | V závislosti na metodě ověření, kterou budou zařízení používat, vyberte **X. 509**, **TPM** nebo **symetrický klíč** pro mechanismus ověřování. |
        | Nastavení ověření identity | Podrobné pokyny týkající se vytváření a používání jednotlivých registrací s symetrickými klíči nebo s certifikáty X. 509 najdete v jednom z těchto [zřízení symetrického zařízení](quick-create-simulated-device-symmetric-key-java.md#create-a-device-enrollment) nebo [zřízení certifikátu x. 509](quick-create-simulated-device-x509-java.md#create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry) pro rychlé zprovoznění.<br><br>Podrobné pokyny k vytváření a používání jednotlivých registrací pomocí ověření identity pomocí čipu TPM najdete v jedné ze [zřízení simulovaných ukázek zařízení TPM](quick-create-simulated-device-tpm-java.md#create-a-device-enrollment-entry) .|
        | **ID zařízení IoT Hub** |  Toto ID bude představovat vaše zařízení. Musí splňovat pravidla pro ID zařízení. Další informace najdete v tématu [Vlastnosti identity zařízení](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).<br><br>Při použití certifikátů X. 509 musí být tento text názvem subjektu v certifikátu zařízení, který nahrajete pro registraci. Tento název subjektu musí odpovídat pravidlům pro ID zařízení.|
            


## <a name="update-an-enrollment-entry"></a>Aktualizace položky registrace
Existující položku registrace můžete aktualizovat na portálu pomocí následujících kroků:

1. Otevřete službu Device Provisioning v Azure Portal a klikněte na **spravovat registrace**. 
1. Přejděte na položku registrace, kterou chcete upravit. Klikněte na položku, která otevře souhrnné informace o registraci zařízení. 
1. Na této stránce můžete upravit jiné položky než typ zabezpečení a přihlašovací údaje, jako je například IoT Hub, se kterým má být zařízení propojeno, a ID zařízení. Můžete také změnit počáteční stav vlákna zařízení. 
1. Po dokončení klikněte na **Uložit** a aktualizujte registraci zařízení. 

    ![Aktualizace registrace na portálu](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Odebrání registrace zařízení
V případech, kdy vaše zařízení není nutné zřídit do služby IoT Hub, můžete související položku registrace odebrat na portálu pomocí následujících kroků:

1. Otevřete službu Device Provisioning v Azure Portal a klikněte na **spravovat registrace**. 
1. Přejděte na adresu a vyberte položku registrace, kterou chcete odebrat. 
1. Klikněte na tlačítko **Odstranit** v horní části a po zobrazení výzvy k potvrzení vyberte **Ano** . 
1. Až se akce dokončí, uvidíte, že se vaše položka odebrala ze seznamu registrací zařízení. 
 
    ![Odebrání registrace na portálu](./media/how-to-manage-enrollments/remove-enrollment.png)
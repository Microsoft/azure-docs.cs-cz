---
title: Rušení registrace zařízení z Azure IoT Hub Device Provisioning Service
description: Jak zrušit registraci zařízení, aby se zabránilo zřizování prostřednictvím Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 120446472038076e34f62b47ba79348e5de8b972
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010924"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Postup při rušení registrace zařízení z Azure IoT Hub Device Provisioning Service

Správná Správa přihlašovacích údajů zařízení je zásadní pro systémy s vysokým profilem, jako jsou řešení IoT. Osvědčeným postupem pro tyto systémy je mít jasný plán, jak odvolat přístup k zařízením v případě, že dojde k ohrožení zabezpečení tokenu sdíleného přístupového podpisu (SAS) nebo certifikátu X. 509. 

Registrace ve službě Device Provisioning umožňuje [zřídit](about-iot-dps.md#provisioning-process)zařízení. Zřízené zařízení je takové, které bylo zaregistrováno ve službě IoT Hub, což umožňuje, aby získalo [počáteční stav](~/articles/iot-hub/iot-hub-devguide-device-twins.md) a data telemetrie pro vytváření sestav. Tento článek popisuje, jak zrušit registraci zařízení z instance služby zřizování a zabránit tak jeho opětovnému zřízení v budoucnu.

> [!NOTE] 
> Uvědomte si zásady opakování zařízení, ke kterým odvoláte přístup. Například zařízení, které má zásady nekonečné opakování, se může neustále pokoušet zaregistrovat ve službě zřizování. Tato situace spotřebovává prostředky služby a může mít vliv na výkon.

## <a name="disallow-devices-by-using-an-individual-enrollment-entry"></a>Zakázat zařízení pomocí individuální položky registrace

Jednotlivé registrace se vztahují na jedno zařízení a můžou použít buď certifikáty X. 509, nebo tokeny SAS (ve skutečném nebo virtuálním čipu TPM) jako mechanismus ověřování. (Zařízení, která používají tokeny SAS jako mechanismus ověření identity, se dají zřídit jenom prostřednictvím individuální registrace.) Pokud chcete zakázat zařízení, které má jednotlivou registraci, můžete buď zakázat nebo odstranit jeho položku registrace. 

Pokud chcete zařízení dočasně zakázat tím, že zakážete jeho zápis: 

1. Přihlaste se k Azure Portal a v nabídce vlevo vyberte **všechny prostředky** .
2. V seznamu prostředků vyberte službu zřizování, ze které chcete zakázat vaše zařízení.
3. V rámci služby zřizování vyberte možnost **spravovat registrace** a pak vyberte kartu **jednotlivé registrace** .
4. Vyberte položku registrace pro zařízení, které chcete zakázat. 

    ![Vyberte svoji jednotlivou registraci.](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na stránce pro registraci se posuňte do dolní části a vyberte **Zakázat** pro přepínač **Povolit položku** a pak vyberte **Uložit**.  

   ![Zakázat položku jednotlivé registrace na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Pokud chcete zařízení trvale zakázat odstraněním jeho registračního záznamu:

1. Přihlaste se k Azure Portal a v nabídce vlevo vyberte **všechny prostředky** .
2. V seznamu prostředků vyberte službu zřizování, ze které chcete zakázat vaše zařízení.
3. V rámci služby zřizování vyberte možnost **spravovat registrace** a pak vyberte kartu **jednotlivé registrace** .
4. Zaškrtněte políčko vedle položky registrace zařízení, které chcete zakázat. 
5. V horní části okna vyberte **Odstranit** a pak kliknutím na **Ano** potvrďte, že chcete registraci odebrat. 

   ![Odstranit jednotlivou položku registrace na portálu](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Po dokončení postupu byste měli vidět, že se vaše položka odebrala ze seznamu jednotlivých zápisů.  

## <a name="disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Zakažte certifikát X. 509 zprostředkující nebo kořenové certifikační autority pomocí skupiny registrace.

Certifikáty X. 509 jsou obvykle uspořádány v řetězu certifikátů důvěryhodnosti. Pokud dojde k ohrožení bezpečnosti certifikátu v jakékoli fázi v řetězci, je vztah důvěryhodnosti přerušený. Aby se zabránilo službě zřizování zařízení v jakémkoli řetězci, který tento certifikát obsahuje, musí být certifikát zakázaný. Další informace o certifikátech X. 509 a o tom, jak se používají se službou zřizování, najdete v tématu [certifikáty x. 509](./concepts-x509-attestation.md#x509-certificates). 

Skupina registrací je záznam pro zařízení, která sdílejí běžný mechanismus ověřování certifikátů X. 509 podepsaných stejnou zprostředkující nebo kořenovou certifikační autoritou. Položka skupiny registrací je nakonfigurovaná s certifikátem X. 509 přidruženým k zprostředkující nebo kořenové certifikační autoritě. Tato položka je taky nakonfigurovaná s použitím hodnot konfigurace, jako je například zdvojený stav a připojení ke službě IoT Hub, které jsou sdíleny zařízeními s tímto certifikátem v řetězu certifikátů. Chcete-li zakázat certifikát, můžete buď zakázat nebo odstranit jeho skupinu registrací.

Dočasné zakázání certifikátu tím, že zakážete jeho skupinu registrace: 

1. Přihlaste se k Azure Portal a v nabídce vlevo vyberte **všechny prostředky** .
2. V seznamu prostředků vyberte službu zřizování, ze které chcete zakázat podpisový certifikát.
3. Ve vaší službě zřizování vyberte **spravovat registrace** a pak vyberte kartu **skupiny** registrací.
4. Vyberte skupinu registrace pomocí certifikátu, který chcete zakázat.
5. V přepínači **Povolit položku** vyberte **Zakázat** a pak vyberte **Uložit**.  

   ![Zakázat zápis skupiny registrací na portálu](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Chcete-li certifikát trvale zakázat odstraněním jeho registrační skupiny:

1. Přihlaste se k Azure Portal a v nabídce vlevo vyberte **všechny prostředky** .
2. V seznamu prostředků vyberte službu zřizování, ze které chcete zakázat vaše zařízení.
3. Ve vaší službě zřizování vyberte **spravovat registrace** a pak vyberte kartu **skupiny** registrací.
4. Zaškrtněte políčko vedle skupiny registrace pro certifikát, který chcete zakázat. 
5. V horní části okna vyberte **Odstranit** a pak kliknutím na **Ano** potvrďte, že chcete odebrat skupinu registrací. 

   ![Odstranit položku registrační skupiny na portálu](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Po dokončení postupu byste měli vidět, že se vaše položka odebrala ze seznamu skupin pro zápis.  

> [!NOTE]
> Pokud odstraníte skupinu registrací pro certifikát, zařízení s certifikátem v řetězu certifikátů se můžou pořád zaregistrovat, pokud už existuje povolená skupina pro registraci pro kořenový certifikát nebo jiný zprostředkující certifikát ve svém řetězu certifikátů.

## <a name="disallow-specific-devices-in-an-enrollment-group"></a>Zakázat specifická zařízení ve skupině pro registraci

Zařízení, která implementují mechanismus ověřování X. 509, používají k ověření svůj řetěz certifikátů a privátní klíč zařízení. Když se zařízení připojí a ověří pomocí služby Device Provisioning, služba nejprve vyhledá jednotlivou registraci, která odpovídá přihlašovacím údajům daného zařízení. Služba pak vyhledá skupiny registrací a určí, jestli je možné zařízení zřídit. Pokud služba nalezne zakázanou jednotlivou registraci zařízení, zabrání v připojení zařízení. Služba brání připojení i v případě, že existuje povolená skupina registrace pro zprostředkující nebo kořenovou certifikační autoritu v řetězu certifikátů zařízení. 

Pokud chcete zakázat jednotlivá zařízení ve skupině registrací, postupujte podle těchto kroků:

1. Přihlaste se k Azure Portal a v nabídce vlevo vyberte **všechny prostředky** .
2. V seznamu prostředků vyberte službu zřizování, která obsahuje skupinu registrace pro zařízení, které chcete zakázat.
3. V rámci služby zřizování vyberte možnost **spravovat registrace** a pak vyberte kartu **jednotlivé registrace** .
4. V horní části vyberte tlačítko **přidat jednotlivou registraci** . 
5. Na stránce **Přidat registraci** jako **mechanismus** ověřování pro zařízení vyberte **X. 509** .

    Nahrajte certifikát zařízení a zadejte ID zařízení, které se má zakázat. Pro certifikát použijte podepsaný certifikát koncové entity nainstalovaný na zařízení. Zařízení používá pro ověřování podepsaný certifikát koncové entity.

    ![Nastavení vlastností zařízení pro nepovolené zařízení](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Posuňte se do dolní části stránky **Přidat registraci** a v přepínači **Povolit položku** vyberte **Zakázat** a pak vyberte **Uložit**. 

    [![Pomocí zakázané položky registrace zakázat zařízení při registraci skupiny na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Po úspěšném vytvoření registrace byste měli vidět zakázaný zápis zařízení uvedený na kartě **jednotlivé registrace** . 

## <a name="next-steps"></a>Další kroky

Zrušení registrace je také součástí většího procesu rušení. Zrušení zřízení zařízení zahrnuje jak zrušit registraci ze služby zřizování, a zrušit registraci ze služby IoT Hub. Další informace o plném procesu najdete v tématu [Postup zrušení zřízení zařízení, která byla dříve automaticky zřízena](how-to-unprovision-devices.md) .
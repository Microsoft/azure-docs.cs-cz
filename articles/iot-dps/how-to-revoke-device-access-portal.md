---
title: Zrušení registrace zařízení ze služby Zřizování zařízení služby Azure IoT Hub
description: Jak zrušit registraci zařízení, abyste zabránili zřizování prostřednictvím služby Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: af883da67f4e1bc819514e88ff480526e16124db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974917"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Jak zrušit registraci zařízení ze služby Azure IoT Hub Device Provisioning Service

Správná správa přihlašovacích údajů zařízení je zásadní pro vysoce profilované systémy, jako jsou řešení IoT. Osvědčeným postupem pro tyto systémy je mít jasný plán, jak odvolat přístup pro zařízení, když jejich pověření, zda token sdílených přístupových podpisů (SAS) nebo certifikát X.509, může být ohrožena. 

Registrace ve službě Device Provisioning Service umožňuje [automatické zřizování](concepts-auto-provisioning.md)zařízení . Zřízené zařízení je zařízení, které bylo zaregistrováno v centru IoT Hub, což mu umožňuje přijímat počáteční stav [dvojčete zařízení](~/articles/iot-hub/iot-hub-devguide-device-twins.md) a začít vykazovat telemetrická data. Tento článek popisuje, jak zrušit registraci zařízení z instance služby zřizování, brání jeho zřizování znovu v budoucnu.

> [!NOTE] 
> Uvědomte si zásady opakování zařízení, pro která odvoláte přístup. Například zařízení, které má zásady nekonečné opakování může neustále pokusí zaregistrovat se službou zřizování. Tato situace spotřebovává prostředky služby a případně ovlivňuje výkon.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Blacklist zařízení pomocí jednotlivé položky registrace

Jednotlivé zápisy platí pro jedno zařízení a můžete použít buď Certifikáty X.509 nebo Tokeny SAS (v reálném nebo virtuální čip TPM) jako mechanismus ověřování. (Zařízení, která používají tokeny SAS jako jejich mechanismus ověřování lze zřídit pouze prostřednictvím individuální registrace.) Chcete-li na černou listinu zařízení, které má individuální zápis, můžete zakázat nebo odstranit jeho zápis. 

Chcete-li zařízení dočasně zařadit na černou listinu zakázáním jeho registrace: 

1. Přihlaste se k portálu Azure a v levé nabídce vyberte **Všechny prostředky.**
2. V seznamu prostředků vyberte zřizovací službu, ze které chcete zařízení zařadit na černou listinu.
3. Ve službě zřizování vyberte **Spravovat registrace**a pak vyberte kartu **Individuální registrace.**
4. Vyberte položku registrace pro zařízení, které chcete na černou listinu. 

    ![Vyberte si individuální registraci](./media/how-to-revoke-device-access-portal/select-individual-enrollment.png)

5. Na stránce registrace přejděte dolů a vyberte **zakázat** přepínač **Povolit položku** a pak vyberte **Uložit**.  

   ![Zakázání jednotlivé položky registrace na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)

Chcete-li trvale zařadit zařízení na černou listinu odstraněním jeho položky registrace:

1. Přihlaste se k portálu Azure a v levé nabídce vyberte **Všechny prostředky.**
2. V seznamu prostředků vyberte zřizovací službu, ze které chcete zařízení zařadit na černou listinu.
3. Ve službě zřizování vyberte **Spravovat registrace**a pak vyberte kartu **Individuální registrace.**
4. Zaškrtněte políčko vedle položky registrace pro zařízení, které chcete na černou listinu. 
5. V horní části okna vyberte **Odstranit** a pak vyberte **Ano,** abyste potvrdili, že chcete zápis odebrat. 

   ![Odstranění jednotlivé položky registrace na portálu](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)


Po dokončení postupu by měla být položka odebrána ze seznamu jednotlivých registrací.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Zařazení certifikátu X.509 zprostředkující nebo kořenové certifikační autority pomocí skupiny pro zápis na černou listinu

Certifikáty X.509 jsou obvykle uspořádány v řetězu důvěryhodnosti certifikátů. Pokud dojde k ohrožení zabezpečení certifikátu v jakékoli fázi řetězce, dojde k porušení vztahu důvěryhodnosti. Certifikát musí být na černé listině, aby se zabránilo device provisioning Service z zřizování zařízení po proudu v libovolném řetězci, který obsahuje tento certifikát. Další informace o certifikátech X.509 a o tom, jak se používají se službou zřizování, naleznete v [tématu Certifikáty X.509](./concepts-security.md#x509-certificates). 

Skupina zápisu je položka pro zařízení, která sdílejí společný mechanismus ověřování certifikátů X.509 podepsaných stejnou zprostředkující nebo kořenovou certifikační autoritou. Položka skupiny zápisu je nakonfigurována s certifikátem X.509 přidruženým k zprostředkující nebo kořenové certifikační autoritě. Položka je také nakonfigurována se všemi hodnotami konfigurace, jako je například dvojče a připojení služby IoT hub, které jsou sdíleny zařízeními s tímto certifikátem v řetězci certifikátů. Chcete-li certifikát zařadit na černou listinu, můžete jeho skupinu pro zápis zakázat nebo odstranit.

Chcete-li certifikát dočasně zařadit na černou listinu zakázáním jeho skupiny zápisu: 

1. Přihlaste se k portálu Azure a v levé nabídce vyberte **Všechny prostředky.**
2. V seznamu prostředků vyberte zřizovací službu, ze které chcete načernou seznam podpisového certifikátu.
3. Ve službě zřizování vyberte **Spravovat registrace**a pak vyberte kartu **Skupiny zápisů.**
4. Vyberte skupinu zápisů pomocí certifikátu, který chcete na černou listinu.
5. Na přepínači **Povolit položku** vyberte **Zakázat** a pak vyberte **Uložit**.  

   ![Zakázání položky skupiny registrace na portálu](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Chcete-li certifikát trvale zařadit na černou listinu odstraněním jeho skupiny pro zápis:

1. Přihlaste se k portálu Azure a v levé nabídce vyberte **Všechny prostředky.**
2. V seznamu prostředků vyberte zřizovací službu, ze které chcete zařízení zařadit na černou listinu.
3. Ve službě zřizování vyberte **Spravovat registrace**a pak vyberte kartu **Skupiny zápisů.**
4. Zaškrtněte políčko vedle skupiny zápisu certifikátu, který chcete zařadit na černou listinu. 
5. V horní části okna vyberte **Odstranit** a pak vyberte **Ano,** abyste potvrdili, že chcete odebrat skupinu zápisů. 

   ![Odstranit položku skupiny registrace na portálu](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Po dokončení procedury by měla být položka odebrána ze seznamu skupin zápisů.  

> [!NOTE]
> Pokud odstraníte skupinu zápisů pro certifikát, zařízení, která mají certifikát v řetězci certifikátů, mohou být stále schopna zaregistrovat, pokud je povolená skupina pro zápis kořenového certifikátu nebo jiného zprostředkujícího certifikátu výše v jejich certifikátu řetězec existuje.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Blacklist konkrétní zařízení ve skupině registrace

Zařízení, která implementují mechanismus ověření X.509, používají k ověření řetěz certifikátů a soukromý klíč zařízení. Když se zařízení připojí a ověří pomocí služby Device Provisioning Service, služba nejprve vyhledá individuální registraci, která odpovídá pověření zařízení. Služba pak prohledá skupiny registrace a určí, zda lze zařízení zřídit. Pokud služba najde zakázanou individuální registraci pro zařízení, zabrání zařízení v připojení. Služba zabrání připojení i v případě, že existuje povolená skupina zápisu pro zprostředkující nebo kořenovou certifikační autoritu v řetězu certifikátů zařízení. 

Chcete-li na černou listinu jednotlivé zařízení ve skupině registrace, postupujte takto:

1. Přihlaste se k portálu Azure a v levé nabídce vyberte **Všechny prostředky.**
2. Ze seznamu prostředků vyberte zřizovací službu, která obsahuje skupinu registrace pro zařízení, které chcete na černou listinu.
3. Ve službě zřizování vyberte **Spravovat registrace**a pak vyberte kartu **Individuální registrace.**
4. Nahoře vyberte tlačítko **Přidat jednotlivé zápisy.** 
5. Na stránce **Přidat zápis** vyberte **X.509** jako **mechanismus** ověřování pro zařízení.

    Nahrajte certifikát zařízení a zadejte ID zařízení zařízení, které má být zařazeno na černou listinu. Pro certifikát použijte podepsaný certifikát koncové entity nainstalovaný v zařízení. Zařízení používá k ověřování podepsaný certifikát koncové entity.

    ![Nastavení vlastností zařízení pro zařízení uvedené na černé listině](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group-1.png)

6. Přejděte do dolní části stránky **Přidat zápis** a na **přepínači Povolit položku** vyberte **Zakázat** a pak vyberte **Uložit**. 

    [![Zakázání majekvatek jednotlivých položek registrace k zakázání registrace zařízení ze skupiny na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Při úspěšném vytvoření registrace byste měli vidět registraci zakázaného zařízení uvedenou na kartě **Jednotlivé registrace.** 

## <a name="next-steps"></a>Další kroky

Zrušení registrace je také součástí většího procesu zrušení zřízení. Zrušení zřízení zařízení zahrnuje jak zrušení registrace ze služby zřizování, tak zrušení registrace z centra IoT Hub. Další informace o celém procesu najdete v tématu [Jak odzřízení zařízení, která byla dříve automaticky zřízená](how-to-unprovision-devices.md) 


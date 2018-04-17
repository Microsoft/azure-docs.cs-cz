---
title: Postup disenroll zařízení z Azure IoT Hub zařízení zřizování služby
description: Postup disenroll zařízení, aby se zabránilo zřizování prostřednictvím Azure IoT Hub zařízení zřizování služby
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr;bryanla
ms.date: 04/05/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 50074eaecacf603d2bc6170183fd632b4a1ab2d1
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Postup disenroll zařízení z Azure IoT Hub zařízení zřizování služby

Správné správu přihlašovacích údajů, zařízení je zásadní pro systémy vysokou profil jako řešení IoT. Osvědčeným postupem pro tyto systémy je mít zrušte plán o tom, jak odvolat přístup k zařízení při jejich přihlašovacích údajů, zda token sdílený přístupový podpis (SAS) nebo certifikátu X.509, může dojít k ohrožení. 

Registrace ve službě zřizování zařízení umožňuje zařízení tak, aby [automaticky zřizovat](concepts-auto-provisioning.md). Zřízené zařízení je ten, který byl registrován u služby IoT Hub, díky kterému jej přijímat úvodního [dvojče zařízení](~/articles/iot-hub/iot-hub-devguide-device-twins.md) stavu a začít reporting telemetrická data. Tento článek popisuje postup disenroll zařízení z zřizování instance služby znemožňuje se znovu zřídí v budoucnu.

> [!NOTE] 
> Pamatujte na zařízení, která odvolání přístupu pro zásady opakování. Například zařízení, které má zásady opakování nekonečné může trvale pokusit zaregistrovat službu zřizování. Tato situace spotřebovává prostředky služby a pravděpodobně má vliv na výkon.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Zařízení zakázaných pomocí položku jednotlivých registrace

Jednotlivé registrace platí pro jedno zařízení a můžete použít certifikáty X.509 nebo tokeny SAS (ve fyzických nebo virtuálních čipu TPM) jako mechanismus ověření. (Zařízení, které používají tokeny SAS jako mechanismus jejich ověření můžete zřídit pouze prostřednictvím jednotlivých zápisu.) Chcete-li blokovaných zařízení, které má jednotlivých zápisu, můžete zakázat nebo odstranit jeho záznam zápisu. 

Dočasně blokovaných zakázáním jeho položku registrace zařízení: 

1. Přihlaste se k Azure portálu a vyberte možnost **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte službu, kterou chcete blokovaných zařízení z zřizování.
3. Ve službě zřizování vyberte **spravovat registrace**a pak vyberte **jednotlivých registrace** kartě.
4. Vyberte položku registrace pro zařízení, které chcete blokovaných. 
5. Posuňte se dolů a vyberte **zakázat** na **povolit položku** přepínače a potom vyberte **Uložit**.  

   [![Zakázat registrace jednotlivé položky na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png#lightbox)  

Chcete-li trvale blokovaných zařízení odstraněním jeho registrace položku:

1. Přihlaste se k Azure portálu a vyberte možnost **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte službu, kterou chcete blokovaných zařízení z zřizování.
3. Ve službě zřizování vyberte **spravovat registrace**a pak vyberte **jednotlivých registrace** kartě.
4. Zaškrtněte políčko u položky registrace pro zařízení, které chcete blokovaných. 
5. Vyberte **odstranit** v horní části okna a potom vyberte **Ano** potvrďte, že chcete odebrat registraci. 

   ![Odstranit položku jednotlivých registrace na portálu](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
Po dokončení procesu, měli byste vidět zadání odebrat ze seznamu jednotlivých registrace.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Blokovaných X.509 zprostředkující nebo certifikát kořenové certifikační Autority pomocí skupinu registrace

X.509 – certifikáty jsou obvykle uspořádány řetěz certifikátů. Pokud bude k ohrožení bezpečnosti certifikátu v jakékoli fázi v řetězu, vztah důvěryhodnosti se přeruší. Certifikát musí být zakázáno zabránit zřizování zařízení po proudu v řetězu certifikátů obsahující tento certifikát služby zřizování zařízení. Další informace o certifikátech X.509 a jak se používají službou zřizování najdete v tématu [certifikáty X.509](./concepts-security.md#x509-certificates). 

Skupinu registrace je záznam pro zařízení, která sdílejí společné mechanismus ověření certifikátů X.509 podepsaný zprostředkující stejné nebo kořenová certifikační Autorita. Zadání skupiny registrace je nakonfigurován s certifikátem X.509 přidružené mezilehlých nebo kořenová certifikační Autorita. Položka je také nakonfigurovaný se žádné konfigurace hodnoty, například twin stavu a IoT připojení rozbočovače, které jsou sdíleny zařízení pomocí certifikátu v jejich řetězu certifikátů. Chcete-li blokovaných certifikát, můžete zakázat nebo odstranit skupinu jeho registrace.

Chcete-li dočasně blokovaných certifikát zakázáním jeho registrace skupiny: 

1. Přihlaste se k Azure portálu a vyberte možnost **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte zřizování službu, kterou chcete blokovaných podpisový certifikát z.
3. Ve službě zřizování vyberte **spravovat registrace**a pak vyberte **registrace skupiny** kartě.
4. Vyberte skupinu registrace pomocí certifikátu, který chcete blokovaných.
5. Vyberte **zakázat** na **povolit položku** přepínače a potom vyberte **Uložit**.  

   ![Zakažte položky skupiny registrace na portálu](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Chcete-li trvale blokovaných certifikát odstraněním skupiny jeho registrace:

1. Přihlaste se k Azure portálu a vyberte možnost **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte službu, kterou chcete blokovaných zařízení z zřizování.
3. Ve službě zřizování vyberte **spravovat registrace**a pak vyberte **registrace skupiny** kartě.
4. Zaškrtněte políčko vedle skupiny zápisu pro certifikát, který chcete blokovaných. 
5. Vyberte **odstranit** v horní části okna a potom vyberte **Ano** k potvrzení odebrání skupiny pro zápis. 

   ![Odstranit položku skupiny registrace na portálu](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Po dokončení procesu, měli byste vidět zadání odebrat ze seznamu skupin registrace.  

> [!NOTE]
> Pokud odstraníte skupinu zápisu certifikátu, zařízení, které mají certifikát v řetězu jejich certifikátů může být stále moci zaregistrovat Pokud skupinu povoleno registrace pro kořenový certifikát nebo jiný certifikát zprostředkující nacházejí v jejich certifikátu existuje řetězec.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Zakázaných konkrétní zařízení ve skupině registrace

Zařízení, které implementují mechanismus ověření X.509 používají k ověření zařízení řetěz certifikátů a privátní klíč. Když se zařízení připojí a ověřuje se službou zřizování zařízení, služby nejprve hledá jednotlivých zápisu odpovídající přihlašovací údaje zařízení. Služba pak prohledá registrace skupiny k určení, jestli zařízení může být zřízen. Pokud službu najde zakázané jednotlivých registrace pro zařízení, brání zařízení v připojení. Služba brání připojení i v případě, že povoleno registrace skupiny pro středně pokročilé nebo kořenové certifikační Autority v řetězu certifikátů v zařízení existuje. 

Chcete-li blokovaných k jednotlivým zařízením ve skupině registrace, postupujte takto:

1. Přihlaste se k Azure portálu a vyberte možnost **všechny prostředky** v levé nabídce.
2. V seznamu zdrojů vyberte zřizování služba, která obsahuje skupinu registrace pro zařízení, které chcete blokovaných.
3. Ve službě zřizování vyberte **spravovat registrace**a pak vyberte **jednotlivých registrace** kartě.
4. Vyberte **přidat** tlačítka v horní části. 
5. Vyberte **X.509** jako mechanismus ověření pro zařízení a nahrání certifikátu zařízení. Toto je nainstalovaný v zařízení certifikát podepsaný držitelem koncové entity. Zařízení se používá pro generování certifikátů pro ověřování.
6. Pro **ID zařízení IoT Hub**, zadejte ID zařízení. 
7. Vyberte **zakázat** na **povolit položku** přepínače a potom vyberte **Uložit**. 

    [![Použití zakázaná položka jednotlivých registrace se zakázat zařízení ze skupiny registrace na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Když vytvoříte úspěšně registraci, měli byste vidět zobrazí na zařízení **jednotlivých registrace** kartě.

## <a name="next-steps"></a>Další postup

Disenrollment je také součástí větší proces zrušení zřízení. Zrušení zřízení zařízení zahrnuje jak disenrollment od zřízení služby a deregistering ze služby IoT hub. Další informace o plné zpracování najdete v tématu [postup zrušení zřízení zařízení, které byly dříve auto zajištěny](how-to-unprovision-devices.md) 


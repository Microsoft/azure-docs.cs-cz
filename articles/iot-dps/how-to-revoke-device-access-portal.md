---
title: Jak disenroll zařízení v Azure IoT Hub Device Provisioning Service
description: Jak disenroll zařízení, aby se zabránilo zřizování prostřednictvím Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: d0720c23e0831b446a92855383fab06b0bfacbc7
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525463"
---
# <a name="how-to-disenroll-a-device-from-azure-iot-hub-device-provisioning-service"></a>Jak disenroll zařízení v Azure IoT Hub Device Provisioning Service

Správné řízení přihlašovací údaje k zařízení je zásadní pro systémy vysoce profilu, jako je řešení IoT. Osvědčeným postupem pro tyto systémy je mít vymazat plán odvoláte přístup pro zařízení při své přihlašovací údaje, zda token sdílených přístupových podpisů (SAS) nebo certifikátu X.509, může dojít k ohrožení bezpečnosti. 

Registrace ve službě Device Provisioning umožňuje v zařízení bude [automatické zřizování](concepts-auto-provisioning.md). Zřízená zařízení je ten, který byl zaregistrován u služby IoT Hub, což umožňuje přijímat úvodního [dvojče zařízení](~/articles/iot-hub/iot-hub-devguide-device-twins.md) stavu a začněte telemetrická data pro generování sestav. Tento článek popisuje, jak disenroll zařízení z zřizování instance služby brání v budoucnu znovu zřizuje.

> [!NOTE] 
> Mějte na paměti zásad opakování zařízení, která odvolat přístup. Například zařízení, která má zásady opakování nekonečné průběžně pokusit zaregistrovat do služby zřizování. Tato situace spotřebovává prostředky služeb a pravděpodobně má vliv na výkon.

## <a name="blacklist-devices-by-using-an-individual-enrollment-entry"></a>Blacklist zařízení s použitím položku jednotlivé registrace

Jednotlivé registrace platí pro jedno zařízení a jako mechanismus ověřování využívat certifikáty X.509 nebo tokeny SAS (na skutečném nebo virtuálním zařízení TPM). (Zařízení, které používají tokeny SAS podle jejich mechanismus ověřování se dá zřídit pouze prostřednictvím jednotlivou registraci.) Na seznam zakázaných jednotlivou registraci zařízení, můžete zakázat nebo odstranit jeho záznam registrace. 

Dočasně blokovaných zakázáním jeho položky registrace zařízení: 

1. Přihlaste se k Azure portal a vyberte **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte, které chcete seznam zakázaných registraci zařízení ve službě zřizování.
3. Ve vaší službě zřizování vyberte **Správa registrací**a pak vyberte **jednotlivé registrace** kartu.
4. Vyberte položku registrace pro zařízení, které chcete seznam zakázaných. 
5. Posuňte dolů a vyberte **zakázat** na **povolit položku** přepnout a pak vyberte **Uložit**.  

   [![Zakázat položku jednotlivé registrace na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment.png#lightbox)  

Chcete-li trvale seznam zakázaných zařízení tak, že odstraníte jeho položky registrace:

1. Přihlaste se k Azure portal a vyberte **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte, které chcete seznam zakázaných registraci zařízení ve službě zřizování.
3. Ve vaší službě zřizování vyberte **Správa registrací**a pak vyberte **jednotlivé registrace** kartu.
4. Zaškrtněte políčko vedle položky registrace zařízení, které chcete seznam zakázaných. 
5. Vyberte **odstranit** v horní části okna a pak vyberte **Ano** potvrďte, že chcete odebrat registraci. 

   ![Odstranit položku jednotlivé registrace na portálu](./media/how-to-revoke-device-access-portal/delete-individual-enrollment.png)
    
Po dokončení tohoto postup byste měli vidět vaší položky odebrat ze seznamu jednotlivé registrace.  

## <a name="blacklist-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group"></a>Seznam zakázaných X.509 zprostředkující nebo kořenové Certifikační autority pomocí skupině pro registraci

Certifikáty X.509 jsou obvykle uspořádané řetěz certifikátů. Pokud certifikát v jakékoli fázi v řetězci ohroženo, vztah důvěryhodnosti se přeruší. Certifikát musí být na seznamu zakázaných adres zabránit zřizování zařízení směru server-klient v libovolné řetězce, který obsahuje tento certifikát služby Device Provisioning. Další informace o certifikátech X.509 a jak se používají ve službě zřizování najdete v tématu [certifikáty X.509](./concepts-security.md#x509-certificates). 

Skupinu registrací je položka pro zařízení, která sdílet společného mechanismu ověřování certifikátů X.509 podepsané stejným zprostředkující nebo kořenové certifikační Autority. Záznam registrace skupiny je nakonfigurován s certifikátem X.509 přidružený přechodný nebo kořenové certifikační Autority. Položka je také nakonfigurováno s všechny hodnoty konfigurace, jako je například stav dvojčete a připojení k centru IoT, které jsou sdíleny zařízení pomocí tohoto certifikátu ve svém řetězu certifikátů. Na seznam zakázaných certifikátu, můžete zakázat nebo odstranit skupiny pro registraci.

Dočasně blokovaných zakázáním skupiny pro registraci certifikátu: 

1. Přihlaste se k Azure portal a vyberte **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte, které chcete seznam zakázaných podpisového certifikátu ze služby zřizování.
3. Ve vaší službě zřizování vyberte **Správa registrací**a pak vyberte **skupiny registrací** kartu.
4. Vyberte skupiny pro registraci pomocí certifikátu, který chcete seznam zakázaných.
5. Vyberte **zakázat** na **povolit položku** přepnout a pak vyberte **Uložit**.  

   ![Zakázat položku registrace skupiny na portálu](./media/how-to-revoke-device-access-portal/disable-enrollment-group.png)

    
Chcete-li trvale seznam zakázaných certifikát tak, že odstraníte jeho registraci skupiny:

1. Přihlaste se k Azure portal a vyberte **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte, které chcete seznam zakázaných registraci zařízení ve službě zřizování.
3. Ve vaší službě zřizování vyberte **Správa registrací**a pak vyberte **skupiny registrací** kartu.
4. Zaškrtněte políčko vedle skupiny registrací pro certifikát, který chcete seznam zakázaných. 
5. Vyberte **odstranit** v horní části okna a pak vyberte **Ano** potvrďte, že chcete odebrat skupinu registrací. 

   ![Odstranit položku registrace skupiny na portálu](./media/how-to-revoke-device-access-portal/delete-enrollment-group.png)

Po dokončení tohoto postup byste měli vidět vaší položky odebrat ze seznamu skupin pro registraci.  

> [!NOTE]
> Pokud odstraníte skupinu registrací pro certifikát, zařízení, která mají certifikát ve svém řetězu certifikátů stále možné zaregistrovat, pokud skupinu registrací povolené pro kořenový certifikát nebo jinou zprostředkující certifikát výše v jejich certifikátu existuje řetězec.

## <a name="blacklist-specific-devices-in-an-enrollment-group"></a>Seznam zakázaných konkrétní zařízení ve skupině pro registraci

Zařízení, která implementovat mechanismus ověřování X.509 používají k ověření zařízení řetěz certifikátů a privátní klíč. Když se zařízení připojí a ověřuje službě Device Provisioning, službu nejprve hledá jednotlivé registrace, který odpovídá přihlašovacích údajů zařízení. Služba potom vyhledá skupinám pro registraci k určení, jestli zařízení dá zřídit. Pokud služba najde zakázané jednotlivou registraci pro zařízení, brání zařízení v připojení. Služba brání připojení i v případě, že k povolena registrace skupiny pro zprostředkující nebo kořenové certifikační Autority v řetězu certifikátů zařízení existuje. 

Na seznam zakázaných jednotlivá zařízení ve skupině pro registraci, postupujte podle těchto kroků:

1. Přihlaste se k Azure portal a vyberte **všechny prostředky** v levé nabídce.
2. V seznamu prostředků vyberte zřizovací služba, která obsahuje skupiny pro registraci zařízení, které chcete seznam zakázaných.
3. Ve vaší službě zřizování vyberte **Správa registrací**a pak vyberte **jednotlivé registrace** kartu.
4. Vyberte **přidat** tlačítko v horní části. 
5. Vyberte **X.509** jako mechanismus ověřování pro zařízení a jak nahrát certifikát zařízení. Jedná se o certifikát podepsaný držitelem koncové entity na zařízení nainstalovaná. Zařízení se používá ke generování certifikátů pro ověřování.
6. Pro **ID zařízení IoT Hubu**, zadejte ID zařízení. 
7. Vyberte **zakázat** na **povolit položku** přepnout a pak vyberte **Uložit**. 

    [![Použití zakázané položky jednotlivé registrace zakázat zařízení ze skupiny registrace na portálu](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png)](./media/how-to-revoke-device-access-portal/disable-individual-enrollment-in-enrollment-group.png#lightbox)

Když úspěšně vytvoříte registraci, měli byste vidět vaše zařízení se zobrazí na **jednotlivé registrace** kartu.

## <a name="next-steps"></a>Další postup

Zrušení registrace je také součástí větší proces zrušení zřízení. Zrušení zřízení zařízení zahrnuje i zrušení registrace ze služby zřizování a zrušení ze služby IoT hub. Další informace o celý proces, viz [jak zrušit zřízení zařízení, které byly dříve automatické zřizování](how-to-unprovision-devices.md) 


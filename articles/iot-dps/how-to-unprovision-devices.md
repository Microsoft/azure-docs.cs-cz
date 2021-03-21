---
title: Zrušení zřízení zařízení, která byla zřízená s Azure IoT Hub Device Provisioning Service
description: Postup zrušení zřízení zařízení, která byla zřízená s Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 7c91cd8cbb1a927a88f05fcea603c6a4ed6e1c14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719028"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Postup zrušení zřízení zařízení, která byla dříve automaticky zřízena 

Možná bude nutné zrušit zřízení zařízení, která byla dříve automaticky zřízena prostřednictvím služby Device Provisioning. Zařízení se například může prodávat nebo přesouvat do jiného centra IoT nebo může být ztraceno, odcizeno nebo jinak ohroženo. 

Obecně platí, že zrušení zřízení zařízení se skládá ze dvou kroků:

1. Zrušením registrace zařízení ze služby zřizování zabráníte budoucímu automatickému zřizování. V závislosti na tom, jestli chcete dočasně nebo trvale odvolat přístup, možná budete chtít zakázat nebo odstranit položku registrace. U zařízení, která používají ověření identity X. 509, můžete chtít zakázat nebo odstranit položku v hierarchii stávajících skupin pro registraci.  
 
   - Informace o tom, jak zrušit registraci zařízení, najdete v tématu [Jak zrušit registraci zařízení z Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Informace o tom, jak zrušit registraci zařízení pomocí jedné ze sad SDK služby zřizování, najdete v tématu [Správa registrace zařízení pomocí sad SDK služby](./quick-enroll-device-x509-java.md).

2. Zrušte registraci zařízení od IoT Hub, abyste zabránili budoucí komunikaci a přenosu dat. Znovu můžete dočasně zakázat nebo trvale odstranit položku zařízení v registru identit pro IoT Hub, kde byla zřízena. Další informace o zakazování najdete v tématu [Zakázání zařízení](../iot-hub/iot-hub-devguide-identity-registry.md#disable-devices) . Projděte si téma Správa zařízení/zařízení IoT pro váš prostředek IoT Hub v [Azure Portal](https://portal.azure.com).

Přesný postup pro zrušení zřízení zařízení závisí na mechanismu ověřování a příslušné položce registrace ve vaší službě zřizování. Následující části obsahují přehled procesu na základě registrace a typu ověření identity.

## <a name="individual-enrollments"></a>Jednotlivé registrace
Zařízení, která používají ověření identity pomocí čipu TPM nebo ověřování X. 509 s listovým certifikátem, se zřídí prostřednictvím jednotlivé položky registrace. 

Zrušení zřízení zařízení, které má jednotlivou registraci: 

1. Zrušení registrace zařízení ze služby zřizování:

   - U zařízení, která používají ověření identity pomocí čipu TPM, odstraňte jednotlivé položky registrace, abyste trvale odvolali přístup zařízení ke službě zřizování, nebo tuto položku zakážete pro dočasné odvolání přístupu. 
   - U zařízení, která používají ověření identity X. 509, můžete položku buď odstranit, nebo zakázat. Mějte na paměti, že pokud odstraníte jednotlivou registraci pro zařízení, které používá X. 509 a povolenou skupinu registrací, existuje pro podpisový certifikát v řetězu certifikátů daného zařízení, může se zařízení znovu zaregistrovat. U takových zařízení může být bezpečnější zakázat položku registrace. Zabráníte tak opětovné registraci zařízení, a to bez ohledu na to, jestli povolená skupina pro registraci existuje pro jeden z jeho podpisových certifikátů.

2. Zakažte nebo odstraňte zařízení v registru identit ve službě IoT Hub, pro kterou byla zřízena. 


## <a name="enrollment-groups"></a>Skupiny registrací
Pomocí ověřování X. 509 se dají zařízení zřídit taky prostřednictvím skupiny pro registraci. Skupiny registrací se konfigurují pomocí podpisového certifikátu, buď zprostředkujícího, nebo kořenového certifikátu certifikační autority, a kontrolujte přístup ke službě zřizování pro zařízení s tímto certifikátem v řetězu certifikátů. Další informace o skupinách registrací a certifikátech X. 509 pomocí služby zřizování najdete v tématu [ověření certifikátu x. 509](concepts-x509-attestation.md). 

Chcete-li zobrazit seznam zařízení, která byla zřízena prostřednictvím skupiny registrací, můžete zobrazit podrobnosti skupiny registrací. Toto je jednoduchý způsob, jak pochopit, ke kterému IoT Hub je každé zařízení zřízené. Zobrazení seznamu zařízení: 

1. Přihlaste se k Azure Portal a klikněte na **všechny prostředky** v nabídce na levé straně.
2. V seznamu prostředků klikněte na svou službu zřizování.
3. Ve vaší službě zřizování klikněte na **Správa** registrací a pak vyberte kartu **skupiny** registrací.
4. Kliknutím na skupinu pro zápis ji otevřete.

   ![Zobrazit položku skupiny registrace na portálu](./media/how-to-unprovision-devices/view-enrollment-group.png)

Pomocí skupin registrací je možné zvážit dva scénáře:

- Zrušení zřízení všech zařízení, která byla zřízena prostřednictvím skupiny pro registraci:
  1. Zakažte skupinu registrací, která zakáže podpisový certifikát. 
  2. Pomocí seznamu zřízených zařízení pro tuto skupinu registrací zakažte nebo odstraňte jednotlivá zařízení z registru identit příslušného centra IoT Hub. 
  3. Po zakázání nebo odstranění všech zařízení ze svých příslušných hub IoT můžete případně odstranit skupinu registrací. Mějte ale na paměti, že pokud odstraníte skupinu registrací a v řetězu certifikátů jednoho nebo více zařízení je povolená skupina pro zápis podpisového certifikátu vyšší, můžou se tato zařízení znovu zaregistrovat. 

- Zrušení zřízení jednoho zařízení ze skupiny pro registraci:
  1. Vytvořte zakázanou jednotlivou registraci pro svůj certifikát na listovém (zařízení). Tím odvoláte přístup ke službě zřizování pro toto zařízení a zároveň povolíte přístup pro jiná zařízení, která mají podpisový certifikát skupiny registrací ve svém řetězci. Neodstraňujte zakázanou jednotlivou registraci zařízení. Tím umožníte, aby se zařízení znovu zaregistrovalo prostřednictvím skupiny pro registraci. 
  2. Pomocí seznamu zřízených zařízení pro tuto skupinu registrací vyhledejte IoT Hub, ke kterému se zařízení zřídilo, a zakažte nebo odstraňte z registru identit tohoto centra.
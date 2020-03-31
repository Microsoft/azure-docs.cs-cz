---
title: Zrušení zřízení zařízení, která byla zřízena se službou Azure IoT Hub Device Provisioning Service
description: Jak zrušili zařízení, která byla zřízena pomocí služby Azure IoT Hub DeviceProvisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 8a3677ba285f5b02407ca3d176979bf6c016ef9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974832"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Jak odpojit zařízení, která byla dříve automaticky zřízených 

Může být nezbytné, aby zrušení zařízení, která byla dříve automaticky zřízené prostřednictvím služby zřizování zařízení. Zařízení může být například prodáno nebo přesunuto do jiného centra IoT hub nebo může být ztraceno, odcizeno nebo jinak ohroženo. 

Obecně deprovisioning zařízení zahrnuje dva kroky:

1. Pokud chcete zabránit budoucímu automatickému zřizování, odevzdejte zařízení ze služby zřizování. V závislosti na tom, zda chcete přístup dočasně nebo trvale odvolat, můžete zakázat nebo odstranit položku zápisu. U zařízení, která používají atestaci X.509, můžete zakázat nebo odstranit položku v hierarchii existujících skupin zápisu.  
 
   - Informace o tom, jak zrušit registraci zařízení, najdete v [tématu Jak zrušit registraci zařízení ze služby Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Informace o tom, jak programově zrušit zařízení pomocí jedné z sad SDK služby zřizovací služby, naleznete [v tématu Správa registrací zařízení pomocí sad SDK služby](how-to-manage-enrollments-sdks.md).

2. Odregistrujte zařízení ze svého centra IoT Hub, abyste zabránili budoucí komunikaci a přenosu dat. Znovu můžete dočasně zakázat nebo trvale odstranit položku zařízení v registru identit pro službu IoT Hub, kde byla zřízena. Další informace o zakázání platnosti tématu [Zakázání zařízení](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) najdete v tématu Zakázání zařízení. Viz "Správa zařízení / Zařízení IoT" pro váš prostředek služby IoT Hub, na [webu Azure Portal](https://portal.azure.com).

Přesné kroky, které provedete k zrušení zřízení zařízení, závisí na jeho mechanismu ověřování a na jeho příslušné matné položce registrace s vaší službou zřizování. Následující části poskytují přehled procesu na základě typu zápisu a osvědčení.

## <a name="individual-enrollments"></a>Individuální zápisy
Zařízení, která používají ověřování čipu TPM nebo osvědčení X.509 s listovým certifikátem, jsou zřízena prostřednictvím jednotlivé položky zápisu. 

Zrušení zřízení zařízení, které má individuální registraci: 

1. Vyžádejte zařízení ze služby zřizování:

   - U zařízení, která používají atestaci čipu TPM, odstraňte jednotlivou položku registrace, abyste trvale zrušili přístup zařízení ke službě zřizování, nebo zakažte položku, abyste dočasně odvolali jeho přístup. 
   - U zařízení, která používají atestaci X.509, můžete položku odstranit nebo zakázat. Uvědomte si však, že pokud odstraníte individuální zápis pro zařízení, které používá X.509 a povolená skupina pro zápis existuje pro podpisový certifikát v řetězu certifikátů tohoto zařízení, zařízení se může znovu zaregistrovat. Pro taková zařízení může být bezpečnější zakázat položku registrace. Tím zabráníte zařízení v opětovnéregistraci bez ohledu na to, zda existuje povolená skupina zápisu pro jeden z jeho podpisových certifikátů.

2. Zakažte nebo odstraňte zařízení v registru identit služby IoT hub, do kterého bylo zřízeno. 


## <a name="enrollment-groups"></a>Skupiny zápisů
S Atestací X.509 lze zařízení také zřídit prostřednictvím skupiny registrace. Skupiny zápisů jsou konfigurovány s podpisovým certifikátem, zprostředkujícím nebo kořenovým certifikátem certifikační autority, a řídí přístup ke službě zřizování pro zařízení s tímto certifikátem v řetězci certifikátů. Další informace o skupinách zápisů a certifikátech X.509 se službou zřizování naleznete v [tématu Certifikáty X.509](concepts-security.md#x509-certificates). 

Chcete-li zobrazit seznam zařízení, která byla zřízena prostřednictvím skupiny registrace, můžete zobrazit podrobnosti skupiny registrace. Toto je snadný způsob, jak pochopit, které centrum IoT každé zařízení bylo zřízeno. Zobrazení seznamu zařízení: 

1. Přihlaste se k portálu Azure a klikněte na **všechny prostředky** v levé nabídce.
2. Klikněte na zřizovací službu v seznamu prostředků.
3. Ve službě zřizování klikněte na **Spravovat registrace**a pak vyberte kartu **Skupiny zápisů.**
4. Kliknutím na skupinu zápisů ji otevřete.

   ![Zobrazit položku skupiny zápisu na portálu](./media/how-to-unprovision-devices/view-enrollment-group.png)

Se skupinami zápisu je třeba zvážit dva scénáře:

- Zrušení zřízení všech zařízení, která byla zřízena prostřednictvím skupiny registrace:
  1. Zakažte skupině zápisů, aby byla na černé listině podpisového certifikátu. 
  2. Pomocí seznamu zřízených zařízení pro tuto skupinu registrací můžete zakázat nebo odstranit každé zařízení z registru identit příslušného centra IoT Hub. 
  3. Po zakázání nebo odstranění všech zařízení z příslušných center IoT můžete volitelně odstranit skupinu registrace. Uvědomte si však, že pokud odstraníte skupinu zápisu a existuje povolená skupina zápisu pro podpisový certifikát výše v řetězci certifikátů jednoho nebo více zařízení, mohou se tato zařízení znovu zaregistrovat. 

- Zrušení zřízení jednoho zařízení ze skupiny registrace:
  1. Vytvořte zakázaný individuální zápis pro jeho listový certifikát (zařízení). Tím se odvolá přístup ke službě zřizování pro toto zařízení a zároveň povolujepřístup pro jiná zařízení, která mají podpisový certifikát skupiny pro zápis v jejich řetězci. Neodstraňujte zakázanou individuální registraci zařízení. To umožní zařízení znovu zaregistrovat prostřednictvím skupiny registrace. 
  2. Pomocí seznamu zřízených zařízení pro tuto skupinu registrací vyhledejte službu IoT hub, do které bylo zařízení zřízeno, a zakažte nebo odstraňte z registru identit tohoto centra. 
  
  











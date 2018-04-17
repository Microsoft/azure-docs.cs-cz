---
title: Postup zrušení zřízení zařízení, které se zřizují službou Azure IoT Hub zařízení zřizování | Microsoft Docs
description: Postup zrušení zřízení zařízení, které se zřizují službou Azure IoT Hub zařízení zřizování
services: iot-dps
keywords: ''
author: bryanla
ms.author: v-jamebr;bryanla
ms.date: 04/06/2018
ms.topic: article
ms.service: iot-dps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 439d4ffa8eec12481f52bd15f0060800411f316e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Postup zrušení zřízení zařízení, které byly dříve auto zajištěny 

Možná bude potřeba deprovision zařízení, které byly dříve zajištěny automaticky prostřednictvím služby zřizování zařízení. Například může být za účelem prodeje nebo přesunout do jiné služby IoT hub zařízení, nebo ho může být ztráty, krádeže nebo jinému ohrožení zabezpečení. 

Obecně platí zrušení zřízení zařízení zahrnuje dva kroky:

1. Disenroll tohoto zařízení ve službě zřizování, aby se zabránilo budoucí zřizování automaticky. V závislosti na tom, jestli chcete odvolat přístup dočasně nebo trvale můžete zakázat nebo odstranit položku, která registrace. Pro zařízení, které používají X.509 ověření můžete chtít zakázat/odstranit položku v hierarchii skupin existující registrace.  
 
   - Další informace o disenroll zařízení naleznete v tématu [postup disenroll zařízení z Azure IoT Hub zařízení zřizování služby](how-to-revoke-device-access-portal.md).
   - Další postup disenroll zařízení programově pomocí jedné z zřizování sady SDK služby najdete v tématu [spravovat registrace zařízení pomocí služby SDK](how-to-manage-enrollments-sdks.md).

2. Zrušení registrace zařízení ze služby IoT Hub, aby se zabránilo přenosu budoucí komunikaci a data. Znovu můžete dočasně zakázat nebo trvale odstranit položku zařízení v registru identit pro službu IoT Hub, kde byla zřízený. V tématu [zakažte zařízení](/azure/iot-hub/iot-hub-devguide-identity-registry.md#disable-devices) Další informace o postižení. V najdete v části "Zařízení správy / zařízení IoT" IoT Hub prostředku, [portál Azure](https://portal.azure.com).

Přesné kroky, které je třeba provést zrušení zřízení zařízení závisí na jeho ověření mechanismu a jeho použitelných zápisu položku s zřizování služby. Přehled procesu, na základě typu registrace a ověření naleznete v následujících částech.

## <a name="individual-enrollments"></a>Jednotlivé registrace
Zařízení, která používají ověření identity TPM nebo X.509 ověření certifikátem listu jsou zřízené prostřednictvím položku jednotlivých registrace. 

Chcete-li zrušit jejich zřízení zařízení, které má jednotlivých zápisu: 

1. Disenroll zařízení ze služby zřizování:

   - Pro zařízení, která používají ověření čipu TPM položku jednotlivých registrace trvale odvolat přístup zařízení ke službě zřizování odstranit nebo zakázat položka dočasně odvolat přístup. 
   - Pro zařízení, které používají X.509 ověření můžete buď odstranit nebo zakázat položku. Mějte na paměti, ale pokud odstraníte jednotlivé zápisu pro zařízení, které používá X.509 a skupinu povoleno registrace pro podpisový certifikát existuje v tom, že zařízení řetěz certifikátů, zařízení můžete znovu zaregistrovat. Pro taková zařízení může být bezpečnější zakázat položka registrace. To zabraňuje tak ze zařízení zapsáno, bez ohledu na to, jestli existuje skupinu povoleno registrace pro vystavení podpisového certifikátu.

2. Zakázat nebo odstranit zařízení v registru identit služby IoT hub, který byl připravené. 


## <a name="enrollment-groups"></a>Registrace skupiny
S X.509 ověření můžete zařízení také zřízené prostřednictvím skupiny služby zápisu. Registrace skupiny jsou nakonfigurovány s podpisový certifikát, buď středně pokročilé nebo kořenový certifikát certifikační Autority a řízení přístupu ke službě zřizování pro zařízení s certifikátu v jejich řetězu certifikátů. Další informace o skupinách registrace a certifikáty X.509 službou zřizování najdete v tématu [certifikáty X.509](concepts-security.md#x509-certificates). 

Pokud chcete zobrazit seznam zařízení, které se zřizují prostřednictvím skupinu zápisu, můžete zobrazit podrobnosti o registraci skupiny. Toto je snadný způsob, jak pochopit, které zřídil každé zařízení do služby IoT hub. Chcete-li zobrazit seznam zařízení: 

1. Přihlaste se k portálu Azure a klikněte na tlačítko **všechny prostředky** v levé nabídce.
2. Klikněte na tlačítko zřizování služby v seznamu prostředků.
3. Ve službě zřizování, klikněte na tlačítko **spravovat registrace**, pak vyberte **registrace skupiny** kartě.
4. Klikněte na skupinu registrace ho otevřete.

   ![Zobrazit položku skupiny registrace na portálu](./media/how-to-unprovision-devices/view-enrollment-group.png)

Registrace skupiny pro existují dva scénáře vzít v úvahu:

- Chcete-li zrušit jejich zřízení všechna zařízení, které se zřizují prostřednictvím skupinu registrace:
  1. Zakažte na skupinu registrace blokovaných jeho podpisový certifikát. 
  2. Pomocí seznamu zřízené zařízení pro tuto skupinu registrace můžete zakázat nebo odstranit každé zařízení z registru identit jeho příslušné služby IoT hub. 
  3. Po zakázání nebo odstranění všechna zařízení z jejich odpovídajících centra IoT, můžete volitelně můžete odstranit skupinu registrace. Nezapomeňte však, že pokud odstranit skupinu registrace a je skupina povoleno registrace pro podpisový certifikát nacházejí v řetězu certifikátů jednoho nebo více zařízení, tato zařízení znovu zaregistrovat. 

- Chcete-li zrušit jejich zřízení jedno zařízení ze skupiny registrace:
  1. Vytvořte zakázaný jednotlivých registrace pro svůj certifikát typu list (zařízení). To odvolá přístup ke službě zřizování pro toto zařízení, přičemž dál umožňuje přístup pro jiná zařízení, které mají podpisový certifikát skupiny pro registraci v jejich řetězu. Neodstraňujte zakázané jednotlivých registrace zařízení. Díky tomu vám umožní zařízení znovu zaregistrovat prostřednictvím registrace skupiny. 
  2. Pomocí seznamu zřízené zařízení pro tuto skupinu registrace můžete najít zařízení zřízenou do služby IoT hub a zakázat nebo odstranit z registru identit v daném rozbočovači. 
  
  











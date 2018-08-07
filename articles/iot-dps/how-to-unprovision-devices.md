---
title: Jak zrušit zřízení zařízení, které byly zajišťovaný pomocí Azure IoT Hub Device Provisioning Service | Dokumentace Microsoftu
description: Zrušení zřízení zařízení, které byly zajišťovaný pomocí Azure IoT Hub Device Provisioning Service
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 3c6e2a9006d73d269422292dc959866d3f6d8a82
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522668"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Jak zrušit zřízení zařízení, které byly dříve automatické zřizování 

Možná bude potřeba zrušení zřizování zařízení, které byly dříve automatické zřízení pomocí služby Device Provisioning. Například může prodává nebo přesunut do jiné služby IoT hub zařízení, nebo to může být ztráty, krádeže nebo jinému ohrožení zabezpečení. 

Zrušení zřízení zařízení většinou zahrnuje dva kroky:

1. Disenroll zařízení z vaší službou zřizování, aby se zabránilo budoucí automatického zřizování. V závislosti na tom, jestli chcete odvolat přístup dočasně nebo trvale můžete zakázat nebo odstranit položku registrace. Zařízení, která používají ověření X.509 můžete chtít zakázat/odstranit položku v hierarchii vaše stávající skupiny registrací.  
 
   - Zjistěte, jak disenroll zařízení, najdete v článku [jak disenroll zařízení v Azure IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Zjistěte, jak disenroll zařízení programově pomocí jednoho z zřizování sady SDK pro služby, najdete v článku [Správa registrací zařízení pomocí sady SDK pro služby](how-to-manage-enrollments-sdks.md).

2. Zrušení registrace zařízení ze služby IoT Hub, aby se zabránilo budoucí komunikaci a přenosu dat. Znovu můžete dočasně zakázat nebo trvale odstranit záznam zařízení v registru identit pro službu IoT Hub, kde byla zřízena. Zobrazit [zakázat zařízení](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices) získat další informace o postižení. V naleznete v tématu "Zařízení Management / zařízení IoT" pro prostředek služby IoT Hub, [webu Azure portal](https://portal.azure.com).

Přesný postup, jak zrušit zřízení zařízení závisí na jeho mechanismus ověřování a jeho příslušné registrace položky ve vaší službě zřizování. Následující části poskytují přehled procesu podle typu registrace a ověření identity.

## <a name="individual-enrollments"></a>Jednotlivé registrace
Zařízení, která používají ověření identity čipem TPM nebo X.509 ověření s listového certifikátu se zřizují prostřednictvím položku jednotlivé registrace. 

Ke zrušení zřízení zařízení, která má jednotlivou registraci: 

1. Disenroll zařízení z vaší službou zřizování:

   - Zařízení, která používají ověření identity čipem TPM odstranit položky jednotlivé registrace trvale odvolat přístup zařízení ke službě zřizování nebo zakázat položku dočasně odvolat přístup. 
   - Zařízení, která používají ověření X.509 odstranit nebo zakázat položku. Mějte na paměti, ale pokud odstraníte jednotlivou registraci pro zařízení, která používá X.509 a skupině pro registraci povolené pro podpisový certifikát existuje v tom, že zařízení řetěz certifikátů, zařízení může znovu zaregistrovat. Pro taková zařízení může být bezpečnější zakázat položku registrace. To zabrání tak ze zařízení zapsáno, bez ohledu na to z toho, zda existuje skupina registrací povolené pro jeden z jeho podpisové certifikáty.

2. Zakázání nebo odstranění zařízení v registru identit služby IoT hub, která byla zřízena pro. 


## <a name="enrollment-groups"></a>Skupiny registrací
Pomocí ověření X.509 můžete zařízení také poskytované prostřednictvím skupinu registrací. Skupiny registrací jsou nakonfigurovány s podpisovým certifikátem, buď zprostředkující kořenový certifikát certifikační Autority a řízení přístupu ke službě zřizování pro zařízení s tímto certifikátem ve svém řetězu certifikátů. Další informace o registraci skupiny a certifikátů X.509 pomocí služby zřizování, najdete v článku [certifikáty X.509](concepts-security.md#x509-certificates). 

Pokud chcete zobrazit seznam zařízení, které se zřizují prostřednictvím skupině pro registraci, můžete zobrazit podrobnosti o skupině registrací. Toto je snadný způsob, jak pochopit, jaké služby IoT hub zřízený každé zařízení. Chcete-li zobrazit seznam zařízení: 

1. Přihlaste se k webu Azure portal a klikněte na tlačítko **všechny prostředky** v nabídce vlevo.
2. Klikněte na vaši službu zřizování v seznamu prostředků.
3. Ve vaší službě zřizování, klikněte na tlačítko **Správa registrací**a pak vyberte **skupiny registrací** kartu.
4. Klikněte na skupinu registrací ho otevřete.

   ![Zobrazit položku skupiny registrací na portálu](./media/how-to-unprovision-devices/view-enrollment-group.png)

Se skupinami pro registraci existují dva scénáře, které byste měli zvážit:

- Ke zrušení zřízení všechna zařízení, které se zřizují prostřednictvím skupině pro registraci:
  1. Zakážete skupiny pro registraci na seznam zakázaných jeho podpisový certifikát. 
  2. Pomocí seznamu zřízená zařízení pro tuto skupinu registrací můžete zakázat nebo odstranit každé zařízení v registru identit ve svých příslušných služby IoT hub. 
  3. Po zakázání nebo odstranění všechna zařízení z jejich odpovídajících centra IoT hub, můžete volitelně můžete odstranit skupinu registrací. Mějte na paměti, ale, že pokud odstraníte skupinu registrací a skupina povolena registrace pro podpisový certifikát v řetězu certifikátů výše jednoho nebo více zařízení, tato zařízení znovu zaregistrovat. 

- Ke zrušení zřízení jedno zařízení ze skupiny pro registraci:
  1. Vytvořte zakázaný jednotlivou registraci pro svůj certifikát typu list (zařízení). To odebere přístup ke službě zřizování pro toto zařízení přičemž dál umožňuje přístup pro další zařízení, které mají podpisový certifikát skupiny pro registraci ve svém řetězu. Odstranění zakázáno jednotlivou registraci pro zařízení. To vám umožní zařízení znovu zaregistrovat prostřednictvím skupiny pro registraci. 
  2. Pomocí seznamu zřízená zařízení pro tuto skupinu registrací pro hledání, které bylo zřízené zařízení do služby IoT hub a zakázat nebo odstranit z registru identit této služby. 
  
  











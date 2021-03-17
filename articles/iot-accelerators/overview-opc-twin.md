---
title: Co je OPC s dvojitou podporou – Azure | Microsoft Docs
description: Tento článek poskytuje přehled o zdvojení OPC. OPC vlákna poskytuje zjišťování, registraci a vzdálené řízení průmyslových zařízení prostřednictvím rozhraní REST API.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91281690"
---
# <a name="what-is-opc-twin"></a>Co je OPC s dvojitým obsahem?

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

OPC se skládá z mikroslužeb, které používají Azure IoT Edge a IoT Hub k propojení cloudu a sítě továrny. OPC vlákna poskytuje zjišťování, registraci a vzdálené řízení průmyslových zařízení prostřednictvím rozhraní REST API. OPC vláken nevyžaduje sadu SDK OPC UA (OPC Unified Architecture), je programovací jazyk nezávislá a může být součástí pracovního postupu bez serveru. Tento článek popisuje několik OPC vlákenných případů použití.

## <a name="discovery-and-control"></a>Zjišťování a řízení
OPC je možné použít pro jednoduché zjišťování a registraci.

### <a name="simple-discovery-and-registration"></a>Jednoduché zjišťování a registrace
OPC s sebou umožňuje operátorům továrny kontrolovat síť továrny, aby bylo možné zjistit a zaregistrovat servery OPC UA. Jako alternativu můžou obsluhy továrny taky ručně registrovat zařízení OPC UA pomocí známé adresy URL zjišťování. Pokud se třeba chcete připojit ke všem zařízením OPC UA po instalaci brány s doIoT Edge daným modulem OPC v produkčním patře, může operátor Factory vzdáleně aktivovat kontrolu sítě a vizuálně zobrazit všechny servery OPC UA. 

### <a name="simple-control"></a>Jednoduchý ovládací prvek
OPC, což umožňuje operátorům továrny reagovat na události a změnit jejich tovární nastavení z cloudu buď automaticky, nebo ručně. OPC s dvojitou platností poskytuje rozhraní REST API k vyvolání služeb na serveru OPC UA, procházení jeho adresního prostoru i pro čtení/zápis proměnných a provádění metod. Například kotl používá k řízení výrobní linky klíčový ukazatel výkonu. Senzor teploty publikuje změnu v datech pomocí vydavatele OPC. Operátor továrny obdrží výstrahu, že teplota dosáhla prahové hodnoty. Výrobní čára se chladí automaticky prostřednictvím OPC vlákna. Pracovník pro továrnu obdrží oznámení o vychladnutí.

## <a name="authentication"></a>Authentication
OPC je možné použít pro jednoduché ověřování a jednoduché prostředí pro vývojáře.

### <a name="simple-authentication"></a>Jednoduché ověřování 
OPC se Azure Active Directory pomocí ověřování a auditu založeného na AAD používá od konce do konce. Například OPC vlákna umožňuje, aby aplikace byla sestavena na základě OPC vláken, aby bylo možné určit, který operátor byl na počítači proveden. Na straně počítače se jedná prostřednictvím auditování OPC UA. Na straně cloudu je ukládání neměnného protokolu auditu klienta a ověřování AAD na REST API.

### <a name="simple-developer-experience"></a>Jednoduché prostředí pro vývojáře 
OPC je možné použít s aplikacemi napsanými v libovolném programovacím jazyce prostřednictvím rozhraní REST API. Protože vývojáři integrují klienta OPC UA do řešení, není nutné znát OPC sady SDK pro UA. OPC je možné bezproblémově integrovat do bezstavové architektury bez serveru. Například plný zásobník webového vývojáře, který vyvíjí aplikaci pro alarm a řídicí panel události, může napsat logiku, aby reagovala na události v JavaScriptu nebo TypeScript pomocí OPC vlákna bez znalosti jazyka C, C# nebo úplné implementace zásobníku OPC UA. 

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o neOPCi a o jejích použitích, je tady doporučený další krok:

[Co je OPC Vault](overview-opc-vault.md)
---
title: Změnit nastavení aplikace v Azure IoT Central | Microsoft Docs
description: Správa aplikace Azure IoT Central změnou názvu aplikace, adresy URL, nahrání obrázku a odstranění aplikace jako správce
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fde4f236a48e00b20817a812810fc3ad7d4b227f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521879"
---
# <a name="change-iot-central-application-settings"></a>Změnit nastavení IoT Central aplikace



Tento článek popisuje, jak jako správce můžete spravovat aplikaci tak, že změníte název aplikace a adresu URL, nahrajete image a odstraníte aplikaci v aplikaci Azure IoT Central.

Pokud chcete získat přístup k části **Správa** a používat ji, musíte být v roli **správce** aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central pro Azure, automaticky se přiřadíte k roli **správce** této aplikace.

## <a name="change-application-name-and-url"></a>Změnit název a adresu URL aplikace

Na stránce **nastavení aplikace** můžete změnit název a adresu URL vaší aplikace a pak vybrat **Uložit**.

![Stránka nastavení aplikace](media/howto-administer/image0-a.png)

Pokud správce vytvoří vlastní motiv pro vaši aplikaci, Tato stránka obsahuje možnost skrýt **název aplikace** v uživatelském rozhraní. Tato možnost je užitečná v případě, že logo aplikace ve vlastním motivu obsahuje název aplikace. Další informace najdete v tématu [přizpůsobení uživatelského rozhraní Azure IoT Central](./howto-customize-ui.md).

> [!Note]
> Pokud změníte adresu URL, může být vaše stará adresa URL pořízena jiným zákazníkem služby Azure IoT Central. Pokud k tomu dojde, nebude již k dispozici pro použití. Když změníte adresu URL, stará adresa URL už nebude fungovat a vy budete muset upozornit uživatele na použití nové adresy URL.

## <a name="delete-an-application"></a>Odstranění aplikace:

K trvalému odstranění IoT Central aplikace použijte tlačítko **Odstranit** . Tato akce trvale odstraní všechna data, která jsou přidružená k aplikaci.

> [!Note]
> Pokud chcete odstranit aplikaci, musíte mít také oprávnění k odstraňování prostředků v předplatném Azure, které jste si zvolili při vytváření aplikace. Další informace najdete v tématu [použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="manage-programmatically"></a>Programová správa

Balíčky sady SDK pro IoT Central Azure Resource Manager jsou k dispozici pro uzly, Python, C#, Ruby, Java a přejít. Tyto balíčky můžete použít k vytvoření, výpisu, aktualizaci nebo odstranění aplikací IoT Central. Balíčky obsahují pomocníky pro správu ověřování a zpracování chyb.

Můžete najít příklady použití sady Azure Resource Manager SDK na adrese [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) .

Další informace najdete v následujících úložištích a balíčcích GitHubu:

| Jazyk | Repository | Balíček |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat vaši aplikaci Azure IoT Central, je doporučeným dalším krokem informace o [správě uživatelů a rolí](howto-manage-users-roles.md) v Azure IoT Central.

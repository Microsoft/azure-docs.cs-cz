---
title: Změna nastavení aplikace Azure IoT Central | Dokumenty společnosti Microsoft
description: Jak jako správce spravovat aplikaci Azure IoT Central změnou názvu aplikace, adresy URL, nahrát image a odstranit aplikaci
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158673"
---
# <a name="change-iot-central-application-settings"></a>Změna nastavení aplikace IoT Central



Tento článek popisuje, jak můžete jako správce spravovat aplikaci změnou názvu aplikace a adresy URL, nahráním bitové kopie a odstraněním aplikace v aplikaci Azure IoT Central.

Chcete-li získat přístup a používat část **Správa,** musíte být v roli **správce** pro aplikaci Azure IoT Central. Pokud vytvoříte aplikaci Azure IoT Central, automaticky se přiřadíte k roli **správce** pro tuto aplikaci.

## <a name="change-application-name-and-url"></a>Změna názvu aplikace a adresy URL

Na stránce **Nastavení aplikace** můžete změnit název a adresu URL aplikace a pak vybrat **uložit**.

![Stránka nastavení aplikace](media/howto-administer/image0-a.png)

Pokud správce vytvoří vlastní motiv pro vaši aplikaci, tato stránka obsahuje možnost skrýt **název aplikace** v uživatelském rozhraní. Tato možnost je užitečná, pokud logo aplikace ve vlastním motivu obsahuje název aplikace. Další informace najdete [v tématu Přizpůsobení centrálního uhlavního uznatelného operačního systému Azure](./howto-customize-ui.md).

> [!Note]
> Pokud změníte adresu URL, může být vaše stará adresa URL převzata jiným zákazníkem Azure IoT Central. Pokud k tomu dojde, již není k dispozici pro použití. Když změníte adresu URL, stará adresa URL již nefunguje a je třeba upozornit uživatele na novou adresu URL, kterou chcete použít.

## <a name="delete-an-application"></a>Odstranění aplikace:

Pomocí tlačítka **Odstranit** trvale odstraňte aplikaci IoT Central. Tato akce trvale odstraní všechna data, která je přidružena k aplikaci.

> [!Note]
> Chcete-li odstranit aplikaci, musíte mít také oprávnění k odstranění prostředků v předplatném Azure, které jste zvolili při vytváření aplikace. Další informace najdete v tématu Správa přístupu ke službě access access access na [základě rolí ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-programmatically"></a>Programová správa

Balíčky sady IoT Central Azure Resource Manager SDK jsou dostupné pro node, python, c#, ruby, java a go. Tyto balíčky můžete použít k vytvoření, seznam, aktualizace nebo odstranění aplikací IoT Central. Balíčky obsahují pomocníky pro správu ověřování a zpracování chyb.

Příklady použití sad SDK správce prostředků Azure najdete na adrese [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Další informace najdete v následujících úložištích a balíčcích GitHubu:

| Jazyk | Repository | Balíček |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Přejít | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o tom, jak spravovat aplikaci Azure IoT Central, je dalším doporučeným krokem informace o [správě uživatelů a rolí](howto-manage-users-roles.md) v Azure IoT Central.

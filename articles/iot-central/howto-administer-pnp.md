---
title: Správa aplikace Azure IoT Central | Microsoft Docs
description: Správa aplikace Azure IoT Central změnou názvu aplikace, adresy URL, nahrání image, kopírování a odstranění aplikace jako správce
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: bdb13ef0d4f05802e4ab0f383c1bf3d82d9ba632
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880966"
---
# <a name="manage-your-iot-central-application"></a>Správa aplikace IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako správce můžete spravovat aplikaci tím, že změníte název aplikace a adresu URL, nahráváte obrázek, taky se naučíte, jak zkopírovat a odstranit aplikaci v aplikaci Azure IoT Central.

Pokud chcete získat přístup k části **Správa** a používat ji, musíte být v roli **správce** aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central pro Azure, automaticky se přiřadíte k roli **správce** této aplikace. 

## <a name="change-application-name-and-url"></a>Změnit název a adresu URL aplikace

Na stránce **nastavení aplikace** můžete změnit název a adresu URL vaší aplikace a pak vybrat **Uložit**.

![Stránka nastavení aplikace](media/howto-administer-pnp/image0-a.png)

Pokud správce vytvoří vlastní motiv pro vaši aplikaci, Tato stránka obsahuje možnost skrýt **název aplikace** v uživatelském rozhraní. To je užitečné v případě, že logo aplikace ve vlastním motivu obsahuje název aplikace. Další informace najdete v tématu [přizpůsobení uživatelského rozhraní Azure IoT Central](./howto-customize-ui-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

> [!Note]
> Pokud změníte adresu URL, může být vaše stará adresa URL pořízena jiným zákazníkem služby Azure IoT Central. Pokud k tomu dojde, nebude již k dispozici pro použití. Když změníte adresu URL, stará adresa URL už nebude fungovat a vy budete muset upozornit uživatele na použití nové adresy URL.

## <a name="prepare-and-upload-image"></a>Příprava a nahrávání image

Postup změny image aplikace najdete v tématu [Příprava a nahrání imagí do aplikace Azure IoT Central](howto-prepare-images.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="copy-an-application"></a>Zkopírování aplikace

Můžete vytvořit kopii libovolné aplikace, mínus instance zařízení, historii dat zařízení a uživatelská data. Kopie je aplikace s průběžnými platbami, za kterou se vám bude účtovat. Tímto způsobem nemůžete vytvořit zkušební aplikaci.

Vyberte **Kopírovat**. Do dialogového okna zadejte podrobnosti nové aplikace s průběžnými platbami. Pak vyberte **Kopírovat** a potvrďte, že chcete pokračovat. Další informace o polích v tomto formuláři najdete v části Vytvoření rychlého startu [aplikace](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) .

![Stránka nastavení aplikace](media/howto-administer-pnp/appcopy2.png)

Po úspěšném dokončení operace kopírování aplikace můžete přejít k nové aplikaci pomocí odkazu.

![Stránka nastavení aplikace](media/howto-administer-pnp/appcopy3a.png)

Zkopírováním aplikace se také zkopírují definice pravidel a e-mailové akce. Některé akce, jako je flow, Logic Apps atd., jsou vázané na konkrétní pravidla přes ID pravidla. Když se pravidlo zkopíruje do jiné aplikace, získá ID vlastního pravidla. V takovém případě budou muset uživatelé vytvořit novou akci a pak k ní přidružit nové pravidlo. Obecně je vhodné zkontrolovat pravidla a akce, abyste se ujistili, že jsou v nové aplikaci aktuální.

> [!WARNING]
> Pokud řídicí panel obsahuje dlaždice, které zobrazují informace o konkrétních zařízeních, pak tyto dlaždice ukázaly, že **požadovaný prostředek** nebyl v nové aplikaci nalezen. Aby bylo možné zobrazit informace o zařízeních v nové aplikaci, je nutné tyto dlaždice znovu nakonfigurovat.

## <a name="delete-an-application"></a>Odstranění aplikace

K trvalému odstranění IoT Central aplikace použijte tlačítko **Odstranit** . Tato akce trvale odstraní všechna data, která jsou přidružená k aplikaci.

> [!Note]
> Pokud chcete odstranit aplikaci, musíte mít také oprávnění k odstraňování prostředků v předplatném Azure, které jste si zvolili při vytváření aplikace. Další informace najdete v tématu [použití řízení přístupu na základě rolí ke správě přístupu k prostředkům předplatného Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Správa programově

Balíčky sady SDK pro IoT Central Azure Resource Manager jsou k dispozici pro C#uzly, Python,, Ruby, Java a přejít. Tyto balíčky můžete použít k vytvoření, výpisu, aktualizaci nebo odstranění aplikací IoT Central. Balíčky obsahují pomocníky pro správu ověřování a zpracování chyb.

Můžete najít příklady použití sady Azure Resource Manager SDK na adrese [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Další informace najdete v následujících úložištích a balíčcích GitHubu:

| Jazyk | Úložiště | Balíček |
| ---------| ---------- | ------- |
| Uzel | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Další postup
 
Teď, když jste se naučili, jak spravovat vaši aplikaci Azure IoT Central, je doporučeným dalším krokem informace o [správě uživatelů a rolí](howto-manage-users-roles-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) v Azure IoT Central.
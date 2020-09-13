---
title: Principy správy verzí šablon zařízení pro aplikace Azure IoT Central | Microsoft Docs
description: Iterujte na šablonách zařízení vytvořením nových verzí a bez dopadu na živá zařízení připojená k síti.
author: sarahhubbard
ms.author: sahubbar
ms.date: 04/24/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: device-developer
ms.openlocfilehash: 2025b127a428afa478cfe839c7619df2d7d688d3
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015955"
---
# <a name="create-a-new-device-template-version"></a>Vytvořit novou verzi šablony zařízení

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

Šablona zařízení obsahuje schéma, které popisuje, jak zařízení komunikuje s IoT Central. Mezi tyto interakce patří telemetrie, vlastnosti a příkazy. Zařízení i IoT Central aplikace spoléhají na sdílené porozumění tohoto schématu k výměně informací. V rámci schématu můžete provádět pouze omezené změny, aniž by došlo k narušení smlouvy. to je důvod, proč většina změn schématu vyžaduje novou verzi šablony zařízení. Správa verzí šablony zařízení umožňuje, aby starší zařízení pokračovala ve verzi schématu, kterou zná, zatímco novější nebo aktualizované zařízení používá novější verzi schématu.

Schéma v šabloně zařízení je definováno v modelu schopností zařízení (DCM) a v jeho rozhraních. Šablony zařízení obsahují další informace, jako jsou vlastnosti cloudu, úpravy zobrazení a zobrazení. Pokud provedete změny v těchto částech šablony zařízení, které nedefinují, jak zařízení vyměňuje data s IoT Central, nemusíte šablonu měnit.

Před použitím operátoru je nutné publikovat všechny změny šablony zařízení, bez ohledu na to, zda vyžadují aktualizaci verze. IoT Central zabrání publikování nejnovějších změn v šabloně zařízení bez první verze šablony.

> [!NOTE]
> Další informace o tom, jak vytvořit šablonu zařízení, najdete v tématu [nastavení a Správa šablony zařízení](howto-set-up-template.md) .

## <a name="versioning-rules"></a>Pravidla správy verzí

Tato část shrnuje pravidla správy verzí, která se vztahují na šablony zařízení. DCMs i rozhraní mají čísla verzí. Následující fragment kódu ukazuje DCM pro zařízení snímače životního prostředí. DCM má dvě rozhraní: **DeviceInformation** a **EnvironmentalSensor**. Na konci polí můžete zobrazit čísla verzí `@id` . Chcete-li zobrazit tyto informace v uživatelském rozhraní IoT Central, vyberte možnost **Zobrazit identitu** v editoru šablon zařízení.

```json
{
  "@id": "urn:contoso:sample_device:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "@id": "urn:contoso:sample_device:deviceinfo:1",
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": {
          "en": "Device Information"
        },
        "contents": [...
        ]
      }
    },
    {
      "@id": "urn:contoso:sample_device:sensor:1",
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:2",
        "@type": "Interface",
        "displayName": {
          "en": "Environmental Sensor"
        },
        "contents": [...
        ]
      }
    }
  ],
  "displayName": {
    "en": "Environment Sensor Capability Model"
  },
  "@context": [
    "http://azureiot.com/v1/contexts/IoTModel.json"
  ]
}
```

* Po publikování DCM nemůžete žádná rozhraní odebrat ani v nové verzi šablony zařízení.
* Po publikování DCM můžete rozhraní přidat, pokud vytvoříte novou verzi šablony zařízení.
* Po publikování DCM můžete rozhraní nahradit novější verzí, pokud vytvoříte novou verzi šablony zařízení. Pokud například šablona zařízení snímač v1 používá rozhraní EnvironmentalSensor V1, můžete vytvořit šablonu zařízení senzor v2, která používá rozhraní EnvironmentalSensor v2.
* Po publikování rozhraní nemůžete odebrat žádný obsah rozhraní, a to ani v nové verzi šablony zařízení.
* Po publikování rozhraní můžete přidat položky do obsahu rozhraní, pokud vytvoříte novou verzi rozhraní a šablony zařízení. Položky, které můžete přidat do rozhraní, zahrnují telemetrii, vlastnosti a příkazy.
* Po publikování rozhraní můžete provést změny bez schématu na existující položky v rozhraní, pokud vytvoříte novou verzi rozhraní a šablony zařízení. Části položky rozhraní, které nejsou součástí schématu, zahrnují zobrazované jméno a sémantický typ. Části schématu položky rozhraní, které nemůžete změnit, jsou název, typ schopnosti a schéma.

Následující části vás seznámí s některými příklady úprav šablon zařízení v IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Přizpůsobení šablony zařízení bez správy verzí

Některé prvky možností zařízení je možné upravovat, aniž byste museli mít k disverzi šablonu a rozhraní pro vaše zařízení. Mezi tato pole patří například zobrazované jméno, sémantický typ, minimální hodnota, maximální hodnota, desetinná místa, barva, jednotka, zobrazovaná jednotka, komentář a popis. Přidání jednoho z těchto přizpůsobení:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, kterou chcete přizpůsobit.
1. Klikněte na kartu **přizpůsobit** .
1. Tady jsou uvedené všechny funkce definované v modelu schopností zařízení. Všechna tato pole můžete upravovat, ukládat a používat bez nutnosti verze šablony zařízení. Pokud existují pole, která chcete upravit, jsou jen pro čtení, je nutné šablonu zařízení změnit, aby se změnila. Vyberte pole, které chcete upravit, a zadejte nové hodnoty.
1. Klikněte na **Uložit**. Tyto hodnoty teď přepisují vše, co bylo původně Uloženo v šabloně zařízení a používá se v rámci aplikace.

## <a name="version-a-device-template"></a>Verze šablony zařízení

Vytvořením nové verze šablony zařízení se vytvoří koncept verze šablony, ve které se dá model schopností zařízení upravit. Všechna publikovaná rozhraní zůstanou publikovaná, dokud nebudou individuálně nahlášená verze. Chcete-li upravit publikované rozhraní, vytvořte nejprve novou verzi šablony zařízení.

Pouze v případě, že se pokoušíte upravit součást modelu schopností zařízení, kterou nemůžete upravit v části přizpůsobení, stačí pouze vytvořit verzi šablony zařízení.

Postup při verzi šablony zařízení:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, u které se pokoušíte o verzi.
1. V horní části stránky klikněte na tlačítko **verze** a zadejte nový název šablony. IoT Central navrhuje nový název, který můžete upravit.
1. Klikněte na **Vytvořit**.
1. Šablona zařízení je teď v režimu konceptu. Vidíte, že rozhraní jsou stále uzamčena. Verze libovolného rozhraní, které chcete upravit.

## <a name="version-an-interface"></a>Verze rozhraní

Správa verzí rozhraní umožňuje přidat, aktualizovat a odebrat funkce v rozhraní, které jste už vytvořili.

Verze rozhraní:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, kterou máte v režimu konceptu.
1. Vyberte rozhraní, které je v publikovaném režimu, ve kterém chcete verzi upravit.
1. Klikněte na tlačítko **verze** v horní části stránky rozhraní.
1. Klikněte na **Vytvořit**.
1. Rozhraní je nyní v režimu konceptu. Můžete přidat nebo upravit funkce rozhraní bez přerušení stávajících úprav a zobrazení.

## <a name="migrate-a-device-across-versions"></a>Migrace zařízení napříč verzemi

Můžete vytvořit několik verzí šablony zařízení. V průběhu času budete mít pomocí těchto šablon zařízení více připojených zařízení. Zařízení můžete migrovat z jedné verze šablony zařízení do jiné. Následující postup popisuje, jak migrovat zařízení:

1. Přejít na stránku **Device Explorer** .
1. Vyberte zařízení, které potřebujete migrovat na jinou verzi.
1. Klikněte na tlačítko **migrovat**.
1. Vyberte šablonu zařízení s číslem verze, na které chcete zařízení migrovat, a zvolte **migrovat**.

![Postup migrace zařízení](media/howto-version-device-template/pick-version.png)

## <a name="next-steps"></a>Další kroky

Pokud jste operátor nebo Tvůrce řešení, navrhneme další krok, kde se dozvíte, [jak spravovat vaše zařízení](./howto-manage-devices.md).

Pokud jste vývojářem zařízení, je navržený další krok Přečtěte si o [Azure IoT Edge zařízeních a Azure IoT Central](./concepts-iot-edge.md).

---
title: Principy správy verzí šablon zařízení pro aplikace Azure IoT Central | Microsoft Docs
description: Iterujte na šablonách zařízení vytvořením nových verzí a bez dopadu na živá zařízení připojená k síti.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 93545c63013c95e3db498b079061da3d9b189efd
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986438"
---
# <a name="create-a-new-device-template-version"></a>Vytvořit novou verzi šablony zařízení

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

Šablona zařízení obsahuje schéma, které popisuje, jak zařízení komunikuje s IoT Central. Mezi tyto interakce patří telemetrie, vlastnosti a příkazy. Zařízení i IoT Central aplikace spoléhají na sdílené porozumění tohoto schématu k výměně informací. V rámci schématu můžete provádět pouze omezené změny, aniž by došlo k narušení smlouvy. to je důvod, proč většina změn schématu vyžaduje novou verzi šablony zařízení. Správa verzí šablony zařízení umožňuje, aby starší zařízení pokračovala ve verzi schématu, kterou zná, zatímco novější nebo aktualizované zařízení používá novější verzi schématu.

Schéma v šabloně zařízení je definováno v modelu zařízení a v jeho rozhraních. Šablony zařízení obsahují další informace, jako jsou vlastnosti cloudu, úpravy zobrazení a zobrazení. Pokud provedete změny v těchto částech šablony zařízení, které nedefinují, jak zařízení vyměňuje data s IoT Central, nemusíte šablonu měnit.

Předtím, než je možné použít operátor, je nutné šablonu aktualizovaného zařízení publikovat vždy. IoT Central zabrání publikování nejnovějších změn v šabloně zařízení bez první verze šablony.

> [!NOTE]
> Další informace o tom, jak vytvořit šablonu zařízení, najdete v tématu [nastavení a Správa šablony zařízení](howto-set-up-template.md) .

## <a name="versioning-rules"></a>Pravidla správy verzí

Tato část shrnuje pravidla správy verzí, která se vztahují na šablony zařízení. Oba modely zařízení i rozhraní mají čísla verzí. Následující fragment kódu ukazuje model zařízení pro termostatické zařízení. Model zařízení má jedno rozhraní. Číslo verze si můžete zobrazit na konci `@id` pole.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

Chcete-li zobrazit tyto informace v uživatelském rozhraní IoT Central, vyberte možnost **Zobrazit identitu** v editoru šablon zařízení:

:::image type="content" source="media/howto-version-device-template/view-identity.png" alt-text="Zobrazit identitu rozhraní pro zobrazení čísla verze":::

Následující seznam obsahuje pravidla, která určují, kdy je nutné vytvořit novou verzi:

* Po publikování modelu zařízení nemůžete žádná rozhraní odebrat, a to ani v nové verzi šablony zařízení.
* Po publikování modelu zařízení můžete přidat rozhraní, pokud vytvoříte novou verzi šablony zařízení.
* Po publikování modelu zařízení můžete v případě, že vytvoříte novou verzi šablony zařízení, nahradit rozhraní novější verzí. Pokud například šablona zařízení snímač v1 používá rozhraní termostat V1, můžete vytvořit šablonu zařízení senzor v2, která používá rozhraní termostat v2.
* Po publikování rozhraní nemůžete odebrat žádný obsah rozhraní, a to ani v nové verzi šablony zařízení.
* Po publikování rozhraní můžete přidat položky do obsahu rozhraní, pokud vytvoříte novou verzi rozhraní a šablony zařízení. Položky, které můžete přidat do rozhraní, zahrnují telemetrii, vlastnosti a příkazy.
* Po publikování rozhraní můžete provést změny bez schématu na existující položky v rozhraní, pokud vytvoříte novou verzi rozhraní a šablony zařízení. Části položky rozhraní, které nejsou součástí schématu, zahrnují zobrazované jméno a sémantický typ. Části schématu položky rozhraní, které nemůžete změnit, jsou název, typ schopnosti a schéma.

Následující části vás seznámí s některými příklady úprav šablon zařízení v IoT Central.

## <a name="customize-the-device-template-without-versioning"></a>Přizpůsobení šablony zařízení bez správy verzí

Některé prvky možností zařízení je možné upravovat, aniž byste museli mít k disverzi šablonu a rozhraní pro vaše zařízení. Mezi tato pole patří například zobrazované jméno, sémantický typ, minimální hodnota, maximální hodnota, desetinná místa, barva, jednotka, zobrazovaná jednotka, komentář a popis. Přidání jednoho z těchto přizpůsobení:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, kterou chcete přizpůsobit.
1. Klikněte na kartu **přizpůsobit** .
1. Tady jsou uvedené všechny funkce definované v modelu vašeho zařízení. Všechna tato pole můžete upravovat, ukládat a používat bez nutnosti verze šablony zařízení. Pokud existují pole, která chcete upravit, jsou jen pro čtení, je nutné šablonu zařízení změnit, aby se změnila. Vyberte pole, které chcete upravit, a zadejte nové hodnoty.
1. Vyberte **Uložit**. Tyto hodnoty teď přepisují vše, co bylo původně Uloženo v šabloně zařízení a používá se v rámci aplikace.

## <a name="version-a-device-template"></a>Verze šablony zařízení

Vytvořením nové verze šablony zařízení se vytvoří koncept verze šablony, kde můžete upravit model zařízení. Všechna publikovaná rozhraní zůstanou publikovaná, dokud nebudou individuálně nahlášená verze. Chcete-li upravit publikované rozhraní, vytvořte nejprve novou verzi šablony zařízení.

Pouze v případě, že se pokoušíte upravit součást modelu zařízení, kterou nelze upravovat v části vlastní nastavení, stačí pouze vytvořit verzi šablony zařízení.

Postup při verzi šablony zařízení:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, u které se pokoušíte o verzi.
1. V horní části stránky vyberte tlačítko **verze** a zadejte nový název šablony. IoT Central navrhuje nový název, který můžete upravit.
1. Vyberte **Vytvořit**.
1. Šablona zařízení je teď v režimu konceptu. Vidíte, že rozhraní jsou stále uzamčena. Verze libovolného rozhraní, které chcete upravit.

## <a name="version-an-interface"></a>Verze rozhraní

Správa verzí rozhraní umožňuje přidat, aktualizovat a odebrat funkce v rozhraní, které jste už vytvořili.

Verze rozhraní:

1. Přejít na stránku **šablony zařízení** .
1. Vyberte šablonu zařízení, kterou máte v režimu konceptu.
1. Vyberte rozhraní, které je v publikovaném režimu, ve kterém chcete verzi upravit.
1. V horní části stránky rozhraní vyberte tlačítko **verze** .
1. Vyberte **Vytvořit**.
1. Rozhraní je nyní v režimu konceptu. Můžete přidat nebo upravit funkce rozhraní bez přerušení stávajících úprav a zobrazení.

## <a name="migrate-a-device-across-versions"></a>Migrace zařízení napříč verzemi

Můžete vytvořit několik verzí šablony zařízení. V průběhu času budete mít pomocí těchto šablon zařízení více připojených zařízení. Zařízení můžete migrovat z jedné verze šablony zařízení do jiné. Následující postup popisuje, jak migrovat zařízení:

1. Přejděte na stránku **Zařízení**.
1. Vyberte zařízení, které potřebujete migrovat na jinou verzi.
1. Vyberte **migrovat**: :::image type="content" source="media/howto-version-device-template/migrate-device.png" alt-text="Vyberte možnost spuštění migrace zařízení"::: .
1. Vyberte šablonu zařízení s číslem verze, na které chcete migrovat zařízení, a vyberte **migrovat**.

## <a name="next-steps"></a>Další kroky

Pokud jste operátor nebo Tvůrce řešení, navrhneme další krok, kde se dozvíte, [jak spravovat vaše zařízení](./howto-manage-devices.md).

Pokud jste vývojářem zařízení, je navržený další krok Přečtěte si o [Azure IoT Edge zařízeních a Azure IoT Central](./concepts-iot-edge.md).

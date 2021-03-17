---
title: Vysvětlení aktualizace zařízení pro Azure IoT Hub APT manifest | Microsoft Docs
description: Pochopte, jak aktualizace zařízení pro IoT Hub používá apt manifest pro aktualizaci založenou na balíčku.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b68b78499aa3bf0d84d8bd0fa5ab55d1f969113
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679281"
---
# <a name="device-update-apt-manifest"></a>APT manifest aktualizace zařízení

Manifest APT je soubor JSON, který popisuje podrobnosti aktualizace vyžadované obslužnou rutinou aktualizace APT. Tento soubor se dá importovat do aktualizace zařízení pro IoT Hub stejně jako jakákoli jiná aktualizace.

[Přečtěte si další informace](import-update.md) o importu aktualizací do aktualizace zařízení.

## <a name="overview"></a>Přehled

Když se do agenta aktualizace zařízení doručí manifest APT jako aktualizace, agent zpracuje manifest a provede potřebné operace. Tyto operace zahrnují stažení a instalaci balíčků zadaných v souboru manifestu APT a jejich závislosti.

Aktualizace zařízení podporuje APT typ aktualizace a obslužnou rutinu aktualizace APT. Tato podpora umožňuje agentovi aktualizace zařízení vyhodnotit nainstalované balíčky Debian a aktualizovat potřebné balíčky. 

## <a name="schema"></a>Schéma

Soubor manifestu APT je soubor JSON se schématem se správou verzí.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Příklad:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Název tohoto manifestu APT To může být jakýkoli název nebo ID, které jsou pro vaše scénáře smysluplné. Například, `contoso-iot-edge`.

### <a name="version"></a>verze

Číslo verze tohoto manifestu APT. Například, `1.0.0.0`.


### <a name="packages"></a>zásilk

Seznam objektů, které obsahují vlastnosti specifické pro daný balíček.

#### <a name="name"></a>name

Název nebo ID balíčku. Například, `iotedge`.

#### <a name="version"></a>verze

Požadovaná kritéria verze balíčku. Například, `1.0.8-2`.

V současné době je podporováno pouze přesné číslo verze. Číslo verze je požadovaná verze balíčku debian ve formátu [epocha:] upstream_version [-debian_revision].

**epocha** je celé číslo bez znaménka.

**upstream_version** může obsahovat alfanumerické znaky a znaky, například ".", "+", "-" a "~". Měl by začínat číslicí.
> [!NOTE]
> ' 1.0.8 ' se rovná ' 1.0.8-0 '

Například **`"name":"iotedge" and "version":"1.0.8-2"`** je ekvivalentem instalace balíčku pomocí příkazu `apt-get install iotedge=1.0.8-2`

> [!NOTE]
> Hodnota verze neobsahuje rovnítko

Pokud je verze vynechána, nainstaluje se nejnovější dostupná verze zadaného balíčku.

[Přečtěte si další informace](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) o tom, jak jsou balíčky Debian ve verzi.

> [!NOTE]
> Správce balíčků APT ignoruje požadavky na správu verzí dané balíčkem při automatickém překladu závislých balíčků k instalaci. Pokud nejsou k dispozici explicitní verze závislých balíčků, budou používat nejnovější, i když samotný balíček může určit striktní požadavek (=) v dané verzi. Toto automatické řešení může vést k chybám souvisejícím s nesplnění závislostí. [Další informace](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Pokud aktualizujete určitou verzi procesu démona zabezpečení Azure IoT Edge, měli byste `iotedge` do MANIFESTU apt zahrnout požadovanou verzi balíčku a jeho závislý `libiothsm-std` balíček.
[Další informace](https://docs.microsoft.com/azure/iot-edge/how-to-update-iot-edge#update-the-security-daemon)

> [!NOTE]
> Manifest apt se dá použít k aktualizaci agenta aktualizace zařízení a jeho závislostí. Seznamte se s názvem agenta aktualizace zařízení a požadovanou verzí v manifestu apt, stejně jako u jakéhokoli jiného balíčku. Tento manifest apt se pak dá naimportovat a nasadit prostřednictvím aktualizace zařízení pro IoT Hub kanál. 

## <a name="removing-packages"></a>Odebírají se balíčky

Pomocí manifestu apt můžete také odebrat nainstalované balíčky ze zařízení. Jeden manifest apt lze použít k odebrání, přidání a aktualizaci více balíčků. Chcete-li odebrat balíček, přidejte po názvu balíčku znaménko minus "–". Neměli byste zahrnovat číslo verze balíčků, které odebíráte. Odebrání balíčků pomocí manifestu apt neodebere jeho závislosti a konfigurace.

Příklad:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Tento manifest apt odebere balíček "foo" ze zařízení, do kterých je nasazen. 

## <a name="recommended-value-for-installed-criteria"></a>Doporučená hodnota pro nainstalovaná kritéria

Nainstalovaná kritéria pro manifest APT jsou `<name>-<version>` , kde `<name>` je název manifestu apt a `<version>` je verze manifestu apt. Například, `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Pokyny k vytvoření manifestu APT

Při vytváření manifestu APT je potřeba mít na paměti několik pokynů:

- Vždy zajistěte, aby manifest APT byl soubor JSON ve správném formátu.
- Každý manifest APT by měl mít jedinečnou verzi. Vyzkoušejte si standardizovanou metodologii, která zvyšuje verzi manifestu APT, takže má smysl pro vaše scénáře a může být snadno následováno.
- Když přichází do požadovaného stavu každého jednotlivého balíčku, zadejte přesný název a verzi balíčku, který chcete nainstalovat do svého zařízení. Vždy ověřte hodnoty pro úložiště balíčků, které máte v úmyslu použít jako zdroj pro balíček.
- Zajistěte, aby se balíčky v manifestu APT zobrazovaly v pořadí, ve kterém by se měly nainstalovat/odebrat.
- Vždy Ověřte instalaci balíčků na testovacím zařízení, aby se zajistilo, že se výsledek požaduje.
- Při instalaci konkrétní verze balíčku (například `iotedge 1.0.9-1` ) je osvědčeným postupem také v MANIFESTU apt explicitní verze závislých balíčků, které mají být nainstalovány (například `libiothsm 1.0.9-1` ).
- I když není zajištěna jeho platnost, vždy zajistěte, aby váš manifest APT byl kumulativní, aby nedocházelo k neznámému stavu zařízení. Kumulativní aktualizace zajistí, že vaše zařízení budou mít požadovanou verzi každého balíčku, a to i v případě, že zařízení přeskočilo nasazení aktualizace APT z důvodu selhání instalace nebo převedení do offline režimu.

Například:

**Základní manifest APT**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**CHYBNÁ AKTUALIZACE**

Tato aktualizace obsahuje pruhový balíček, ale ne balíček foo.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**DOBRÁ AKTUALIZACE**

Tato aktualizace zahrnuje balíček foo a také obsahuje pruhový balíček.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Importovat novou aktualizaci](import-update.md)


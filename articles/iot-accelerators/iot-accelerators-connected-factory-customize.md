---
title: Přizpůsobení připojen objekt pro vytváření řešení – Azure | Microsoft Docs
description: Popis toho, jak přizpůsobit chování akcelerátoru řešení připojen objekt pro vytváření.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: e91f36c9d6f0cb3195e6903d55cd676f1e9ccf5a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626546"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Přizpůsobit způsob, jakým připojen objekt pro vytváření řešení zobrazí data z vašich serverů OPC UA

Řešení připojen objekt pro vytváření agreguje a zobrazí data ze serverů OPC UA připojení k řešení. Můžete procházet a odesílat příkazy na OPC UA servery ve vašem řešení. Další informace o OPC UA najdete v tématu [Propojená továrna – Nejčastější dotazy](iot-accelerators-faq-cf.md).

Agregovaná data v řešení příkladem efektivitu celkové zařízení (OEE) a klíčových ukazatelů výkonu (KPI), můžete zobrazit v řídicím panelu v objektu pro vytváření, řádku a úrovně stanice. Následující snímek obrazovky ukazuje hodnoty OEE a klíčového ukazatele výkonu **sestavení** na stanici **produkční řádku 1**v **Mnichov** objekt pro vytváření:

![Příklad hodnoty OEE a klíčových ukazatelů výkonu v řešení][img-oee-kpi]

Toto řešení umožňuje zobrazit podrobné informace z konkrétní datové položky z OPC UA servery, nazývané *stanice*. Následující snímek obrazovky ukazuje pozemků počet vyrobenou položek z konkrétní stanice:

![Pozemků počet vyrobenou položek][img-manufactured-items]

Pokud kliknete na některý z grafů, můžete si prostudovat data další pomocí časové řady přehledy (TSI):

![Prozkoumejte data pomocí statistiky časové řady][img-tsi]

Tento článek popisuje:

- Data jak je k dispozici různé zobrazení v řešení.
- Jak můžete přizpůsobit způsob řešení zobrazí data.

## <a name="data-sources"></a>Zdroje dat

Připojen objekt pro vytváření řešení zobrazí data ze serverů OPC UA připojení k řešení. Výchozí instalace zahrnuje několik OPC UA servery se systémem simulace objekt pro vytváření. Můžete přidat vlastní servery OPC UA, [připojení přes bránu] [ lnk-connect-cf] do řešení.

Můžete procházet datových položek, které připojený server OPC UA může odesílat do řešení v řídicím panelu:

1. Zvolte **prohlížeče** přejděte na **vyberte server OPC UA** zobrazení:

    ![Přejděte do vyberte zobrazení serverů OPC UA][img-select-server]

1. Vyberte server a klikněte na tlačítko **Connect**. Klikněte na tlačítko **pokračovat** Jakmile se zobrazí upozornění zabezpečení.

    > [!NOTE]
    > Toto upozornění pouze vyskytuje jednou pro každý server a vytvoří vztah důvěryhodnosti mezi řídicí panel řešení a serverem.

1. Nyní je možné procházet datových položek, které může server odeslat do řešení. Položky, které jsou odesílány do řešení mají zaškrtnuto:

    ![Publikované položky][img-published]

1. Pokud jste *správce* v řešení, můžete publikovat položku dat, aby byl k dispozici v rámci připojení objektu pro vytváření řešení. Jako správce můžete také změnit hodnotu datové položky a volání metody v serveru OPC UA.

## <a name="map-the-data"></a>Mapování dat.

Řešení připojen objekt pro vytváření mapy a agreguje položky publikovaných dat ze serveru OPC UA u různých zobrazení v řešení. Připojen objekt pro vytváření řešení se nasadí do účtu Azure při zřizování řešení. Soubor JSON v řešení Visual Studio připojené Factory ukládá tyto informace o mapování. Můžete zobrazit a upravit tento konfigurační soubor JSON v řešení nástroje Visual Studio připojené objekt pro vytváření. Můžete znovu nasadit řešení po provedení změny.

Můžete použít konfiguračního souboru na:

- Upravte existující simulované objekty Factory, řádky výroby a stanice.
- Mapování dat ze skutečné OPC UA serverů, které se připojují k řešení.

Další informace o mapování a agregace dat podle specifických požadavků najdete v tématu [postup konfigurace akcelerátoru řešení připojen objekt pro vytváření ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Nasazení změny

Když dokončíte provádění změn **ContosoTopologyDescription.json** souboru, je nutné znovu nasadit řešení vytváření připojení k účtu Azure.

**Azure-iot připojené factory** zahrnuje úložiště **build.ps1** skript prostředí PowerShell můžete znovu vytvořit a nasadit řešení.

## <a name="next-steps"></a>Další kroky

Další informace o řešení akcelerátor připojen objekt pro vytváření přečíst v následujících článcích:

* [Seznámení s akcelerátorem řešení propojené továrny][lnk-rm-walkthrough]
* [Nasaďte bránu pro připojení objektu pro vytváření][lnk-connect-cf]
* [Oprávnění na webu azureiotsuite.com][lnk-permissions]
* [Propojená továrna – nejčastější dotazy](iot-accelerators-faq-cf.md)
* [NEJČASTĚJŠÍ DOTAZY][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md
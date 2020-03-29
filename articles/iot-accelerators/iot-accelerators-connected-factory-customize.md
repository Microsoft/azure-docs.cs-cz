---
title: Přizpůsobení řešení Připojené továrny – Azure | Dokumenty společnosti Microsoft
description: Popis, jak přizpůsobit chování akcelerátoru řešení připojené továrny.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 6062f8b3992732e0e0f9bbdae9549e69c393f4ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67080488"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Přizpůsobení způsobu, jakým řešení Connected Factory zobrazuje data z vašich serverů OPC UA

Řešení Connected Factory agreguje a zobrazuje data ze serverů OPC UA připojených k řešení. Příkazy můžete procházet a odesílat na servery OPC UA ve vašem řešení. Další informace o OPC UA naleznete v [nejčastějších dotazech k připojení k továrním dotazu](iot-accelerators-faq-cf.md).

Příklady agregovaných dat v řešení zahrnují celkovou efektivitu zařízení (OEE) a klíčové ukazatele výkonu (KU), které můžete zobrazit na řídicím panelu na úrovni výroby, linky a stanice. Následující snímek obrazovky ukazuje hodnoty OEE a KPI pro **montážní** stanici na **výrobní lince 1**v **mnichovské** továrně:

![Příklad hodnot OEE a klíčových ukazatelů výkonu v řešení][img-oee-kpi]

Toto řešení umožňuje zobrazit podrobné informace z konkrétních datových položek ze serverů OPC UA, *nazývaných stanice*. Následující snímek obrazovky ukazuje obrázky počtu vyrobených položek z určité stanice:

![Obrázky počtu vyrobených položek][img-manufactured-items]

Pokud klepnete na jeden z grafů, můžete data dále prozkoumat pomocí přehledů časové řady (TSI):

![Prozkoumejte data pomocí přehledů časových řad][img-tsi]

Tento článek popisuje:

- Jak jsou data k dispozici pro různá zobrazení v řešení.
- Jak můžete přizpůsobit způsob, jakým řešení zobrazuje data.

## <a name="data-sources"></a>Zdroje dat

Řešení Connected Factory zobrazuje data ze serverů OPC UA připojených k řešení. Výchozí instalace zahrnuje několik serverů OPC UA, které běží tovární simulace. K vašemu řešení můžete přidat vlastní servery OPC UA, které [propojují prostřednictvím brány][lnk-connect-cf].

Můžete procházet datové položky, které může připojený server OPC UA odeslat vašemu řešení na řídicím panelu:

1. Zvolte **Prohlížeč,** chcete-li přejít do zobrazení **Vybrat server OPC UA:**

    ![Přechod do zobrazení Vybrat server OPC UA][img-select-server]

1. Vyberte server a klepněte na tlačítko **Připojit**. Po vydání upozornění zabezpečení klepněte na tlačítko **Pokračovat.**

    > [!NOTE]
    > Toto upozornění se zobrazí pouze jednou pro každý server a vytváří vztah důvěryhodnosti mezi řídicím panelem řešení a serverem.

1. Nyní můžete procházet datové položky, které může server odeslat do řešení. Položky odesílané do řešení mají zaškrtnutí:

    ![Publikované položky][img-published]

1. Pokud jste *správce* v řešení, můžete publikovat datovou položku, aby byla k dispozici v řešení Připojená továrna. Jako správce můžete také změnit hodnotu datových položek a metod volání na serveru OPC UA.

## <a name="map-the-data"></a>Mapování dat

Řešení Connected Factory mapuje a agreguje publikované datové položky ze serveru OPC UA do různých zobrazení v řešení. Řešení Connected Factory se nasazuje do vašeho účtu Azure při zřizování řešení. Soubor JSON v řešení Visual Studio Connected Factory ukládá tyto informace o mapování. Tento konfigurační soubor JSON můžete zobrazit a upravit v řešení Connected Factory Visual Studio. Řešení můžete znovu nasadit po změně.

Konfigurační soubor můžete použít k použití:

- Upravte existující simulované továrny, výrobní linky a stanice.
- Mapujte data ze skutečných serverů OPC UA, které připojíte k řešení.

Další informace o mapování a agregaci dat tak, aby splňovaly vaše specifické požadavky, naleznete v [tématu Konfigurace akcelerátoru řešení připojené továrny ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Nasazení změn

Po dokončení provádění změn v souboru **ContosoTopologyDescription.json** je nutné znovu nasadit řešení připojené továrny do svého účtu Azure.

Úložiště **azure-iot-connected-factory** obsahuje skript **prostředí PowerShell build.ps1,** který můžete použít k opětovnému sestavení a nasazení řešení.

## <a name="next-steps"></a>Další kroky

Další informace o akcelerátoru řešení Připojené továrny najdete v následujících článcích:

* [Oprávnění na webu azureiotsolutions.com][lnk-permissions]
* [Nejčastější dotazy k připojení k výrobě](iot-accelerators-faq-cf.md)
* [Nejčastější dotazy][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md
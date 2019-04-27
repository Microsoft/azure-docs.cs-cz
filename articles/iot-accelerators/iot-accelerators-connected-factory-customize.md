---
title: Přizpůsobení řešení připojené továrny – Azure | Dokumentace Microsoftu
description: Popis postupu přizpůsobení chování akcelerátor řešení připojená továrna.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 7d4e95d066e191e1d5b6d083ede65843dbe73f31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450395"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Přizpůsobit způsob, jakým zobrazuje řešení připojená továrna dat ze serverů OPC UA

Řešení připojená továrna agreguje a zobrazí data ze serverů OPC UA připojených k řešení. Můžete procházet a odesílání příkazů do serverů OPC UA v rámci vašeho řešení. Další informace o OPC UA najdete v tématu [Propojená továrna – Nejčastější dotazy](iot-accelerators-faq-cf.md).

Příklady agregovaných dat v řešení obsahovat celkové efektivity zařízení (OEE) a klíčových ukazatelů výkonu (KPI), který se zobrazí na řídicím panelu na objekt pro vytváření, řádku a úrovně stanice. Následující snímek obrazovky ukazuje hodnoty celkové efektivity zařízení a klíčové ukazatele výkonu **sestavení** stanice na **výrobní linky 1**v **Mnichově** objekt pro vytváření:

![Příklad hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu v řešení][img-oee-kpi]

Toto řešení umožňuje zobrazit podrobné informace z konkrétní datové položky ze serverů OPC UA, volá *stanice*. Následující snímek obrazovky ukazuje vykreslení počet vyrobených položek z konkrétní stanice:

![Vykreslení počet vyrobených položek][img-manufactured-items]

Pokud kliknete na některý z grafy, můžete prozkoumat data s využitím další Time Series Insights (TSI):

![Zkoumání dat pomocí služby Time Series Insights][img-tsi]

Tento článek popisuje:

- Data jak je k dispozici na různá zobrazení v řešení.
- Jak můžete přizpůsobit tak, jak řešení zobrazí data.

## <a name="data-sources"></a>Zdroje dat

Zobrazuje řešení připojená továrna data ze serverů OPC UA připojených k řešení. Výchozí instalace zahrnuje několik serverů OPC UA spuštění simulace objekt pro vytváření. Můžete přidat vlastní servery OPC UA, který [připojení prostřednictvím brány] [ lnk-connect-cf] do vašeho řešení.

Můžete procházet datové položky, které můžete odeslat připojený server OPC UA do vašeho řešení na řídicím panelu:

1. Zvolte **prohlížeče** přejděte **vyberte server OPC UA** zobrazení:

    ![Přejděte na Select zobrazení serverů OPC UA][img-select-server]

1. Vyberte server a klikněte na tlačítko **připojit**. Klikněte na tlačítko **pokračovat** Jakmile se zobrazí upozornění zabezpečení.

    > [!NOTE]
    > Toto upozornění pouze se vyskytuje jednou pro každý server a vytváří vztah důvěryhodnosti mezi řídicím panelu řešení a serverem.

1. Nyní můžete procházet datové položky, které může server odeslat do řešení. Položky, které jsou odesílány do řešení se zobrazí zaškrtnutí:

    ![Publikované položky][img-published]

1. Pokud jste *správce* v řešení, můžete publikovat datové položky, které ho zpřístupní v řešení připojené továrny. Jako správce můžete také změnit hodnotu položky dat a volání metod na serveru OPC UA.

## <a name="map-the-data"></a>Mapy dat

Řešení připojená továrna mapuje a agreguje položky publikovaných dat ze serveru OPC UA na různá zobrazení v řešení. Řešení připojená továrna nasadí ke svému účtu Azure, máte při zřizování řešení. Soubor JSON do řešení aplikace Visual Studio připojená továrna ukládá tyto informace o mapování. Můžete zobrazit a upravit tento konfigurační soubor JSON v řešení sady Visual Studio připojené továrny. Můžete znovu nasadit řešení po provedení změny.

Můžete použít konfigurační soubor na:

- Upravte existující simulované objekty pro vytváření, výrobní linky a stanice.
- Mapování dat ze skutečných serverů OPC UA, které se připojují k řešení.

Další informace o mapování a agregace dat podle vašich specifických požadavků najdete v tématu [konfigurace akcelerátor řešení připojená továrna ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Nasazení změny

Po dokončení provádění změn **ContosoTopologyDescription.json** souboru, je nutné znovu nasadit řešení připojená továrna ke svému účtu Azure.

**Azure-iot-connected-factory** zahrnuje úložiště **build.ps1** skript prostředí PowerShell můžete použít k sestavení a nasazení řešení.

## <a name="next-steps"></a>Další kroky

Další informace o akcelerátor řešení připojená továrna v následujících článcích:

* [Seznámení s akcelerátorem řešení propojené továrny][lnk-rm-walkthrough]
* [Nasazení brány pro propojenou továrnu][lnk-connect-cf]
* [Oprávnění na webu azureiotsolutions.com][lnk-permissions]
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
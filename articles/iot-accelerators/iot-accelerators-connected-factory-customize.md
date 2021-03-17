---
title: Přizpůsobení řešení propojené továrny – Azure | Microsoft Docs
description: Popis postupu přizpůsobení chování akcelerátoru řešení propojené továrny.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: a092859517ee6ac41e62e55fe95f72b8bfdf87d5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011196"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>Přizpůsobení způsobu, jakým řešení propojené továrny zobrazuje data ze serverů OPC UA

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Řešení propojené továrny agreguje a zobrazuje data ze serverů OPC UA připojených k řešení. Můžete procházet a odesílat příkazy na servery OPC UA ve vašem řešení. Další informace o OPC UA najdete v tématu [propojená továrna – Nejčastější dotazy](iot-accelerators-faq-cf.md).

Mezi agregovaná data v řešení patří například Celková efektivita vybavení (celkové efektivity zařízení) a klíčové ukazatele výkonu, které můžete zobrazit na řídicím panelu na úrovni továrny, řádku a stanice. Následující snímek obrazovky ukazuje hodnoty celkové efektivity zařízení a KPI pro **montážní** stanici na **výrobní lince 1** v továrně **Mnichov** :

![Příklad hodnot celkové efektivity zařízení a klíčového ukazatele výkonu v řešení][img-oee-kpi]

Řešení umožňuje zobrazit podrobné informace o konkrétních datových položkách ze serverů OPC UA, nazývaných *stanice*. Na následujícím snímku obrazovky vidíte počet zpracovaných položek z konkrétní stanice:

![Počet zpracovaných položek][img-manufactured-items]

Pokud kliknete na jeden z grafů, můžete prozkoumat data dále pomocí Time Series Insights (TSI):

![Zkoumání dat pomocí Time Series Insights][img-tsi]

Tento článek popisuje:

- Způsob zpřístupnění dat různým zobrazením v řešení.
- Jak lze přizpůsobit způsob, jakým řešení zobrazuje data.

## <a name="data-sources"></a>Zdroje dat

Řešení propojené továrny zobrazuje data ze serverů OPC UA připojených k řešení. Výchozí instalace zahrnuje několik serverů OPC UA, které spouštějí simulaci továrny. Do vašeho řešení můžete přidat vlastní servery OPC UA, které [připojit prostřednictvím brány] [LNK-Connect-CF].

Můžete procházet datové položky, které může připojený server OPC UA odeslat do vašeho řešení na řídicím panelu:

1. Zvolte **prohlížeč** a přejděte do zobrazení **Vybrat server OPC UA** :

    ![Přejděte do zobrazení vybrat server OPC UA.][img-select-server]

1. Vyberte server a klikněte na **připojit**. Po zobrazení upozornění zabezpečení klikněte na **pokračovat** .

    > [!NOTE]
    > Toto upozornění se zobrazí pouze jednou pro každý server a vytvoří vztah důvěryhodnosti mezi řídicím panelem řešení a serverem.

1. Nyní můžete procházet datové položky, které může server odeslat do řešení. Položky, které jsou odesílány do řešení, mají značku zaškrtnutí:

    ![Publikované položky][img-published]

1. Pokud jste *správce* v řešení, můžete publikovat datovou položku, která ji zpřístupní v řešení propojené továrny. Jako správce můžete také změnit hodnotu položky dat a volat metody na serveru OPC UA.

## <a name="map-the-data"></a>Mapování dat

Řešení propojené továrny mapuje a agreguje položky publikovaných dat ze serveru OPC UA do různých zobrazení v řešení. Řešení propojené továrny se po zřízení řešení nasadí do svého účtu Azure. Tyto informace o mapování ukládá soubor JSON v řešení propojené továrny sady Visual Studio. Tento konfigurační soubor JSON můžete zobrazit a upravit v řešení propojené továrny sady Visual Studio. Řešení můžete po provedení změny znovu nasadit.

Konfigurační soubor můžete použít k těmto akcím:

- Upravte existující simulované továrny, výrobní linky a stanice.
- Namapujte data ze skutečných serverů OPC UA, ke kterým se připojujete.

Další informace o mapování a agregaci dat pro splnění konkrétních požadavků najdete v tématu [Konfigurace akcelerátoru řešení propojené továrny ](iot-accelerators-connected-factory-configure.md).

## <a name="deploy-the-changes"></a>Nasazení změn

Po dokončení změn **ContosoTopologyDescription.jsv** souboru musíte řešení propojené továrny znovu nasadit do svého účtu Azure.

Úložiště **Azure-IoT-Connected-Factory** zahrnuje **build.ps1** skript PowerShellu, který můžete použít k opětovnému sestavení a nasazení řešení.

## <a name="next-steps"></a>Další kroky

Další informace o akcelerátoru řešení propojené továrny najdete v následujících článcích:

* [Oprávnění na webu azureiotsolutions.com][lnk-permissions]
* [Nejčastější dotazy k propojené továrně](iot-accelerators-faq-cf.md)
* [Nejčastější dotazy][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md
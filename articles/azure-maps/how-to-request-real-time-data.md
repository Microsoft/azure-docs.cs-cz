---
title: Vyžádat data veřejného přenosu v reálném čase | Mapy Microsoft Azure
description: Naučte se, jak vyžádat data veřejného přenosu v reálném čase, jako jsou například doručení při přenosu. Podívejte se, jak používat službu Azure Maps mobility pro tento účel.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6f0cf663b42c8487495602e4cdbf1a88427f9daf
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310930"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Vyžádání dat veřejného přenosu v reálném čase pomocí služby Azure Maps mobility

V tomto článku se dozvíte, jak používat [službu Azure Maps mobility](https://aka.ms/AzureMapsMobilityService) k vyžádání dat veřejného přenosu v reálném čase.

V tomto článku se dozvíte, jak požádat o další doručení v reálném čase pro všechny řádky přicházející v daném zastavení.

## <a name="prerequisites"></a>Požadavky

Nejprve musíte mít účet Azure Maps a klíč předplatného, aby bylo možné volat rozhraní API pro veřejné průjezdy Azure Maps. Informace najdete v pokynech v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-azure-maps-account) pro vytvoření účtu Azure Maps. Použijte k získání primárního klíče pro váš účet postup uvedený v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) . Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.

## <a name="request-real-time-arrivals-for-a-stop"></a>Vyžádat doručení v reálném čase o zastavení

Aby bylo možné vyžádat data o doručení konkrétního zastavení veřejného přenosu v reálném čase, budete muset požádat o [rozhraní API doručení](https://aka.ms/AzureMapsMobilityRealTimeArrivals) Azure Maps [služby mobility](https://aka.ms/AzureMapsMobilityService)v reálném čase. K dokončení žádosti budete potřebovat **metroID** a **stopID** . Další informace o tom, jak si vyžádat tyto parametry, najdete v naší příručce o tom, jak [vyžádat trasy veřejného přenosu](https://aka.ms/AMapsHowToGuidePublicTransitRouting).

Pojďme použít "522" jako naše ID Metro, což je ID Metro pro oblast "Praha – Tacoma – Bellevue, WA". Jako ID zastavení použijte "522---2060603", tato Sběrnicová zastávka je "ne 24 července St & 162nd Ave No Bellevue WA". Pokud chcete požádat o dalších pět dat o doručení v reálném čase, pro všechna další živá přijetí v tomto zastavení proveďte následující kroky:

1. Otevřete aplikaci pro odesílání a pojďme vytvořit kolekci pro uložení požadavků. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění, kam se má žádost Uložit. Pak vyberte **Uložit**.

    ![Vytvoření žádosti v post](./media/how-to-request-transit-data/postman-new.png)

3. Na kartě tvůrce vyberte metodu **Get** http a zadejte následující adresu URL pro vytvoření žádosti o získání. Nahraďte `{subscription-key}` Azure Mapsým primárním klíčem.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po úspěšné žádosti obdržíte následující odpověď.  Všimněte si, že parametr ' scheduleType ' definuje, zda je předpokládaná doba doručení založena na statických datech v reálném čase.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak požadovat přenosová data pomocí služby mobility:

> [!div class="nextstepaction"]
> [Požadavky na přenosová data](how-to-request-transit-data.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps mobility:

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)

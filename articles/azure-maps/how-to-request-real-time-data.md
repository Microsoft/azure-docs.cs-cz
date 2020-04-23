---
title: Požadovat údaje o veřejné dopravě v reálném čase | Mapy Microsoft Azure
description: Vyžádejte si data veřejné dopravy v reálném čase pomocí služby Mobility Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086073"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Vyžádejte si data veřejné dopravy v reálném čase pomocí služby Mobility Azure Maps

Tento článek ukazuje, jak používat [službu Mobility](https://aka.ms/AzureMapsMobilityService) Azure Maps k vyžádání dat veřejné dopravy v reálném čase.

V tomto článku se dozvíte, jak požádat o další příjezdy v reálném čase pro všechny linky, které přicházejí na danou zastávku

## <a name="prerequisites"></a>Požadované součásti

Abyste mohli volat na rozhraní API pro veřejnou dopravu v Azure Maps, musíte mít nejdřív účet Azure Maps a klíč předplatného. Další informace najdete v následujících pokynech [k vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) Azure Maps. Postupujte podle kroků v [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) a získejte primární klíč pro váš účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](./how-to-manage-authentication.md).

Tento článek používá [aplikaci Postman](https://www.getpostman.com/apps) k vytváření volání REST. Můžete použít libovolné vývojové prostředí rozhraní API, které upřednostňujete.

## <a name="request-real-time-arrivals-for-a-stop"></a>Žádost o příjezdy v reálném čase pro zastávku

Chcete-li požádat o data o příchozích v reálném čase na konkrétní zastávce veřejné dopravy, budete muset požádat [rozhraní API pro příchozí](https://aka.ms/AzureMapsMobilityRealTimeArrivals) v reálném čase [služby](https://aka.ms/AzureMapsMobilityService)Azure Maps Mobility Service . K dokončení žádosti budete potřebovat **metroID** a **stopID.** Další informace o tom, jak tyto parametry vyžádat, najdete v našem průvodci, jak [požádat o trasy veřejné dopravy](https://aka.ms/AMapsHowToGuidePublicTransitRouting).

Pojďme použít "522" jako naše metro ID, což je metro ID pro "Seattle-Tacoma-Bellevue, WA" oblasti. Použijte "522---2060603" jako ID zastávky, tato autobusová zastávka je na adrese "Ne 24th St & 162nd Ave Ne, Bellevue WA". Chcete-li požádat o dalších pět údajů o příjezdech v reálném čase, pro všechny další živé příjezdy na tuto zastávku proveďte následující kroky:

1. Otevřete aplikaci Postman a pojďme vytvořit kolekci pro uložení požadavků. V horní části aplikace Pošťák vyberte **Nový**. V okně **Vytvořit novou** vyberte **kolekci**.  Pojmenujte kolekci a vyberte tlačítko **Vytvořit.**

2. Chcete-li vytvořit požadavek, vyberte znovu **Nový.** V okně **Vytvořit nový** vyberte **Request**. Zadejte **název požadavku** pro požadavek. Vyberte kolekci, kterou jste vytvořili v předchozím kroku, jako umístění, do kterého chcete požadavek uložit. Potom vyberte **Uložit**.

    ![Vytvoření požadavku v Pošťákovi](./media/how-to-request-transit-data/postman-new.png)

3. Vyberte metodu **GET** HTTP na kartě tvůrce a zadejte následující adresu URL pro vytvoření požadavku GET. Nahraďte `{subscription-key}`primárním klíčem Azure Maps.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Po úspěšné žádosti obdržíte následující odpověď.  Všimněte si, že parametr scheduleType definuje, zda je odhadovaný čas doručení založen na datech v reálném čase nebo statických dat.

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

Přečtěte si, jak požádat o údaje o tranzitu pomocí služby Mobility Service:

> [!div class="nextstepaction"]
> [Jak požádat o údaje o tranzitu](how-to-request-transit-data.md)

Seznamte se s dokumentací k rozhraní AZURE Maps Mobility Service API:

> [!div class="nextstepaction"]
> [Dokumentace rozhraní API služby mobility](https://aka.ms/AzureMapsMobilityService)

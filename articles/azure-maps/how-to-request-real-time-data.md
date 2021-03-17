---
title: Vyžádat si data veřejného přenosu v reálném čase pomocí služby Mobility Service Microsoft Azure Maps (Preview)
description: Naučte se, jak vyžádat data veřejného přenosu v reálném čase, jako jsou například doručení při přenosu. Podívejte se, jak používat služby Azure Maps mobility (Preview) k tomuto účelu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d3e3dc4b0e3bc64a38856da8344583b744ea62b6
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906042"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-services-preview"></a>Vyžádání dat veřejného přenosu v reálném čase pomocí služeb Azure Maps mobility (Preview) 

> [!IMPORTANT]
> Služby Azure Maps mobility jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


V tomto článku se dozvíte, jak používat [služby Azure Maps mobility](/rest/api/maps/mobility) k vyžádání dat veřejného přenosu v reálném čase.

V tomto článku se dozvíte, jak požádat o další doručení v reálném čase pro všechny řádky přicházející v daném zastavení.

## <a name="prerequisites"></a>Předpoklady

Nejprve musíte mít účet Azure Maps a klíč předplatného, aby bylo možné volat rozhraní API pro veřejné průjezdy Azure Maps. Informace najdete v pokynech v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-azure-maps-account) pro vytvoření účtu Azure Maps. Použijte k získání primárního klíče pro váš účet postup uvedený v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) . Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](./how-to-manage-authentication.md).

V tomto článku se k sestavení volání REST používá [aplikace pro publikování](https://www.getpostman.com/apps) . Můžete použít libovolné vývojové prostředí API, které dáváte přednost.

## <a name="request-real-time-arrivals-for-a-stop"></a>Vyžádat doručení v reálném čase o zastavení

Aby bylo možné vyžádat data o doručení konkrétního zastavení veřejného přenosu v reálném čase, budete muset vytvořit žádost o [rozhraní API pro doručení](/rest/api/maps/mobility/getrealtimearrivalspreview) Azure Maps [služby mobility v reálném čase (Preview)](/rest/api/maps/mobility). K dokončení žádosti budete potřebovat **metroID** a **stopID** . Další informace o tom, jak si vyžádat tyto parametry, najdete v naší příručce o tom, jak [vyžádat trasy veřejného přenosu](./how-to-request-transit-data.md).

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

Přečtěte si, jak vyžádat přenosová data pomocí služeb mobility (Preview):

> [!div class="nextstepaction"]
> [Požadavky na přenosová data](how-to-request-transit-data.md)

Prozkoumejte dokumentaci k rozhraní API služby Azure Maps mobility Services (Preview):

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní API služby mobility Services](/rest/api/maps/mobility)
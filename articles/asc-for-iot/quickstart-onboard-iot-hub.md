---
title: 'Rychlý start: Povolení služby'
description: Zjistěte, jak napojit a povolit službu zabezpečení Azure Security Center pro IoT ve vašem Azure IoT Hub.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 0d3e4ad63baf6883aa8fadaca5b1f2d28fa14881
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310749"
---
# <a name="quickstart-onboard-azure-security-center-for-iot-service-in-iot-hub"></a>Úvodní příručka: Palubní Služba Azure Security Center pro IoT v centru IoT Hub

Tento článek obsahuje vysvětlení, jak povolit Azure Security Center pro službu IoT ve vašem existujícím centru IoT Hub. Pokud v současné době nemáte službu IoT Hub, přečtěte si, že [můžete začít vytvářet službu IoT Hub pomocí portálu Azure.](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal)

> [!NOTE]
> Azure Security Center pro IoT aktuálně podporuje jenom standardní úrovně IoT hubů.

## <a name="prerequisites-for-enabling-the-service"></a>Předpoklady pro povolení služby

- Pracovní prostor služby Log Analytics
  - Dva typy informací jsou ve výchozím nastavení uloženy v pracovním prostoru Log Analytics pomocí Azure Security Center pro IoT; **výstrahy** a **doporučení zabezpečení**.
  - Můžete přidat úložiště dalšího typu informací, **nezpracovaných událostí**. Všimněte si, že ukládání **nezpracovaných událostí** v Log Analytics s sebou nese další náklady na úložiště.
- IoT Hub (standardní úroveň)
- Splnění všech [požadavků na služby](service-prerequisites.md)

## <a name="enable-azure-security-center-for-iot-on-your-iot-hub"></a>Povolení Centra zabezpečení Azure pro IoT ve službě IoT Hub

Povolení zabezpečení ve službě IoT Hub:

1. Otevřete **ioT hub** na Webu Azure Portal.
1. V nabídce **Zabezpečení** klikněte na **Zabezpečit řešení IoT**.

Blahopřejeme! Už jste dokončili povolení Azure Security Center pro IoT na vašem IoT Hubu.

### <a name="geolocation-and-ip-address-handling"></a>Zpracování geolokace a IP adres

K zabezpečení vašeho řešení IoT se ve výchozím nastavení shromažďují a ukládají IP adresy příchozích a odchozích připojení k vašim zařízením IoT, IoT Edge a IoT Hub(s). Tyto informace jsou nezbytné pro detekci abnormálního připojení z podezřelých zdrojů IP. Například při pokusech o navázání připojení ze zdroje IP známého botnetu nebo ze zdroje IP mimo geolokaci. Služba Azure Security Center for IoT nabízí flexibilitu při povoluje a zakáže shromažďování dat IP adres.

Povolení nebo zakázání shromažďování dat adresy IP:

1. Otevřete ioT hub a pak z nabídky **Zabezpečení** vyberte **Přehled.**
1. Zvolte obrazovku **Nastavení** a upravte nastavení geolokace a/nebo IP manipulátoru, jak si přejete.

### <a name="log-analytics-creation"></a>Vytvoření analýzy protokolů

Když je zapnutá Azure Security Center pro IoT, vytvoří se výchozí pracovní prostor Azure Log Analytics pro ukládání nezpracovaných událostí zabezpečení, výstrah a doporučení pro vaše zařízení IoT, IoT Edge a IoT Hub. Každý měsíc je prvních pět (5) GB dat požitých na zákazníka služby Azure Log Analytics zdarma. Každý GB dat požitých do pracovního prostoru Azure Log Analytics se uchovává zdarma po dobu prvních 31 dnů. Přečtěte si další informace o [cenách Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)

Změna konfigurace pracovního prostoru analýzy protokolů:

1. Otevřete ioT hub a pak z nabídky **Zabezpečení** vyberte **Přehled.**
1. Zvolte obrazovku **Nastavení** a upravte konfiguraci pracovního prostoru nastavení Log Analytics podle potřeby.

### <a name="customize-your-iot-security-solution"></a>Přizpůsobení zabezpečení IoT řešení

Ve výchozím nastavení zapnutí azure security center pro řešení IoT automaticky zabezpečuje všechny služby IoT huby v rámci předplatného Azure.

Zapnutí nebo vypnutí služby Azure Security Center for IoT v konkrétním centru IoT Hub:

1. Otevřete ioT hub a pak z nabídky **Zabezpečení** vyberte **Přehled.**
1. Zvolte obrazovku **Nastavení** a upravte nastavení zabezpečení libovolného centra IoT hub ve vašem předplatném Azure, jak chcete.

## <a name="next-steps"></a>Další kroky

Převést na další článek nakonfigurovat řešení ...

> [!div class="nextstepaction"]
> [Konfigurace vašeho řešení](quickstart-configure-your-solution.md)

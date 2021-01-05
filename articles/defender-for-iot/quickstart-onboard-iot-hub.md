---
title: 'Rychlý Start: povolení služby'
description: Naučte se integrovat a povolit službu Defender for IoT Security ve vašem Azure IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: e3768ef233c60f1687bc804778c3dabf32666e1d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835155"
---
# <a name="quickstart-onboard-azure-defender-for-iot-service-in-iot-hub"></a>Rychlý Start: zprovoznění Azure Defenderu pro službu IoT v IoT Hub

Tento článek popisuje, jak ve stávající IoT Hub povolit službu Defender for IoT. Pokud v tuto chvíli nemáte IoT Hub, přečtěte si téma [vytvoření IoT Hub pomocí Azure Portal](../iot-hub/iot-hub-create-through-portal.md) pro začátek.

> [!NOTE]
> Defender pro IoT aktuálně podporuje jenom centra IoT úrovně Standard.

## <a name="prerequisites-for-enabling-the-service"></a>Předpoklady pro povolení služby

- Pracovní prostor služby Log Analytics
  - Ve výchozím nastavení jsou ve vašem Log Analytics pracovním prostoru od programu Defender pro IoT standardně uloženy dva typy informací. **výstrahy** a **doporučení** zabezpečení.
  - Můžete zvolit, že chcete přidat úložiště pro další typ informací, **nezpracované události**. Pamatujte, že ukládání **nezpracovaných událostí** v Log Analytics přináší další náklady na úložiště.
- IoT Hub (úroveň Standard)
- Splnění všech [systémových požadavků](quickstart-system-prerequisites.md).

## <a name="enable-defender-for-iot-on-your-iot-hub"></a>Povolit Defender pro IoT v IoT Hub

Postup při povolení zabezpečení na IoT Hub:

1. Otevřete **IoT Hub** v Azure Portal.
1. V nabídce **zabezpečení** klikněte na **zabezpečení vašeho řešení IoT**.

Blahopřejeme! Dokončili jste povolování programu Defender pro IoT v IoT Hub.

### <a name="geolocation-and-ip-address-handling"></a>Geografická poloha a zpracování IP adres

V rámci zabezpečení řešení IoT se shromažďují a ukládají IP adresy příchozích a odchozích připojení do a ze zařízení IoT, IoT Edge a IoT Hub, a to ve výchozím nastavení. Tyto informace jsou nezbytné ke zjištění neobvyklého připojení z podezřelých zdrojů IP adres. Například při pokusech o navázání připojení ze zdroje IP adresy známého botnetu nebo ze zdroje IP adresy mimo vaši geografickou polohu. Defender pro službu IoT nabízí flexibilitu, která umožňuje kdykoli povolit a zakázat shromažďování dat IP adres.

Povolení nebo zakázání shromažďování dat IP adres:

1. Otevřete IoT Hub a v nabídce **zabezpečení** vyberte **Nastavení** .
1. Vyberte obrazovku **shromažďování dat** a podle potřeby upravte nastavení geografického umístění nebo manipulace s IP adresami.

### <a name="log-analytics-creation"></a>Vytváření Log Analytics

Když je program Defender pro IoT zapnutý, vytvoří se výchozí pracovní prostor Azure Log Analytics pro ukládání nezpracovaných událostí zabezpečení, výstrah a doporučení pro vaše zařízení IoT, IoT Edge a IoT Hub. Každý měsíc je prvních pět (5) GB dat přijatých na zákazníky do služby Azure Log Analytics zdarma. Po prvních 31 dnech se všechny GB dat, která se ingestují do vašeho pracovního prostoru Azure Log Analytics, zachová zdarma. Přečtěte si další informace o cenách [Log Analytics](https://azure.microsoft.com/pricing/details/monitor/) .

Změna konfigurace pracovního prostoru Log Analytics:

1. Otevřete IoT Hub a v nabídce **zabezpečení** vyberte **Nastavení** .
1. Klikněte na obrazovku **shromažďování dat** a upravte konfiguraci pracovního prostoru Log Analytics nastavení podle vašich přání.

### <a name="customize-your-iot-security-solution"></a>Přizpůsobení řešení zabezpečení IoT

Ve výchozím nastavení zapínání řešení Defender for IoT automaticky zabezpečuje všechna centra IoT v rámci vašeho předplatného Azure.

Postup zapnutí a vypnutí služby IoT pro službu IoT v konkrétním IoT Hub:

1. Otevřete IoT Hub a v nabídce **zabezpečení** vyberte **Nastavení** .
1. Vyberte obrazovku **shromažďování dat** a v rámci vašeho předplatného Azure upravte nastavení zabezpečení libovolného centra IoT.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a nakonfigurujte vaše řešení...

> [!div class="nextstepaction"]
> [Konfigurace vašeho řešení](quickstart-configure-your-solution.md)
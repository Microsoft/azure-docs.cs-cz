---
title: 'Úvodní příručka: Konfigurace řešení'
description: V tomto rychlém startu se dozvíte, jak nakonfigurovat komplexní řešení IoT pomocí Azure Security Center pro IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: 04505527428a6bb312c6fb7a12c116947fbd7cf6
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310857"
---
# <a name="quickstart-configure-your-iot-solution"></a>Úvodní příručka: Konfigurace řešení IoT

Tento článek obsahuje vysvětlení, jak provést počáteční konfiguraci řešení zabezpečení IoT pomocí Azure Security Center pro IoT.

## <a name="azure-security-center-for-iot"></a>Azure Security Center for IoT

Azure Security Center pro IoT poskytuje komplexní komplexní zabezpečení pro řešení IoT založená na Azure.

Pomocí Azure Security Center pro IoT můžete monitorovat celé vaše řešení IoT na jednom řídicím panelu a vynořovat všechna vaše zařízení IoT, platformy IoT a back-endové prostředky v Azure.

Jakmile je Azure Security Center for IoT v centru IoT povoleno, automaticky identifikuje další služby Azure, které jsou také připojené k vašemu IoT Hubu a souvisejí s vaším řešením IoT.

Kromě automatického zjišťování vztahů můžete také vybrat, které další skupiny prostředků Azure označit jako součást řešení IoT.

Vaše výběry umožňují přidat celá předplatná, skupiny prostředků nebo jednotlivé prostředky.

Po definování všech vztahů prostředků využívá Azure Security Center for IoT Azure Security Center k poskytování doporučení zabezpečení a výstrah pro tyto prostředky.

## <a name="add-azure-resources-to-your-iot-solution"></a>Přidání prostředků Azure do svého řešení IoT

Pokud chcete do řešení IoT přidat nový prostředek, postupujte takto:

1. Otevřete **ioT hub** na Webu Azure Portal.
1. V levé nabídce vyberte a otevřete položku **Zdroje** z pole **Zabezpečení.**
1. Vyberte **Upravit** a zvolte skupiny prostředků, které patří do vašeho řešení IoT.
1. Klikněte na **Přidat**.

Blahopřejeme! Do řešení IoT jste přidali novou skupinu prostředků.

Azure Security Center pro IoT teď monitoruje nově přidané skupiny prostředků a zobrazuje příslušná doporučení zabezpečení a výstrahy jako součást vašeho řešení IoT.

## <a name="next-steps"></a>Další kroky

Přejdete k dalšímu článku a dozvíte se, jak vytvořit bezpečnostní moduly...

> [!div class="nextstepaction"]
> [Vytvoření modulů zabezpečení](quickstart-create-security-twin.md)

---
title: Konfigurace vašeho ASC pro řešení IoT ve verzi Preview | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat řešení IoT začátku do konce pomocí ASC pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 2a24db1124ecd9c2437eafee54d856120a77e97f
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576441"
---
# <a name="quickstart-configure-your-iot-solution"></a>Rychlý start: Konfigurovat řešení IoT

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje vysvětlení, jak provést počáteční konfiguraci zabezpečení řešení IoT pomocí ASC pro IoT. 

## <a name="asc-for-iot"></a>ASC for IoT

ASC pro IoT poskytuje komplexní-koncové zabezpečení pro řešení IoT založené na Azure.

S ASC pro IoT můžete monitorovat celé řešení IoT na jednom řídicím panelu, zpřístupnění všech zařízení IoT, platformy IoT a back-endovým prostředkům v Azure.

Jakmile povolíte službu ve službě IoT Hub, ASC pro IoT automaticky identifikuje dalšími službami Azure, také připojené ke službě IoT hub a týkající se řešení IoT.

Kromě zjišťování automatické relací vyberte and můžete také jaké další prostředky Azure ke značce jako součást řešení IoT.
Vyberte požadované možnosti umožňují přidat celou předplatná, skupiny prostředků nebo jednoho zdroje.

Po definování všechny vztahy prostředků, využívá ASC pro IoT Azure Security Center k poskytování doporučení zabezpečení a upozornění pro tyto prostředky.

## <a name="add-azure-resources-to-your-iot-solution"></a>Přidání prostředků Azure do vašeho řešení IoT

Chcete-li přidat nový prostředek do vašeho řešení IoT, postupujte takto: 

1. Otevřete váš **služby IoT Hub** na webu Azure portal. 
2. Vyberte a otevřete **prostředky** pod **zabezpečení** v levé nabídce. 
3. Vyberte **přidat prostředky**.
4. Zvolte prostředky, které patří do vašeho řešení IoT.
5. Klikněte na tlačítko **Add** (Přidat). 

Blahopřejeme! Přidání nového prostředku do vašeho řešení IoT.

ASC pro IoT nyní monitorování, která jste nově přidané prostředky a zařízení Surface zabezpečení relevantní doporučení a výstrahy jako součást tohoto řešení IoT.

## <a name="next-steps"></a>Další postup

Přejděte k dalším článku se naučíte, jak vytvářet moduly zabezpečení...

> [!div class="nextstepaction"]
> [Vytvoření modulů zabezpečení](quickstart-create-security-twin.md)
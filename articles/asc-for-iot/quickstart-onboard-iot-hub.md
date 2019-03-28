---
title: Povolit ASC pro služby ve verzi Preview centra IoT Azure IoT | Dokumentace Microsoftu
description: Zjistěte, jak povolit ASC pro služby Azure IoT ve službě IoT Hub.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.service: ascforiot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4fd7fb68fd2eb8be0c1c109d76cff341faba9391
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541624"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Rychlý start: Povolit službu ve službě IoT Hub

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje vysvětlení, jak povolit ASC pro službu IoT ve verzi preview ve službě IoT Hub.  

> [!NOTE]
> ASC pro IoT aktuálně podporuje pouze úroveň standard a vyšší centra IoT hub.
>ASC pro IoT je řešení jednoho rozbočovače. Pokud budete potřebovat více Center, se vyžadují více řešení. 

## <a name="prerequisites-for-enabling-the-service"></a>Požadavky pro povolení služby

- Pracovní prostor Log Analytics
  - Dva druhy informace jsou uloženy ve výchozím nastavení v pracovním prostoru Log Analytics ve služba ASC pro IoT; **výstrahy zabezpečení** a **doporučení**. 
  - Můžete také přidat další informace o typu, úložiště **nezpracovaných událostí**. Všimněte si, že ukládání **nezpracovaných událostí** v Log Analytics představuje náklady na další úložiště. 
- IoT Hub (úrovně standard nebo vyšší)

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Povolit ASC pro IoT ve službě IoT Hub 

Pokud chcete povolit zabezpečení ve službě IoT Hub, postupujte takto: 

1. Otevřete váš **služby IoT Hub** na webu Azure portal. 
2. Vyberte a otevřete **zabezpečení** v levé nabídce. 
3. Zvolte **povolit zabezpečení IoT**. 
4. Zadejte podrobnosti vašeho pracovního prostoru Log Analytics. 
   - Zvolit k uložení **nezpracovaných událostí** kromě výchozí informace typy úložiště se zaškrtnutým políčkem **nezpracovaných událostí** přepnout **na**. 
   - Aktivujete **dvojčete kolekce** se zaškrtnutým políčkem **dvojčete kolekce** přepnout **na**. 
5. Klikněte na **OK**. 
6. Klikněte na **Uložit**. 

Blahopřejeme! Dokončení povolení ASC pro IoT ve službě IoT Hub. 

## <a name="next-steps"></a>Další postup

Přejděte k dalším článku se naučíte, jak nakonfigurovat řešení...

> [!div class="nextstepaction"]
> [Konfigurace řešení](quickstart-configure-your-solution.md)
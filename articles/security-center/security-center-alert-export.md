---
title: Exportovat protokoly oznámení k pracovnímu prostoru Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak exportovat výstrah, které byly generovány pomocí služby Azure Security Center pracovnímu prostoru Log Analytics.
services: security-center
documentationcenter: na
author: monhaber
manager: mbaldwin
editor: ''
ms.assetid: 2bc67ce5-ec3a-49df-afc3-b4bad5d8ce21
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/13/2019
ms.author: monhaber
ms.openlocfilehash: 12b8b376a0ff149cbfafc7fe69dd8da636280de8
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56653692"
---
# <a name="export-azure-security-center-alerts"></a>Export upozornění ve službě Azure Security Center
Tento článek vysvětluje, jak exportovat výstrahy generované v umístění, které lze použít v jiných nástrojích Azure Security Center.

Některé Security Center zákazníků vyjádřili potřebu využívají data generovaná službou Security Center z centrálního umístění, která obsahuje všechny agregované a zpracovaná data (například výstrahy detekce hrozeb, doporučení zásad zabezpečení, pokrytí, Zabezpečte skóre atd.). Díky tomu data, která mají být dále analyzovat a zpracovat. Nebo může být jiný způsob, jak programově zpracovávat data, namísto použití portálu Security Center.

Protože pracovních prostorů Azure Log Analytics se používají k ukládání a zpracování nezpracovaná data shromážděná z virtuálních počítačů, **Export do služby Log Analytics** funkce poskytuje řešení pro tyto potřeby podle streamovaných dat s pracovním prostorem definované uživatelem.

## <a name="prerequisites"></a>Požadavky
Potřebujete pracovní prostor Log Analytics. Doporučujeme použít stejný pracovní prostor Log Analytics, který jste definovali v nastavení shromažďování dat. Při definování, jeden pro shromažďování dat. [Pracovní prostor log Analytics pro shromažďování dat](security-center-enable-data-collection.md)


## <a name="export-the-alerts"></a>Export výstrahy
1. Klikněte na tlačítko **zásady zabezpečení**.
1. V řádku předplatné, pro kterou chcete použít, klikněte na tlačítko **upravit nastavení**.
  ![Upravit nastavení](./media/security-center-alert-export/edit_settings.png "upravit nastavení")
1. Klikněte na tlačítko **shromažďování dat**.
1. Přejděte dolů k položce **Store Security Center rozšíření dat** a klikněte na možnost přepnout **na**.

   ![Možnost data Store](./media/security-center-alert-export/store_data_option.png "zásady zabezpečení")
1. Vyberte pracovní prostor, který chcete exportovat výstrah za účelem.

    > [!NOTE]
    > Upozornění protokolu nelze exportovat do výchozího pracovního prostoru Security Center. Jak je uvedeno v [požadavky](#Prerequisites), musíte použít pracovní prostor Log Analytics.

1. Klikněte na tlačítko **Uložit** (v horní části obrazovky).
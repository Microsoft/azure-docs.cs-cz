---
title: Azure Data Box Gateway & Poznámky k verzi Azure Data Box Edge 1906| Dokumenty společnosti Microsoft
description: Popisuje kritické otevřené problémy a řešení pro Azure Data Box Gateway a Azure Data Box Edge s verzí 1906.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099483"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge a Azure Data Box Gateway 1906 poznámky k verzi

Následující poznámky k verzi identifikují důležité problémy s otevřením a vyřešené problémy pro verzi 1906 pro Azure Data Box Edge a Azure Data Box Gateway.

Poznámky k verzi jsou průběžně aktualizovány a jako kritické problémy, které vyžadují řešení jsou zjištěny, jsou přidány. Před nasazením brány Data Box Edge/Data Box Gateway pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Tato verze odpovídá verzím softwaru:

- **Brána datové schránky 1906 (1.6.978.743)**
- **Datová schránka Edge 1906 (1.6.978.743)**

> [!NOTE]
> Aktualizaci 1906 lze použít pouze pro zařízení Data Box Edge, na kterých je spuštěna verze softwaru s obecnou dostupností (GA) nebo 1905.

## <a name="whats-new"></a>Co je nového

- **Oprava chybv pracovním postupu správy obnovovacího klíče** – v dřívější verzi došlo k chybě, kvůli které nebyl použit obnovovací klíč. Tato chyba je opravena v této verzi. Důrazně doporučujeme nainstalovat tuto aktualizaci, protože obnovovací klíč umožňuje obnovit data v zařízení v případě, že se zařízení nespustí. Další informace naleznete v tématu jak [uložit obnovovací klíč při nasazování data box edge nebo brány datové schránky](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Vylepšení protokolování Field ProgramMable Gate Array (FPGA)** - Od vydání 1905 byla provedena vylepšení protokolování, protokolování a výstrah související s FPGA. To to bude i nadále požadovanou aktualizací pro Data Box Edge, pokud používáte výpočetní funkci Edge s FPGA. Další informace najdete v tématu [transformace dat pomocí edge compute na datové schránce Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Známé problémy ve verzi GA

Žádné nové problémy jsou vydání uvedeno pro tuto verzi. Všechny problémy s vydáním se přenesly z předchozích verzí. Seznam známých problémů naleznete ve verzi GA na jdete na [známé problémy.](data-box-gateway-release-notes.md#known-issues-in-ga-release)


## <a name="next-steps"></a>Další kroky

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Příprava k nasazení služby Azure Data Box Edge](data-box-edge-deploy-prep.md)

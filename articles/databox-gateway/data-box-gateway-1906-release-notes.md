---
title: Azure Data Box Gateway & Azure Data Box Edge 1906 zpráva k vydání verze | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro Azure Data Box Gateway a Azure Data Box Edge se spuštěnou verzí 1906.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 51f4995ffd6a86022d95df15ae0eb7694d878c60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581828"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Poznámky k verzi pro Azure Data Box Edge a Azure Data Box Gateway 1906

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro vydání 1906 pro Azure Data Box Edge a Azure Data Box Gateway. 

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením Data Box Edge/Data Box Gateway pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Tato verze odpovídá verzím softwaru:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Aktualizace 1906 se dá použít jenom pro Data Box Edge zařízení, na kterých běží Obecná dostupnost (GA) nebo 1905 verze softwaru.

## <a name="whats-new"></a>Novinky

- **Oprava chyb v pracovním postupu správy klíčů pro obnovení** – v předchozí verzi došlo k chybě, vzhledem k tomu, že obnovovací klíč nebyl použit. Tato chyba je opravena v této verzi. Důrazně doporučujeme použít tuto aktualizaci, protože obnovovací klíč vám umožní obnovit data v zařízení v případě, že se zařízení nespustí. Další informace najdete v tématu Jak [Uložit obnovovací klíč při nasazení data box Edge nebo data box Gateway](../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Vylepšení protokolování v poli programovatelné brány pole (FPGA)** – bylo provedeno spouštění 1905 vydání, protokolování a upozornění souvisejících s FPGA. Tato možnost bude i nadále požadovaná aktualizace pro Data Box Edge, pokud používáte funkci Edge COMPUTE s FPGA. Další informace najdete v tématu Jak [transformovat data pomocí hraničních výpočtů na data box Edge](../databox-online/azure-stack-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Známé problémy ve verzi GA

Pro tuto verzi se neuvedly žádné nové problémy. Všechny uvedené verze převzaly problémy z předchozích verzí. Pokud chcete zobrazit seznam známých problémů, přejděte ke [známým problémům ve verzi GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Další kroky

- [Příprava k nasazení služby Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Příprava k nasazení služby Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md)

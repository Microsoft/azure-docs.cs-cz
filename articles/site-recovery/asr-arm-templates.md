---
title: Šablony Azure Resource Manageru
description: Azure Resource Manager šablony pro použití funkcí Azure Site Recovery
services: site-recovery
author: rishjai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/04/2021
ms.author: rishjai
ms.openlocfilehash: efd5c1ab3e23348373be76c79d1b9689bc5f4941
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720524"
---
# <a name="azure-resource-manager-templates-for-azure-site-recovery"></a>Šablony Azure Resource Manager pro Azure Site Recovery

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro použití funkcí Azure Site Recovery.

| Template (Šablona) | Description |
|---|---|
|**Z Azure do Azure** | |
| [Vytvoření trezoru Recovery Services](./quickstart-create-vault-template.md)| Vytvořte trezor služby Recovery Services. Trezor je možné použít pro služby Azure Backup a Azure Site Recovery. |
| [Povolení replikace pro virtuální počítače Azure](https://aka.ms/asr-arm-enable-replication) | Povolte replikaci pro virtuální počítače Azure pomocí stávajícího trezoru a nastavení vlastní cíl.|
| [Aktivovat převzetí služeb při selhání a znovu zapnout ochranu](https://aka.ms/asr-arm-failover-reprotect) | Aktivovat operaci převzetí služeb při selhání a znovu zapnout ochranu pro sadu virtuálních počítačů Azure. |
| [Spuštění koncového toku DR pro virtuální počítače Azure](https://aka.ms/asr-arm-e2e-flow) | Zahajte kompletní tok obnovení po havárii (Povolit replikaci a převzetí služeb při selhání a znovu provést ochranu + navrácení služeb po obnovení a opětovné zapnutí ochrany) pro virtuální počítače Azure, označovaný také jako 540. tok.|
|   |   |
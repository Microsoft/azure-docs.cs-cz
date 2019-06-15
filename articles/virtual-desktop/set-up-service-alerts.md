---
title: Nastavení oznámení služby pro virtuální plochy Windows – Azure
description: Jak nastavit službu Azure Service Health pro příjem oznámení služby pro virtuální plochy Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: v-chjenk
ms.openlocfilehash: cae75f16da2cad453c74b7e6e9fb62dd789fe5c7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081368"
---
# <a name="tutorial-set-up-service-alerts"></a>Kurz: Nastavení oznámení služby

Azure Service Health můžete použít ke sledování problémů se službou a poradci pro stav pro virtuální plochy Windows. Azure Service Health můžete informovat prostřednictvím různých typech výstrah (například e-mail nebo SMS), vám pomůžou pochopit vliv problém, a zachovat, můžete aktualizovat, protože tento problém řeší. Azure Service Health vám také umožňují snížit prostoje a příprava na plánovanou údržbu a změny, které by mohly ovlivnit dostupnost vašich prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a konfigurace výstrah služeb.

Další informace o Azure Service Health, najdete v článku [dokumentaci k Azure stavu](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Požadavky

- [Kurz: Vytvořit tenanta v náhledu virtuální plochy Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory)
- [Kurz: Vytvoření instančních objektů a přiřazení role pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell)
- [Kurz: Vytvoření fondu hostitele pomocí webu Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)

## <a name="create-service-alerts"></a>Vytvořit oznámení služby

V této části se dozvíte, jak nakonfigurovat služby Azure Service Health a jak nastavit upozornění, které je k dispozici na na webu Azure portal. Můžete nastavit různé typy výstrah a naplánují je upozornit vás včas.

### <a name="recommended-service-alerts"></a>Doporučená služba Výstrahy

Doporučujeme že vytvořit následující typy událostí stavu výstrahy služby:

- **Služba problému:** Přijímání oznámení na hlavní problémy, které ovlivňují připojení uživatelů ke službě nebo možnost správy vašeho tenanta virtuální plochy Windows.
- **Poradce pro stav:** Dostávat oznámení, které vyžadují vaši pozornost. Následují příklady tohoto typu oznámení:
    - Virtuální počítače (VM) není bezpečné nakonfigurované jako otevřený port 3389.
    - Zastarání funkcí

### <a name="configure-service-alerts"></a>Konfigurace výstrah služeb

Konfigurace výstrah služeb:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Vyberte **stavu služby.**
3. Postupujte podle pokynů v [vytvoření upozornění protokolu aktivit pro oznámení služby](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) nastavit výstrahy a oznámení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak nastavit a používat služby Azure Service Health ke sledování problémů se službou a poradci pro stav pro virtuální plochy Windows. Další informace o tom, jak se přihlásit k virtuálnímu klientovi Windows, i nadále připojit k virtuální ploše postupy Windows.

> [!div class="nextstepaction"]
> [Připojte se ke klientovi vzdálené plochy na Windows 7 a Windows 10](./connect-windows-7-and-10.md)

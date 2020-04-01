---
title: Nastavení výstrah služby pro Virtuální plochu Windows – Azure
description: Jak nastavit Azure Service Health pro příjem oznámení o službách pro Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/11/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2834ba924fa9c29d955c38fbaeb45ab23e5c4e9b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127712"
---
# <a name="tutorial-set-up-service-alerts"></a>Kurz: Nastavení výstrah služby

Azure Service Health můžete použít ke sledování problémů se službami a informačních zpravodajů o stavu pro Virtuální plochu Windows. Azure Service Health vás může upozornit pomocí různých typů výstrah (například e-mailu nebo SMS), pomůže vám pochopit účinek problému a průběžně aktualizovat, jak problém řeší. Azure Service Health vám taky může pomoct zmírnit prostoje a připravit se na plánovanou údržbu a změny, které by mohly ovlivnit dostupnost vašich prostředků.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte a nakonfigurujte výstrahy služby.

Další informace o stavu služby Azure najdete v tématu [Dokumentace ke zdraví Azure](https://docs.microsoft.com/azure/service-health/).

## <a name="prerequisites"></a>Požadavky

- [Kurz: Vytvoření klienta ve Virtuální ploše Windows](tenant-setup-azure-active-directory.md)
- [Kurz: Vytvoření instančních objektů a přiřazení rolí pomocí Prostředí PowerShell](create-service-principal-role-powershell.md)
- [Kurz: Vytvoření fondu hostitelů s Azure Marketplace](create-host-pools-azure-marketplace.md)

## <a name="create-service-alerts"></a>Vytváření výstrah služby

V této části se ukazuje, jak nakonfigurovat Azure Service Health a jak nastavit oznámení, ke kterým máte přístup na webu Azure Portal. Můžete nastavit různé typy výstrah a naplánovat je tak, aby vás včas upozornily.

### <a name="recommended-service-alerts"></a>Doporučená upozornění na služby

Doporučujeme vytvořit výstrahy služby pro následující typy událostí stavu:

- **Problém se službou:** Dostávat oznámení o hlavních problémech, které mají vliv na připojení uživatelů ke službě nebo s možností spravovat klienta virtuální plochy Windows.
- **Zdravotní poradenství:** Dostávat oznámení, která vyžadují vaši pozornost. Níže jsou uvedeny některé příklady tohoto typu oznámení:
    - Virtuální počítače (VM) nejsou bezpečně nakonfigurované jako otevřený port 3389
    - Vyřazení funkčnosti

### <a name="configure-service-alerts"></a>Konfigurace výstrah služby

Postup konfigurace výstrah služby:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Vyberte **Stav služby.**
3. Pomocí pokynů v části [Vytvoření upozornění na protokol aktivit u oznámení o službách](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log-service-notifications?toc=%2Fazure%2Fservice-health%2Ftoc.json#alert-and-new-action-group-using-azure-portal) nastavte upozornění a oznámení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak nastavit a používat Azure Service Health ke sledování problémů se službami a zdravotních doporučení pro Virtuální plochu Windows. Chcete-li se dozvědět o tom, jak se přihlásit k virtuální ploše systému Windows, pokračujte v návodech pro připojení k virtuální ploše systému Windows.

> [!div class="nextstepaction"]
> [Připojení ke klientovi Vzdálené plochy ve Windows 7 a Windows 10](./connect-windows-7-and-10.md)

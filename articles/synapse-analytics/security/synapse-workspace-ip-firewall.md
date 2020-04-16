---
title: Konfigurace pravidel brány firewall IP v Azure Synapse Analytics
description: Článek, který vás naučí konfigurovat pravidla brány firewall IP v Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: f36a76187538d93b57d2d3f5973408f141271f67
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424108"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Pravidla brány firewall AZURE Synapse Analytics (preview)

Tento článek vám vysvětlí pravidla brány firewall IP a naučí vás, jak je nakonfigurovat v Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Pravidla brány firewall IP

Pravidla brány firewall IP udělují nebo odepírají přístup k vašemu pracovnímu prostoru Synapse na základě původní IP adresy každého požadavku. Pravidla brány firewall IP můžete nakonfigurovat pro váš pracovní prostor. Pravidla brány firewall IP nakonfigurovaná na úrovni pracovního prostoru platí pro všechny veřejné koncové body pracovního prostoru (fondy SQL, SQL na vyžádání a Vývoj).

## <a name="create-and-manage-ip-firewall-rules"></a>Vytvoření a správa pravidel brány firewall IP

Existují dva způsoby, jak jsou pravidla brány firewall IP přidána do pracovního prostoru Synapse. Chcete-li do pracovního prostoru přidat bránu firewall IP, vyberte **možnost Zabezpečení + sítě** a během vytváření pracovního prostoru **zaškrtněte políčko Povolit připojení ze všech adres IP.**

![Konfigurace IP pracovního prostoru portálu Azure Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Konfigurace IP pracovního prostoru portálu Azure Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Po vytvoření pracovního prostoru můžete také přidat pravidla brány firewall IP do pracovního prostoru Synapse. V části **Zabezpečení** na webu Azure Portal vyberte **Brány firewall.** Chcete-li přidat nové pravidlo brány firewall IP, pojmenujte jej, počáteční IP a koncová ADRESA IP. Po dokončení vyberte **Uložit**.

![Konfigurace IP pracovního prostoru Azure Synapse na webu Azure Portal](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Připojení k Synapse z vlastní sítě

K pracovnímu prostoru Synapse se můžete připojit pomocí synapse studia. Sql Server Management Studio (SSMS) můžete také použít pro připojení k prostředkům SQL (fondy SQL a SQL na vyžádání) ve vašem pracovním prostoru.

Ujistěte se, že brána firewall v síti a místní počítač umožňuje odchozí komunikaci na portech TCP 80, 443 a 1443 pro Synapse Studio.

Také je třeba povolit odchozí komunikaci na Port UDP 53 pro Synapse Studio. Chcete-li se připojit pomocí nástrojů, jako jsou SSMS a Power BI, musíte povolit odchozí komunikaci na portu TCP 1433.

Pokud používáte výchozí nastavení zásad přesměrování, bude pravděpodobně nutné povolit odchozí komunikaci na dalších portech. Další informace o zásadách připojení naleznete zde.

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru Azure Synapse](../quickstart-create-workspace.md)

Vytvoření pracovního prostoru Azure Synapse pomocí [spravované virtuální sítě pracovního prostoru](./synapse-workspace-managed-vnet.md)
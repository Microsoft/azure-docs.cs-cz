---
title: Použití slevy předběžného nákupu Azure Databricks
description: Zjistěte, jak o nižší předběžného nákupu Azure Databricks platí pro vaše použití.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827642"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Jak se uplatňuje sleva ve výši předběžného nákupu Azure Databricks

Kdykoli během období nákupu můžete použít předem zakoupené jednotky potvrzení Azure Databricks (DBCU). Žádné Azure Databricks, který využívá odečte snížení z předem zakoupené DBCUs automaticky.

Na rozdíl od virtuálních počítačů, předem zakoupené jednotky nevyprší po hodinách. Můžete je kdykoli během období nákupu. Chcete-li získat slevy předběžného nákupu, nemusíte znovu provádět nasazení ani přiřadit kupují předem plán do vašich pracovních prostorů Azure Databricks za využití.

Sleva za předběžného nákupu vztahuje pouze na využití jednotek (DBU) Azure Databricks. Další poplatky, jako jsou výpočty, úložiště a sítě se účtují samostatně.

## <a name="pre-purchase-discount-application"></a>Předplatit slev

Databricks předběžného nákupu se vztahuje na všechny úrovně a úloh Databricks. Před nákupem jako fond předplacené jednotek Databricks potvrzení změn si můžete představit. Využití se odečte od fondu, bez ohledu na to, úlohy nebo úroveň. Využití je odečtena poměr následující:

| **Úloha** | **Poměr aplikace DBU – úroveň Standard** | **Poměr aplikace DBU – úroveň Premium** |
| --- | --- | --- |
| Analýza dat | 0.4 | 0.55 |
| Příprava dat | 0.15 | 0,30 |
| Jednoduchá příprava dat | 0,07 | 0.22 |

Například když je zpracován množství Data Analytics – úroveň Standard, předem zakoupené jednotky Databricks potvrzení odečte 0.4 jednotkami. Když množství dat Engineering světla – úroveň Standard se používá, předem zakoupené jednotky Databricks potvrzení se odečte 0,07 jednotky

## <a name="determine-plan-use"></a>Určení plánu

Pokud chcete zjistit DBCU používání plánu, přejděte k webu Azure portal > **rezervace** a klikněte na tlačítko zakoupené plán Databricks. Vaše využití k datu se zobrazí se všechny zbývající jednotky. Další informace o určení vaší rezervace, najdete v tématu [zobrazit využití rezervace](billing-reservation-apis.md#see-reservation-usage) článku.

## <a name="how-discount-application-shows-in-usage-data"></a>Způsob, jakým zobrazuje slev za využití dat

Když slevy předběžného nákupu se vztahuje na využití Databricks na vyžádání poplatky se zobrazí jako nuly v datech využití. Další informace o rezervaci nákladů a využití najdete v tématu [získat smlouvu Enterprise rezervace nákladů a využití](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte otázky nebo potřebujete pomoc, [vytvořit žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

- Další informace o správě rezervaci, najdete v článku [spravovat Azure rezervace](billing-manage-reserved-vm-instance.md).
- Další informace o předběžného nákupu Azure Databricks ještě ušetříte peníze, najdete v článku [Azure Databricks optimalizovat náklady pomocí předběžného nákupu](billing-prepay-databricks-reserved-capacity.md).
- Další informace o rezervacích Azure, najdete v následujících článcích:
  - [Co jsou Azure rezervace?](billing-save-compute-costs-reservations.md)
  - [Správa rezervací v Azure](billing-manage-reserved-vm-instance.md)
  - [Vysvětlení využití rezervace pro předplatné s průběžnými sazbami](billing-understand-reserved-instance-usage.md)
  - [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)

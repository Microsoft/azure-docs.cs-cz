---
title: Přehled protokolů platformy Azure | Microsoft Docs
description: Přehled protokolů v Azure Monitor poskytujících bohatou a častou práci o provozu prostředku Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: f2b22746b7a78349580220f4aa7579876fd21c33
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074586"
---
# <a name="overview-of-azure-platform-logs"></a>Přehled protokolů platformy Azure
Protokoly platforem poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. Generují se automaticky, přestože potřebujete nakonfigurovat některé protokoly platformy, které se mají přemístit do jednoho nebo více cílů, aby je bylo možné zachovat. Tento článek obsahuje přehled protokolů platforem, včetně informací, které poskytují, a o tom, jak je můžete nakonfigurovat pro shromažďování a analýzu.

## <a name="types-of-platform-logs"></a>Typy protokolů platformy
V následující tabulce jsou uvedeny konkrétní protokoly platformy, které jsou k dispozici v různých vrstvách Azure.

| Protokol | Vrstva | Popis |
|:---|:---|:---|
| [Protokoly prostředků](./resource-logs.md) | Prostředky Azure | Poskytněte přehled o operacích, které byly provedeny v rámci prostředku Azure ( *rovina dat*), například získání tajného kódu z Key Vault nebo vytvoření žádosti do databáze. Obsah protokolů prostředků se liší podle typu prostředku a služby Azure.<br><br>*Protokoly prostředků se dřív odkazovaly na diagnostické protokoly.*  |
| [Protokol aktivit](../essentials/activity-log.md) | Předplatné Azure | Poskytuje přehled o operacích u každého prostředku Azure v předplatném mimo (*rovinu správy*) kromě aktualizací Service Healthch událostí. Pomocí protokolu aktivit můžete určit, _kdo_ a _kdy_ se u prostředků ve vašem předplatném mají _dělat_ operace zápisu (Put, post, DELETE). Pro každé předplatné Azure existuje jeden protokol aktivit. |
| [Protokoly služby Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) | Tenant Azure |  Obsahuje historii přihlašovací aktivity a záznam pro audit změn provedených v Azure Active Directory pro konkrétního tenanta.   |

> [!NOTE]
> Protokol aktivit Azure je primárně pro aktivity, ke kterým dochází v Azure Resource Manager. Nesleduje prostředky pomocí modelu Classic/RDFE. Některé typy klasických prostředků mají poskytovatele prostředků proxy v Azure Resource Manager (například Microsoft. ClassicCompute). Pokud pracujete s klasickým typem prostředku prostřednictvím Azure Resource Manager pomocí těchto zprostředkovatelů prostředků proxy, operace se zobrazí v protokolu aktivit. Pokud pracujete s klasickým typem prostředku mimo Azure Resource Manager proxy, vaše akce se zaznamenávají pouze do protokolu operací. Protokol operací se dá procházet v samostatné části portálu.

![Přehled protokolů platformy](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Zobrazení protokolů platformy
K dispozici jsou různé možnosti pro zobrazení a analýzu různých protokolů platformy Azure.

- Zobrazení protokolu aktivit v Azure Portal a přístup k událostem z PowerShellu a rozhraní příkazového řádku. Podrobnosti najdete v [protokolu aktivit](../essentials/activity-log.md#view-the-activity-log) . 
- Zobrazení Azure Active Directorych sestav zabezpečení a aktivit v Azure Portal. Podívejte [se, co jsou sestavy Azure Active Directory?](../../active-directory/reports-monitoring/overview-reports.md)  , kde najdete podrobnosti.
- Protokoly prostředků se automaticky generují pomocí podporovaných prostředků Azure, ale nedají se zobrazit, pokud je neodešlete do [cílového umístění](#destinations). 

## <a name="destinations"></a>Cíle
Protokoly platforem můžete odesílat do jednoho nebo více cílů v následující tabulce v závislosti na požadavcích na monitorování. Nakonfigurujte cíle pro protokoly platforem [vytvořením nastavení diagnostiky](../essentials/diagnostic-settings.md).

| Cíl | Description |
|:---|:---|
| Pracovní prostor služby Log Analytics | Analyzujte protokoly všech vašich prostředků Azure společně a využijte výhod všech funkcí, které jsou k dispozici pro [Azure monitor protokolů](../logs/data-platform-logs.md) , včetně [dotazů protokolu](../logs/log-query-overview.md) a [výstrah protokolů](../alerts/alerts-log.md). Připněte výsledky dotazu protokolu na řídicí panel Azure nebo ho do sešitu zahrňte jako součást interaktivní sestavy. | 
| Centrum událostí | Odesílat data protokolu platformy mimo Azure, například pro SIEM nebo vlastní platformu telemetrie třetích stran. |
| Azure Storage | Archivujte protokoly pro audit nebo zálohování. |

- Podrobnosti o vytvoření nastavení diagnostiky pro protokol aktivit nebo protokoly prostředků najdete v tématu [Vytvoření nastavení diagnostiky pro odesílání protokolů a metrik platforem do různých umístění](../essentials/diagnostic-settings.md). 
- Podrobnosti o vytvoření nastavení diagnostiky pro protokoly Azure Active Directory najdete v následujících článcích.
  - [Integrace protokolů služby Azure AD s protokoly Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Kurz: streamování Azure Active Directorych protokolů do centra událostí Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Kurz: archivace protokolů služby Azure AD do účtu služby Azure Storage](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Další kroky

* [Přečtěte si další podrobnosti o protokolu aktivit.](../essentials/activity-log.md)
* [Přečtěte si další podrobnosti o protokolech prostředků](./resource-logs.md)

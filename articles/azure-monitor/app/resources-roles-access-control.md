---
title: Prostředky, role a řízení přístupu ve službě Azure Application Insights | Dokumentace Microsoftu
description: Vlastníci, přispěvatelé a čtenáři přehledů, které vaší organizaci.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 49f736a5-67fe-4cc6-b1ef-51b993fb39bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: mbullwin
ms.openlocfilehash: 023f0e560900aa582be1f28e553358adb0c87b1e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118468"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Prostředky, role a řízení přístupu ve službě Application Insights

Můžete řídit, kdo má číst a aktualizovat přístup ke svým datům v Azure [Application Insights][start], s použitím [řízení přístupu na základě rolí ve službě Microsoft Azure](../../role-based-access-control/role-assignments-portal.md).

> [!IMPORTANT]
> Přiřadit uživatelům v přístupu **skupiny prostředků nebo předplatného** , ke které prostředek vaší aplikace patří - není v vlastní prostředek. Přiřazení **Přispěvatel součástí Application Insights** role. Tím se zajistí uniform řízení přístupu k webové testy a upozornění spolu s prostředek vaší aplikace. [Další informace](#access).

## <a name="resources-groups-and-subscriptions"></a>Prostředky, skupiny a předplatná

První, definice:

* **Prostředek** – instance služby Microsoft Azure. Prostředek Application Insights shromažďuje, analyzuje a zobrazuje telemetrická data odeslaná z aplikace.  Jiné typy prostředků Azure patří webové aplikace, databáze a virtuální počítače.
  
    Chcete-li zobrazit vaše prostředky, otevřete [webu Azure portal][portal], přihlaste se a klikněte na možnost všechny prostředky. Najít prostředek, napište část názvu do pole filtru.
  
    ![Seznam prostředků Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Skupina prostředků** ] [ group] – každý prostředek patří do jedné skupiny. Skupina je pohodlný způsob, jak spravovat související prostředky, zejména pro řízení přístupu. Do jedné skupiny prostředků můžete například umístit webové aplikace, prostředek Application Insights pro monitorování aplikací a prostředků úložiště zachovat exportovaná data.

* [**Předplatné** ](https://portal.azure.com) – Pokud chcete používat Application Insights nebo další prostředky Azure, přihlaste se k předplatnému Azure. Každá skupina prostředků patří na jedno předplatné Azure, kde vyberete balíčku cena a, pokud je předplatné organizace, zvolte členy a jejich přístupových oprávnění.
* [**Účet Microsoft** ] [ account] – uživatelské jméno a heslo, které používáte pro přihlášení k Microsoft Azure předplatných, XBox Live, Outlook.com a dalším službám společnosti Microsoft.

## <a name="access"></a> Řízení přístupu ve skupině prostředků

Je důležité pochopit, že kromě prostředku, který jste vytvořili pro vaši aplikaci, jsou k dispozici také samostatné prostředky skryté výstrahy a webové testy. Jsou připojeny ke stejné [skupiny prostředků](#resource-group) jako vaši aplikaci. Jste může také umístili další služby Azure do něj, třeba websites nebo úložiště.

Řízení přístupu k těmto prostředkům se proto doporučuje:

* Řízení přístupu na **skupiny prostředků nebo předplatného** úroveň.
* Přiřazení **komponenty Application Insights Přispěvatel** role pro uživatele. To jim umožňuje upravit webové testy, upozornění a prostředky Application Insights, bez poskytnutí přístupu k jiným službám ve skupině.

## <a name="to-provide-access-to-another-user"></a>Pro zajištění přístupu k jiným uživatelem

Musíte mít práva vlastníka předplatného nebo skupiny prostředků.

Uživatel musí mít [Account Microsoft][account], nebo přístup k jejich [Account organizace Microsoft](../../active-directory/fundamentals/sign-up-organization.md). Můžete poskytnout přístup jednotlivců a také skupiny uživatelů, které jsou definované v Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Přejděte do skupiny prostředků nebo přímo na vlastní prostředek

Zvolte **řízení přístupu (IAM)** z nabídky na levé straně.

![Snímek obrazovky přístupu ovládacího prvku tlačítko na webu Azure portal](./media/resources-roles-access-control/0001-access-control.png)

Vyberte **přidat přiřazení role**

![Snímek obrazovky přístupu ovládací prvek nabídky pomocí tlačítka Přidat zvýrazněný červenou barvou](./media/resources-roles-access-control/0002-add.png)

**Přidat oprávnění** zobrazení níže je primárně specifické pro prostředky Application Insights při prohlížení oprávnění pro řízení přístupu z vyšší úrovně, jako jsou skupiny prostředků, zobrazí se další mimo aplikaci Zaměřené na insights role.

Chcete-li zobrazit informace na všechny řízení přístupu na základě rolí Azure použít předdefinované role [oficiální referenční obsah](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Seznam rolí uživatele – snímek obrazovky přístupu ovládacího prvku](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Vyberte roli

Případně odkaz na související oficiální dokumentaci.

| Role | Ve skupině prostředků |
| --- | --- |
| [Vlastník](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Můžete změnit všechno včetně přístupu uživatelů. |
| [Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Můžete upravit všechno včetně všech prostředků. |
| [Přispěvatel aplikace součást Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Můžete upravit prostředky Application Insights, webové testy a upozornění. |
| [Čtenář](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Můžete zobrazit, ale nic nezměníte. |
| [Application Insights Snapshot debuggeru.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Uděluje oprávnění uživatele používat funkce Application Insights Snapshot debuggeru. Všimněte si, že tato role je součástí rolí Přispěvatel ani vlastník. |
| Přispěvatel správy vydaných verzí nasazení služby Azure | Role přispěvatele pro služby nasazení prostřednictvím nasazení služby Azure. |
| [Purger dat](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Zvláštní roli pro vymazání osobních údajů. Najdete v našich [pokyny ohledně osobních údajů](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data) Další informace.   |
| Správce ExpressRoute | Můžete odstranit vytvářet a spravovat tras express.|
| [Přispěvatel log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Přispěvatel log Analytics může číst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače na virtuální počítače; čtení klíčů účtů úložiště, abyste mohli konfigurovat shromažďování protokolů ze služby Azure Storage; Vytvoření a konfigurace účtů služby Automation; přidávání řešení a konfigurace diagnostik Azure na všech prostředcích Azure.  |
| [Čtenář log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Čtenář Log Analytics si může zobrazit a vyhledat všechna data monitorování a jeho nastavení. Může si zobrazit konfiguraci diagnostiky Azure na všech prostředcích Azure. |
| masterreader | Umožňuje uživateli zobrazit všechno, ale ne provádět změny. |
| [Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Může číst všechna data monitorování a aktualizovat jeho nastavení. |
| [Monitorování metrik vydavatele](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Povolí publikování metrik pro prostředky Azure. |
| [Čtenář monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Může číst všechna data monitorování. |
| Přispěvatel zásad prostředků (Preview) | Obnovení uživatelé z EA s oprávněními vytvářet a upravovat zásady prostředků, vytvářet lístky podpory a číst prostředek a hierarchii.  |
| [Správce uživatelských přístupů](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Umožňuje uživateli spravovat přístup jiných uživatelů k prostředkům Azure.|
| [Přispěvatel webů](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Umožňuje správu webů (ne webových plánů), ale ne přístup k nim...|

"Úpravy" zahrnuje vytváření, odstraňování a aktualizace:

* Zdroje a prostředky
* Webové testy
* Výstrahy
* Průběžný export

#### <a name="select-the-user"></a>Vybrat uživatele

Pokud uživatel, kterého chcete, aby se v adresáři, můžete pozvat kdokoli s účtem Microsoft.
(Pokud používají služby, jako je Outlook.com, OneDrive, Windows Phone nebo XBox Live, nemají účet Microsoft.)

## <a name="related-content"></a>Související obsah

* [Řízení přístupu v Azure na základě rolí](../../role-based-access-control/role-assignments-portal.md)

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/resource-group-overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

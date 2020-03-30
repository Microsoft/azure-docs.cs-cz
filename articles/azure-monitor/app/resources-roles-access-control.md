---
title: Prostředky, role a řízení přístupu v Přehledech aplikací Azure | Dokumenty společnosti Microsoft
description: Vlastníci, přispěvatelé a čtenáři přehledů vaší organizace.
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 1e57af269c4052d0dcd4a8f7970ca23017024299
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473128"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Prostředky, role a řízení přístupu v Application Insights

Pomocí [ovládacího prvku přístupu na základě rolí v Microsoft Azure](../../role-based-access-control/role-assignments-portal.md)můžete určit, kdo má přístup ke vašim datům a aktualizovat je v Azure Application [Insights][start].

> [!IMPORTANT]
> Přiřaďte přístup uživatelům ve **skupině prostředků nebo předplatném,** do kterého prostředek aplikace patří – nikoli v samotném prostředku. Přiřaďte roli **přispěvatele komponenty Application Insights.** Tím je zajištěna jednotná kontrola přístupu k webovým testům a výstrahám spolu s prostředkem aplikace. [Další informace](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Prostředky, skupiny a předplatná

Za prvé, některé definice:

* **Prostředek** – instance služby Microsoft Azure. Prostředek Application Insights shromažďuje, analyzuje a zobrazuje telemetrická data odeslaná z vaší aplikace.  Mezi další typy prostředků Azure patří webové aplikace, databáze a virtuální počítače.
  
    Pokud chcete zobrazit vaše prostředky, otevřete [portál Azure][portal], přihlaste se a klikněte na Všechny prostředky. Chcete-li zdroj najít, zadejte do pole filtru jeho část názvu.
  
    ![Seznam prostředků Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Skupina prostředků**][group] - Každý prostředek patří do jedné skupiny. Skupina je pohodlný způsob správy souvisejících prostředků, zejména pro řízení přístupu. Do jedné skupiny prostředků můžete například umístit webovou aplikaci, prostředek Application Insights pro monitorování aplikace a prostředek úložiště pro zachování exportovaných dat.

* [**Předplatné**](https://portal.azure.com) – pokud chcete použít Application Insights nebo jiné prostředky Azure, přihlaste se k předplatnému Azure. Každá skupina prostředků patří do jednoho předplatného Azure, kde si vyberete cenový balíček a pokud se jedná o předplatné organizace, zvolte členy a jejich přístupová oprávnění.
* [**Účet Microsoft**][account] – uživatelské jméno a heslo, které používáte k přihlášení k předplatným Microsoft Azure, XBox Live, Outlook.com a dalším službám Microsoftu.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a>Řízení přístupu ve skupině prostředků

Je důležité si uvědomit, že kromě prostředku, který jste vytvořili pro vaši aplikaci, existují také samostatné skryté prostředky pro výstrahy a webové testy. Jsou připojeny ke stejné [skupině prostředků](#resource-group) jako prostředek Application Insights. Možná jste tam dali i další služby Azure, jako jsou weby nebo úložiště.

Chcete-li řídit přístup k těmto prostředkům, doporučujeme proto:

* Řízení přístupu na **úrovni skupiny prostředků nebo předplatného.**
* Přiřaďte uživatelům roli **přispěvatele komponenty Application Insights.** To jim umožňuje upravovat webové testy, výstrahy a prostředky Application Insights, aniž by poskytovaly přístup k jiným službám ve skupině.

## <a name="to-provide-access-to-another-user"></a>Poskytnutí přístupu jinému uživateli

Musíte mít práva vlastníka k předplatnému nebo skupině prostředků.

Uživatel musí mít [účet Microsoft][account]nebo přístup ke svému [organizačnímu účtu Microsoft](../../active-directory/fundamentals/sign-up-organization.md). Můžete poskytnout přístup jednotlivcům a také skupinám uživatelů definovaným ve službě Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Přechod do skupiny prostředků nebo přímo na samotný prostředek

Z levé nabídky zvolte **Řízení přístupu (IAM).**

![Snímek obrazovky s tlačítkem Řízení přístupu na Webu Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Vybrat **přidat přiřazení role**

![Snímek obrazovky nabídky Řízení přístupu se zvýrazněným červeným tlačítkem Přidat](./media/resources-roles-access-control/0002-add.png)

Zobrazení **Přidat oprávnění** níže je primárně specifické pro prostředky Application Insights, pokud jste prohlíželi oprávnění řízení přístupu z vyšší úrovně, jako jsou skupiny prostředků, zobrazí se další role, které nejsou zaměřeny na aplikace.

Chcete-li zobrazit informace o všech předdefinovaných rolích řízení přístupu na základě rolí Azure, použijte [oficiální referenční obsah](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

![Snímek obrazovky se seznamem rolí uživatele řízení přístupu](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Vybrat roli

Případně odkazujeme na související oficiální referenční dokumentaci.

| Role | Ve skupině prostředků |
| --- | --- |
| [Vlastník](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |Může změnit cokoliv, včetně přístupu uživatelů. |
| [Přispěvatel](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |Může upravovat cokoli, včetně všech zdrojů. |
| [Přispěvatel komponenty Application Insights](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |Můžete upravovat prostředky Application Insights. |
| [Čtenář](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |Může zobrazit, ale nic nezmění. |
| [Ladicí program snímku přehledů aplikací](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | Uděluje uživateli oprávnění používat funkce ladicího programu snímek Přehledy aplikací. Všimněte si, že tato role je zahrnuta v rolích Vlastník ani Přispěvatel. |
| Přispěvatel pro nasazení správy verzí služby Azure | Role přispěvatele pro služby nasazující prostřednictvím nasazení služby Azure Service. |
| [Vychodění dat](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | Zvláštní úloha při očištění osobních údajů. Další informace naleznete v našich [pokynech pro osobní údaje.](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data)   |
| Správce expressroute | Můžete vytvořit odstranění a spravovat expresní trasy.|
| [Přispěvatel Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Přispěvatel log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úpravy nastavení monitorování zahrnuje přidání rozšíření virtuálních počítače do virtuálních počítače; čtení klíčů účtu úložiště, abyste mohli konfigurovat kolekci protokolů z Azure Storage; vytváření a konfigurace účtů automatizace; přidávání řešení; a konfigurace diagnostiky Azure na všech prostředcích Azure.  |
| [Čtenář Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Log Analytics Reader můžete zobrazit a prohledat všechna data monitorování, stejně jako a zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. |
| masterreader | Umožňuje uživateli zobrazit vše, ale neprovádět změny. |
| [Přispěvatel monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | Dokáže číst všechna data monitorování a aktualizovat nastavení monitorování.|
| [Vydavatel metrik monitorování](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | Umožňuje metriky publikování oproti prostředkům Azure. |
| [Monitorovací čtečka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | Umí číst všechna data monitorování. |
| Přispěvatel zásad zdrojů (preview) | Zaplněné uživatelé z EA, s právy k vytvoření / upravit zásady prostředků, vytvořit lístek podpory a číst prostředek/hierarchie.  |
| [Správce uživatelského přístupu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | Umožňuje uživateli spravovat přístup ostatních uživatelů k prostředkům Azure.|
| [Přispěvatel webových stránek](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | Umožňuje spravovat weby (ne webové plány), ale ne přístup k nim..|

"Editace" zahrnuje vytváření, mazání a aktualizaci:

* Prostředky
* Webové testy
* Výstrahy
* Průběžný export

#### <a name="select-the-user"></a>Výběr uživatele

Pokud požadovaný uživatel není v adresáři, můžete pozvat kohokoli s účtem Microsoft.
(Pokud používají služby jako Outlook.com, OneDrive, Windows Phone nebo XBox Live, mají účet Microsoft.)

## <a name="related-content"></a>Související obsah

* [Řízení přístupu na základě rolí v Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Dotaz na PowerShell k určení členství v roli

Vzhledem k tomu, že některé role mohou být propojeny s oznámeními a e-mailovými výstrahami, může být užitečné generovat seznam uživatelů, kteří patří do dané role. Chcete-li pomoci s generováním těchto typů seznamů, nabízíme následující ukázkové dotazy, které lze upravit tak, aby vyhovovaly vašim specifickým potřebám:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Dotaz na celé předplatné pro role správce + role přispěvatele

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Dotaz v rámci konkrétního prostředku Application Insights pro vlastníky a přispěvatele

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Dotaz v rámci určité skupiny prostředků pro vlastníky a přispěvatele

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

---
title: Prostředky, role a řízení přístupu v Azure Application Insights | Microsoft Docs
description: Vlastníci, přispěvatelé a čtenáři přehledů vaší organizace.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4254170ad100b634439cc846dc381e305ae71d1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589601"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Prostředky, role a řízení přístupu v Application Insights

Pomocí [řízení přístupu založeného na rolích Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)můžete řídit, kdo má ke svým datům přístup pro čtení a aktualizace dat v Azure [Application Insights][start].

> [!IMPORTANT]
> Přiřaďte přístup uživatelům ve **skupině prostředků nebo předplatném** , ke kterému patří váš prostředek aplikace – ne do samotného prostředku. Přiřaďte roli **Přispěvatel komponent Application Insights** . Tím se zajistí jednotné řízení přístupu k webovým testům a výstrahám spolu s vaším prostředkem aplikace. [Další informace](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Prostředky, skupiny a předplatná

Nejprve některé definice:

* **Prostředek** – instance Microsoft Azure služby. Váš Application Insights prostředek shromažďuje, analyzuje a zobrazuje data telemetrie odesílaná z vaší aplikace.  Mezi další typy prostředků Azure patří webové aplikace, databáze a virtuální počítače.
  
    Pokud chcete zobrazit prostředky, otevřete [Azure Portal][portal], přihlaste se a klikněte na všechny prostředky. Chcete-li najít prostředek, zadejte část názvu do pole Filtr.
  
    ![Seznam prostředků Azure](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Skupina prostředků**][group] – každý prostředek patří do jedné skupiny. Skupina je pohodlný způsob, jak spravovat související prostředky, zejména pro řízení přístupu. Do jedné skupiny prostředků můžete například vložit webovou aplikaci, prostředek Application Insights pro monitorování aplikace a prostředek úložiště pro zachování exportovaných dat.

* [**Předplatné**](https://portal.azure.com) – Pokud chcete používat Application Insights nebo jiné prostředky Azure, přihlásíte se k předplatnému Azure. Každá skupina prostředků patří k jednomu předplatnému Azure, kde si zvolíte svůj cenový balíček, a pokud se jedná o předplatné organizace, vyberte členy a jejich přístupová oprávnění.
* [**Účet Microsoft**][account] – uživatelské jméno a heslo, které používáte k přihlášení k předplatným Microsoft Azure, Xbox Live, Outlook.com a dalším službám Microsoftu.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> Řízení přístupu ve skupině prostředků

Je důležité pochopit, že kromě prostředku, který jste vytvořili pro vaši aplikaci, existují také samostatné skryté prostředky pro výstrahy a webové testy. Jsou připojené ke stejné [skupině prostředků](#resource-group) jako prostředek Application Insights. Do této služby můžete také umístit další služby Azure, jako jsou weby nebo úložiště.

## <a name="to-provide-access-to-another-user"></a>Poskytnutí přístupu jinému uživateli

Musíte mít práva vlastníka k předplatnému nebo skupině prostředků.

Uživatel musí mít [účet Microsoft][account]nebo mít přístup ke svému [organizačnímu účtu Microsoft](../../active-directory/fundamentals/sign-up-organization.md). Můžete poskytnout přístup jednotlivcům a také skupinám uživatelů definovaným v Azure Active Directory.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Přejděte do skupiny prostředků nebo přímo k samotnému prostředku.

V nabídce na levé straně vyberte **řízení přístupu (IAM)** .

![Snímek obrazovky s tlačítkem řízení přístupu v Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Vyberte **Přidat přiřazení role** .

![Obrazovka nabídky řízení přístupu se zvýrazněným tlačítkem přidat na červenou](./media/resources-roles-access-control/0002-add.png)

Zobrazení **Přidat oprávnění** níže je primárně specifické pro Application Insights prostředky. Pokud jste si prohlíželi oprávnění k řízení přístupu z vyšší úrovně, jako jsou skupiny prostředků, měli byste vidět další role orientované na jiné než Application Insights.

Pokud chcete zobrazit informace o všech předdefinovaných rolích řízení přístupu na základě role Azure, použijte [oficiální referenční obsah](../../role-based-access-control/built-in-roles.md).

![Snímek obrazovky seznamu rolí uživatele řízení přístupu](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Vybrat roli

Tam, kde je to vhodné, odkazujeme na přidruženou oficiální referenční dokumentaci.

| Role | Ve skupině prostředků |
| --- | --- |
| [Vlastník](../../role-based-access-control/built-in-roles.md#owner) |Může měnit cokoli, včetně přístupu uživatelů. |
| [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) |Může upravovat cokoli, včetně všech prostředků. |
| [Přispěvatel Application Insights komponent](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Může upravit prostředky Application Insights. |
| [Čtenář](../../role-based-access-control/built-in-roles.md#reader) |Může zobrazovat, ale ne měnit. |
| [Application Insights Snapshot Debugger](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | Udělí uživateli oprávnění použít Application Insights Snapshot Debugger funkce. Všimněte si, že tato role je zahrnutá ani v rolích vlastníka ani Přispěvatel. |
| Přispěvatel Release Management nasazení služby Azure | Role Přispěvatel pro služby nasazování prostřednictvím Azure Service Deploy. |
| [Nástroj pro vyprázdnění dat](../../role-based-access-control/built-in-roles.md#data-purger) | Speciální role pro vyprazdňování osobních údajů Další informace najdete v našich [pokynech k osobním datům](../logs/personal-data-mgmt.md) .   |
| Správce ExpressRoute | Může vytvořit odstranění a spravovat expresní trasy.|
| [Přispěvatel Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Přispěvatel Log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úprava nastavení monitorování zahrnuje přidání rozšíření virtuálního počítače do virtuálních počítačů. čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z Azure Storage; vytváření a konfigurace účtů služby Automation; přidávání řešení; a konfigurují se diagnostiky Azure na všech prostředcích Azure.  |
| [Čtenář Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | Log Analytics čtenář může zobrazit a vyhledat všechna data monitorování a také zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. |
| masterreader | Umožňuje uživateli zobrazit vše, ale nemůže provádět změny. |
| [Přispěvatel monitorování](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | Může číst všechna data monitorování a aktualizovat nastavení monitorování.|
| [Monitorování vydavatele metrik](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Povoluje publikování metrik pro prostředky Azure. |
| [Čtečka monitorování](../../role-based-access-control/built-in-roles.md#monitoring-reader) | Může číst všechna data monitorování. |
| Přispěvatel zásad prostředků (Preview) | Uživatelé z EA, s právy k vytváření a úpravám zásad prostředků, vytvářejí lístek podpory a čtou prostředek/hierarchii.  |
| [Správce uživatelského přístupu](../../role-based-access-control/built-in-roles.md#user-access-administrator) | Umožňuje uživateli spravovat přístup k prostředkům Azure ostatním uživatelům.|
| [Přispěvatel webu](../../role-based-access-control/built-in-roles.md#website-contributor) | Umožňuje spravovat weby (nikoli webové plány), ale ne přístup k nim.|

Úpravy obsahují vytváření, odstraňování a aktualizace:

* Zdroje informací
* Webové testy
* Výstrahy
* Průběžný export

#### <a name="select-the-user"></a>Vybrat uživatele

Pokud požadovaný uživatel v adresáři není, můžete pozvat kohokoli s účet Microsoft.
(Pokud používají služby, jako jsou Outlook.com, OneDrive, Windows Phone nebo XBox Live, mají účet Microsoft.)

## <a name="related-content"></a>Související obsah

* [Řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>Dotaz PowerShellu k určení členství v rolích

Vzhledem k tomu, že některé role mohou být propojeny s oznámeními a e-mailovými výstrahami, může být užitečné, aby bylo možné vygenerovat seznam uživatelů, kteří patří k dané roli. Abychom vám pomohli vygenerovat tyto typy seznamů, nabízíme následující ukázkové dotazy, které je možné upravit podle konkrétních potřeb:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Dotazování na celé předplatné pro role správců a role přispěvatele

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Dotaz v kontextu konkrétního Application Insights prostředku pro vlastníky a přispěvatele

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Dotaz v kontextu konkrétní skupiny prostředků pro vlastníky a přispěvatele

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

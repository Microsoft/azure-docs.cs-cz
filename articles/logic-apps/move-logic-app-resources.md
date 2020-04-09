---
title: Migrace aplikací logiky mezi předplatnými, skupinami prostředků nebo oblastmi
description: Migrace aplikací logiky nebo účtů integrace do jiných předplatných Azure, skupin prostředků nebo umístění (oblastí)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 065bbc62d65d7e91728b10cd9f95b2e73ea03abc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878727"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Přesunutí prostředků aplikace logiky do jiných skupin prostředků, oblastí nebo předplatných Azure

Chcete-li migrovat aplikaci logiky nebo související prostředky do jiné skupiny prostředků Azure, oblasti nebo předplatného, máte různé způsoby, jak dokončit tyto úkoly, jako je například portál Azure, Azure PowerShell, Azure CLI a REST API. Před přesunutím prostředků zkontrolujte tyto důležité informace: 

* Mezi skupinami prostředků Azure nebo předplatnými můžete přesunout jenom [konkrétní typy prostředků aplikace logiky.](../azure-resource-manager/management/move-support-resources.md#microsoftlogic)

* Zkontrolujte [omezení](../logic-apps/logic-apps-limits-and-config.md) počtu prostředků aplikace logiky, které můžete mít ve vašem předplatném Azure a v každé oblasti Azure. Tato omezení ovlivňují, zda můžete přesunout konkrétní typy prostředků, když oblast zůstane stejná mezi předplatnými nebo skupinami prostředků. Například můžete mít jenom jeden účet integrace úrovně Free pro každou oblast Azure v každém předplatném Azure.

* Když přesunete prostředky, Azure vytvoří nová ID prostředků. Takže se ujistěte, že místo toho použijete nová ID a aktualizujete všechny skripty nebo nástroje, které jsou přidruženy k přesunutým prostředkům.

* Po migraci aplikací logiky mezi předplatnými, skupinami prostředků nebo oblastmi je nutné znovu vytvořit nebo znovu autorizovat všechna připojení, která vyžadují otevřené ověřování (OAuth).

* Prostředí [integrační služby (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) můžete přesunout pouze do jiné skupiny prostředků, která existuje ve stejné oblasti Azure nebo předplatné Azure. Službu ISE nelze přesunout do skupiny prostředků, která existuje v jiné oblasti Azure nebo předplatné azure. Po takovém přesunutí je také nutné aktualizovat všechny odkazy na službu ISE v pracovních postupech aplikace logiky, účtech integrace, připojení a tak dále.

## <a name="prerequisites"></a>Požadavky

* Stejné předplatné Azure, které bylo použito k vytvoření aplikace logiky nebo účtu integrace, který chcete přesunout

* Oprávnění vlastníka prostředků k přesunutí a nastavení požadovaných prostředků. Další informace o [řízení přístupu na základě rolí (RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Přesunutí prostředků mezi předplatnými

Pokud chcete přesunout prostředek, jako je například aplikace logiky nebo účet integrace, do jiného předplatného Azure, můžete použít portál Azure, Azure PowerShell, Azure CLI nebo REST API. Tyto kroky zahrnují portál Azure, který můžete použít, když oblast prostředků zůstane stejná. Další kroky a obecná příprava najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte prostředek aplikace logiky, který chcete přesunout.

1. Na stránce **Přehled** prostředku vyberte vedle **položku Odběr**odkaz **na změnu.**

1. Na stránce **Přesunout prostředky** vyberte prostředek aplikace logiky a všechny související prostředky, které chcete přesunout.

1. V seznamu **Odběr** vyberte cílové předplatné.

1. V seznamu **Skupina zdrojů** vyberte cílovou skupinu prostředků. Chcete-li vytvořit jinou skupinu prostředků, vyberte možnost **Vytvořit novou skupinu**.

1. Chcete-li potvrdit, že skripty nebo nástroje přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete novými ID prostředků, zaškrtněte políčko potvrzení a pak vyberte **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Přesunutí zdrojů mezi skupinami zdrojů

Pokud chcete přesunout prostředek, jako je aplikace logiky, účet integrace nebo [prostředí integrační služby (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), do jiné skupiny prostředků Azure, můžete použít portál Azure, Azure PowerShell, Azure CLI nebo REST API. Tyto kroky zahrnují portál Azure, který můžete použít, když oblast prostředků zůstane stejná. Další kroky a obecná příprava najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Před skutečně přesunutí prostředků mezi skupinami, můžete otestovat, zda můžete úspěšně přesunout prostředek do jiné skupiny. Další informace naleznete v [tématu Ověření přesunu](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte prostředek aplikace logiky, který chcete přesunout.

1. Na stránce **Přehled** zdroje vyberte vedle **skupiny Prostředků**odkaz **na změnu.**

1. Na stránce **Přesunout prostředky** vyberte prostředek aplikace logiky a všechny související prostředky, které chcete přesunout.

1. V seznamu **Skupina zdrojů** vyberte cílovou skupinu prostředků. Chcete-li vytvořit jinou skupinu prostředků, vyberte možnost **Vytvořit novou skupinu**.

1. Chcete-li potvrdit, že skripty nebo nástroje přidružené k přesunutým prostředkům nebudou fungovat, dokud je neaktualizujete novými ID prostředků, zaškrtněte políčko potvrzení a pak vyberte **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Přesun zdrojů mezi oblastmi

Pokud chcete přesunout aplikaci logiky do jiné oblasti, vaše možnosti závisí na způsobu, jakým jste vytvořili aplikaci logiky. Na základě možnosti, kterou zvolíte, je nutné znovu vytvořit nebo znovu autorizovat připojení v aplikaci logiky.

* Na webu Azure Portal znovu vytvořte aplikaci logiky v nové oblasti a překonfigurujte nastavení pracovního postupu. Chcete-li ušetřit čas, můžete zkopírovat základní definici pracovního postupu a připojení ze zdrojové aplikace do cílové aplikace. Chcete-li zobrazit "kód" za aplikaci logiky, na panelu nástrojů Návrhář aplikace logiky vyberte **zobrazení kódu**.

* Pomocí Visual Studia a nástrojů Azure Logic Apps tools pro Visual Studio můžete [otevřít a stáhnout aplikaci logiky](../logic-apps/manage-logic-apps-with-visual-studio.md) z webu Azure Portal jako [šablonu Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Tato šablona je většinou připravena k nasazení a obsahuje definice prostředků pro vaši aplikaci logiky, včetně samotného pracovního postupu a připojení. Šablona také deklaruje parametry pro hodnoty, které mají být při nasazení používány. Tímto způsobem můžete snadněji změnit, kde a jak nasadit aplikaci logiky, na základě vašich potřeb. Chcete-li zadat umístění a další potřebné informace pro nasazení, můžete použít samostatný soubor parametrů.

* Pokud jste vytvořili a nasadili aplikaci logiky pomocí nástrojů průběžné integrace (CI) a průběžného doručování (CD), jako jsou Azure Pipelines v Azure DevOps, můžete nasadit aplikaci do jiné oblasti pomocí těchto nástrojů.

Další informace o šablonách nasazení pro aplikace logiky najdete v těchto tématech:

* [Přehled: Automatizace nasazení pro Azure Logic Apps pomocí šablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Vyhledání, otevření a stažení aplikace logiky z portálu Azure do Visual Studia](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Vytváření šablon Azure Resource Manager pro aplikace Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Nasazení šablon Azure Resource Manageru pro aplikace Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Související prostředky

Některé prostředky Azure, jako jsou místní prostředky brány dat v Azure, můžou existovat v oblasti, která se liší od aplikací logiky, které tyto prostředky používají. Ostatní prostředky Azure, jako jsou například propojené účty integrace, však musí existovat ve stejné oblasti jako aplikace logiky. Na základě vašeho scénáře se ujistěte, že vaše aplikace logiky mají přístup k prostředkům, které vaše aplikace očekávají, že existují ve stejné oblasti.

Chcete-li například propojit aplikaci logiky s účtem integrace, musí existovat oba prostředky ve stejné oblasti. Ve scénářích, jako je zotavení po havárii, obvykle chcete integrační účty, které mají stejnou konfiguraci a artefakty. V jiných scénářích můžete potřebovat účty integrace s různými konfiguracemi a artefakty.

Vlastní konektory v Azure Logic Apps jsou viditelné pro autory konektorů a uživatele, kteří mají stejné předplatné Azure a stejného klienta Azure Active Directory. Tyto konektory jsou k dispozici ve stejné oblasti, kde se nasazují aplikace logiky. Další informace najdete v tématu [Sdílení vlastních konektorů ve vaší aplikaci](https://docs.microsoft.com/connectors/custom-connectors/share).

Šablona, kterou získáte z Visual Studia obsahuje pouze definice prostředků pro aplikaci logiky a její připojení. Takže pokud vaše aplikace logiky používá jiné prostředky, například účet integrace a artefakty B2B, jako jsou partneři, smlouvy a schémata, musíte exportovat šablonu tohoto účtu integrace pomocí portálu Azure. Tato šablona obsahuje definice prostředků pro účet integrace i artefakty. Šablona však není plně parametrizována. Proto je nutné ručně parametrizovat hodnoty, které chcete použít pro nasazení.

### <a name="export-templates-for-integration-accounts"></a>Export šablon pro účty integrace

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete svůj účet integrace.

1. V nabídce účtu integrace vyberte v části **Nastavení** **položku Exportovat šablonu**.

1. Na panelu nástrojů vyberte **Stáhnout**a uložte šablonu.

1. Otevřete a upravte šablonu, abyste parametrizovali potřebné hodnoty pro nasazení.

## <a name="next-steps"></a>Další kroky

[Přesunutí prostředků Azure do nových skupin prostředků nebo předplatných](../azure-resource-manager/management/move-resource-group-and-subscription.md)

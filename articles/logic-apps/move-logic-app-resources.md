---
title: Přesuňte aplikace logiky napříč předplatnými, skupinami prostředků nebo oblastmi.
description: Migrace aplikací logiky nebo integračních účtů do jiných předplatných Azure, skupin prostředků nebo umístění (oblastí)
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: aca2c51ff14b99ba41b159cf32e59dc861de7a53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87826207"
---
# <a name="move-logic-app-resources-to-other-azure-resource-groups-regions-or-subscriptions"></a>Přesunutí prostředků aplikace logiky do jiných skupin prostředků Azure, oblastí nebo předplatných

K migraci aplikace logiky nebo souvisejících prostředků do jiné skupiny prostředků Azure, oblasti nebo předplatného máte různé způsoby, jak tyto úlohy dokončit, například Azure Portal, Azure PowerShell, Azure CLI a REST API. Před přesunutím prostředků si prostudujte tyto informace: 

* Mezi skupinami prostředků Azure nebo předplatnými můžete přesunout jenom [konkrétní typy prostředků aplikace logiky](../azure-resource-manager/management/move-support-resources.md#microsoftlogic) .

* Ověřte [omezení](../logic-apps/logic-apps-limits-and-config.md) počtu prostředků aplikace logiky, které můžete mít v předplatném Azure a v každé oblasti Azure. Tato omezení mají vliv na to, jestli můžete přesunout konkrétní typy prostředků, pokud tato oblast zůstane v rámci předplatných nebo skupin prostředků stejná. Pro každou oblast Azure v každém předplatném Azure můžete mít například jenom jeden účet pro integraci bezplatné úrovně.

* Když přesunete prostředky, Azure vytvoří nová ID prostředků. Nezapomeňte místo toho použít nová ID a aktualizovat skripty nebo nástroje, které jsou přidruženy k přesunutým prostředkům.

* Po dokončení migrace Logic Apps mezi předplatnými, skupinami prostředků nebo oblastmi musíte znovu vytvořit nebo znovu autorizovat všechna připojení, která vyžadují otevřené ověřování (OAuth).

* [Prostředí ISE (Integration Service Environment)](connect-virtual-network-vnet-isolated-environment-overview.md) můžete přesunout jenom do jiné skupiny prostředků, která existuje ve stejné oblasti Azure nebo předplatném Azure. ISE nejde přesunout do skupiny prostředků, která existuje v jiné oblasti Azure nebo předplatném Azure. Po takovém přesunutí musíte také aktualizovat všechny odkazy na ISE v pracovních postupech aplikace logiky, účtů pro integraci, připojeních atd.

## <a name="prerequisites"></a>Požadavky

* Stejné předplatné Azure, které se použilo k vytvoření aplikace logiky nebo účtu pro integraci, který chcete přesunout

* Oprávnění vlastníka prostředku pro přesun a nastavení prostředků, které chcete. Přečtěte si další informace o [řízení přístupu na základě role v Azure (Azure RBAC)](../role-based-access-control/built-in-roles.md#owner).

<a name="move-subscription"></a>

## <a name="move-resources-between-subscriptions"></a>Přesunutí prostředků mezi předplatnými

Pokud chcete přesunout prostředek, jako je aplikace logiky nebo účet pro integraci, do jiného předplatného Azure, můžete použít Azure Portal, Azure PowerShell, Azure CLI nebo REST API. Tyto kroky zahrnují Azure Portal, které můžete použít, když je oblast prostředku stejná. Další kroky a obecnou přípravu najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte prostředek aplikace logiky, který chcete přesunout.

1. Na stránce **Přehled** prostředku klikněte vedle položku **předplatné**na odkaz **změnit** .

1. Na stránce **Přesunutí prostředků** vyberte prostředek aplikace logiky a všechny související prostředky, které chcete přesunout.

1. V seznamu **předplatné** vyberte cílové předplatné.

1. V seznamu **Skupina prostředků** vyberte cílovou skupinu prostředků. Nebo pokud chcete vytvořit jinou skupinu prostředků, vyberte **vytvořit novou skupinu**.

1. Pokud chcete potvrdit, že všechny skripty nebo nástroje, které jsou přidružené k přesunutým prostředkům, nebudou fungovat, dokud je neaktualizujete pomocí nových ID prostředků, zaškrtněte políčko potvrzení a pak vyberte **OK**.

<a name="move-resource-group"></a>

## <a name="move-resources-between-resource-groups"></a>Přesunutí prostředků mezi skupinami prostředků

Pokud chcete přesunout prostředek, jako je aplikace logiky, účet pro integraci nebo [prostředí Integration Service (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md), do jiné skupiny prostředků Azure, můžete použít Azure Portal, Azure PowerShell, Azure CLI nebo REST API. Tyto kroky zahrnují Azure Portal, které můžete použít, když je oblast prostředku stejná. Další kroky a obecnou přípravu najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Před skutečným přesunutím prostředků mezi skupinami můžete otestovat, jestli můžete prostředek úspěšně přesunout do jiné skupiny. Další informace najdete v tématu [ověření vašeho přesunu](../azure-resource-manager/management/move-resource-group-and-subscription.md#validate-move).

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte prostředek aplikace logiky, který chcete přesunout.

1. Na stránce **Přehled** prostředku vedle **skupiny prostředků**vyberte odkaz **změnit** .

1. Na stránce **Přesunutí prostředků** vyberte prostředek aplikace logiky a všechny související prostředky, které chcete přesunout.

1. V seznamu **Skupina prostředků** vyberte cílovou skupinu prostředků. Nebo pokud chcete vytvořit jinou skupinu prostředků, vyberte **vytvořit novou skupinu**.

1. Pokud chcete potvrdit, že všechny skripty nebo nástroje, které jsou přidružené k přesunutým prostředkům, nebudou fungovat, dokud je neaktualizujete pomocí nových ID prostředků, zaškrtněte políčko potvrzení a pak vyberte **OK**.

<a name="move-location"></a>

## <a name="move-resources-between-regions"></a>Přesunutí prostředků mezi oblastmi

Pokud chcete aplikaci logiky přesunout do jiné oblasti, závisí vaše možnosti na způsobu, jakým jste vytvořili aplikaci logiky. V závislosti na zvolené možnosti musíte znovu vytvořit nebo znovu autorizovat připojení v aplikaci logiky.

* V Azure Portal znovu vytvořte aplikaci logiky v nové oblasti a znovu nakonfigurujte nastavení pracovního postupu. Pokud chcete ušetřit čas, můžete zkopírovat základní definici pracovního postupu a připojení ze zdrojové aplikace do cílové aplikace. Pokud chcete zobrazit kód na pozadí aplikace logiky, vyberte na panelu nástrojů návrháře aplikace logiky možnost **zobrazení kódu**.

* Pomocí sady Visual Studio a Azure Logic Apps nástrojů pro Visual Studio můžete [aplikaci logiky otevřít a stáhnout](../logic-apps/manage-logic-apps-with-visual-studio.md) z Azure Portal jako [šablonu Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md). Tato šablona je většinou připravená pro nasazení a zahrnuje definice prostředků pro vaši aplikaci logiky, včetně samotného pracovního postupu a připojení. Šablona také deklaruje parametry pro hodnoty, které se mají použít při nasazení. Tímto způsobem můžete snadněji změnit umístění a způsob nasazení aplikace logiky podle vašich potřeb. Chcete-li určit umístění a další nezbytné informace pro nasazení, můžete použít samostatný soubor parametrů.

* Pokud jste vytvořili a nasadili aplikaci logiky pomocí nástrojů pro kontinuální integraci (CI) a průběžné doručování (CD), jako je například Azure Pipelines v Azure DevOps, můžete aplikaci nasadit do jiné oblasti pomocí těchto nástrojů.

Další informace o šablonách nasazení pro Logic Apps najdete v těchto tématech:

* [Přehled: Automatizace nasazení pro Azure Logic Apps pomocí šablon Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Hledání, otevření a stažení aplikace logiky z Azure Portal do sady Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Vytvoření šablon Azure Resource Manager pro Azure Logic Apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Nasazování šablon Azure Resource Manageru pro Azure Logic Apps](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)

### <a name="related-resources"></a>Související prostředky

Některé prostředky Azure, například prostředky místní brány dat v Azure, můžou existovat v oblasti, která se liší od aplikací logiky, které tyto prostředky využívají. Nicméně jiné prostředky Azure, jako jsou propojené integrační účty, musí existovat ve stejné oblasti jako aplikace logiky. V závislosti na vašem scénáři se ujistěte, že vaše aplikace logiky mají přístup k prostředkům, které vaše aplikace očekávají ve stejné oblasti.

Například pro propojení aplikace logiky s účtem pro integraci musí oba prostředky existovat ve stejné oblasti. Ve scénářích, jako je například zotavení po havárii, obvykle chcete účty pro integraci se stejnou konfigurací a artefakty. V jiných scénářích budete možná potřebovat účty pro integraci s různými konfiguracemi a artefakty.

Vlastní konektory v Azure Logic Apps jsou viditelné pro autory konektorů a uživatele, kteří mají stejné předplatné Azure a stejného Azure Active Directoryho tenanta. Tyto konektory jsou k dispozici ve stejné oblasti, ve které jsou nasazené Logic Apps. Další informace najdete v tématu [Sdílení vlastních konektorů ve vaší aplikaci](/connectors/custom-connectors/share).

Šablona, kterou dostanete ze sady Visual Studio, obsahuje jenom definice prostředků pro vaši aplikaci logiky a její připojení. Takže pokud aplikace logiky používá jiné prostředky, například účet pro integraci a artefakty B2B, jako jsou partneři, smlouvy a schémata, musíte Exportovat šablonu tohoto účtu pro integraci pomocí Azure Portal. Tato šablona obsahuje definice prostředků pro integrační účet a artefakty. Šablona ale není plně Parametrizovaná. Proto je nutné ručně parametrizovat hodnoty, které chcete použít pro nasazení.

### <a name="export-templates-for-integration-accounts"></a>Exportovat šablony pro integrační účty

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete účet pro integraci.

1. V nabídce účtu pro integraci v části **Nastavení**vyberte **Exportovat šablonu**.

1. Na panelu nástrojů vyberte **Stáhnout**a pak šablonu uložte.

1. Otevřením a úpravou šablony můžete parametrizovat nezbytné hodnoty pro nasazení.

## <a name="next-steps"></a>Další kroky

[Přesunutí prostředků Azure do nových skupin prostředků nebo předplatných](../azure-resource-manager/management/move-resource-group-and-subscription.md)

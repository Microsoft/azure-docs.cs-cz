---
title: 'Rychlý Start: Vytvoření skupiny pro správu pomocí portálu'
description: V tomto rychlém startu použijete Azure Portal k vytvoření skupiny pro správu, která slouží k uspořádání prostředků do hierarchie prostředků.
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: c8cb7b8bd3ad33d1f315670bfbb1782972e6e97f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89661744"
---
# <a name="quickstart-create-a-management-group"></a>Rychlý Start: Vytvoření skupiny pro správu

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení. Další informace najdete v tématu [počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Libovolný uživatel Azure AD v tenantovi může vytvořit skupinu pro správu bez oprávnění k zápisu skupiny pro správu, která jsou přiřazená tomuto uživateli, pokud není povolena [ochrana hierarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Tato nová skupina pro správu se stal podřízenou skupinou kořenové skupiny pro správu nebo [výchozí skupině pro správu](./how-to/protect-resource-hierarchy.md#setting---default-management-group) a autorovi je přiděleno přiřazení role "vlastník". Služba skupiny pro správu umožňuje tuto možnost, takže přiřazení rolí není nutné na kořenové úrovni. Žádní uživatelé nemají přístup ke kořenové skupině pro správu při jejím vytvoření. Aby nedocházelo k tomu, že by globální Správci služby Azure AD mohli začít používat skupiny pro správu, umožníme vytváření počátečních skupin pro správu na kořenové úrovni.

### <a name="create-in-portal"></a>Vytvořit na portálu

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby**  >  **Správa a zásady správného řízení**.

1. Vyberte **skupiny pro správu**.

1. Vyberte **+ Přidat skupinu pro správu**.

   :::image type="content" source="./media/main.png" alt-text="Snímek obrazovky se stránkou skupiny pro správu, která zobrazuje podřízené skupiny pro správu a odběry." border="false":::

1. Nechejte možnost **vytvořit nové** a vyplnit pole ID skupiny pro správu.

   - **ID skupiny pro správu** je jedinečný identifikátor adresáře, který se používá k odesílání příkazů v této skupině pro správu. Tento identifikátor není po vytvoření upravitelný, protože se používá v systému Azure k identifikaci této skupiny. [Kořenová skupina pro správu](./overview.md#root-management-group-for-each-directory) se automaticky vytvoří s ID, které je Azure Active Directory ID. U všech ostatních skupin pro správu přiřaďte jedinečné ID.
   - Pole Zobrazovaný název je název, který se zobrazí v Azure Portal. Samostatné zobrazované jméno je volitelné pole při vytváření skupiny pro správu a může být kdykoli změněno.

   :::image type="content" source="./media/create_context_menu.png" alt-text="Snímek obrazovky se stránkou skupiny pro správu, která zobrazuje podřízené skupiny pro správu a odběry.":::

1. Vyberte **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat vytvořenou skupinu pro správu, postupujte podle následujících kroků:

1. Vyberte **všechny služby**  >  **Správa a zásady správného řízení**.

1. Vyberte **skupiny pro správu**.

1. Vyhledejte skupinu pro správu, kterou jste vytvořili, vyberte ji a pak klikněte na **Podrobnosti** vedle názvu.
   Pak vyberte **Odstranit** a potvrďte výzvu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu pro správu, která bude organizovat svou hierarchii prostředků. Skupina pro správu může obsahovat předplatná nebo jiné skupiny pro správu.

Pokud chcete získat další informace o skupinách pro správu a o tom, jak spravovat hierarchii prostředků, pokračujte tady:

> [!div class="nextstepaction"]
> [Správa prostředků pomocí skupin pro správu](./manage.md)
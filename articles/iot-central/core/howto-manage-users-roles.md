---
title: Správa uživatelů a rolí v Azure IoT Central aplikaci | Microsoft Docs
description: Jak spravovat uživatele a role v aplikaci Azure IoT Central jako správce
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: f6c45b8d9804f16c4e59d259f562cc03f187e6a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92122973"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Správa uživatelů a rolí v aplikaci IoT Central

Tento článek popisuje, jak jako správce můžete přidávat, upravovat a odstraňovat uživatele v aplikaci Azure IoT Central. Tento článek také popisuje, jak spravovat role v aplikaci Azure IoT Central.

Pokud chcete získat přístup k části **Správa** a používat ji, musíte být v roli **správce** aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central Azure, automaticky se přiřadíte do role **správce** této aplikace.

## <a name="add-users"></a>Přidání uživatelů

Každý uživatel musí mít uživatelský účet, aby se mohl přihlásit a získat přístup k aplikaci Azure IoT Central. Účty Microsoft a účty Azure Active Directory jsou podporované ve službě Azure IoT Central. Azure Active Directory skupiny se v Azure IoT Central aktuálně nepodporují.

Další informace najdete v tématu [účet Microsoft nápovědu](https://support.microsoft.com/products/microsoft-account?category=manage-account) a  [rychlé zprovoznění: přidání nových uživatelů do Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md).

1. Chcete-li přidat uživatele do aplikace IoT Central, v části **Správa** klikněte na stránku **Uživatelé** .
    
    > [!div class="mx-imgBorder"]
    >![Správa uživatelů](media/howto-manage-users-roles/manage-users-pnp.png)

1. Chcete-li přidat uživatele, klikněte na stránce **Uživatelé** na příkaz **+ Přidat uživatele**.

1. V rozevírací nabídce **role** vyberte roli pro uživatele. Další informace o rolích najdete v části [Správa rolí](#manage-roles) v tomto článku.

    > [!div class="mx-imgBorder"]
    >![Přidat uživatele a vybrat roli](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Uživatel, který je ve vlastní roli, která jim uděluje oprávnění k přidávání dalších uživatelů, může přidat uživatele do role se stejnými nebo méně oprávněními než jejich vlastní role.

Pokud se ID uživatele IoT Central z Azure Active Directory odstraní a pak se znovu přidá, uživatel se nebude moct přihlásit k aplikaci IoT Central. Chcete-li znovu povolit přístup, správce IoT Central by měl uživatele v aplikaci odstranit a číst.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Úprava rolí, které jsou přiřazeny uživatelům

Role se po přiřazení nedají změnit. Chcete-li změnit roli přiřazenou uživateli, odstraňte uživatele a pak znovu přidejte uživatele s jinou rolí.

> [!NOTE]
> Přiřazené role jsou specifické pro IoT Central aplikaci a nejde je spravovat z webu Azure Portal.

## <a name="delete-users"></a>Odstranění uživatelů

Pokud chcete uživatele odstranit, zaškrtněte jedno nebo více políček na stránce **Uživatelé** . Vyberte **Odstranit**.

## <a name="manage-roles"></a>Spravovat role

Role umožňují řídit, kdo ve vaší organizaci může provádět různé úkoly v IoT Central. Existují tři předdefinované role, které můžete přiřadit uživatelům vaší aplikace. Pokud vyžadujete jemnější kontrolu, můžete také [vytvořit vlastní role](#create-a-custom-role) .

> [!div class="mx-imgBorder"]
> ![Spravovat výběr rolí](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Správce

Uživatelé v roli **správce** můžou spravovat a řídit každou část aplikace, včetně fakturace.

Uživatel, který vytváří aplikaci, je automaticky přiřazen k roli **správce** . V roli **správce** musí být vždy alespoň jeden uživatel.

### <a name="builder"></a>Tvůrce

Uživatelé v roli **Tvůrce** můžou spravovat každou část aplikace, ale nemůžou dělat změny na kartách pro správu nebo průběžné exportování dat.

### <a name="operator"></a>Operátor

Uživatelé v roli **operátora** mohou monitorovat stav a stav zařízení. Neumožňují provádět změny v šablonách zařízení nebo spravovat aplikace. Operátoři můžou přidávat a odstraňovat zařízení, spravovat sady zařízení a spouštět analýzy a úlohy. 

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pokud vaše řešení vyžaduje jemnější řízení přístupu, můžete vytvořit vlastní role s vlastními sadami oprávnění. Chcete-li vytvořit vlastní roli, přejděte na stránku **role** v části **Správa** vaší aplikace. Pak vyberte **+ Nová role** a přidejte název a popis pro vaši roli. Vyberte oprávnění, které vaše role vyžaduje, a pak vyberte **Uložit**.

Do vlastní role můžete přidat uživatele stejným způsobem, jakým přidáváte uživatele do předdefinované role.

> [!div class="mx-imgBorder"]
> ![Vytvoření vlastní role](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Možnosti vlastní role

Při definování vlastní role zvolíte sadu oprávnění, které uživatel udělí, pokud se jedná o člena této role. Některá oprávnění jsou závislá na ostatních. Pokud například přidáte do role oprávnění **řídicí panely aplikace aktualizace** , bude automaticky přidáno oprávnění **zobrazit řídicí panely aplikace** . Následující tabulky shrnují dostupná oprávnění a jejich závislosti, které můžete použít při vytváření vlastních rolí.

#### <a name="managing-devices"></a>Správa zařízení

**Oprávnění pro šablony zařízení**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Spravovat | Zobrazení <br/> Další závislosti: zobrazení instancí zařízení  |
| Úplné řízení | Zobrazit, spravovat <br/> Další závislosti: zobrazení instancí zařízení |

**Oprávnění instance zařízení**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Vytvořit | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Odstranit | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Spustit příkazy | Aktualizace, zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit, spustit příkazy <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |

**Oprávnění skupin zařízení**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení   |
| Vytvořit | Zobrazit, aktualizovat <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení   |
| Odstranit | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení |

**Oprávnění pro správu připojení zařízení**

| Name | Závislosti |
| ---- | -------- |
| Čtení instance | Žádné <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, instancí zařízení |
| Spravovat instanci | Žádné |
| Číst globální | Žádné   |
| Spravovat globální | Číst globální |
| Úplné řízení | Čtení instance, Správa instance, čtení globálních prostředků, Správa globálních prostředků. <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, instancí zařízení |

**Oprávnění úloh**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Vytvořit | Zobrazit, aktualizovat <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Odstranit | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Spuštěním | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení; Aktualizace instancí zařízení; Spouštění příkazů na instancích zařízení |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit, spustit <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení; Aktualizace instancí zařízení; Spouštění příkazů na instancích zařízení |

**Oprávnění pravidel**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné <br/> Další závislosti: zobrazení šablon zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení |
| Vytvořit | Zobrazit, aktualizovat <br/> Další závislosti: zobrazení šablon zařízení |
| Odstranit | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit <br/> Další závislosti: zobrazení šablon zařízení |

#### <a name="managing-the-app"></a>Správa aplikace

**Oprávnění nastavení aplikace**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Aktualizace | Zobrazení   |
| Kopírovat | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení, skupin zařízení, řídicích panelů, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |
| Odstranit | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, kopírovat, odstranit <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, řídicích panelů aplikací, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |

**Oprávnění k exportu šablony aplikace**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Export | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení, skupin zařízení, řídicích panelů, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |
| Úplné řízení | Zobrazit, exportovat <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, řídicích panelů aplikací, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |

**Fakturační oprávnění**

| Name | Závislosti |
| ---- | -------- |
| Spravovat | Žádné     |
| Úplné řízení | Spravovat |

#### <a name="managing-users-and-roles"></a>Správa uživatelů a rolí

**Oprávnění vlastních rolí**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné |
| Aktualizace | Zobrazení |
| Vytvořit | Zobrazit, aktualizovat |
| Odstranit | Zobrazení |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění správy uživatelů**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné <br/> Další závislosti: zobrazení vlastních rolí |
| Přidání | Zobrazení <br/> Další závislosti: zobrazení vlastních rolí |
| Odstranit | Zobrazení <br/> Další závislosti: zobrazení vlastních rolí |
| Úplné řízení | Zobrazit, přidat, odstranit <br/> Další závislosti: zobrazení vlastních rolí |

> [!NOTE]
> Uživatel, který je ve vlastní roli, která jim uděluje oprávnění k přidávání dalších uživatelů, může přidat uživatele do role se stejnými nebo méně oprávněními než jejich vlastní role.

#### <a name="customizing-the-app"></a>Přizpůsobení aplikace

**Oprávnění řídicího panelu aplikace**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Aktualizace | Zobrazení   |
| Vytvořit | Zobrazit, aktualizovat |
| Odstranit | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění osobních řídicích panelů**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Aktualizace | Zobrazení   |
| Vytvořit | Zobrazit, aktualizovat   |
| Odstranit | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění brandingu, favicon a barev**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Aktualizace | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat |

**Oprávnění k odkazům na odkazy**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Aktualizace | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat |

#### <a name="extending-the-app"></a>Rozšíření aplikace

**Oprávnění k exportu dat**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Aktualizace | Zobrazení   |
| Vytvořit | Zobrazit, aktualizovat  |
| Odstranit | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění tokenu API**

| Name | Závislosti |
| ---- | -------- |
| Zobrazení | Žádné     |
| Vytvořit | Zobrazení   |
| Odstranit | Zobrazení   |
| Úplné řízení | Zobrazit, vytvořit, odstranit |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat uživatele a role v aplikaci IoT Central Azure, je doporučeným dalším krokem informace o tom, jak [spravovat vaše vyúčtování](howto-view-bill.md).
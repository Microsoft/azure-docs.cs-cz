---
title: Správa uživatelů a rolí v Azure IoT Central aplikaci | Microsoft Docs
description: Jak spravovat uživatele a role v aplikaci Azure IoT Central jako správce
author: lmasieri
ms.author: lmasieri
ms.date: 10/22/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 603bc4ad39a472f2ebc786257bc7c625e3f6b9a4
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73895745"
---
# <a name="manage-users-and-roles-in-your-iot-central-application-preview-features"></a>Správa uživatelů a rolí v aplikaci IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako správce můžete přidávat, upravovat a odstraňovat uživatele v aplikaci Azure IoT Central. Tento článek také popisuje, jak spravovat role v aplikaci Azure IoT Central.

Pokud chcete získat přístup k části **Správa** a používat ji, musíte být v roli **správce** aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central Azure, automaticky se přiřadíte do role **správce** této aplikace.

## <a name="add-users"></a>Přidání uživatelů

Každý uživatel musí mít uživatelský účet, aby se mohl přihlásit a získat přístup k aplikaci Azure IoT Central. Účty Microsoft (účty spravované služby) a Azure Active Directory (Azure AD) se v Azure IoT Central podporují. Azure Active Directory skupiny se v Azure IoT Central aktuálně nepodporují.

Další informace najdete v tématu [účet Microsoft nápovědu](https://support.microsoft.com/products/microsoft-account?category=manage-account) a [rychlé zprovoznění: přidání nových uživatelů do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Chcete-li přidat uživatele do aplikace IoT Central, v části **Správa** klikněte na stránku **Uživatelé** .
    
    > [!div class="mx-imgBorder"]
    >![Správa uživatelů](media/howto-manage-users-roles/manage-users-pnp.png)

1. Chcete-li přidat uživatele, klikněte na stránce **Uživatelé** na příkaz **+ Přidat uživatele**.

1. V rozevírací nabídce **role** vyberte roli pro uživatele. Další informace o rolích najdete v části [Správa rolí](#manage-roles) v tomto článku.

    > [!div class="mx-imgBorder"]
    >![přidat uživatele a vybrat roli](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Uživatel, který je ve vlastní roli, která jim uděluje oprávnění k přidávání dalších uživatelů, může přidat uživatele do role se stejnými nebo méně oprávněními než jejich vlastní role.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Úprava rolí, které jsou přiřazeny uživatelům

Role se po přiřazení nedají změnit. Chcete-li změnit roli přiřazenou uživateli, odstraňte uživatele a pak znovu přidejte uživatele s jinou rolí.

> [!NOTE]
> Přiřazené role jsou specifické pro IoT Central aplikaci a nejde je spravovat z webu Azure Portal.

## <a name="delete-users"></a>Odstraňování uživatelů

Pokud chcete uživatele odstranit, zaškrtněte jedno nebo více políček na stránce **Uživatelé** . Vyberte **Odstranit**.

## <a name="manage-roles"></a>Správa rolí

Role umožňují řídit, kdo ve vaší organizaci může provádět různé úkoly v IoT Central. Existují tři předdefinované role, které můžete přiřadit uživatelům vaší aplikace. Pokud vyžadujete jemnější kontrolu, můžete také [vytvořit vlastní role](#create-a-custom-role) .

> [!div class="mx-imgBorder"]
> ![Správa výběru rolí](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Správce

Uživatelé v roli **správce** můžou spravovat a řídit každou část aplikace, včetně fakturace.

Uživatel, který vytváří aplikaci, je automaticky přiřazen k roli **správce** . V roli **správce** musí být vždy alespoň jeden uživatel.

### <a name="builder"></a>Tvůrce

Uživatelé v roli **Tvůrce** můžou spravovat každou část aplikace, ale nemůžou dělat změny na kartách pro správu nebo průběžné exportování dat.

### <a name="operator"></a>Operátor

Uživatelé v roli **operátora** mohou monitorovat stav a stav zařízení. Neumožňují provádět změny v šablonách zařízení nebo spravovat aplikace. Operátoři můžou přidávat a odstraňovat zařízení, spravovat sady zařízení a spouštět analýzy a úlohy. 

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pokud vaše řešení vyžaduje jemnější řízení přístupu, můžete vytvořit vlastní role s vlastními sadami oprávnění. Chcete-li vytvořit vlastní roli, přejděte na stránku **role** v části **Správa** vaší aplikace. Pak vyberte **+ Nová role**a přidejte název a popis pro vaši roli. Vyberte oprávnění, které vaše role vyžaduje, a pak vyberte **Uložit**.

Do vlastní role můžete přidat uživatele stejným způsobem, jakým přidáváte uživatele do předdefinované role.

> [!div class="mx-imgBorder"]
> ![vytvořit vlastní roli](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Možnosti vlastní role

Při definování vlastní role zvolíte sadu oprávnění, které uživatel udělí, pokud se jedná o člena této role. Některá oprávnění jsou závislá na ostatních. Pokud například přidáte do role oprávnění **řídicí panely aplikace aktualizace** , bude automaticky přidáno oprávnění **zobrazit řídicí panely aplikace** . Následující tabulky shrnují dostupná oprávnění a jejich závislosti, které můžete použít při vytváření vlastních rolí.

#### <a name="managing-devices"></a>Správa zařízení

**Oprávnění pro šablony zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Spravovat | Zobrazení <br/> Další závislosti: zobrazení instancí zařízení  |
| Úplné řízení | Zobrazit, spravovat <br/> Další závislosti: zobrazení instancí zařízení |

**Oprávnění instance zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Vytvoření | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Odstranění | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Spustit příkazy | Aktualizace, zobrazení <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit, spustit příkazy <br/> Další závislosti: zobrazení šablon zařízení a skupin zařízení  |

**Oprávnění skupin zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení   |
| Vytvoření | Zobrazit, aktualizovat <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení   |
| Odstranění | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit <br/> Další závislosti: zobrazení šablon zařízení a instancí zařízení |

**Oprávnění pro správu připojení zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Čtení instance | Žádný <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, instancí zařízení |
| Správa Instnace | Žádný |
| Číst globální | Žádný   |
| Spravovat globální | Číst globální |
| Úplné řízení | Čtení instance, Správa instance, čtení globálních prostředků, Správa globálních prostředků. <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, instancí zařízení |

**Oprávnění úloh**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Vytvoření | Zobrazit, aktualizovat <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Odstranění | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Spuštění | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení; Aktualizace instancí zařízení; Spouštění příkazů na instancích zařízení |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit, spustit <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení a skupin zařízení; Aktualizace instancí zařízení; Spouštění příkazů na instancích zařízení |

**Oprávnění pravidel**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: zobrazení šablon zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení |
| Vytvoření | Zobrazit, aktualizovat <br/> Další závislosti: zobrazení šablon zařízení |
| Odstranění | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit <br/> Další závislosti: zobrazení šablon zařízení |

#### <a name="managing-the-app"></a>Správa aplikace

**Oprávnění nastavení aplikace**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Kopírovat | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení, skupin zařízení, řídicích panelů, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, kopírovat, odstranit <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, řídicích panelů aplikací, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |

**Oprávnění k exportu šablony aplikace**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Export | Zobrazení <br/> Další závislosti: zobrazení šablon zařízení, instancí zařízení, skupin zařízení, řídicích panelů, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |
| Úplné řízení | Zobrazit, exportovat <br/> Další závislosti: zobrazení šablon zařízení, skupin zařízení, řídicích panelů aplikací, exportu dat, brandingování, odkazů na nápovědě, vlastních rolí, pravidel |

**Fakturační oprávnění**

| Name (Název) | Závislosti |
| ---- | -------- |
| Spravovat | Žádný     |
| Úplné řízení | Spravovat |

#### <a name="managing-users-and-roles"></a>Správa uživatelů a rolí

**Oprávnění vlastních rolí**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný |
| Aktualizace | Zobrazení |
| Vytvoření | Zobrazit, aktualizovat |
| Odstranění | Zobrazení |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění správy uživatelů**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: zobrazení vlastních rolí |
| Přidat | Zobrazení <br/> Další závislosti: zobrazení vlastních rolí |
| Odstranění | Zobrazení <br/> Další závislosti: zobrazení vlastních rolí |
| Úplné řízení | Zobrazit, přidat, odstranit <br/> Další závislosti: zobrazení vlastních rolí |

> [!NOTE]
> Uživatel, který je ve vlastní roli, která jim uděluje oprávnění k přidávání dalších uživatelů, může přidat uživatele do role se stejnými nebo méně oprávněními než jejich vlastní role.

#### <a name="customizing-the-app"></a>Přizpůsobení aplikace

**Oprávnění řídicího panelu aplikace**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Vytvoření | Zobrazit, aktualizovat |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění osobních řídicích panelů**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Vytvoření | Zobrazit, aktualizovat   |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění brandingu, favicon a barev**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat |

**Oprávnění k odkazům na odkazy**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat |

#### <a name="extending-the-app"></a>Rozšíření aplikace

**Oprávnění k exportu dat**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Vytvoření | Zobrazit, aktualizovat  |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit |

**Oprávnění tokenu API**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Vytvoření | Zobrazení   |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, vytvořit, odstranit |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat uživatele a role v aplikaci IoT Central Azure, je doporučeným dalším krokem informace o tom, jak [spravovat vaše vyúčtování](howto-view-bill.md).

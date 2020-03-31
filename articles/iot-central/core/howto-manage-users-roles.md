---
title: Správa uživatelů a rolí v aplikaci Azure IoT Central | Dokumenty společnosti Microsoft
description: Jak spravovat uživatele a role ve vaší aplikaci Azure IoT Central jako správce
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: c00f9d8baa55ef0d0cf6322ee71f22e739e6acdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365508"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Správa uživatelů a rolí v aplikaci IoT Central

Tento článek popisuje, jak jako správce můžete přidávat, upravovat a odstraňovat uživatele v aplikaci Azure IoT Central. Článek také popisuje, jak spravovat role ve vaší aplikaci Azure IoT Central.

Chcete-li získat přístup a používat část **Správa,** musíte být v roli **správce** pro aplikaci Azure IoT Central. Pokud vytvoříte aplikaci Azure IoT Central, automaticky se přidáte do role **správce** pro tuto aplikaci.

## <a name="add-users"></a>Přidání uživatelů

Každý uživatel musí mít uživatelský účet, než se bude moci přihlásit a přistupovat k aplikaci Azure IoT Central. Účty Microsoft a účty Azure Active Directory jsou podporované v Azure IoT Central. Skupiny Azure Active Directory nejsou aktuálně podporované v Azure IoT Central.

Další informace najdete v [tématu Nápověda k účtu Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) a [úvodní příručka: Přidání nových uživatelů do služby Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Pokud chcete přidat uživatele do aplikace IoT Central, přejděte na stránku **Uživatelé** v části **Správa.**
    
    > [!div class="mx-imgBorder"]
    >![Správa uživatelů](media/howto-manage-users-roles/manage-users-pnp.png)

1. Chcete-li přidat uživatele, zvolte na stránce **Uživatelé** **možnost + Přidat uživatele**.

1. Zrozené nabídky **Role** zvolte roli pro uživatele. Další informace o rolích najdete v tomto článku v části [Spravovat role.](#manage-roles)

    > [!div class="mx-imgBorder"]
    >![Přidání uživatele a výběr role](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Uživatel, který je ve vlastní roli, která jim uděluje oprávnění k přidávání dalších uživatelů, může přidávat uživatele pouze do role se stejnými nebo menšími oprávněními než jejich vlastní role.

Pokud id uživatele IoT Central se odstraní z Azure Active Directory a pak znovu, uživatel nebude moct přihlásit do aplikace IoT Central. Chcete-li znovu povolit přístup, správce IoT Central by měl odstranit a přečíst uživatele v aplikaci.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Úpravy rolí přiřazených uživatelům

Role nelze po přiřazení změnit. Chcete-li změnit roli přiřazenou uživateli, odstraňte uživatele a pak znovu přidejte uživatele s jinou rolí.

> [!NOTE]
> Přiřazené role jsou specifické pro aplikaci IoT Central a nelze je spravovat z webu Azure Portal.

## <a name="delete-users"></a>Odstranění uživatelů

Chcete-li odstranit uživatele, zaškrtněte jedno nebo více zaškrtávacích políček na stránce **Uživatelé.** Vyberte **Odstranit**.

## <a name="manage-roles"></a>Správa rolí

Role umožňují řídit, kdo ve vaší organizaci může provádět různé úkoly v ioT centru. Existují tři předdefinované role, které můžete přiřadit uživatelům vaší aplikace. Můžete také [vytvořit vlastní role,](#create-a-custom-role) pokud požadujete jemnější odstupňovanou kontrolu.

> [!div class="mx-imgBorder"]
> ![Správa výběru rolí](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Správce

Uživatelé v roli **Správce** mohou spravovat a řídit každou část aplikace, včetně fakturace.

Uživateli, který vytvoří aplikaci, je automaticky přiřazena role **Správce.** V roli **správce** musí být vždy alespoň jeden uživatel.

### <a name="builder"></a>Tvůrce

Uživatelé v roli **tvůrce** můžou spravovat každou část aplikace, ale nemůžou provádět změny na kartách Správa nebo Průběžný export dat.

### <a name="operator"></a>Operátor

Uživatelé v roli **Operátor** mohou sledovat stav a stav zařízení. Není povoleno provádět změny v šablonách zařízení nebo spravovat aplikaci. Operátoři můžou přidávat a odstraňovat zařízení, spravovat sady zařízení a spouštět analýzy a úlohy. 

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Pokud vaše řešení vyžaduje podrobnější ovládací prvky přístupu, můžete vytvořit vlastní role s vlastními sadami oprávnění. Chcete-li vytvořit vlastní roli, přejděte na stránku **Role** v části **Správa** aplikace. Pak vyberte **+ Nová role**a přidejte název a popis pro vaši roli. Vyberte oprávnění, která vaše role vyžaduje, a pak vyberte **Uložit**.

Do vlastní role můžete přidávat uživatele stejným způsobem, jakým přidáváte uživatele do předdefinované role.

> [!div class="mx-imgBorder"]
> ![Vytvoření vlastní role](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Možnosti vlastní role

Když definujete vlastní roli, zvolíte sadu oprávnění, která je uživateli udělena, pokud je členem role. Některá oprávnění jsou závislá na jiných. Pokud například přidáte oprávnění **řídicích panelů aplikace aktualizace** do role, automaticky se přidá oprávnění **Řídicí panely aplikace Zobrazení.** Následující tabulky shrnují dostupná oprávnění a jejich závislosti, které můžete použít při vytváření vlastních rolí.

#### <a name="managing-devices"></a>Správa zařízení

**Oprávnění předlohy zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Správa | Zobrazení <br/> Další závislosti: Zobrazení instancí zařízení  |
| Úplné řízení | Zobrazení, správa <br/> Další závislosti: Zobrazení instancí zařízení |

**Oprávnění instance zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: Zobrazení šablon zařízení a skupin zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení a skupin zařízení  |
| Vytvořit | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení a skupin zařízení  |
| Odstranění | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení a skupin zařízení  |
| Spustit příkazy | Aktualizovat, Zobrazit <br/> Další závislosti: Zobrazení šablon zařízení a skupin zařízení  |
| Úplné řízení | Zobrazit, aktualizovat, vytvořit, odstranit, spustit příkazy <br/> Další závislosti: Zobrazení šablon zařízení a skupin zařízení  |

**Oprávnění skupin zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: Zobrazení šablon zařízení a instancí zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení a instancí zařízení   |
| Vytvořit | Zobrazit, Aktualizovat <br/> Další závislosti: Zobrazení šablon zařízení a instancí zařízení   |
| Odstranění | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení a instancí zařízení   |
| Úplné řízení | Zobrazit, Aktualizovat, Vytvořit, Odstranit <br/> Další závislosti: Zobrazení šablon zařízení a instancí zařízení |

**Oprávnění pro správu připojení zařízení**

| Name (Název) | Závislosti |
| ---- | -------- |
| Read instance | Žádný <br/> Další závislosti: Zobrazení šablon zařízení, skupin zařízení, instancí zařízení |
| Správa instance | Žádný |
| Přečtěte si globální | Žádný   |
| Správa globálního | Přečtěte si globální |
| Úplné řízení | Číst instance, Spravovat instanci, Číst globální, Spravovat globální. <br/> Další závislosti: Zobrazení šablon zařízení, skupin zařízení, instancí zařízení |

**Oprávnění úloh**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Vytvořit | Zobrazit, Aktualizovat <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Odstranění | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení a skupin zařízení |
| Spuštění | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení a skupin zařízení; Aktualizovat instance zařízení; Spouštění příkazů na instancích zařízení |
| Úplné řízení | Zobrazit, Aktualizovat, Vytvořit, Odstranit, Spustit <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení a skupin zařízení; Aktualizovat instance zařízení; Spouštění příkazů na instancích zařízení |

**Oprávnění pravidel**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: Zobrazení šablon zařízení |
| Aktualizace | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení |
| Vytvořit | Zobrazit, Aktualizovat <br/> Další závislosti: Zobrazení šablon zařízení |
| Odstranění | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení |
| Úplné řízení | Zobrazit, Aktualizovat, Vytvořit, Odstranit <br/> Další závislosti: Zobrazení šablon zařízení |

#### <a name="managing-the-app"></a>Správa aplikace

**Oprávnění k nastavení aplikace**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Kopírovat | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení, skupin zařízení, řídicích panelů, exportu dat, brandingu, odkazů nápovědy, vlastních rolí, pravidel |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, Aktualizovat, Kopírovat, Odstranit <br/> Další závislosti: Zobrazení šablon zařízení, skupin zařízení, řídicích panelů aplikací, exportu dat, brandingu, odkazů nápovědy, vlastních rolí, pravidel |

**Oprávnění exportu šablony aplikace**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Export | Zobrazení <br/> Další závislosti: Zobrazení šablon zařízení, instancí zařízení, skupin zařízení, řídicích panelů, exportu dat, brandingu, odkazů nápovědy, vlastních rolí, pravidel |
| Úplné řízení | Zobrazení, export <br/> Další závislosti: Zobrazení šablon zařízení, skupin zařízení, řídicích panelů aplikací, exportu dat, brandingu, odkazů nápovědy, vlastních rolí, pravidel |

**Oprávnění k fakturaci**

| Name (Název) | Závislosti |
| ---- | -------- |
| Správa | Žádný     |
| Úplné řízení | Správa |

#### <a name="managing-users-and-roles"></a>Správa uživatelů a rolí

**Oprávnění k vlastním rolím**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný |
| Aktualizace | Zobrazení |
| Vytvořit | Zobrazit, Aktualizovat |
| Odstranění | Zobrazení |
| Úplné řízení | Zobrazit, Aktualizovat, Vytvořit, Odstranit |

**Oprávnění pro správu uživatelů**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný <br/> Další závislosti: Zobrazení vlastních rolí |
| Přidat | Zobrazení <br/> Další závislosti: Zobrazení vlastních rolí |
| Odstranění | Zobrazení <br/> Další závislosti: Zobrazení vlastních rolí |
| Úplné řízení | Zobrazit, Přidat, Odstranit <br/> Další závislosti: Zobrazení vlastních rolí |

> [!NOTE]
> Uživatel, který je ve vlastní roli, která jim uděluje oprávnění k přidávání dalších uživatelů, může přidávat uživatele pouze do role se stejnými nebo menšími oprávněními než jejich vlastní role.

#### <a name="customizing-the-app"></a>Přizpůsobení aplikace

**Oprávnění řídicího panelu aplikace**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Vytvořit | Zobrazit, Aktualizovat |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, Aktualizovat, Vytvořit, Odstranit |

**Oprávnění osobních řídicích panelů**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Vytvořit | Zobrazit, Aktualizovat   |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, Aktualizovat, Vytvořit, Odstranit |

**Oprávnění k brandingu, favicon a barvám**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Úplné řízení | Zobrazit, Aktualizovat |

**Oprávnění k odkazům nápovědy**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Úplné řízení | Zobrazit, Aktualizovat |

#### <a name="extending-the-app"></a>Rozšíření aplikace

**Oprávnění k exportu dat**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Aktualizace | Zobrazení   |
| Vytvořit | Zobrazit, Aktualizovat  |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazit, Aktualizovat, Vytvořit, Odstranit |

**Oprávnění tokenů rozhraní API**

| Name (Název) | Závislosti |
| ---- | -------- |
| Zobrazení | Žádný     |
| Vytvořit | Zobrazení   |
| Odstranění | Zobrazení   |
| Úplné řízení | Zobrazení, vytvoření, odstranění |

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o tom, jak spravovat uživatele a role v aplikaci Azure IoT Central, je dalším doporučeným krokem naučit se [spravovat fakturu](howto-view-bill.md).

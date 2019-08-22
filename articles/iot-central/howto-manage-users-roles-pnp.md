---
title: Správa uživatelů a rolí v Azure IoT Central aplikaci | Microsoft Docs
description: Jak spravovat uživatele a role v aplikaci Azure IoT Central jako správce
author: v-krghan
ms.author: v-krghan
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 0c1a6b7370a4d1f8e01b22bfd52caa6cb6973947
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880641"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Správa uživatelů a rolí v aplikaci IoT Central

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Tento článek popisuje, jak jako správce můžete přidávat, upravovat a odstraňovat uživatele v aplikaci Azure IoT Central a také jak spravovat role v aplikaci Azure IoT Central.

Pokud chcete získat přístup k části **Správa** a používat ji, musíte být v roli **správce** aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central pro Azure, automaticky se přiřadíte k roli **správce** této aplikace.


## <a name="add-users"></a>Přidat uživatele

Každý uživatel musí mít uživatelský účet, aby se mohl přihlásit a získat přístup k aplikaci Azure IoT Central. Účty Microsoft (účty spravované služby) a Azure Active Directory (Azure AD) se v Azure IoT Central podporují. Azure Active Directory skupiny se v Azure IoT Central aktuálně nepodporují.

Další informace najdete v tématu [účet Microsoft nápovědě](https://support.microsoft.com/products/microsoft-account?category=manage-account) a [rychlém startu: Přidejte nové uživatele do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Chcete-li přidat uživatele do aplikace IoT Central, v části **Správa** klikněte na stránku **Uživatelé** .

    ![Seznam uživatelů](media/howto-manage-users-roles-pnp/image1.png)

1. Chcete-li přidat uživatele, klikněte na stránce **Uživatelé** na příkaz **+ Přidat uživatele**.

1. V rozevírací nabídce **role** vyberte roli pro uživatele. Další informace o rolích najdete v části [Správa rolí](#manage-roles) v tomto článku.

    ![Výběr role](media/howto-manage-users-roles-pnp/image3.png)

    > [!NOTE]
    >  Pokud chcete uživatele přidat hromadně, zadejte ID uživatelů všech uživatelů, které chcete přidat oddělené středníkem. V rozevírací nabídce **role** vyberte roli. Potom vyberte **Uložit**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Úprava rolí, které jsou přiřazeny uživatelům

Role se po přiřazení nedají změnit. Chcete-li změnit roli přiřazenou uživateli, odstraňte uživatele a pak znovu přidejte uživatele s jinou rolí.

> [!NOTE]
> Přiřazené role jsou specifické pro IoT Central aplikaci a nejde je spravovat z webu Azure Portal.

## <a name="delete-users"></a>Odstraňování uživatelů

Pokud chcete uživatele odstranit, zaškrtněte jedno nebo více políček na stránce **Uživatelé** . Vyberte **Odstranit**.

## <a name="manage-roles"></a>Správa rolí

Role umožňují řídit, kdo ve vaší organizaci může provádět různé úkoly v IoT Central. Existují tři role, které můžete přiřadit uživatelům vaší aplikace.

### <a name="administrator"></a>Správce

Uživatelé role **správce** mají přístup ke všem funkcím aplikace.

Uživatel, který vytváří aplikaci, je automaticky přiřazen k roli **správce** . V roli **správce** musí být vždy alespoň jeden uživatel.

### <a name="application-builder"></a>Tvůrce aplikací

Uživatelé v roli **Tvůrce aplikací** můžou dělat všechno v aplikaci s výjimkou správy aplikace. Tvůrci můžou vytvářet, upravovat a odstraňovat šablony zařízení a zařízení, spravovat sady zařízení a spouštět analýzy a úlohy. Tvůrci nebudou mít přístup k části **Správa** aplikace.

### <a name="application-operator"></a>Operátor aplikace

Uživatelé v roli **operátora aplikace** nemůžou dělat změny v šablonách zařízení a nemůžou aplikaci spravovat. Operátoři můžou přidávat a odstraňovat zařízení, spravovat sady zařízení a spouštět analýzy a úlohy. Operátory nebudou mít přístup k stránkám pro tvůrce a **správu** **aplikací** .

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak spravovat uživatele a role v IoT Central Azure, je doporučeným dalším krokem informace o [zobrazení vašeho vyúčtování](howto-view-bill-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) ve službě Azure IoT Central.

---
title: Přístup k řešení Azure VMware podle CloudSimple – portál
description: Popisuje, jak získat přístup k portálu VMware Solution by CloudSimple z webu Azure Portal.
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0aa7a9a1f19a9d4fb2c555b08753e0b57c657974
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895151"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Přístup k řešení VMware pomocí portálu CloudSimple z Azure Portal

Pro přístup k portálu CloudSimple je podporováno jednotné přihlašování. Po přihlášení k Azure Portal máte přístup k portálu CloudSimple bez opětovného přihlášení. Při prvním přístupu k portálu CloudSimple se zobrazí výzva k autorizaci [autorizační aplikace služby CloudSimple](#consent-to-cloudsimple-service-authorization-application) .  Autorizace je jednorázová akce.

## <a name="before-you-begin"></a>Než začnete

Uživatelé s rolemi Builtin **Owner** a **Přispěvatel** mají přístup k portálu CloudSimple.  Role musí být nakonfigurované ve skupině prostředků, ve které je nasazená služba CloudSimple.  Role je také možné nakonfigurovat v objektu služby CloudSimple.  Další informace o kontrole role najdete v článku [zobrazení přiřazení rolí](../role-based-access-control/check-access.md) . Přístup k portálu CloudSimple mají jenom uživatelé s předdefinovaným **vlastníkem** a rolemi **přispěvatele** .  Role musí být nakonfigurované v předplatném.  Další informace o kontrole role najdete v článku [zobrazení přiřazení rolí](../role-based-access-control/check-access.md) .

Pokud používáte vlastní role, role by měla mít následující operace ```Actions``` .  Další informace o vlastních rolích najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).  Pokud je některá z operací součástí ```NotActions``` , uživatel nemá přístup k portálu CloudSimple.

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

1. Vyberte **Všechny služby**.

2. Vyhledejte **služby CloudSimple Services**.

3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.

4. Na stránce **Přehled** klikněte na **Přejít na portál CloudSimple**.  Pokud k portálu CloudSimple přistupujete poprvé z Azure Portal, budete vyzváni k autorizaci [autorizační aplikace služby CloudSimple](#consent-to-cloudsimple-service-authorization-application) . 

    ![Spustit portál CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Pokud vyberete operaci privátního cloudu (například vytvoření nebo rozšíření privátního cloudu) přímo z Azure Portal, otevře se portál CloudSimple se zvýrazněnou stránkou.

Na portálu CloudSimple vyberte **Domů** v nabídce vedle sebe, aby se zobrazily souhrnné informace o vašich privátních cloudech. Zobrazí se prostředky a kapacita vašich privátních cloudů spolu s výstrahami a úlohami, které vyžadují pozornost. U běžných úloh klikněte na pojmenované ikony v horní části stránky.

![Domovská stránka](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Souhlas s aplikací autorizace služby CloudSimple

Spuštění portálu CloudSimple z Azure Portal poprvé vyžaduje souhlas s aplikací autorizace služby CloudSimple.  Vyberte **přijmout** a udělte vám požadovaná oprávnění a přístup k portálu CloudSimple.

![Souhlas s autorizací služby CloudSimple – správci](media/cloudsimple-azure-consent.png)

Pokud máte oprávnění globálního správce, můžete vyjádřit souhlas s vaší organizací.  Zaškrtněte políčko **Souhlas jménem vaší organizace**.

![Souhlas s autorizací služby CloudSimple – globální správce](media/cloudsimple-azure-consent-global-admin.png)

Pokud vaše oprávnění nepovolují přístup k portálu CloudSimple, obraťte se na globálního správce vašeho tenanta, aby udělil požadovaná oprávnění.  Globální správce může vyjádřit souhlas jménem vaší organizace.

![Souhlas s autorizací služby CloudSimple – vyžaduje správce.](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [vytvořit privátní cloud](./create-private-cloud.md)
* Informace o tom, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)

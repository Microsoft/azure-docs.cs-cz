---
title: Přístup k řešením Azure VMware (AVS) – portál
description: Popisuje, jak získat přístup k řešením Azure VMware (AVS) z Azure Portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0b0601b9d2cf505aa5b25e10875338352cab771
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015946"
---
# <a name="access-azure-vmware-solutions-avs-from-the-azure-portal"></a>Přístup k řešením Azure VMware (AVS) z Azure Portal

Pro přístup k portálu služby AVS je podporováno jednotné přihlašování. Po přihlášení k Azure Portal máte přístup k portálu pro funkci AVS bez opětovného přihlášení. Při prvním přístupu k portálu služby AVS se zobrazí výzva k autorizaci aplikace pro [autorizaci služby AVS](#consent-to-avs-service-authorization-application) . Autorizace je jednorázová akce.

## <a name="before-you-begin"></a>Než začnete

Uživatelé s rolemi Builtin **Owner** a **Přispěvatel** mají přístup k portálu AVS. Role musí být nakonfigurované ve skupině prostředků, ve které je nasazená služba AVS. Role je možné nakonfigurovat taky v objektu služby AVS. Další informace o kontrole role najdete v článku [zobrazení přiřazení rolí](https://docs.microsoft.com/azure/role-based-access-control/check-access) . Přístup k portálu funkce AVS mají pouze uživatelé s předdefinovaným **vlastníkem** a rolemi **přispěvatele** . Role musí být nakonfigurované v předplatném. Další informace o kontrole role najdete v článku [zobrazení přiřazení rolí](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

Pokud používáte vlastní role, role by měla mít v rámci ```Actions```kteroukoli z následujících operací.  Další informace o vlastních rolích najdete v tématu [vlastní role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). Pokud je některá z operací součástí ```NotActions```, uživatel nemá přístup k portálu AVS. 

```
Microsoft.VMwareCloudSimple/*
Microsoft.VMwareCloudSimple/*/write
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*
Microsoft.VMwareCloudSimple/dedicatedCloudServices/*/write
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-avs-portal"></a>Přístup k portálu pro funkci AVS

1. Vyberte **Všechny služby**.

2. Vyhledejte **služby AVS**.

3. Vyberte službu AVS, na které chcete vytvořit privátní cloud.

4. Na stránce **Přehled** klikněte na **Přejít na portál AVS**. Pokud k portálu služby AVS přistupujete poprvé z Azure Portal, budete vyzváni k autorizaci [autorizační aplikace služby AVS](#consent-to-avs-service-authorization-application) . 

    ![Spustit portál AVS](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Pokud vyberete operaci privátního cloudu (například vytvoření nebo rozšíření privátního cloudu) přímo z Azure Portal, otevře se portál AVS na označené stránce.

Na portálu služby AVS vyberte **Domů** v nabídce vedle sebe, aby se zobrazily souhrnné informace o vašem privátním cloudu služby AVS. Zobrazí se prostředky a kapacita privátního cloudu služby AVS spolu s výstrahami a úlohami, které vyžadují pozornost. U běžných úloh klikněte na pojmenované ikony v horní části stránky.

![Domovská stránka](media/cloudsimple-portal-home.png)

## <a name="consent-to-avs-service-authorization-application"></a>Souhlas s aplikací pro autorizaci služby AVS

Spuštění portálu pro službu AVS z Azure Portal poprvé vyžaduje souhlas s aplikací pro autorizaci služby AVS. Vyberte **přijmout** pro udělení požadovaných oprávnění a přístup k portálu AVS.

![Souhlas s autorizací služby AVS – správci](media/cloudsimple-azure-consent.png)

Pokud máte oprávnění globálního správce, můžete vyjádřit souhlas s vaší organizací. Vyberte **souhlas jménem vaší organizace**.

![Souhlas s autorizací služby AVS – globální správce](media/cloudsimple-azure-consent-global-admin.png)

Pokud vaše oprávnění nepovolují přístup k portálu služby AVS, obraťte se na globálního správce vašeho tenanta a udělte požadovaná oprávnění. Globální správce může vyjádřit souhlas jménem vaší organizace.

![Souhlas s autorizací služby AVS – vyžaduje správce.](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [vytvořit privátní cloud](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Informace o tom, jak [nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)

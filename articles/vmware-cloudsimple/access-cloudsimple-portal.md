---
title: Přístup k řešení Azure VMware pomocí portálu CloudSimple
description: Popisuje, jak získat přístup k portálu VMware Solution by CloudSimple z webu Azure Portal.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 91d3dd2eee6f771df23b72eae7a33122c42f1690
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869330"
---
# <a name="access-the-vmware-solution-by-cloudsimple-portal-from-the-azure-portal"></a>Přístup k řešení VMware pomocí portálu CloudSimple z portálu Azure

Jednotné přihlašování je podporováno pro přístup k portálu CloudSimple. Po přihlášení k portálu Azure, můžete přistupovat k portálu CloudSimple bez přihlášení znovu. Při prvním přístupu na portál CloudSimple budete vyzváni k autorizaci aplikace [CloudSimple Service Authorization.](#consent-to-cloudsimple-service-authorization-application)  Autorizace je jednorázová akce.

## <a name="before-you-begin"></a>Před zahájením

Uživatelé s integrovanými rolemi **vlastníka** a **přispěvatele** mají přístup k portálu CloudSimple.  Role musí být nakonfigurovány ve skupině prostředků, kde se nasadí služba CloudSimple.  Role lze také nakonfigurovat na objektu služby CloudSimple.  Další informace o kontrole role najdete v [článku Zobrazení přiřazení rolí.](https://docs.microsoft.com/azure/role-based-access-control/check-access) K portálu CloudSimple mají přístup pouze uživatelé s předdefinovanými rolemi **vlastníka** a **přispěvatele.**  Role musí být nakonfigurovány v rámci předplatného.  Další informace o kontrole role najdete v [článku Zobrazení přiřazení rolí.](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Pokud používáte vlastní role, role by měla mít ```Actions```některou z následujících operací v části .  Další informace o vlastních rolích najdete [v tématu Vlastní role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).  Pokud některá z operací ```NotActions```je součástí aplikace , uživatel nemůže získat přístup k portálu CloudSimple.

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

2. Vyhledejte **cloudové jednoduché služby**.

3. Vyberte službu CloudSimple, na které chcete vytvořit privátní cloud.

4. Na stránce **Přehled** klikněte na **Přejít na portál CloudSimple**.  Pokud přistupujete k portálu CloudSimple z portálu Azure poprvé, budete vyzváni k autorizaci aplikace [Autorizace služby CloudSimple.](#consent-to-cloudsimple-service-authorization-application) 

    ![Spuštění portálu CloudSimple](media/launch-cloudsimple-portal.png)

> [!NOTE]
> Pokud vyberete operaci privátního cloudu (například vytvoření nebo rozšíření privátního cloudu) přímo z portálu Azure, otevře se portál CloudSimple na uvedenou stránku.

Na portálu CloudSimple vyberte **domovskou** stránku v boční nabídce, abyste zobrazili souhrnné informace o privátních cloudech. Zdroje a kapacita privátního cloudu jsou zobrazeny spolu s výstrahami a úkoly, které vyžadují pozornost. U běžných úkolů klikněte na pojmenované ikony v horní části stránky.

![Domovská stránka](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Souhlas s aplikací pro autorizaci služby CloudSimple

První spuštění portálu CloudSimple z portálu Azure vyžaduje váš souhlas s aplikací CloudSimple Service Authorization.  Chcete-li udělit požadovaná oprávnění a získat přístup k portálu CloudSimple, vyberte **možnost Přijmout.**

![Souhlas s autorizací služby CloudSimple – správci](media/cloudsimple-azure-consent.png)

Pokud máte oprávnění globálního správce, můžete souhlasit s organizací.  Zaškrtněte políčko **Souhlas jménem vaší organizace**.

![Souhlas s autorizací služby CloudSimple – globální správce](media/cloudsimple-azure-consent-global-admin.png)

Pokud vaše oprávnění neumožňují přístup k portálu CloudSimple, obraťte se na globálního správce vašeho tenanta a udělte požadovaná oprávnění.  Globální správce může souhlasit jménem vaší organizace.

![Souhlas s autorizací služby CloudSimple – vyžaduje správce](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit privátní cloud.](https://docs.microsoft.com/azure/vmware-cloudsimple/create-private-cloud/)
* Přečtěte [si, jak nakonfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md)

---
title: Řešení Azure VMware přístupu podle CloudSimple – portál
description: Popisuje, jak získat přístup k řešení VMware portálem CloudSimple z webu Azure portal
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c7bb080b350742d0722cdb4e07b82a6881ba05b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073670"
---
# <a name="accessing-the-vmware-solution-by-cloudsimple-portal-from-azure-portal"></a>Přístup k řešení VMware s CloudSimple portálu z webu Azure portal

Pro přístup k portálu CloudSimple se podporuje jednotné přihlašování. Po přihlášení k webu Azure portal můžete přistupovat na portál CloudSimple bez opakovaného přihlášení. Při prvním přístupu CloudSimple portál zobrazí výzva k autorizaci [autorizace služby CloudSimple](#consent-to-cloudsimple-service-authorization-application) aplikace.  Autorizace je jednorázová akce.

## <a name="before-you-begin"></a>Než začnete

Pouze uživatelé s builtin **vlastníka** a **Přispěvatel** role portálu CloudSimple přístup.  Role musí být nakonfigurované u daného předplatného.  Další informace o kontrole vaší role, naleznete v tématu [zobrazit přiřazení rolí](https://docs.microsoft.com/azure/role-based-access-control/check-access) článku.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Přístup k portálu CloudSimple

1. Vyberte **Všechny služby**.

2. Vyhledejte **CloudSimple služby**.

3. Vyberte službu CloudSimple, na kterém chcete vytvořit privátní Cloud.

4. Na **přehled** klikněte na **přejít na portál CloudSimple**.  Pokud přistupujete k portálu CloudSimple z portálu Azure portal poprvé, budete vyzváni k autorizaci [autorizace služby CloudSimple](#consent-to-cloudsimple-service-authorization-application) aplikace. 

    ![Spusťte portál CloudSimple](media/launch-cloudsimple-portal.png)

> [!TIP]
> Pokud vyberete operace privátního cloudu (jako je například vytvoření nebo rozšíření privátního cloudu) přímo z portálu Azure portal, CloudSimple portálu se otevře na stránce uvedené.

Na portálu CloudSimple vyberte **Domů** v nabídce na straně zobrazíte souhrnné informace o privátních Cloudů. Prostředků a kapacity privátních cloudů, zobrazují společně s výstrahami a úlohami, které vyžadují pozornost. Pro běžné úlohy klikněte na tlačítko s názvem ikony v horní části stránky.

![Domovská stránka](media/cloudsimple-portal-home.png)

## <a name="consent-to-cloudsimple-service-authorization-application"></a>Souhlas s aplikací CloudSimple autorizace služby

Spuštění portálu CloudSimple z portálu Azure portal poprvé vyžaduje váš souhlas pro aplikaci CloudSimple autorizace služby.  Vyberte **přijmout** udělit požadovaná oprávnění a přístup k portálu CloudSimple. 

![Souhlas s autorizace služby CloudSimple – správci](media/cloudsimple-azure-consent.png)

Pokud máte oprávnění globálního správce, vyjadřujete souhlas pro vaši organizaci.  Vyberte **souhlas jménem svojí organizace**.

![Souhlas s autorizace služby CloudSimple – globální správce](media/cloudsimple-azure-consent-global-admin.png)

Pokud vaše oprávnění nedovolují přístup k portálu CloudSimple, obraťte se na globálního správce vašeho tenanta udělit požadovaná oprávnění.  Globální správce může dát souhlas jménem svojí organizace.

![Souhlas s autorizace služby CloudSimple – vyžaduje správci](media/cloudsimple-azure-consent-requires-administrator.png)

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vytvoření privátního cloudu](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Zjistěte, jak [konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md)
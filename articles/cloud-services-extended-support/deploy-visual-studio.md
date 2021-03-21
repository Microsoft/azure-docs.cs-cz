---
title: Použít Cloud Services (Rozšířená podpora) (Preview)
description: Naučte se vytvářet a nasazovat cloudovou službu Azure pomocí Azure Resource Manager se sadou Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: 80aa160c53b278137467dba2afa41384c7c4f378
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722666"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Vytvoření a nasazení cloudové služby Azure (Rozšířená podpora) pomocí sady Visual Studio

Počínaje [verzí Visual Studio 2019 verze 16,9](https://visualstudio.microsoft.com/vs/preview/) (aktuálně ve verzi Preview) můžete pracovat s cloud services pomocí Azure Resource Manager (ARM), což značně zjednodušuje a modernizes údržbu a správu prostředků Azure. Tato možnost je povolená novou službou Azure, která se označuje jako Cloud Services (Rozšířená podpora). Existující cloudovou službu můžete publikovat na Cloud Services (Rozšířená podpora). Informace o této službě Azure najdete v [dokumentaci Cloud Services (Rozšířená podpora)](overview.md).

> [!IMPORTANT]
> Cloud Services (Rozšířená podpora) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registrace funkce pro vaše předplatné
Cloud Services (Rozšířená podpora) je aktuálně ve verzi Preview. Zaregistrujte funkci pro vaše předplatné následujícím způsobem:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Další informace najdete v tématu [předpoklady pro nasazení Cloud Services (Rozšířená podpora)](deploy-prerequisite.md) .

## <a name="create-a-project"></a>Vytvoření projektu

Visual Studio poskytuje šablonu projektu, která vám umožní vytvořit cloudovou službu Azure s rozšířenou podporou s názvem **Azure Cloud Service (Rozšířená podpora)**. Cloudová služba je jednoduchá služba Azure pro obecné účely. Po vytvoření projektu Visual Studio umožňuje konfigurovat, ladit a nasazovat cloudovou službu do Azure.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Vytvoření projektu cloudové služby Azure (Rozšířená podpora) v aplikaci Visual Studio

V této části se seznámíte s vytvořením projektu cloudové služby Azure v aplikaci Visual Studio s jednou nebo více webovými rolemi.

1. V okně Start vyberte možnost **vytvořit nový projekt**.

1. Do vyhledávacího pole zadejte *Cloud* a pak zvolte **Azure Cloud Service (Rozšířená podpora)**.

   ![Nová cloudová služba Azure s rozšířenou podporou](./media/choose-project-template.png)

1. Zadejte název projektu a klikněte na **vytvořit**.

   ![Zadat název projektu](./media/configure-new-project.png)

1. V dialogovém okně **nová Microsoft Azure cloudová služba** vyberte role, které chcete přidat, a zvolte tlačítko se šipkou vpravo a přidejte je do svého řešení.

    ![Vyberte nové role Azure Cloud Service.](./media/choose-roles.png)

1. Pokud chcete přejmenovat přidanou roli, najeďte na ni v dialogovém okně **nový Microsoft Azure cloudová služba** a v místní nabídce vyberte **Přejmenovat**. Můžete také přejmenovat roli v rámci řešení (v **Průzkumník řešení**) po jejím přidání.

    ![Přejmenování role cloudové služby Azure](./media/new-cloud-service-rename.png)

Projekt aplikace Visual Studio Azure obsahuje přidružení k projektům rolí v řešení. Projekt obsahuje také *soubor definice služby* a *konfigurační soubor služby*:

- **Definiční soubor služby** – definuje nastavení modulu runtime pro vaši aplikaci, včetně toho, jaké role jsou povinné, koncové body a velikost virtuálního počítače.
- **Konfigurační soubor služby** – konfiguruje, kolik instancí role se spouští, a hodnoty nastavení definované pro roli.

Další informace o těchto souborech najdete v tématu [Konfigurace rolí pro cloudovou službu Azure se sadou Visual Studio](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Publikování cloudové služby

1. Vytvořte nebo otevřete projekt cloudové služby Azure v aplikaci Visual Studio.

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a v místní nabídce vyberte **publikovat**.

   ![Přihlašovací stránka](./media/publish-step-1.png)

1. **Účet** – vyberte účet nebo v rozevíracím seznamu účet vyberte **Přidat účet** .

1. **Vyberte své předplatné** – vyberte předplatné, které chcete použít pro vaše nasazení. Předplatné, které používáte pro nasazení Cloud Services (Rozšířená podpora), musí mít přiřazené role vlastníka nebo přispěvatele prostřednictvím řízení přístupu na základě role (RBAC). Pokud vaše předplatné nemá žádnou z těchto rolí, přečtěte si téma [Postup přidání přiřazení role](../role-based-access-control/role-assignments-steps.md) , který chcete přidat, než budete pokračovat dál.

1. Kliknutím na tlačítko **Další** přejděte na stránku **Nastavení** .

   ![Společná nastavení](./media/publish-settings.png)

1. **Cloudová služba** – pomocí rozevíracího seznamu vyberte existující cloudovou službu nebo vyberte **vytvořit novou** a vytvořte cloudovou službu. Datové centrum se zobrazí v závorkách pro každou cloudovou službu. Doporučuje se, aby bylo umístění datového centra pro cloudovou službu stejné jako umístění datového centra pro účet úložiště.

   Pokud se rozhodnete vytvořit novou cloudovou službu, zobrazí se dialogové okno **vytvořit cloudovou službu (Rozšířená podpora)** . Zadejte umístění a skupinu prostředků, které chcete použít pro cloudovou službu.

   ![Vytvoření cloudové služby s rozšířenou podporou](./media/extended-support-dialog.png)

1. **Konfigurace sestavení** – vyberte buď **ladění** , nebo **vydání**.

1. **Konfigurace služby** – vyberte buď **Cloud** , nebo **místní**.

1. **Účet úložiště** – vyberte účet úložiště, který se má použít pro toto nasazení, nebo **vytvořte nový** pro vytvoření účtu úložiště. Oblast se zobrazí v závorkách pro každý účet úložiště. Doporučuje se, aby bylo umístění datového centra pro účet úložiště stejné jako umístění datového centra pro cloudovou službu (společné nastavení).

   Účet úložiště Azure ukládá balíček pro nasazení aplikace. Po nasazení aplikace se balíček odebere z účtu úložiště.

1. **Key Vault** – zadejte Key Vault, která obsahuje tajné klíče pro tuto cloudovou službu. Tato možnost je povolená, pokud je povolená Vzdálená plocha nebo se do konfigurace přidávají certifikáty.

1. **Povolit vzdálenou plochu pro všechny role** – tuto možnost vyberte, pokud chcete být schopni se vzdáleně připojit ke službě. Zobrazí se výzva k zadání přihlašovacích údajů.

   ![Nastavení vzdálené plochy](./media/remote-desktop-configuration.png)

1. Kliknutím na tlačítko **Další** přejdete na stránku **nastavení diagnostiky** .

   ![Nastavení diagnostiky](./media/diagnostics-settings.png)

   Diagnostika umožňuje řešit potíže s cloudovou službou Azure (nebo virtuálním počítačem Azure). Informace o diagnostice najdete v tématu [Konfigurace diagnostiky pro Azure Cloud Services a Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Informace o Application Insights najdete v tématu [co je Application Insights?](../azure-monitor/app/app-insights-overview.md).

1. Kliknutím na tlačítko **Další** přejděte na stránku **Souhrn** .

   ![Souhrn](./media/publish-summary.png)

1. **Cílový profil** – můžete zvolit vytvoření profilu publikování z nastavení, které jste si zvolili. Můžete například vytvořit jeden profil pro testovací prostředí a jiný pro produkční prostředí. Chcete-li uložit tento profil, klikněte na ikonu **Uložit** . Průvodce vytvoří profil a uloží jej do projektu aplikace Visual Studio. Chcete-li změnit název profilu, otevřete seznam **cílový profil** a pak zvolte možnost **Spravovat...**.

   > [!Note]
   > Profil publikování se zobrazí v Průzkumník řešení v aplikaci Visual Studio a nastavení profilu se zapisuje do souboru s příponou. azurePubxml. Nastavení jsou uložena jako atributy značek XML.

1. Jakmile nakonfigurujete všechna nastavení pro nasazení projektu, v dolní části dialogového okna vyberte **publikovat** . Stav procesu můžete sledovat v okně výstup **protokolu aktivit Azure** v aplikaci Visual Studio.

Gratulujeme! Publikovali jste projekt cloudové služby Rozšířená podpora do Azure. Chcete-li znovu publikovat se stejným nastavením, můžete znovu použít profil publikování, nebo opakováním těchto kroků vytvořit nové.

## <a name="clean-up-azure-resources"></a>Vyčištění prostředků Azure

Pokud chcete vyčistit prostředky Azure, které jste vytvořili pomocí tohoto kurzu, klikněte na [Azure Portal](https://portal.azure.com), vyberte **skupiny prostředků**, najděte a otevřete skupinu prostředků, kterou jste použili k vytvoření služby, a vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Pomocí tlačítka **Konfigurovat** na obrazovce **publikování** nastavte průběžnou integraci (CI). Další informace najdete v [dokumentaci Azure Pipelines](/azure/devops/pipelines).
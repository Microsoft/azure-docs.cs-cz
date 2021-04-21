---
title: Instalace místní brány dat pro Azure Analysis Services | Microsoft Docs
description: Přečtěte si, jak nainstalovat a nakonfigurovat místní bránu dat pro připojení k místním zdrojům dat ze serveru Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43e5b64d06a6ec145876798b2e0da6499ab94bfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769224"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalace a konfigurace místní brány dat

Místní brána dat se vyžaduje v případě, že se nejmíň jeden Azure Analysis Services Server ve stejné oblasti připojí k místním zdrojům dat.  I když je brána, kterou instalujete, stejná jako pro jiné služby, jako je Power BI, Power Apps a Logic Apps, při instalaci pro Azure Analysis Services je potřeba provést několik dalších kroků. Tento článek instalace je určený pro **Azure Analysis Services**. 

Další informace o tom, jak Azure Analysis Services funguje s bránou, najdete v tématu [připojení k místním zdrojům dat](analysis-services-gateway.md). Další informace o rozšířených scénářích instalace a bráně obecně najdete v [dokumentaci k místní bráně dat](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Požadavky

**Minimální požadavky:**

* .NET 4.5 Framework
* 64-bitová verze systému Windows 8/Windows Server 2012 R2 (nebo novější)

**Doporučil**

* Procesor s 8 jádry
* Paměť 8 GB
* 64-bitová verze systému Windows 8/Windows Server 2012 R2 (nebo novější)

**Důležité informace:**

* Během instalace se při registraci brány v Azure vybere výchozí oblast pro vaše předplatné. Můžete zvolit jiné předplatné a oblast. Pokud máte servery ve více než jedné oblasti, musíte bránu nainstalovat pro každou oblast. 
* Bránu nejde nainstalovat na řadič domény.
* V jednom počítači může být nainstalovaná jenom jedna brána.
* Bránu nainstalujte do počítače, který zůstane zapnutý a nepřejde do režimu spánku.
* Bránu neinstalujte na počítač, který má jenom bezdrátové připojení k vaší síti. Výkon může být snížený.
* Když instalujete bránu, uživatelský účet, ke kterému jste se přihlásili, musí mít oprávnění přihlásit se jako služba. Po dokončení instalace použije služba místní brány dat účet NT SERVICE\PBIEgwService k přihlášení jako službu. Během instalace nebo po dokončení instalace je možné zadat jiný účet. Ujistěte se, že nastavení Zásady skupiny umožňují účtu, ke kterému jste se přihlásili při instalaci, a účet služby, který jste zvolili, oprávnění přihlásit se jako služba.
* Přihlaste se k Azure pomocí účtu ve službě Azure AD pro stejného [tenanta](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) jako předplatné, ve kterém bránu zaregistrujete. Při instalaci a registraci brány se nepodporují účty Azure B2B (Guest).
* Pokud jsou zdroje dat na Virtual Network Azure (VNet), musíte nakonfigurovat vlastnost serveru [AlwaysUseGateway](analysis-services-vnet-gateway.md) .

## <a name="download"></a>Stáhnout

 [Stažení brány](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Instalace

1. Spusťte instalační program.

2. Vyberte **místní bránu dat**.

   ![Vyberte](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Vyberte umístění, přijměte podmínky a klikněte na **nainstalovat**.

   ![Umístění instalace a licenčních podmínek](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Přihlaste se k Azure. Účet musí být ve Azure Active Directory vašeho tenanta. Tento účet se používá pro Správce brány. Při instalaci a registraci brány se nepodporují účty Azure B2B (Guest).

   ![Přihlášení k Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Pokud se přihlásíte pomocí účtu domény, namapuje se na účet vaší organizace ve službě Azure AD. Účet vaší organizace se používá jako Správce brány.

## <a name="register"></a>Registrovat

Aby bylo možné vytvořit prostředek brány v Azure, musíte zaregistrovat místní instanci, kterou jste nainstalovali s cloudovou službou brány. 

1.  Vyberte možnost **zaregistrovat novou bránu na tomto počítači**.

    ![Snímek obrazovky, který zvýrazní novou bránu na tomto počítači.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Zadejte název a obnovovací klíč pro bránu. Ve výchozím nastavení používá brána výchozí oblast vašeho předplatného. Pokud potřebujete vybrat jinou oblast, vyberte **změnit oblast**.

    > [!IMPORTANT]
    > Uložte si obnovovací klíč na bezpečné místo. Obnovovací klíč se vyžaduje v rámci převzetí, migrace nebo obnovení brány. 

   ![Registrovat](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Vytvoření prostředku brány Azure

Po instalaci a registraci brány musíte vytvořit prostředek brány v Azure. Přihlaste se k Azure pomocí stejného účtu, který jste použili při registraci brány.

1. V Azure Portal klikněte na **vytvořit prostředek**, vyhledejte místní **bránu dat** a pak klikněte na **vytvořit**.

   ![Vytvoření prostředku brány](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. V možnosti **vytvořit bránu připojení** zadejte tato nastavení:

   * **Název**: zadejte název prostředku brány. 

   * **Předplatné**: vyberte předplatné Azure, které chcete přidružit k vašemu prostředku brány. 
   
     Výchozí předplatné vychází z účtu Azure, který jste použili k přihlášení.

   * **Skupina prostředků**: Vytvořte skupinu prostředků, nebo vyberte existující.

   * **Umístění**: Vyberte oblast, ve které jste zaregistrováni bránu.

   * **Název instalace**: Pokud vaše instalace brány ještě není vybraná, vyberte bránu, kterou jste nainstalovali v počítači a zaregistrujte se. 

     Až skončíte, klikněte na **vytvořit**.

## <a name="connect-gateway-resource-to-server"></a>Připojit prostředek brány k serveru

> [!NOTE]
> Připojení k prostředku brány v jiném předplatném ze serveru není na portálu podporované, ale podporuje se pomocí PowerShellu.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V přehledu serveru Azure Analysis Services klikněte na **místní bránu dat**.

   ![Připojení serveru k bráně](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. V **nabídce vyberte místní bránu dat, kterou chcete připojit**, vyberte prostředek brány a pak klikněte na **připojit vybranou bránu**.

   ![Připojit server k prostředku brány](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Pokud se vaše brána v seznamu nezobrazí, server pravděpodobně není ve stejné oblasti jako oblast, kterou jste zadali při registraci brány.

    Po úspěšném připojení mezi serverem a prostředkem brány se zobrazí stav **připojeno**.


    ![Připojení serveru k úspěšnému dokončení prostředku brány](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K získání brány ResourceID použijte [příkaz Get-AzResource](/powershell/module/az.resources/get-azresource) . Pak připojte prostředek brány k existujícímu nebo novému serveru zadáním parametru **-GatewayResourceID** v [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) nebo [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver).

Postup získání ID prostředku brány:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Konfigurace existujícího serveru:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

Hotovo. Pokud potřebujete otevřít porty nebo provést nějaké řešení potíží, zkontrolujte [místní bránu dat](analysis-services-gateway.md).

## <a name="next-steps"></a>Další kroky

* [Správa služby Analysis Services](analysis-services-manage.md)   
* [Získání dat ze služby Azure Analysis Services](analysis-services-connect.md)   
* [Použití brány pro zdroje dat ve službě Azure Virtual Network](analysis-services-vnet-gateway.md)

---
title: Instalace místní brány dat pro Službu Azure Analysis Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak nainstalovat a nakonfigurovat místní datovou bránu pro připojení k místním zdrojům dat ze serveru Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6218b32fb9574adf62384d2a6ee5a62f3788de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062145"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Instalace a konfigurace místní brány dat

Místní brána dat je vyžadována, když se jeden nebo více serverů Azure Analysis Services ve stejné oblasti připojí k místním zdrojům dat.  Zatímco brána, kterou nainstalujete, je stejná jako u jiných služeb, jako jsou Power BI, Power Apps a Logic Apps, při instalaci pro Azure Analysis Services, je třeba provést některé další kroky. Tento článek o instalaci je specifický pro **Službu Azure Analysis Services**. 

Další informace o tom, jak Služba Azure Analysis Services funguje s bránou, najdete v [tématu Připojení k místním zdrojům dat](analysis-services-gateway.md). Další informace o pokročilých scénářích instalace a bráně obecně naleznete v [dokumentaci k místním datovým bránám](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Požadavky

**Minimální požadavky:**

* .NET 4.5 Framework
* 64bitová verze Windows 8 / Windows Server 2012 R2 (nebo novější)

**Doporučené:**

* Procesor s 8 jádry
* Paměť 8 GB
* 64bitová verze Windows 8 / Windows Server 2012 R2 (nebo novější)

**Důležité aspekty:**

* Během instalace při registraci brány s Azure je vybrána výchozí oblast pro vaše předplatné. Můžete zvolit jiné předplatné a oblast. Pokud máte servery ve více než jedné oblasti, je nutné nainstalovat bránu pro každou oblast. 
* Bránu nelze nainstalovat do řadiče domény.
* V jednom počítači lze nainstalovat pouze jednu bránu.
* Nainstalujte bránu do počítače, který zůstane zapnutý a nepřejde do režimu spánku.
* Bránu neinstalujte do počítače, který má pouze bezdrátové připojení k síti. Výkon může být snížen.
* Při instalaci brány musí mít uživatelský účet, pomocí kterých jste přihlášeni k počítači, oprávnění Přihlásit se jako oprávnění služby. Po dokončení instalace používá služba Místní brána dat účet NT SERVICE\PBIEgwService k přihlášení jako služba. Jiný účet lze zadat během instalace nebo ve službách po dokončení instalace. Zajistěte, aby nastavení zásad skupiny umožňovalo jak účet, ke kterým jste přihlášeni při instalaci, tak účet služby, který zvolíte, mají oprávnění Přihlásit se jako servisní služby.
* Přihlaste se k Azure pomocí účtu ve službě Azure AD pro stejného [klienta](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) jako předplatné, ve které registrujete bránu. Azure B2B (host) účty nejsou podporované při instalaci a registraci brány.
* Pokud jsou zdroje dat ve virtuální síti Azure (Virtuální síť), musíte nakonfigurovat vlastnost serveru [AlwaysUseGateway.](analysis-services-vnet-gateway.md)

## <a name="download"></a><a name="download"></a>Stáhnout

 [Stažení brány](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a><a name="install"></a>Instalace

1. Spusťte nastavení.

2. Vyberte **Místní brána dat**.

   ![Vyberte](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Vyberte umístění, přijměte podmínky a klepněte na tlačítko **Instalovat**.

   ![Instalace umístění a licenčních podmínek](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Přihlaste se k Azure. Účet musí být ve službě Azure Active Directory vašeho tenanta. Tento účet se používá pro správce brány. Azure B2B (host) účty nejsou podporovány při instalaci a registraci brány.

   ![Přihlášení k Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Pokud se přihlásíte pomocí účtu domény, je namapován na váš účet organizace ve službě Azure AD. Váš účet organizace se používá jako správce brány.

## <a name="register"></a><a name="register"></a>Zaregistrovat

Chcete-li vytvořit prostředek brány v Azure, musíte zaregistrovat místní instanci, kterou jste nainstalovali pomocí služby Gateway Cloud Service. 

1.  Vyberte **možnost Zaregistrovat novou bránu v tomto počítači**.

    ![Zaregistrovat](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Zadejte název a obnovovací klíč brány. Ve výchozím nastavení brána používá výchozí oblast vašeho předplatného. Pokud potřebujete vybrat jinou oblast, vyberte **Změnit oblast**.

    > [!IMPORTANT]
    > Uložte obnovovací klíč na bezpečném místě. Obnovovací klíč je vyžadován v pořadí k převzetí, migraci nebo obnovení brány. 

   ![Zaregistrovat](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a><a name="create-resource"></a>Vytvoření prostředku brány Azure

Po instalaci a registraci brány je potřeba vytvořit prostředek brány v Azure. Přihlaste se k Azure pomocí stejného účtu, který jste použili při registraci brány.

1. Na webu Azure Portal klikněte na **Vytvořit prostředek**, vyhledejte **místní bránu dat**a potom klikněte na **Vytvořit**.

   ![Vytvoření prostředku brány](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. Do **části Vytvořit bránu připojení**zadejte tato nastavení:

   * **Název**: Zadejte název prostředku brány. 

   * **Předplatné**: Vyberte předplatné Azure, které chcete přidružit k prostředku brány. 
   
     Výchozí předplatné je založené na účtu Azure, který jste použili k přihlášení.

   * **Skupina prostředků**: Vytvořte skupinu prostředků, nebo vyberte existující.

   * **Umístění**: Vyberte oblast, ve které jste bránu zaregistrovali.

   * **Název instalace**: Pokud instalace brány ještě není vybraná, vyberte bránu, kterou jste nainstalovali do počítače a zaregistrovali. 

     Až budete hotovi, klikněte na **Vytvořit**.

## <a name="connect-servers-to-the-gateway-resource"></a><a name="connect-servers"></a>Připojení serverů k prostředku brány

1. V přehledu serveru Azure Analysis Services klikněte **na Místní brána dat**.

   ![Připojení serveru k bráně](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. V **části Vyberte místní bránu dat, která se chcete připojit**, vyberte prostředek brány a klikněte na Připojit **vybranou bránu**.

   ![Připojení serveru k prostředku brány](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Pokud se brána v seznamu nezobrazí, server pravděpodobně není ve stejné oblasti jako oblast, kterou jste zadali při registraci brány.

    Pokud je připojení mezi serverem a prostředkem brány úspěšné, zobrazí se stav **Připojeno**.


    ![Připojení serveru k úspěchu prostředků brány](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

A to je vše. Pokud potřebujete otevřít porty nebo provést jakékoli řešení potíží, nezapomeňte se podívat [na místní bránu dat](analysis-services-gateway.md).

## <a name="next-steps"></a>Další kroky

* [Správa služby Analysis Services](analysis-services-manage.md)   
* [Získání dat ze služby Azure Analysis Services](analysis-services-connect.md)   
* [Použití brány pro zdroje dat ve službě Azure Virtual Network](analysis-services-vnet-gateway.md)

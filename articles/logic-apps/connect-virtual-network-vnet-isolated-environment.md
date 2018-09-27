---
title: Připojení k virtuálním sítím Azure z Azure Logic Apps
description: Pro přístup k virtuálním sítím Azure z Azure Logic Apps, prostředí můžete vytvořit privátních, vyhrazených a izolované integrační služby, které udržují logic apps a dalších prostředků nezávislá na veřejných nebo "globální" Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: 354c31014448b914b33d2bef5483efc78092f726
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391917"
---
# <a name="create-isolated-environments-to-access-azure-virtual-networks-from-azure-logic-apps"></a>Vytvoření izolované prostředí pro přístup k virtuálním sítím Azure z Azure Logic Apps

> [!NOTE]
> Tato funkce je v *ve verzi private preview*. Chcete-li požádat o přístup, [vytváření žádosti o připojení tady](https://aka.ms/iseprivatepreview).

Pro scénáře integrace, kde se vaše aplikace logiky a účty pro integraci potřebují přístup k [virtuální síť Azure](../virtual-network/virtual-networks-overview.md), můžete vytvořit [ *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) , který odkazuje na vaše virtuální síť a nasadí služba Logic Apps do vaší sítě. Při vytváření aplikací logiky a účty pro integraci, vyberte tento ISE jako jejich umístění. Logic apps a účty pro integraci můžete pak přímý přístup k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby ve vaší virtuální síti. 

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Vaše ISE je privátní a izolované prostředí, která používá vyhrazeného úložiště a další prostředky, které existují samostatně z veřejného nebo *globální* služba Logic Apps. Tato separace pomáhá také omezit dopady, které jiných tenantů Azure může mít na výkon vaší aplikace. 

Tento článek popisuje, jak k provádění těchto úkolů:

* Nastavení oprávnění ve vaší virtuální sítí Azure, tak soukromé instanci aplikace logiky můžete přistupovat k vaší virtuální sítě.

* Vytvořte prostředí integrační služby (ISE). 

* Vytvoření aplikace logiky, který může spouštět ve vaší ISE. 

* Vytvoření účtu pro integraci pro vaše aplikace logiky v vaše ISE.

Další informace o prostředí integrační služby naleznete v tématu [přístup k prostředkům Azure Virtual Network z izolované Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* Pokud nemáte virtuální síť Azure, zjistěte, jak [vytvořit virtuální síť Azure](../virtual-network/quick-create-portal.md). 

  > [!IMPORTANT]
  > Když už nebudete potřebovat virtuální síť Azure pro vytváření prostředí, můžete *pouze* výběr virtuální sítě jako partnera pro vaše prostředí při vytváření tohoto prostředí. 

* Do aplikace logiky umožnit přímý přístup ke službě Azure virtual network, [nastavení oprávnění řízení přístupu na základě Role (RBAC)](#vnet-access) tak služba Logic Apps má oprávnění pro přístup k vaší virtuální sítě. 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Nastavit oprávnění pro virtuální síť

Při vytváření vašeho prostředí integrační služby, můžete vybrat virtuální síť Azure jako *peer* pro vaše prostředí. Však lze provést pouze tento krok nebo *partnerský vztah*, při vytváření vašeho prostředí. Tento vztah umožňuje připojit se přímo k prostředkům v dané virtuální síti služba Logic Apps a poskytuje prostředí přístup k těmto prostředkům. 

Abyste mohli vybrat virtuální síť, musíte vytvořit oprávnění řízení přístupu na základě Role (RBAC) ve vaší virtuální síti. Tento úkol provést, musíte přiřadit specifické role ve službě Azure Logic Apps.

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte virtuální síť. 

1. V nabídce vaší virtuální sítě, vyberte **řízení přístupu (IAM)**. 

1. V části **řízení přístupu**vyberte **přiřazení Role** Pokud ještě není vybraná. Na **přiřazení Role** nástrojů, zvolte **přidat**. 

   ![Přidání přiřazení role](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Na **přidat oprávnění** podokno nastavení každou roli v této tabulce pro službu Azure Logic Apps. Ujistěte se, že zvolíte **Uložit** po dokončení každé role:

   | Role | Přiřadit přístup k | Vyberte | 
   |------|------------------|--------|
   | **Přispěvatel sítě** | **Azure AD uživatele, skupinu nebo aplikaci** | Zadejte **Azure Logic Apps**. Jakmile se zobrazí seznam členů, vyberte stejnou hodnotu. <p>**Tip**: Pokud nelze najít tuto službu, zadejte ID aplikace služby Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Přispěvatel modelu Classic** | **Azure AD uživatele, skupinu nebo aplikaci** | Zadejte **Azure Logic Apps**. Jakmile se zobrazí seznam členů, vyberte stejnou hodnotu. <p>**Tip**: Pokud nelze najít tuto službu, zadejte ID aplikace služby Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

   Příklad:

   ![Přidání oprávnění](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   Další informace o roli oprávnění požadovaná pro partnerský vztah, najdete v článku [oprávnění tématu Vytvoření, změna nebo odstranění partnerského vztahu virtuálních sítí](../virtual-network/virtual-network-manage-peering.md#permissions). 

Pokud vaše virtuální síť připojená prostřednictvím Azure ExpressRoute, sítě VPN Azure Point-to-Site nebo Azure VPN typu Site-to-Site, pokračujte v další části, můžete přidat podsíť vyžaduje brána. V opačném případě pokračujte [vytvořit prostředí](#create-environment).

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>Přidání podsítě brány pro virtuální sítě s využitím ExpressRoute nebo VPN

Po dokončení předchozích kroků, abyste prostředí integrační služby (ISE) přístup ke službě Azure virtual network, který je připojený prostřednictvím [Azure ExpressRoute](../expressroute/expressroute-introduction.md), [Azure Point-to-Site VPN](../vpn-gateway/point-to-site-about.md), nebo [Azure VPN typu Site-to-Site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), musíte taky přidat [ *podsíť brány* ](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub) ke službě virtual network:

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte virtuální síť. V nabídce vaší virtuální sítě, vyberte **podsítě**a klikněte na tlačítko **podsíť brány** > **OK**.

   ![Přidání podsítě brány](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. Teď vytvořte [ *směrovací tabulka*](../virtual-network/manage-route-table.md), který budete přidružit podsíť brány, který jste vytvořili dříve.

   1. V hlavní nabídce Azure zvolte **vytvořit prostředek** > 
    **sítě** > **směrovací tabulka**.

      ![Vytvoření směrovací tabulky](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. Zadání informací o směrovací tabulky, jako je například název, vaše předplatné Azure se má použít, skupinu prostředků Azure a umístění. Ujistěte se, že **šíření tras BGP** je nastavena na **povoleno**a klikněte na tlačítko **vytvořit**.

      ![Zadejte podrobnosti směrovací tabulky](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. V nabídce směrovací tabulku, vyberte **podsítě**a klikněte na tlačítko **přidružit**. 

      ![Připojte směrovací tabulky k podsíti](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. Vyberte **virtuální síť**a potom vyberte virtuální síť.
   
   1. Vyberte **podsítě**a potom vyberte podsíť dříve vytvořené brány.

   1. Jakmile budete hotovi, zvolte **OK**.

1. Pokud máte připojení VPN point-to-site, příliš proveďte následující kroky:

   1. V Azure vyhledejte a vyberte váš prostředek brány virtuální sítě.

   1. V nabídce brány, vyberte **KonfiguracePoint-to-site**. 
   a klikněte na tlačítko **stáhnout klienta VPN** tak, že máte nejaktuálnější konfiguraci klienta VPN.

      ![Stáhněte si nejnovější verzi klienta VPN](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

Teď budete hotovi s nastavením podsíť brány pro virtuální sítě, které používají ExpressRoute a VPN typu point-to-site, VPN typu site-to-site. Pokud chcete pokračovat ve vytváření vašeho prostředí integrační služby, postupujte podle dalších kroků.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Vytvoření vašeho ISE

Pokud chcete vytvořit prostředí integrační služby (ISE), postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **vytvořit prostředek**.

   ![Vytvořit nový prostředek](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Do vyhledávacího pole zadejte jako filtr "prostředí integrační služby".
V seznamu výsledků vyberte **prostředí integrační služby (preview)** a klikněte na tlačítko **vytvořit**.

   ![Vyberte "Prostředí integrační služby"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Zvolte možnost "Vytvořit"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Uveďte následující údaje pro vaše prostředí:

   ![Zadejte podrobnosti prostředí](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Název** | Ano | <*Název prostředí*> | Název prostředí | 
   | **Předplatné** | Ano | <*Název předplatného Azure*> | Předplatné Azure, které můžete použít pro vaše prostředí | 
   | **Skupina prostředků** | Ano | <*Azure-resource-group-name*> | Skupina prostředků Azure, ve kterém chcete vytvořit prostředí |
   | **Umístění** | Ano | <*Oblast datového centra Azure*> | Datacentru Azure v oblasti kam se mají ukládat informace o vašem prostředí |
   | **Partnerská virtuální síť** | Ne | <*Název virtuální sítě Azure*> | Virtuální síť Azure pro přidružení k prostředí jako *peer* tak logiku aplikace v daném prostředí mají přístup k vaší virtuální sítě. Než budete moct vytvořit tuto relaci, ujistěte se, že jste již [nastavit řízení přístupu na základě role ve službě virtual network pro Azure Logic Apps](#vnet-access). <p>**Důležité**: i když se virtuální síť se nevyžaduje, můžete vybrat virtuální síť *pouze* při vytváření vašeho prostředí. | 
   | **Název partnerského vztahu** | Ano, s vybranou virtuální síť. | <*Název partnerského vztahu*> | Název sdílené relace | 
   | **Rozsah adres IP pro virtuální síť** | Ano, s vybranou virtuální síť. | <*Rozsah IP adres*> | Rozsah IP adres pro vytváření prostředků ve vašem prostředí. Musíte použít tento rozsah [notace CIDR (Classless Inter-Domain Routing) formát](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing), například 10.0.0.1/16 a vyžaduje třídy B adresní prostor. Rozsah nesmí existovat v rámci adresního prostoru virtuální sítě vybraný v **partnerské virtuální sítě** vlastností, ani v žádné jiné privátních IP adres ve kterém je připojená síť sdílené, buď prostřednictvím partnerského vztahu ani brány. <p><p>**Důležité**: můžete *nelze změnit* tento rozsah adres po vytvoření prostředí. |
   |||||
   
1. Jakmile budete hotoví, vyberte **Vytvořit**. 

   Azure spustí nasazení prostředí, ale tento proces může trvat *až dvě hodiny* před dokončením. 
   Chcete-li zkontrolovat stav nasazení, na panelu nástrojů Azure, vyberte ikonu oznámení, které se otevře podokno oznámení.

   ![Zkontrolujte stav nasazení](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Po úspěšném dokončení nasazení Azure zobrazí toto oznámení:

   ![Nasazení bylo úspěšné.](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Chcete-li zobrazit své prostředí, zvolte **přejít k prostředku** Pokud Azure nemá automaticky přejít do svého prostředí po dokončení nasazení.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Vytvoření aplikace logiky – ISE

K vytváření aplikací logiky, které používají prostředí integrační služby (ISE), postupujte podle kroků obvyklé v [jak vytvořit aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) , ale tyto rozdíly a aspekty: 

* Když vytvoříte aplikaci logiky **umístění** vaše ISEs podle seznamů vlastností **prostředí integrační služby** spolu se dozvíte dostupné oblasti. Vyberte vaše ISE, místo oblasti, například:

  ![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Můžete použít stejném předdefinované, jako je například HTTP triggeru nebo akce, které běží v prostředí ISE stejné jako nadřazená aplikace logiky. Konektory s **ISE** popisek také spustit v prostředí ISE stejné jako nadřazená aplikace logiky. Konektory bez **ISE** popisek spustit v globální službě Logic Apps.

  ![Výběr konektorů ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Pokud jste dříve nastavili vaše ISE s Azure virtual network jako na partnera, logic apps v vaše ISE můžete přímo přístup k prostředkům v dané virtuální síti. Pro místní systémy ve virtuální síti, který je propojen ISE aplikace logiky přímo přístupné tyto systémy pomocí některé z těchto položek: 

  * ISE konektor pro daný systém, například SQL Server

  * Akce HTTP 

  * Vlastní konektor

  Pro místní systémy, které nejsou ve virtuální síti nebo nemají ISE konektory, můžete se připojit až [nastavit a používat místní brány dat](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Vytvořit účet pro integraci – ISE

Použití účtu pro integraci s logic apps v prostředí integrační služby (ISE), musíte použít tento účet pro integraci *stejné prostředí* jako logic apps. Aplikace logiky do ISE může odkazovat pouze účty pro integraci v prostředí ISE stejné. 

Chcete-li vytvořit integrační účet, který používá ISE, postupujte podle obvyklé kroky v [vytvoření účtů pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s výjimkou **umístění** vlastnost, která nyní obsahuje vaše ISEs pod  **Prostředí integrační služby** spolu se dozvíte dostupné oblasti. Vyberte vaše ISE, místo oblasti, například:

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum Azure Logic Apps</a>.
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte <a href="http://aka.ms/logicapps-wish" target="_blank">web zpětné vazby od uživatelů Logic Apps</a>.

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Další informace o [integrace služby virtual network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)

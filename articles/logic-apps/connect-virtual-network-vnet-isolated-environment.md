---
title: Připojení k virtuálním sítím Azure z Azure Logic Apps prostřednictvím integrace služby prostředí (ISE)
description: Vytvořit prostředí integrační služby (ISE) aby logic apps a účty pro integraci měli přístup k virtuálním sítím Azure (Vnet), při zachování soukromých a izolované od veřejné nebo "globální" Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 11/29/2018
ms.openlocfilehash: 798b50887bcfdf5b4298c37beb1b9eea8f9abdda
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682193"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-through-an-integration-service-environment-ise"></a>Připojení k virtuálním sítím Azure z Azure Logic Apps prostřednictvím integrace služby prostředí (ISE)

> [!NOTE]
> Tato funkce je v *ve verzi private preview*. Chcete-li požádat o přístup, [vytváření žádosti o připojení tady](https://aka.ms/iseprivatepreview).

Pro scénáře, ve kterém logic apps a účty pro integraci potřebují přístup k [virtuální síť Azure](../virtual-network/virtual-networks-overview.md), vytvořte [ *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE je privátní a izolované prostředí, která používá vyhrazeného úložiště a další prostředky, které uchovávají nezávislá na infrastruktuře veřejného nebo *globální* služba Logic Apps. Toto oddělení také snižuje předejde jiných tenantů Azure může mít na výkon vaší aplikace. Je vaše ISE *vložený* do ke službě Azure virtual network, která pak nasadí služba Logic Apps do vaší virtuální sítě. Při vytváření logiku aplikace nebo integračního účtu, vyberte tento ISE jako jejich umístění. Váš účet integrace nebo aplikace logiky můžete pak přímý přístup k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby ve vaší virtuální síti. 

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Tento článek popisuje, jak k dokončení těchto úloh:

* Nastavení oprávnění ve vaší virtuální sítí Azure, tak soukromé instanci aplikace logiky můžete přistupovat k vaší virtuální sítě.

* Vytvořte prostředí integrační služby (ISE). 

* Vytvoření aplikace logiky, který může spouštět ve vaší ISE. 

* Vytvoření účtu pro integraci pro vaše aplikace logiky v vaše ISE.

Další informace o prostředí integrační služby naleznete v tématu [přístup k prostředkům Azure Virtual Network v Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. 

* [Virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Pokud nemáte virtuální síť, zjistěte, jak [vytvořit virtuální síť Azure](../virtual-network/quick-create-portal.md). 

* Do aplikace logiky umožnit přímý přístup ke službě Azure virtual network, [nastavení oprávnění řízení přístupu na základě Role (RBAC)](#vnet-access) tak služba Logic Apps má oprávnění pro přístup k vaší virtuální sítě. 

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Nastavit oprávnění pro virtuální síť

Při vytváření prostředí integrační služby (ISE), vyberte virtuální síť Azure tom, kde jste *vložit* vašeho prostředí. Ale předtím, než můžete vybrat virtuální síť pro vkládání prostředí, musíte vytvořit oprávnění řízení přístupu na základě Role (RBAC) ve vaší virtuální síti. Nastavení oprávnění, přiřaďte tyto konkrétní role ve službě Azure Logic Apps:

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte virtuální síť. 

1. V nabídce vaší virtuální sítě, vyberte **řízení přístupu (IAM)**. 

1. V části **řízení přístupu (IAM)**, zvolte **přidat**. 

   ![Přidání rolí](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Na **přidat přiřazení role** podokno nastavení každou roli pro službu Azure Logic Apps, jak je popsáno v tabulce v tomto kroku. Ujistěte se, že zvolíte **Uložit** po dokončení každé role.

   ![Přidání přiřazení role](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   | Role | Přiřadit přístup k | Vyberte | 
   |------|------------------|--------|
   | **Přispěvatel sítě** | **Azure AD uživatele, skupinu nebo aplikaci** | Zadejte **Azure Logic Apps**. Jakmile se zobrazí seznam členů, vyberte stejnou hodnotu. <p>**Tip**: Pokud nelze najít tuto službu, zadejte ID aplikace služby Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   | **Přispěvatel modelu Classic** | **Azure AD uživatele, skupinu nebo aplikaci** | Zadejte **Azure Logic Apps**. Jakmile se zobrazí seznam členů, vyberte stejnou hodnotu. <p>**Tip**: Pokud nelze najít tuto službu, zadejte ID aplikace služby Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba` | 
   |||| 

Další informace najdete v tématu [oprávnění pro přístup k virtuální síti](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Vytvoření vašeho ISE

Pokud chcete vytvořit prostředí integrační služby (ISE), postupujte takto:

1. V [webu Azure portal](https://portal.azure.com), v hlavní nabídce Azure zvolte **vytvořit prostředek**.

   ![Vytvořit nový prostředek](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Do vyhledávacího pole zadejte jako filtr "prostředí integrační služby".
V seznamu výsledků vyberte **prostředí integrační služby (preview)** a klikněte na tlačítko **vytvořit**.

   ![Vyberte "Prostředí integrační služby"](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![Zvolte možnost "Vytvořit"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Zadejte tyto údaje pro vaše prostředí a pak zvolte **revize + vytvořit**, například:

   ![Zadejte podrobnosti prostředí](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Vlastnost | Požaduje se | Hodnota | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Ano | <*název_předplatného_Azure*> | Předplatné Azure, které můžete použít pro vaše prostředí | 
   | **Skupina prostředků** | Ano | <*Azure-resource-group-name*> | Skupina prostředků Azure, ve kterém chcete vytvořit prostředí |
   | **Název prostředí integrační služby** | Ano | <*Název prostředí*> | Název prostředí | 
   | **Umístění** | Ano | <*Oblast datového centra Azure*> | Oblast datového centra Azure, jak nasadíte prostředí | 
   | **Kapacita** | Ano | 0, 1, 2, 3 | Počet jednotek zpracování pro tento prostředek ISE | 
   | **Virtuální síť** | Ano | <*Azure--název virtuální sítě –*> | Virtuální síť Azure ve které chcete vložit prostředí, takže aplikace logiky v daném prostředí mají přístup k vaší virtuální sítě. Pokud nejste připojeni k síti, můžete jeden vytvořit tady. <p>**Důležité**: můžete *pouze* provádět tento vkládání při vytváření vašeho ISE. Ale předtím, než budete moct vytvořit tuto relaci, ujistěte se, že jste již [nastavit řízení přístupu na základě role ve službě virtual network pro Azure Logic Apps](#vnet-access). | 
   | **Podsítě** | Ano | <*Rozsah IP adres*> | ISE vyžaduje čtyři *prázdný* podsítě, které nemají delegování k libovolné službě a slouží k vytváření prostředků ve vašem prostředí. Každá podsíť musí splňovat tato kritéria: <p>-Používá [notace CIDR (Classless Inter-Domain Routing) formát](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing). <br>-Vyžaduje třídy B adresní prostor. <br>-Má název, který nezačíná znakem čísla nebo pomlčku. <br>-Zahrnuje `/27`, například každá podsíť určuje rozsah adres 32-bit: `10.0.0.0/27`, `10.0.0.32/27`, `10.0.0.64/27`, a `10.0.0.96/27`. <br>– Musí neexistuje ve stejném rozsahu adres pro vámi zvolené virtuální síti ani žádné jiné privátních IP adres ve kterých je připojený virtuální sítě. <br>– Musí být prázdný. <p><p>**Důležité**: můžete *nelze změnit* tyto rozsahy IP adres po vytvoření prostředí. |
   |||||

1. Když Azure úspěšně ověří vaše ISE informace, zvolte **vytvořit**, například:

   ![Po úspěšném ověření zvolte možnost "Vytvořit"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure spustí nasazení prostředí, ale tento proces může trvat *až dvě hodiny* před dokončením. 
   Chcete-li zkontrolovat stav nasazení, na panelu nástrojů Azure, vyberte ikonu oznámení, které se otevře podokno oznámení.

   ![Zkontrolujte stav nasazení](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Po úspěšném dokončení nasazení Azure zobrazí toto oznámení:

   ![Nasazení bylo úspěšné.](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. Chcete-li zobrazit své prostředí, zvolte **přejít k prostředku** Pokud Azure nemá automaticky přejít do svého prostředí po dokončení nasazení.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Vytvoření aplikace logiky – ISE

K vytváření aplikací logiky, které používají prostředí integrační služby (ISE), postupujte podle kroků v [jak vytvořit aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) ale s těmito rozdíly: 

* Když vytvoříte aplikaci logiky, vyberte vaše ISE, místo oblasti Azure, ze **umístění** ze seznamu **prostředí integrační služby** části, například:

  ![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Můžete používat stejné integrované triggery a akce, jako je například HTTP, které běží v prostředí ISE stejné jako aplikace logiky. Konektory s **ISE** popisek také spustit v prostředí ISE stejné jako aplikace logiky. Konektory bez **ISE** popisek spustit v globální službě Logic Apps.

  ![Výběr konektorů ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Po vložení vaše ISE do služby Azure virtual network, logic apps v vaše ISE můžete přímo přístup k prostředkům v dané virtuální síti. Pro místní systémy ve virtuální síti, který je propojen ISE aplikace logiky přímo přístupné tyto systémy pomocí některé z těchto položek: 

  * ISE konektor pro daný systém, například SQL Server
  * Akce HTTP 
  * Vlastní konektor

  Pro místní systémy, které nejsou ve virtuální síti nebo nemají ISE konektory, nejprve [nastavit a používat místní brány dat](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Vytvořit účet pro integraci – ISE

Použití účtu pro integraci s logic apps v prostředí integrační služby (ISE), musíte použít tento účet pro integraci *stejné prostředí* jako logic apps. Aplikace logiky do ISE může odkazovat pouze účty pro integraci v prostředí ISE stejné. 

Chcete-li vytvořit integrační účet, který používá ISE, postupujte podle obvyklé kroky v [vytvoření účtů pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s výjimkou **umístění** vlastnost, která nyní obsahuje vaše ISEs pod  **Prostředí integrační služby** spolu se dozvíte dostupné oblasti. Vyberte vaše ISE, místo oblasti, například:

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum Azure Logic Apps</a>.
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte <a href="https://aka.ms/logicapps-wish" target="_blank">web zpětné vazby od uživatelů Logic Apps</a>.

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Další informace o [integrace služby virtual network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)

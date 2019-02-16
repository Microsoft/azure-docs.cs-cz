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
ms.date: 02/15/2019
ms.openlocfilehash: d67bc99a63242dd56d65d6bdac0448c7742a6b9d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311898"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Připojení k virtuálním sítím Azure z Azure Logic Apps s využitím integrace služby prostředí (ISE)

> [!NOTE]
> Tato funkce je v *ve verzi private preview*. Získat privátní verzi preview, [vytváření žádosti o zde](https://aka.ms/iseprivatepreview).

Pro scénáře, ve kterém logic apps a účty pro integraci potřebují přístup k [virtuální síť Azure](../virtual-network/virtual-networks-overview.md), vytvořte [ *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). ISE je privátní a izolované prostředí, která používá vyhrazeného úložiště a dalším prostředkům udržovány odděleně od veřejné nebo "globální" služba Logic Apps. Toto oddělení také snižuje předejde jiných tenantů Azure může mít na výkon vaší aplikace. Je vaše ISE *vložený* do ke službě Azure virtual network, která pak nasadí služba Logic Apps do vaší virtuální sítě. Při vytváření logiku aplikace nebo integračního účtu, vyberte tento ISE jako jejich umístění. Váš účet integrace nebo aplikace logiky můžete pak přímý přístup k prostředkům, jako jsou virtuální počítače (VM), servery, systémy a služby ve vaší virtuální síti.

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Tento článek popisuje, jak k dokončení těchto úloh:

* Nastavení porty ve vaší virtuální sítí Azure tak prostřednictvím prostředí integrační služby (ISE) mohou projít provoz mezi podsítěmi ve virtuální síti.

* Nastavení oprávnění ve vaší virtuální sítí Azure, tak soukromé instanci aplikace logiky můžete přistupovat k vaší virtuální sítě.

* Vytvořte prostředí integrační služby (ISE).

* Vytvoření aplikace logiky, který může spouštět ve vaší ISE.

* Vytvoření účtu pro integraci pro vaše aplikace logiky v vaše ISE.

Další informace o prostředí integrační služby naleznete v tématu [přístup k prostředkům Azure Virtual Network v Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

  > [!IMPORTANT]
  > Logic apps, integrované akce a konektory, na kterých běží vaše ISE používá jiný cenový plán není založenou na skutečné spotřebě cenového plánu. Další informace najdete v tématu [ceny Logic Apps](../logic-apps/logic-apps-pricing.md).

* [Virtuální síť Azure](../virtual-network/virtual-networks-overview.md). Pokud nemáte virtuální síť, zjistěte, jak [vytvořit virtuální síť Azure](../virtual-network/quick-create-portal.md). Budete také potřebovat podsítě ve virtuální síti pro nasazení vaší ISE. Můžete vytvořit tyto podsítě předem, nebo počkejte na vytvoření vašeho ISE, kde můžete vytvořit podsítě ve stejnou dobu. Navíc [Ujistěte se, že vaše virtuální síť zpřístupní tyto porty](#ports) tak, aby vaše ISE správně funguje a zůstane dostupný.

* Do aplikace logiky umožnit přímý přístup ke službě Azure virtual network, [nastavení oprávnění pro řízení přístupu na základě Role (RBAC) vaší sítě](#vnet-access) tak služba Logic Apps má oprávnění pro přístup k vaší virtuální sítě.

* Použít jeden nebo více vlastní servery DNS pro nasazení vaší virtuální sítí Azure, [nastavit tyto servery následující návod](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) před nasazením vaší ISE k virtuální síti. V opačném případě při každé změně vašeho serveru DNS, máte také restartovat ISE, což je funkce, která je k dispozici ve verzi public preview ISE.

* Základní znalosti o [postupy vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Nastavte síťové porty

Prostředí integrační služby (ISE) fungovat správně a zůstat přístupný, musí mít určité porty, které jsou k dispozici ve vaší virtuální síti. Jinak pokud kterýkoliv z těchto portů je k dispozici, může ztratit přístup k vaší ISE, což může přestat pracovat. Při použití ISE ve virtuální síti běžný problém instalační program má jeden nebo více portů blokované. Pro připojení mezi vaší ISE a cílový systém konektor, který můžete použít také může mít vlastní požadavky na porty. Například pokud komunikovat s FTP systémem pomocí konektoru služby FTP, ujistěte se, že port, který používáte v systému FTP, jako je například 21 portu pro odesílání příkazů, je k dispozici.

K řízení příchozího a odchozího provozu mezi podsítěmi virtuální sítě nasadíte kdekoli vašeho ISE, můžete nastavit [skupiny zabezpečení sítě](../virtual-network/security-overview.md) pro tyto podsítě učení [jak filtrovat síťový provoz mezi podsítě](../virtual-network/tutorial-filter-network-traffic.md). Tyto tabulky popisují porty ve vaší virtuální síti, která používá vaše ISE a kde získat používají tyto porty. Hvězdička (*) představuje všechny přenosy zdroje. [Značka služby](../virtual-network/security-overview.md#service-tags) představuje skupinu předpon IP adres, které pomáhá minimalizovat složitost při vytváření pravidel zabezpečení.

| Účel | Směr | Zdrojový port <br>Cílový port | Značka zdrojové služby <br>Značka cílové služby |
|---------|-----------|---------------------------------|-----------------------------------------------|
| Komunikace s Azure Logic Apps <br>Sdělení Azure Logic Apps | Příchozí <br>Odchozí | * <br>80 & 443 | INTERNET <br>VIRTUAL_NETWORK |
| Azure Active Directory | Odchozí | * <br>80 & 443 | VIRTUAL_NETWORK <br>AzureActiveDirectory |
| Závislosti Azure Storage | Odchozí | * <br>80 & 443 | VIRTUAL_NETWORK <br>Storage |
| Historie spouštění vaší aplikace logiky | Příchozí | * <br>443 | INTERNET <br>VIRTUAL_NETWORK |
| Správa připojení | Odchozí | * <br>443 | VIRTUAL_NETWORK <br>INTERNET |
| Publikování diagnostické protokoly a metriky | Odchozí | * <br>443 | VIRTUAL_NETWORK <br>AzureMonitor |
| Návrhář pro Logic Apps – dynamické vlastnosti <br>Nasazení konektoru <br>Aktivační událost požadavku koncového bodu | Příchozí | * <br>454 | INTERNET <br>VIRTUAL_NETWORK |
| Závislost aplikace Service Management | Příchozí | * <br>454 & 455 | AppServiceManagement <br>VIRTUAL_NETWORK |
| API Management – koncový bod správy | Příchozí | * <br>3443 | APIManagement <br>VIRTUAL_NETWORK |
| Závislost z protokolu do zásady centra událostí a agenta monitorování | Odchozí | * <br>5672 | VIRTUAL_NETWORK <br>Centrum událostí |
| Přístup k mezipaměti Azure pro instance Redis mezi instancemi Role | Příchozí <br>Odchozí | * <br>6381-6383 | VIRTUAL_NETWORK <br>VIRTUAL_NETWORK |
|||||

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>Nastavit oprávnění pro virtuální síť

Při vytváření prostředí integrační služby (ISE), vyberte virtuální síť Azure tom, kde jste *vložit* vašeho prostředí. Ale předtím, než můžete vybrat virtuální síť pro vkládání prostředí, musíte vytvořit oprávnění řízení přístupu na základě Role (RBAC) ve vaší virtuální síti. Nastavení oprávnění, přiřaďte tyto konkrétní role ve službě Azure Logic Apps:

1. V [webu Azure portal](https://portal.azure.com)vyhledejte a vyberte virtuální síť.

1. V nabídce vaší virtuální sítě, vyberte **řízení přístupu (IAM)**.

1. V části **řízení přístupu (IAM)**, zvolte **přidat přiřazení role**.

   ![Přidání rolí](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. Na **přidat přiřazení role** podokno, přidejte nezbytné rolí do služby Azure Logic Apps, jak je popsáno.

   1. V části **Role**vyberte **Přispěvatel sítě**.

   1. V části **přiřadit přístup k**vyberte **uživatele, skupinu nebo instanční objekt služby Azure AD**.

   1. V části **vyberte**, zadejte **Azure Logic Apps**.

   1. Po dokončení se zobrazí seznam členů, vyberte **Azure Logic Apps**.

      > [!TIP]
      > Pokud nemůžete najít tuto službu, zadejte ID aplikace služby Logic Apps: `7cd684f4-8a78-49b0-91ec-6a35d38739ba`

   1. Jakmile budete hotoví, vyberte **Uložit**.

   Příklad:

   ![Přidat přiřazení role](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

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

   | Vlastnost | Požaduje se | Value | Popis |
   |----------|----------|-------|-------------|
   | **Předplatné** | Ano | <*název_předplatného_Azure*> | Předplatné Azure, které můžete použít pro vaše prostředí |
   | **Skupina prostředků** | Ano | <*Azure-resource-group-name*> | Skupina prostředků Azure, ve kterém chcete vytvořit prostředí |
   | **Název prostředí integrační služby** | Ano | <*environment-name*> | Název prostředí |
   | **Umístění** | Ano | <*Azure-datacenter-region*> | Oblast datového centra Azure, jak nasadíte prostředí |
   | **Zvýšení kapacity** | Ano | 0, 1, 2, 3 | Počet jednotek zpracování pro tento prostředek ISE |
   | **Virtuální síť** | Ano | <*Azure-virtual-network-name*> | Virtuální síť Azure ve které chcete vložit prostředí, takže aplikace logiky v daném prostředí mají přístup k vaší virtuální sítě. Pokud nejste připojeni k síti, můžete jeden vytvořit tady. <p>**Důležité**: Je možné *pouze* provádět tento vkládání při vytváření vašeho ISE. Ale předtím, než budete moct vytvořit tuto relaci, ujistěte se, že jste již [nastavit řízení přístupu na základě role ve službě virtual network pro Azure Logic Apps](#vnet-access). |
   | **Podsítě** | Ano | <*subnet-resource-list*> | ISE vyžaduje čtyři *prázdný* podsítě pro vytváření prostředků ve vašem prostředí. Ano, ujistěte se, že tyto podsítě *nejsou přidělena* na libovolnou službu. Můžete *nelze změnit* tyto adresy podsítě po vytvoření prostředí. <p><p>K vytvoření každé podsíti [, použijte postup v této tabulce](#create-subnet). Každá podsíť musí splňovat tato kritéria: <p>– Musí být prázdný. <br>-Používá název, který nezačíná znakem čísla nebo pomlčku. <br>-Používá [notace CIDR (Classless Inter-Domain Routing) formát](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) a prostor adres třídy B. <br>-Zahrnuje nejméně jednoho `/27` v adresním prostoru, získá alespoň 32 adres podsítě. Další informace o výpočtu počet adres najdete v tématu [bloky IPv4 CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks). Příklad: <p>- `10.0.0.0/24` protože má 256 adresy 2<sup>(32-24)</sup> je 2<sup>8</sup> nebo 256. <br>- `10.0.0.0/27` protože má 32 adres 2<sup>(32-27)</sup> je 2<sup>5</sup> nebo 32. <br>- `10.0.0.0/28` protože má jenom 16 adresy 2<sup>(32-28)</sup> je 2<sup>4</sup> nebo 16. |
   |||||

   <a name="create-subnet"></a>

   **Vytvoření podsítě**

   1. V části **podsítě** klikněte na položku **spravovat konfiguraci podsítě**.

      ![Spravovat konfiguraci podsítě](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Na **podsítě** podokně zvolte **podsítě**.

      ![Přidání podsítě](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Na **přidat podsíť** podokně zadejte tyto informace.

      * **Název**: Název pro vaši podsíť
      * **Rozsah adres (blok CIDR)**: Rozsah vaší podsítě ve virtuální síti a ve formátu CIDR

      ![Přidat podrobnosti o podsíti](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Jakmile budete hotovi, zvolte **OK**.

   1. Tento postup opakujte pro tři další podsítě.

1. Když Azure úspěšně ověří vaše ISE informace, zvolte **vytvořit**, například:

   ![Po úspěšném ověření zvolte možnost "Vytvořit"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure spouští vaše prostředí, ale tento proces nasazení *může* trvat až dvě hodiny před dokončením. 
   Chcete-li zkontrolovat stav nasazení, na panelu nástrojů Azure, vyberte ikonu oznámení, které se otevře podokno oznámení.

   ![Zkontrolujte stav nasazení](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Pokud se nasazení dokončí úspěšně, zobrazí toto upozornění Azure:

   ![Nasazení bylo úspěšné.](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > Pokud nasazení selže nebo je odstranit ISE, Azure *může* trvat až jednu hodinu před uvolněním podsítě. Ano budete muset počkat před opětovným použitím těchto podsítí v jiném prostředí ISE.

1. Chcete-li zobrazit své prostředí, zvolte **přejít k prostředku** Pokud Azure nemá automaticky přejít do svého prostředí po dokončení nasazení.  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Vytvoření aplikace logiky – ISE

K vytváření aplikací logiky, které používají prostředí integrační služby (ISE), postupujte podle kroků v [jak vytvořit aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) ale s těmito rozdíly: 

* Když vytvoříte aplikaci logiky, v části **umístění** vlastnosti, vyberte vaše ISE z **prostředí integrační služby** části, například:

  ![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* Můžete používat stejné integrované triggery a akce, jako je například HTTP, které běží v prostředí ISE stejné jako aplikace logiky. Konektory s **ISE** popisek také spustit v prostředí ISE stejné jako aplikace logiky. Konektory bez **ISE** popisek spustit v globální službě Logic Apps.

  ![Výběr konektorů ISE](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Po vložení vaše ISE do služby Azure virtual network, logic apps v vaše ISE můžete přímo přístup k prostředkům v dané virtuální síti. Pro místní systémy, které jsou připojené k virtuální síti vložit ISE do této sítě tak, aby aplikace logiky můžete tyto systémy přístup přímo pomocí některé z těchto položek: 

  * ISE konektor pro daný systém, například SQL Server
  
  * Akce HTTP 
  
  * Vlastní konektor

  Pro místní systémy, které nejsou ve virtuální síti nebo nemají ISE konektory, nejprve [nastavit na místní bránu dat](../logic-apps/logic-apps-gateway-install.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Vytvořit účet pro integraci – ISE

Použití účtu pro integraci s logic apps v prostředí integrační služby (ISE), musíte použít tento účet pro integraci *stejné prostředí* jako logic apps. Aplikace logiky do ISE může odkazovat pouze účty pro integraci v prostředí ISE stejné. 

Chcete-li vytvořit integrační účet, který používá ISE, postupujte podle kroků v [vytvoření účtů pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s výjimkou **umístění** vlastnost kde **prostředí integrační služby**  nyní se zobrazí část. Místo toho vyberte váš ISE, místo oblasti, například:

![Vyberte prostředí integrační služby](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">fórum Azure Logic Apps</a>.
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte <a href="https://aka.ms/logicapps-wish" target="_blank">web zpětné vazby od uživatelů Logic Apps</a>.

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Další informace o [integrace služby virtual network pro služby Azure](../virtual-network/virtual-network-for-azure-services.md)

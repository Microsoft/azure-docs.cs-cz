---
title: Plánování spravované aplikace Azure pro nabídku aplikací Azure
description: Zjistěte, co je potřeba k vytvoření plánu spravované aplikace pro novou nabídku aplikací Azure pomocí portálu pro komerční tržiště v partnerském centru Microsoftu.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621412"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Plánování spravované aplikace Azure pro nabídku aplikací Azure

Plán _spravované aplikace_ Azure je jedním ze způsobů, jak publikovat nabídku aplikací azure v Azure Marketplace. Pokud jste to ještě neudělali, přečtěte si téma [plánování nabídky aplikací Azure pro komerční tržiště](plan-azure-application-offer.md).

Spravované aplikace jsou nabídky v jazyce Transact, které se nasazují a účtují prostřednictvím Azure Marketplace. Možnost výpisu, kterou uživatel vidí, se teď získá.

Použití aplikace Azure: plán spravované aplikace, pokud jsou potřeba následující podmínky:

- Řešení založené na předplatném můžete nasadit pro zákazníka pomocí virtuálního počítače nebo celého řešení založeného na infrastruktuře jako služby (IaaS).
- Vy nebo váš zákazník vyžaduje, aby bylo řešení spravované partnerem. Partnerem může být například systémový integrátor nebo poskytovatel spravované služby (MSP).

## <a name="managed-application-offer-requirements"></a>Požadavky na nabídku spravované aplikace

| Požadavky | Podrobnosti |
| ------------ | ------------- |
| Předplatné Azure | Spravované aplikace musí být nasazeny do předplatného zákazníka, ale mohou být spravovány třetí stranou. |
| Fakturace a měření | Prostředky jsou k dispozici v předplatném Azure zákazníka. Virtuální počítače, které používají platební model průběžných plateb, se účtují u zákazníka přes Microsoft a účtují se prostřednictvím předplatného Azure zákazníka. <br><br> V případě virtuálních počítačů využívajících vlastní licenci společnost Microsoft účtuje náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka, ale poplatky za licence na software se zákazníky účtují přímo. |
| Virtuální pevný disk kompatibilní s Azure (VHD) | Virtuální počítače musí být postavené na Windows nebo Linux. Další informace najdete tady:<br> • [Vytvořte technický prostředek virtuálního počítače Azure](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (pro virtuální pevné disky Windows).<br> •  [Distribuce systému Linux schválené v Azure](../virtual-machines/linux/endorsed-distros.md) (pro virtuální pevné disky se systémem Linux). |
| Přisuzování využití ze strany zákazníků | Všechny nové nabídky aplikací Azure musí zahrnovat i identifikátor GUID [poskytovatele využívání zákaznických zákazníků Azure](azure-partner-customer-usage-attribution.md) . Další informace o přidaných zákaznických využití a o tom, jak ji povolit, najdete v tématu věnovaném [přidělení zákaznického využívání Azure partnerovi](azure-partner-customer-usage-attribution.md). |
| Balíček pro nasazení | Budete potřebovat balíček pro nasazení, který zákazníkům umožní nasadit váš plán. Pokud vytvoříte více plánů vyžadujících stejnou technickou konfiguraci, můžete použít stejný balíček. Podrobnosti najdete v další části: balíček pro nasazení. |
|||

> [!NOTE]
> Spravované aplikace je potřeba nasadit prostřednictvím Azure Marketplace. Pokud je komunikace s zákazníky zájmem, zajímá se zákazníkům, kteří si zapnuli sdílení potenciálních zákazníků.

## <a name="deployment-package"></a>Balíček pro nasazení

Balíček pro nasazení obsahuje všechny soubory šablon potřebné pro tento plán a všechny další prostředky zabalené jako soubor. zip.

Všechny aplikace Azure musí tyto dva soubory zahrnout do kořenové složky archivu. zip:

- Soubor šablony Správce prostředků s názvem [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Tato šablona definuje prostředky, které se mají nasadit do předplatného Azure zákazníka. Příklady šablon Správce prostředků najdete v tématu [Galerie šablon pro rychlý Start pro Azure](https://azure.microsoft.com/documentation/templates/) nebo odpovídající úložiště [šablon githubu pro Azure Resource Manager pro rychlý Start](https://github.com/azure/azure-quickstart-templates) .
- Definice uživatelského rozhraní pro prostředí pro vytváření aplikací Azure s názvem [createUiDefinition.js](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). V uživatelském rozhraní zadáváte elementy, které uživatelům umožňují zadávat hodnoty parametrů.

Maximální podporované velikosti souborů jsou:

- Až 1 GB v celkovém komprimované velikosti archivu zip
- Až 1 GB pro jakýkoliv jednotlivý nekomprimovaný soubor v archivu. zip

Všechny nové nabídky aplikací Azure musí zahrnovat i identifikátor GUID [poskytovatele využívání zákaznických zákazníků Azure](azure-partner-customer-usage-attribution.md) .

## <a name="azure-regions"></a>Oblast Azure

Svůj plán můžete publikovat do veřejné oblasti Azure, Azure Government oblasti nebo obojího. Před publikováním [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), otestujte a ověřte svůj plán v prostředí, protože se některé koncové body můžou lišit. Pokud chcete nastavit a otestovat svůj plán, vyžádejte si zkušební účet z [Microsoft Azure Government zkušební verze](https://azure.microsoft.com/global-infrastructure/government/request/).

Jako vydavatel zodpovídáte za řízení dodržování předpisů, míry zabezpečení a osvědčené postupy. Azure Government používá fyzicky izolovaná datová centra a sítě (jenom v USA).

Seznam zemí a oblastí, které podporuje komerční tržiště, najdete v tématu [geografická dostupnost a podpora měn](marketplace-geo-availability-currencies.md).

Služba Azure Government Services zpracovává data, která se vztahují na určité předpisy a požadavky státní správy. Příklad: FedRAMP, NIST 800,171 (DIB), ITAR, finanční úřad 1075, DoD – L4 a CJIS. Pro zajištění povědomí o certifikaci pro tyto programy můžete poskytnout až 100 odkazů, které je popisují. Můžou to být buď odkazy na váš výpis v programu, nebo odkazy na popisy dodržování předpisů s nimi na vašich vlastních webech. Tyto odkazy jsou viditelné pouze pro Azure Government zákazníky.

## <a name="choose-who-can-see-your-plan"></a>Zvolit, kdo může zobrazit váš plán

Každý plán můžete nakonfigurovat tak, aby byl viditelný pro všechny (veřejné) nebo jenom pro konkrétní cílovou skupinu (soukromá). Můžete vytvořit až 100 plánů a až 45 z nich může být privátní. Možná budete chtít vytvořit soukromý plán, který bude nabízet různé cenové možnosti nebo technické konfigurace konkrétním zákazníkům.

Přístup k privátnímu plánu udělíte pomocí ID předplatných Azure s možností zahrnutí popisu každého ID předplatného, které přiřadíte. Pomocí můžete přidat maximálně 10 ID předplatných nebo až 10 000 ID předplatných. Soubor CSV. ID předplatných Azure jsou reprezentovaná jako identifikátory GUID a písmena musí být malá.

U předplatných Azure vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP) se nepodporují privátní plány. Další informace najdete v tématu [soukromé nabídky na komerčním webu Microsoft Marketplace](private-offers.md).

> [!NOTE]
> Pokud publikujete soukromý plán, můžete později změnit jeho viditelnost na veřejné. Po publikování veřejného plánu ale nemůžete změnit jeho viditelnost na Private.

## <a name="define-pricing"></a>Definovat ceny

Pro každý plán musíte zadat cenu za měsíc. Tato cena se doplní na všechny infrastruktury Azure nebo náklady na software s průběžnými platbami, které vznikají prostředky nasazené tímto řešením.

Kromě ceny za měsíc můžete také nastavit ceny za spotřebu nestandardních jednotek pomocí [měřených faktur](partner-center-portal/azure-app-metered-billing.md). V případě, že budete chtít, můžete nastavit cenu za měsíc na nulu a účtovat výhradně pomocí měřených faktur.

Ceny se nastavují v USD (USD = USA dolar) se převádějí do místní měny všech vybraných trhů za použití aktuálních směnných kurzů při uložení. Můžete ale zvolit, že chcete nastavit ceny zákazníků pro každý trh.

## <a name="just-in-time-jit-access"></a>Přístup k JIT (just in time)

Přístup JIT vám umožní požádat o zvýšený přístup k prostředkům spravované aplikace pro účely řešení potíží nebo údržby. Vždy máte přístup k prostředkům jen pro čtení, ale v určitém časovém období můžete mít větší přístup. Další informace najdete v tématu [povolení a vyžádat přístup za běhu pro Azure Managed Applications](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> Nezapomeňte soubor aktualizovat tak, aby `createUiDefinition.json` podporoval tuto funkci.

## <a name="deployment-mode"></a>Režim nasazení

Plán spravované aplikace můžete nakonfigurovat tak, aby používal buď režim **úplného** nebo **přírůstkového** nasazení. V režimu úplného nasazení aplikace výsledkem je odebrání prostředků ve skupině spravovaných prostředků v případě, že prostředky nejsou definovány v [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). V přírůstkovém režimu opětovné nasazení aplikace opustí stávající prostředky beze změny. Další informace najdete v tématu [režimy nasazení Azure Resource Manager](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>Adresa URL koncového bodu oznámení

Volitelně můžete zadat koncový bod Webhooku HTTPS pro příjem oznámení o všech operacích CRUD na instancích spravované aplikace plánu.

## <a name="customize-allowed-customer-actions-optional"></a>Přizpůsobení povolených akcí zákazníka (volitelné)

Volitelně můžete určit, které akce mohou zákazníci provádět u spravovaných prostředků kromě `*/read` akcí, které jsou ve výchozím nastavení k dispozici.

Pokud zvolíte tuto možnost, musíte zadat buď akce ovládacího prvku, nebo povolené datové akce, nebo obojí. Další informace najdete v tématu [Principy přiřazení zamítnutí pro prostředky Azure](../role-based-access-control/deny-assignments.md). Dostupné akce najdete v tématu [Azure Resource Manager operací poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md). Například pokud chcete, aby uživatelé mohli restartovat virtuální počítače, přidejte `Microsoft.Compute/virtualMachines/restart/action` do povolených akcí.

## <a name="choose-who-can-manage-the-application"></a>Zvolit, kdo může aplikaci spravovat

Musíte určit, kdo může spravovat spravovanou aplikaci v každém z vybraných cloudů: _veřejný cloud Azure_ a _Azure Government_. Shromážděte následující údaje:

- **Azure Active Directory ID tenanta** – ID TENANTA Azure AD (označované taky jako ID adresáře) obsahující identity uživatelů, skupin nebo aplikací, kterým chcete udělit oprávnění. ID tenanta služby Azure AD najdete na Azure Portal v části [vlastnosti pro Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).
- **Autorizace** – přidejte Azure Active Directory ID objektu pro každého uživatele, skupinu nebo aplikaci, kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Identifikujte uživatele podle ID objektu zabezpečení, které najdete v okně [Azure Active Directory uživatelé na Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Pro každé ID objektu zabezpečení přidružíte jednu z předdefinovaných rolí Azure AD (vlastník nebo přispěvatel). Vybraná role popisuje oprávnění, která bude mít objekt zabezpečení u prostředků v rámci předplatného zákazníka. Další informace najdete v tématu [Předdefinované role v Azure](../role-based-access-control/built-in-roles.md). Další informace o řízení přístupu na základě role (RBAC) najdete v tématu [Začínáme s RBAC v Azure Portal](../role-based-access-control/overview.md).

> [!NOTE]
> I když můžete přidat až 100 autorizací na oblast Azure, většinou je snazší vytvořit skupinu uživatelů služby Active Directory a zadat její ID v ID objektu zabezpečení. To vám umožní přidat další uživatele do skupiny pro správu po nasazení plánu a snížit nutnost aktualizovat plán jenom na přidání dalších autorizací.

## <a name="policy-settings"></a>Nastavení zásad

Můžete použít [zásady Azure](../governance/policy/index.yml) na spravovanou aplikaci a zadat požadavky na dodržování předpisů pro nasazené řešení. Definice zásad a formátování hodnot parametrů najdete v tématu [Ukázky Azure Policy](../governance/policy/samples/index.md).

Můžete nakonfigurovat maximálně pět zásad a jenom jednu instanci každého typu zásad. Některé typy zásad vyžadují další parametry.

| Typ zásady | Požadované parametry zásad |
| ------------ | ------------- |
| Azure SQL Database šifrování | No |
| Nastavení služby Azure SQL Server Audit | Yes |
| Azure Data Lake Store šifrování | No |
| Nastavení diagnostiky auditu | Yes |
| Auditovat dodržování předpisů v umístění prostředku | No |
|||

Pro každý typ zásad, který přidáte, je nutné přiřadit SKU zásady úrovně Standard nebo Free. Pro zásady auditu se vyžaduje standardní SKU. Názvy zásad jsou omezené na 50 znaků.

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit nabídku aplikace Azure na komerčním webu Marketplace](create-new-azure-apps-offer.md)

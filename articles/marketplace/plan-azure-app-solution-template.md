---
title: Plánování šablony řešení pro nabídku aplikací Azure
description: Přečtěte si, co je potřeba k vytvoření plánu šablony řešení pro novou nabídku aplikací Azure pomocí portálu pro komerční tržiště v partnerském centru Microsoftu.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: aab856b1e1d2d991cc4964d061a990dbedbeddb7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98876507"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>Plánování šablony řešení pro nabídku aplikací Azure

Tento článek popisuje požadavky pro publikování plánu šablon řešení pro nabídku aplikace Azure. Plán šablony řešení je jeden ze dvou typů plánů podporovaných nabídkami aplikací Azure. Informace o rozdílech mezi těmito dvěma typy plánů najdete v tématu [typy plánů](plan-azure-application-offer.md#plans). Pokud jste to ještě neudělali, přečtěte si téma [plánování nabídky aplikací Azure](plan-azure-application-offer.md).

Typ plánu šablony řešení vyžaduje [šablonu Azure Resource Manager (šablona ARM)](../azure-resource-manager/templates/overview.md) k automatickému nasazení vaší infrastruktury řešení.

## <a name="solution-template-requirements"></a>Požadavky na šablonu řešení

| Požadavky | Podrobnosti |
| ------------ | ------------- |
| Fakturace a měření | Plány šablon řešení nejsou transakční, ale dají se použít k nasazení placených nabídek virtuálních počítačů, které se účtují prostřednictvím komerčního tržiště Microsoftu. Prostředky, které jsou nasazené šablonou ARM řešení, se nastavují v předplatném Azure zákazníka. Virtuální počítače s průběžnými platbami se účtují u zákazníka prostřednictvím Microsoftu a účtují se prostřednictvím předplatného Azure zákazníka. <br><br> V případě fakturace s využitím vlastní licence (BYOL), i když Microsoft účtuje náklady na infrastrukturu, které se účtují v rámci předplatného zákazníka, vyúčtujete poplatky za licence na software přímo pro zákazníka. |
| Virtuální pevný disk kompatibilní s Azure (VHD) | Virtuální počítače musí být postavené na Windows nebo Linux. Další informace naleznete v tématu:<ul><li>[Vytvoření technického prostředku virtuálního počítače Azure](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (pro virtuální pevné disky s Windows)</li><li>[Distribuce systému Linux schválená v Azure](../virtual-machines/linux/endorsed-distros.md) (pro virtuální pevné disky se systémem Linux).</li></ul> |
| Přisuzování využití ze strany zákazníků | U všech šablon řešení, které jsou publikovány na Azure Marketplace, je nutné povolit přidělení zákaznického využití. Další informace o přidaných zákaznických využití a o tom, jak ji povolit, najdete v tématu věnovaném [přidělení zákaznického využívání Azure partnerovi](azure-partner-customer-usage-attribution.md). |
| Použití spravovaných disků | [Managed disks](../virtual-machines/managed-disks-overview.md) je výchozí možností pro trvalé disky virtuálních počítačů infrastruktury jako služby (IaaS) v Azure. Spravované disky je nutné použít v šablonách řešení.<ul><li>Pokud chcete aktualizovat šablony řešení, postupujte podle pokynů v části [použití spravovaných disků v šablonách Azure Resource Manager](../virtual-machines/using-managed-disks-template-deployments.md)a použijte uvedené [ukázky](https://github.com/Azure/azure-quickstart-templates).</li><li>Pokud chcete publikovat VHD jako image v Azure Marketplace, importujte základní virtuální pevný disk spravovaných disků do účtu úložiště pomocí [Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) nebo rozhraní příkazového [řádku Azure](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) .</ul> |
| Balíček pro nasazení | Budete potřebovat balíček pro nasazení, který zákazníkům umožní nasadit váš plán. Pokud vytvoříte více plánů, které vyžadují stejnou technickou konfiguraci, můžete použít stejný balíček plánu. Podrobnosti najdete v další části: balíček pro nasazení. |
|||

## <a name="deployment-package"></a>Balíček pro nasazení

Balíček pro nasazení obsahuje všechny soubory šablon potřebné pro tento plán a všechny další prostředky, které jsou zabaleny jako soubor. zip.

Všechny aplikace Azure musí tyto dva soubory zahrnout do kořenové složky archivu. zip:

- Soubor šablony Správce prostředků s názvem [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Tato šablona definuje prostředky, které se mají nasadit do předplatného Azure zákazníka. Příklady šablon Správce prostředků najdete v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/) nebo v příslušném úložišti [šablon pro rychlý Start Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .
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

> [!NOTE]
> Pokud publikujete soukromý plán, můžete později změnit jeho viditelnost na veřejné. Po publikování veřejného plánu ale nemůžete změnit jeho viditelnost na Private.

Pro plány šablon řešení můžete také zvolit možnost skrýt plán z Azure Marketplace. To může být vhodné, pokud plán je nasazen pouze nepřímo prostřednictvím jiné šablony řešení nebo spravované aplikace.

> [!NOTE]
> U předplatných Azure vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP) se nepodporují privátní plány.

Další informace najdete v tématu [soukromé nabídky na komerčním webu Microsoft Marketplace](private-offers.md).

## <a name="next-steps"></a>Další kroky

- [Jak vytvořit nabídku aplikace Azure na komerčním webu Marketplace](create-new-azure-apps-offer.md)
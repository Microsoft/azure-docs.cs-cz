---
title: Vytvoření technických datových zdrojů pro nabídku virtuálních strojů pro Azure Marketplace
description: Vysvětluje, jak vytvořit technické prostředky pro nabídku virtuálních strojů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: dsindona
ms.openlocfilehash: a62af1d8d751d36150c236280077cde8f6547385
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273966"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Vytvoření technických prostředků pro nabídku virtuálních strojů

> [!IMPORTANT]
> dubna 2020 začneme s přesouváním nabídky virtuálních strojů Azure do Partnerského centra. Po migraci vytvoříte a spravujete nabídky v Centru partnerů. Postupujte podle pokynů v [části Vytvoření technických prostředků virtuálního počítače Azure](https://aka.ms/AzureVMTechAsset) a spravujte migrované nabídky.

Tato část vás provede vytvořením a konfigurací technických prostředků pro nabídku virtuálního počítače (VM) pro Azure Marketplace.  Virtuální počítač obsahuje dvě součásti: virtuální pevný disk (VHD) řešení a volitelné přidružené datové disky.  

- *Virtuální pevné disky (VND)* obsahující operační systém a vaše řešení, které nasadíte s nabídkou Azure Marketplace. Proces přípravy virtuálního pevného disku se liší v závislosti na tom, zda se jedná o virtuální počítač založený na Linuxu, Windows nebo na vlastním webu.
- *Datové disky* představují vyhrazené trvalé úložiště pro virtuální počítač. *Nepoužívejte* řešení VHD (například `C:` jednotka) k ukládání trvalých informací.

Bitová kopie virtuálního počítače obsahuje jeden disk operačního systému a nula nebo více datových disků. Jeden virtuální disk je potřeba na disk. Dokonce i prázdné datové disky vyžadují vytvoření virtuálního pevného disku.
Je nutné nakonfigurovat operační systém virtuálního počítače, velikost virtuálního počítače, porty pro otevření a až 15 připojených datových disků.

> [!TIP] 
> Bez ohledu na to, který operační systém používáte, přidejte pouze minimální počet datových disků potřebných pro skladovou položku. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazení, ale mohou vždy přidat disky během nebo po nasazení. 

> [!IMPORTANT]
> *Neměňte počet disků v nové verzi bitové kopie.* Pokud je nutné změnit konfiguraci datových disků v bitové kopii, definujte novou skladovou položku. Publikování nové verze image s různými počty disků bude mít potenciál prolomit nové nasazení na základě nové verze image v případech automatickéškálování, automatické nasazení řešení prostřednictvím šablon Azure Resource Manager a další scénáře.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Základní technické znalosti

Navrhování, vytváření a testování těchto prostředků vyžaduje čas a vyžaduje technické znalosti platformy Azure i technologií používaných k sestavení nabídky. Kromě domény řešení by váš technický tým měl mít znalosti o následujících technologiích společnosti Microsoft: 
-    Základní znalosti [služeb Azure](https://azure.microsoft.com/services/) 
-    Jak [navrhovat a navrhovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
-    Pracovní znalosti virtuálních [počítačů Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) a Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
-    Pracovní znalosti [Správce prostředků Azure](https://azure.microsoft.com/features/resource-manager/)
-    Pracovní znalosti [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Navrhované nástroje 

Chcete-li spravovat virtuální diat a virtuální virtuální měna, zvolte jedno nebo obě následující skriptovací prostředí:
-    [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-    [Azure CLI](https://docs.microsoft.com/cli/azure)

Kromě toho doporučujeme přidat do vývojového prostředí následující nástroje: 

-    [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-    [Visual Studio Code](https://code.visualstudio.com/)
    *    Rozšíření: [Nástroje Azure Správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *    Rozšíření: [Zkrášlení](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *    Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Doporučujeme také zkontrolovat dostupné nástroje na stránce [Nástroje pro vývojáře Azure](https://azure.microsoft.com/tools/) a pokud používáte Visual Studio, [tržiště Visual Studia](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Další kroky

Následující články v této části vás provedou kroky vytváření a registrace těchto prostředků virtuálních zařízení:

1. [Vytvoření virtuálního pevného disku kompatibilního s Azure](./cpp-create-vhd.md) vysvětluje, jak vytvořit virtuální pevný disk založený na Linuxu nebo Windows, který je kompatibilní s Azure.  Zahrnuje osvědčené postupy, jako je například změna velikosti, opravy a příprava virtuálního virtuálního mísa pro nahrávání.

2. [Připojení k virtuálnímu počítači](./cpp-connect-vm.md) vysvětluje, jak se vzdáleně připojit k nově vytvořenému virtuálnímu počítači a přihlásit se k němu.  Tento článek také vysvětluje, jak zastavit virtuální počítač ušetřit na nákladech na využití.

3. [Konfigurace virtuálního počítače](./cpp-configure-vm.md) vysvětluje, jak zvolit správnou velikost virtuálního pevného disku, zobecnit bitovou kopii, použít nejnovější aktualizace (opravy) a naplánovat vlastní konfigurace.

4. [Nasazení virtuálního počítače z virtuálního pevného disku](./cpp-deploy-vm-vhd.md) vysvětluje, jak zaregistrovat virtuální počítač z virtuálního pevného disku nasazeného v Azure.  Obsahuje seznam požadovaných nástrojů a jejich použití k vytvoření image virtuálního počítače uživatele a následné nasazení do Azure pomocí [portálu Microsoft Azure nebo](https://ms.portal.azure.com/) skriptů PowerShellu. 

5. [Certifikujte image virtuálního počítače](./cpp-certify-vm.md) vysvětluje, jak otestovat a odeslat image virtuálního počítače pro certifikaci Azure Marketplace. Vysvětluje, kde získat *nástroj certifikačnítest pro* nástroj Azure Certified a jak tento nástroj použít k certifikaci image virtuálního počítače. 

6. [Získejte Identifikátor URI SAS](./cpp-get-sas-uri.md) vysvětluje, jak získat identifikátor URI sdíleného přístupového podpisu (SAS) pro image virtuálních počítačů.
 
Jako podpůrný článek společné [problémy s adresou URL sdíleného přístupu](./cpp-common-sas-url-issues.md) obsahují seznam některých běžných problémů, se kterými se můžete setkat pomocí identifikátorů URI SAS, a odpovídající možná řešení.

Po dokončení všech těchto kroků budete připraveni [publikovat nabídku virtuálních počítačích](./cpp-publish-offer.md) na Azure Marketplace.

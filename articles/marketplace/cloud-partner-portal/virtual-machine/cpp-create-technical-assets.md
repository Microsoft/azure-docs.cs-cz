---
title: Vytváření technických prostředků pro nabídku virtuálního počítače pro Azure Marketplace
description: Vysvětluje, jak vytvořit technické prostředky pro nabídku virtuálního počítače v Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: 45d0ff5b7b3fea1566b13b61bd01cc17da61e4b3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824509"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Vytváření technických prostředků pro nabídku virtuálního počítače

V této části se seznámíte s vytvořením a konfigurací technických prostředků pro nabídku virtuálního počítače pro Azure Marketplace.  Virtuální počítač obsahuje dvě komponenty: virtuální pevný disk řešení (VHD) a volitelné přidružené datové disky.  

- *Virtuální pevné disky (VHD)* , které obsahují operační systém a řešení, které nasadíte pomocí nabídky Azure Marketplace. Proces přípravy VHD se liší v závislosti na tom, jestli se jedná o virtuální počítač založený na platformě Linux, Windows nebo na vlastním virtuálním počítači.
- *Datové disky* reprezentují vyhrazené a trvalé úložiště pro virtuální počítač. Nepoužívejte *virtuální* pevný disk řešení (například `C:` jednotka) k ukládání trvalých informací.

Image virtuálního počítače obsahuje jeden disk s operačním systémem a žádný nebo více datových disků. Pro každý disk je potřeba jeden VHD. I prázdné datové disky vyžadují vytvoření virtuálního pevného disku.
Musíte nakonfigurovat operační systém virtuálního počítače, velikost virtuálního počítače, porty, které se mají otevřít, a až 15 připojených datových disků.

> [!TIP] 
> Bez ohledu na použitý operační systém přidávejte jenom nejmenší počet datových disků, které skladová jednotka potřebuje. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazování, ale mohou vždy přidávat disky během nebo po nasazení. 

> [!IMPORTANT]
> *Neměňte počet disků v nové verzi image.* Pokud potřebujete znovu nakonfigurovat datové disky v imagi, definujte novou SKLADOVOU položku. Publikování nové verze image s různými počty disků bude mít potenciál na narušení nového nasazení založeného na nové verzi image v případech automatického škálování, automatického nasazení řešení prostřednictvím Azure Resource Manager šablon a dalších scénářů.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>Základní technické poznatky

Návrh, sestavování a testování těchto assetů trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky. Kromě vaší domény řešení by váš technický tým měl mít znalosti o těchto technologiích společnosti Microsoft: 
-   Základní porozumění [službám Azure](https://azure.microsoft.com/services/) 
-   [Návrh a architekt aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
-   Praktické znalosti [Virtual Machines Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) a [sítí Azure](https://azure.microsoft.com/services/?filter=networking)
-   Praktické znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Praktické znalosti formátu [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Navrhované nástroje 

Vyberte jedno nebo obě následující skriptovací prostředí, která vám pomůžou spravovat virtuální pevné disky a virtuální počítače:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Kromě toho doporučujeme do vývojového prostředí přidat následující nástroje: 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Navrhujeme také kontrolu dostupných nástrojů na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/tools/) a pokud používáte Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Další kroky

Následující články v této části vás seznámí s postupem vytvoření a registrace prostředků virtuálního počítače:

1. [Vytvoření virtuálního pevného disku kompatibilního s Azure](./cpp-create-vhd.md) vysvětluje, jak vytvořit virtuální pevný disk s operačním systémem Linux nebo Windows, který je kompatibilní s Azure.  Zahrnuje osvědčené postupy, jako je třeba určení velikosti, opravování a Příprava virtuálního počítače pro nahrávání.

2. [Připojení k virtuálnímu počítači](./cpp-connect-vm.md) vysvětluje, jak se vzdáleně připojit k nově VYTVOŘENému virtuálnímu počítači a přihlásit se k němu.  Tento článek také vysvětluje, jak zastavit virtuální počítač, aby ušetřil náklady na používání.

3. [Konfigurace virtuálního počítače](./cpp-configure-vm.md) vysvětluje, jak vybrat správnou velikost virtuálního pevného disku, zobecnit bitovou kopii, použít nedávné aktualizace (opravy) a naplánovat vlastní konfigurace.

4. Postup [nasazení virtuálního počítače z virtuálního pevného disku](./cpp-deploy-vm-vhd.md) vysvětluje, jak zaregistrovat virtuální počítač z virtuálního pevného disku nasazeného v Azure.  Seznam požadovaných nástrojů a jejich použití k vytvoření uživatelské image virtuálního počítače a jeho nasazení do Azure pomocí [portál Microsoft Azure](https://ms.portal.azure.com/) nebo skriptů PowerShellu. 

5. Certifikace [image virtuálního počítače](./cpp-certify-vm.md) vysvětluje, jak otestovat a odeslat image virtuálního počítače pro Azure Marketplace certifikace. Vysvětluje, kde získat nástroj *certifikace pro certifikaci pro nástroj Azure Certified* a jak tento nástroj použít k certifikaci vaší image virtuálního počítače. 

6. [Získání identifikátoru URI SAS](./cpp-get-sas-uri.md) vysvětluje, jak získat identifikátor URI sdíleného přístupového podpisu (SAS) pro vaše image virtuálních počítačů.
 
V rámci podpůrného článku [problémy s adresou URL běžných sdílených přístupových podpisů](./cpp-common-sas-url-issues.md) uvádí některé běžné problémy, se kterými se můžete setkat pomocí identifikátorů URI SAS a odpovídajících možných řešení.

Po dokončení všech těchto kroků budete připraveni k [publikování nabídky virtuálních počítačů](./cpp-publish-offer.md) do Azure Marketplace.

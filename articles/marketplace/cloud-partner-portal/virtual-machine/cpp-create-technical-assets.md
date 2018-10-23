---
title: Vytvoření technických prostředků pro nabídky virtuálních počítačů pro Azure Marketplace | Dokumentace Microsoftu
description: Vysvětluje, jak se vytvářejí technické materiály pro nabídky virtuálních počítačů na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/20/2018
ms.author: pbutlerm
ms.openlocfilehash: da3e7e44ed1ad0698392ba1afcb59887ffbc53fe
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639629"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>Vytvoření technických prostředků pro nabídky virtuálních počítačů

Tato část vás provede vytvořením a konfigurace technických prostředků pro nabídky virtuálních počítačů (VM) pro Azure Marketplace.  Virtuální počítač obsahuje dvě součásti: řešení virtuálního pevného disku (VHD) a volitelné přidružené datové disky.  

- *Virtuální pevné disky (VHD)*, který obsahuje operační systém a řešení, kterou nasadíte v rámci vaší nabídky Azure Marketplace. Proces přípravy virtuálního pevného disku se liší v závislosti na tom, jestli je založenou na Linuxu a Windows, nebo virtuální počítač založený na vlastní.
- *Datové disky* představují vyhrazené, trvalé úložiště pro virtuální počítač. Proveďte *není* řešení virtuálního pevného disku (například `C:` jednotky) k ukládání informací o trvalé.

Image virtuálního počítače obsahuje jeden operační systém disku a nula nebo více datových disků. Jeden virtuální pevný disk je potřeba na disk. I prázdné datové disky vyžadují vytvoření virtuálního pevného disku.
Musíte nakonfigurovat virtuální počítač operačního systému, velikost virtuálního počítače, porty otevřít a až do 15 připojené datové disky.

> [!TIP] 
> Bez ohledu na použitý operační systém přidávejte jenom nejmenší počet datových disků, které skladová jednotka potřebuje. Zákazníci, nelze odebrat disky, které jsou součástí image v době nasazování, ale můžou vždy přidat disky během nebo po nasazení. 

> [!IMPORTANT]
> *Neměňte počet disků v nové verzi image.* Pokud je nutné překonfigurovat datové disky na obrázku, definujte novou skladovou Položku. Publikování nové verze image s počty jiný disk bude mít potenciál zásadní nové nasazení založené na novou verzi image v případech, automatické škálování, automatické nasazení řešení pomocí šablon Azure Resource Manageru a další scénáře.


## <a name="fundamental-technical-knowledge"></a>Základní znalosti

Navrhování, sestavování a testování tyto prostředky dobu trvat a vyžaduje technické znalosti platformy Azure a s technologiemi použitými k vytvoření nabídky. Kromě vaší domény řešení váš technický tým by měl mít znalosti následující technologie společnosti Microsoft: 
-   Základní znalosti o [služeb Azure](https://azure.microsoft.com/services/) 
-   Jak [návrhu a architektury aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
-   Praktické znalosti [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [služby Azure Storage](https://azure.microsoft.com/services/?filter=storage) a [sítě Azure](https://azure.microsoft.com/services/?filter=networking)
-   Praktické znalosti [Azure Resource Manageru](https://azure.microsoft.com/features/resource-manager/)
-   Praktické znalosti [JSON](https://www.json.org/)


## <a name="suggested-tools"></a>Doporučené nástroje 

Zvolte jednu nebo obě z následujících skriptovací prostředí ke správě virtuálních pevných disků a virtuálních počítačů:
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Kromě toho doporučujeme přidat následující nástroje do svého vývojového prostředí: 

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Rozšíření: [nástroje Azure Resource Manageru](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Rozšíření: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Doporučujeme také kontrola nástroje dostupné v [Azure Developer Tools](https://azure.microsoft.com/tools/) stránky a pokud používáte Visual Studio [Visual Studio Marketplace](https://marketplace.visualstudio.com/).


## <a name="next-steps"></a>Další postup

V dalších článcích v této části vás provede kroky pro vytvoření a registrace tyto prostředky virtuálních počítačů:

1. [Vytvoření virtuálního pevného disku kompatibilního s Azure](./cpp-create-vhd.md) vysvětluje, jak vytvořit buď nebo Windows založenou na Linuxu virtuální pevný disk, který je kompatibilní s Azure.  Obsahuje osvědčené postupy, jako je například velikost, použití dílčích oprav a příprava virtuálního počítače pro odesílání.

2. [Připojení k virtuálnímu počítači](./cpp-connect-vm.md) vysvětluje, jak pro vzdáleně připojit k nově vytvořenému virtuálnímu počítači a přihlášení do něj.  Tento článek také vysvětluje, jak zastavit virtuální počítač uložit na náklady na využití.

3. [Konfigurace virtuálního počítače](./cpp-configure-vm.md) vysvětluje, jak k výběr správné velikosti virtuálního pevného disku, zobecnění image, použít poslední aktualizace (opravy) a plánování vlastní konfigurace.

4. [Nasazení virtuálního počítače z virtuálního pevného disku](./cpp-deploy-vm-vhd.md) vysvětluje postup při registraci virtuálního počítače z VHD nasazení Azure.  Obsahuje nástroje potřebné a jak se dají použít k vytvoření uživatelské image virtuálního počítače a pak ji nasadit do Azure s využitím buď [portálu Microsoft Azure](https://ms.portal.azure.com/) nebo skripty prostředí PowerShell. 

5. [Certifikovat image virtuálního počítače](./cpp-certify-vm.md) vysvětluje, jak otestovat a odešlete image virtuálního počítače k certifikaci Azure Marketplace. Vysvětluje, kde lze získat *certifikační nástroj pro testování Azure Certified* nástroj a jak tento nástroj použijte k certifikaci vaší image virtuálního počítače. 

6. [Získat identifikátor URI SAS](./cpp-get-sas-uri.md) vysvětluje, jak získat sdílený přístupový podpis (SAS) identifikátor URI pro vaši Image virtuálního počítače.
 
Jako podpůrný článku [sdílené běžné potíže s přístupem k podpisu URL](./cpp-common-sas-url-issues.md) uvádí některé běžné problémy, které můžou nastat při používání identifikátorů URI SAS a odpovídající možná řešení.

Po dokončení těchto kroků, které budete moci [publikování vaší nabídky virtuálních počítačů](./cpp-publish-offer.md) na webu Azure Marketplace.

---
title: Testovací verze virtuálního počítače na záložce portál partnerů cloudu pro Azure Marketplace | Dokumentace Microsoftu
description: Popisuje kartu Test Drive použité při vytváření virtuálního počítače Azure Marketplace nabídku.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 97fb21dc390bd365357f6395c72aa282423c83c9
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639644"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta testovací verze virtuálního počítače

<!-- TD: The AMP tree needs a conceptual/business overview of Test Drive. I've deleted all the marketing fluff and most of overview from this topic.   See also https://azure.microsoft.com/blog/azure-marketplace-test-drive/ and https://github.com/Azure/AzureTestDrive/wiki/What-is-a-Test-Drive. --> 

**Test Drive** karty **nová nabídka** stránky umožňuje, aby vás vaši potenciální zákazníci s praktické, samostatně prováděného ukázkové klíčové funkce a výhody, které jsme vám ukázali v produktu standardizované scénář.  Testovací verze je volitelná funkce pro typy nabídek, které podporují testovací verze.  Test Drive, někdy potřebuje Podpůrné prostředky správně implementovaná.  Další informace najdete v článku [vyzkoušejte Azure Marketplace](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Chcete tuto funkci povolit na **Test Drive** klikněte na tlačítko **Ano** možnost **povolit si testovací jízdu**.  **Test Drive** karta zobrazuje pole, které jsou k dispozici pro úpravy.  Připojený hvězdička (*) na název pole označuje, že je povinný.

![Karta testu jednotky ve formuláři nová nabídka pro virtuální počítače](./media/publishvm_007.png)

<br/>

Následující tabulka popisuje účel a obsah těchto polí.


|  **Pole**                |     **Popis**                                                          |
|  ---------                |     ---------------                                                          |
|  *Podrobnosti*   |  |
| **Popis**           | Poskytovat přehled o scénáři vaše testovací verze. Tento text se zobrazí uživateli, zatímco testovací verze se zřizuje. Toto pole podporuje základní HTML, pokud byste chtěli poskytnout formátovaný obsah.  |
| **Uživatelská příručka**           | Nahrajte podrobné uživatelská příručka (PDF), která pomáhá Test Drive uživatelům pochopit, jak použít řešení.  |
| **Test jednotky – ukázkové Video** | Nahrání videa, která prezentuje vaše řešení.  Pokud si vyberete tuhle možnost, je nutné zadat název, adresu URL videa (hostované na YouTube nebo Vimeo) a miniaturu (v pixelech 533 x 324) pro video. |
| *Technické konfigurace* |  |
| **Instance**             | Zadejte dostupnost v oblastech a relativně dostupnosti instancí virtuálních počítačů (klikněte na ikonu informace o další informace).  <br/>Potenciální souběžných relací testovací verze by neměla překročit limit kvóty pro vaše předplatné.  Nejprve se vypočte takto: [číslo z oblasti dílčích] x [aktivní instance] + [číslo z oblasti dílčích] x [teplé instance] + [číslo z oblasti dílčích] x [studenou instance] |
| **Doba trvání testu jednotky**   | Maximální časový limit relace doba trvání v hodinách. Po překročení tohoto období se automaticky ukončí relaci testovací verze.  |
|**Šablona ARM testu jednotky**| Nahrajte šablonu Azure Resource Manageru, které jsou přidružené k této testovací verzi. Další informace najdete v tématu [transformace šablony nasazení virtuálního počítače pro Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Přístup k informacím**    | Přístup ke službě Azure Resource Manageru a zkušební verze přihlašovací údaje jako prostý text nebo jednoduchého kódu HTML. |
| *Podrobnosti předplatného nasazení testu jednotky* |  |
| **Id předplatného Azure** | Po přihlášení můžete získat [portálu Microsoft Azure](https://ms.portal.azure.com) a kliknete na **předplatná** na levém řádku nabídek. (Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Tento identifikátor musí být GUID ve formátu `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **Id klienta Azure AD**    | ID tenanta Azure Active Directory  Po přihlášení můžete získat [portálu Microsoft Azure](https://ms.portal.azure.com) a kliknete na **Azure Active Directory** na levém řádku nabídek kliknete **vlastnosti** v řádku nabídek střední Zkopírujte **ID adresáře** z formuláře.  Tento identifikátor musí být také identifikátor GUID.  Pokud je pole prázdné, musíte vytvořit ID tenanta pro vaši organizaci. |
| **Id aplikace Azure AD**       | Identifikátor pro vaše řešení registrované virtuálního počítače Azure  |
| **Klíč aplikace Azure AD**      | Ověřovací klíč pro registrované řešení |
|  |  |

<br/>

V dalším [Marketplace](./cpp-marketplace-tab.md) kartu, bude poskytovat uvádění na trh a právní informace o řešení.

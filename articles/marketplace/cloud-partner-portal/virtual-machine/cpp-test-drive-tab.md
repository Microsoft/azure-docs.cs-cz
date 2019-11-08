---
title: Karta testovací jednotka virtuálního počítače v portál partnerů cloudu pro Azure Marketplace
description: Popisuje kartu testovací jednotka použitou při vytváření nabídky virtuálního počítače s Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: 94ebbcca50916f1675ab990a4b45f3b90e069104
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808872"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta testovací jednotka virtuálního počítače

Karta **Test Drive** na nové stránce **nabídky** vám umožní poskytnout potenciálním zákazníkům praktickou ukázku klíčových funkcí a výhod vašeho produktu, které jsou znázorněné ve standardizovaném scénáři.  Testovací jednotka je volitelnou funkcí pro typy nabídek, které podporují testovací jednotku.  Test Drive vyžaduje, aby byly správně implementovány pomocné prostředky.  Další informace najdete v článku [Azure Marketplace testovacím disku](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Tuto funkci povolíte tak, že na kartě **testovací jednotka** kliknete na možnost **Ano** v nabídce **Povolit testovací jednotku**.  Karta **testovací jednotka** zobrazuje pole, která jsou k dispozici pro úpravy.  Připojená hvězdička (*) v názvu pole označuje, že je požadovaná.

![Karta testovací jednotka na novém formuláři nabídky pro virtuální počítače](./media/publishvm_007.png)


## <a name="field-values"></a>Hodnoty polí

Následující tabulka popisuje účel a obsah těchto polí.  Požadovaná pole jsou indicted hvězdičkou (*).


|    Pole                  |       Popis                                                            |
|  ---------                |     ---------------                                                          |
|  *Podrobnosti*   |  |
| **Popis\***           | Poskytněte Přehled scénářů testovacích jednotek. Tento text se zobrazí uživateli při zřizování testovací jednotky. Toto pole podporuje základní kód HTML, pokud chcete zadat formátovaný obsah.  |
| **Ruční\* uživatele**           | Nahrajte podrobného uživatelského ručního (. PDF), který pomůže uživatelům testovacích jednotek pochopit, jak vaše řešení používáte.  |
| **Ukázkové video testovacího disku** | Nahrajte video, které prezentuje vaše řešení.  Pokud jste vybrali tuto možnost, musíte zadat jméno, adresu URL videa (hostované na YouTube nebo Vimeo) a miniaturu (533x324 pixelů) pro video. |
| *Technická konfigurace* |  |
| **Instance\***             | Určete dostupnost oblasti a relativně dostupnost instance virtuálního počítače (další podrobnosti získáte kliknutím na ikonu informace).  <br/>Potenciální souběžné relace testovacích jednotek by neměly překročit limit kvót pro vaše předplatné.  Předchozí hodnota se vypočítá takto: [počet vybraných oblastí] × [horké instance] + [počet vybraných oblastí] × [teplé instance] + [počet vybraných oblastí] x [studené instance] |
| **\* doby trvání testovacích jednotek**   | Maximální doba trvání relace v hodinách Po uplynutí tohoto časového období se relace testovacího disku automaticky ukončí.  |
|**\* šablonou ARM testovacích jednotek**| Nahrajte šablonu Azure Resource Manager přidruženou k této testovací jednotce. Další informace najdete v tématu [transformace šablony nasazení virtuálního počítače pro Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Přístup k informacím\***    | Azure Resource Manager přístup a zkušební přihlašovací údaje, které jsou zapsané jako prostý text nebo jednoduché HTML. |
| *Podrobnosti o předplatném nasazení testovacích jednotek* |  |
| **ID předplatného Azure\*** | Lze získat přihlášením do [portál Microsoft Azure](https://ms.portal.azure.com) a kliknutím na **odběry** na levém řádku nabídek. (Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Tento identifikátor by měl být identifikátorem GUID formuláře `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **ID tenanta Azure AD\***    | Azure Active Directory ID tenanta.  Lze získat přihlášením do [portál Microsoft Azure](https://ms.portal.azure.com) a kliknutím na **Azure Active Directory** na levé panelu nabídek a následným kliknutím na položku **vlastnosti** v prostřední nabídce nabídek a zkopírováním **ID adresáře** z formuláře.  Tento identifikátor by měl také být identifikátor GUID.  Pokud je toto pole prázdné, musíte vytvořit ID tenanta pro vaši organizaci. |
| **ID Aplikace Azure AD\***       | Identifikátor vašeho registrovaného řešení virtuálního počítače Azure  |
| **Aplikace Azure AD Key\***      | Ověřovací klíč pro vaše registrované řešení |
|   |   |


## <a name="next-steps"></a>Další kroky

Na další kartě [Marketplace](./cpp-marketplace-tab.md) budete poskytovat marketingové a právní informace o vašem řešení.

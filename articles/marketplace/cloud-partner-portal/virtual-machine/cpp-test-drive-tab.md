---
title: Karta testovací jednotka virtuálního počítače v portál partnerů cloudu pro Azure Marketplace
description: Popisuje kartu testovací jednotka použitou při vytváření nabídky virtuálního počítače s Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: adac73d64feb6280c5043776249072e9f7595faa
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142988"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta testovací jednotka virtuálního počítače

> [!IMPORTANT]
> Od 13. dubna 2020 začneme přesouvat správu nabídek virtuálních počítačů Azure do partnerského centra. Po dokončení migrace vytvoříte a budete spravovat své nabídky v partnerském centru. Postupujte podle pokynů v tématu [Vytvoření virtuálního počítače Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) pro správu migrovaných nabídek.

Karta **Test Drive** na nové stránce **nabídky** vám umožní poskytnout potenciálním zákazníkům praktickou ukázku klíčových funkcí a výhod vašeho produktu, které jsou znázorněné ve standardizovaném scénáři.  Testovací jednotka je volitelnou funkcí pro typy nabídek, které podporují testovací jednotku.  Test Drive vyžaduje, aby byly správně implementovány pomocné prostředky.  Další informace najdete v článku [Azure Marketplace testovacím disku](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Tuto funkci povolíte tak, že na kartě **testovací jednotka** kliknete na možnost **Ano** v nabídce **Povolit testovací jednotku**.  Karta **testovací jednotka** zobrazuje pole, která jsou k dispozici pro úpravy.  Připojená hvězdička (*) v názvu pole označuje, že je požadovaná.

![Karta testovací jednotka na novém formuláři nabídky pro virtuální počítače](./media/publishvm_007.png)


## <a name="field-values"></a>Hodnoty polí

Následující tabulka popisuje účel a obsah těchto polí.  Požadovaná pole jsou indicted hvězdičkou (*).


|    Pole                  |       Popis                                                            |
|  ---------                |     ---------------                                                          |
|  *Zobrazí*   |  |
| **Popis\***           | Poskytněte Přehled scénářů testovacích jednotek. Tento text se zobrazí uživateli při zřizování testovací jednotky. Toto pole podporuje základní kód HTML, pokud chcete zadat formátovaný obsah.  |
| **Uživatelská příručka\***           | Nahrajte podrobného uživatelského ručního (. PDF), který pomůže uživatelům testovacích jednotek pochopit, jak vaše řešení používáte.  |
| **Ukázkové video testovacího disku** | Nahrajte video, které prezentuje vaše řešení.  Pokud jste vybrali tuto možnost, musíte zadat jméno, adresu URL videa (hostované na YouTube nebo Vimeo) a miniaturu (533x324 pixelů) pro video. |
| *Technická konfigurace* |  |
| **Instance\***             | Určete dostupnost oblasti a relativně dostupnost instance virtuálního počítače (další podrobnosti získáte kliknutím na ikonu informace).  <br/>Potenciální souběžné relace testovacích jednotek by neměly překročit limit kvót pro vaše předplatné.  Předchozí hodnota se vypočítá takto: [počet vybraných oblastí] × [horké instance] + [počet vybraných oblastí] × [teplé instance] + [počet vybraných oblastí] x [studené instance] |
| **Doba trvání testovacího disku\***   | Maximální doba trvání relace v hodinách Po uplynutí tohoto časového období se relace testovacího disku automaticky ukončí.  |
|**Šablona ARM testovacích jednotek\***| Nahrajte šablonu Azure Resource Manager přidruženou k této testovací jednotce. Další informace najdete v tématu [transformace šablony nasazení virtuálního počítače pro Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Přístup k informacím\***    | Azure Resource Manager přístup a zkušební přihlašovací údaje, které jsou zapsané jako prostý text nebo jednoduché HTML. |
| *Podrobnosti o předplatném nasazení testovacích jednotek* |  |
| **ID předplatného Azure\*** | Lze získat přihlášením do [portál Microsoft Azure](https://ms.portal.azure.com) a kliknutím na **odběry** na levém řádku nabídek. (Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Tento identifikátor by měl být identifikátor GUID formuláře `a83645ac-1234-5ab6-6789-1h234g764ghty`.|
| **ID tenanta Azure AD\***    | Azure Active Directory ID tenanta.  Lze získat přihlášením do [portál Microsoft Azure](https://ms.portal.azure.com) a kliknutím na **Azure Active Directory** na levé panelu nabídek a následným kliknutím na položku **vlastnosti** v prostřední nabídce nabídek a zkopírováním **ID adresáře** z formuláře.  Tento identifikátor by měl také být identifikátor GUID.  Pokud je toto pole prázdné, musíte vytvořit ID tenanta pro vaši organizaci. |
| **ID Aplikace Azure AD\***       | Identifikátor vašeho registrovaného řešení virtuálního počítače Azure  |
| **Aplikace Azure AD klíč\***      | Ověřovací klíč pro vaše registrované řešení |
|   |   |


## <a name="next-steps"></a>Další kroky

Na další kartě [Marketplace](./cpp-marketplace-tab.md) budete poskytovat marketingové a právní informace o vašem řešení.

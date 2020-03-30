---
title: Karta Testovací jednotka virtuálního počítače na portálu cloudových partnerů pro Azure Marketplace
description: Popisuje kartu Test Drive používanou při vytváření nabídky virtuálního počítače Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: d4fc8762e25825b21637b16b751d57a0dcbf369e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288797"
---
# <a name="virtual-machine-test-drive-tab"></a>Karta Testovací jednotka virtuálního počítače

Karta **Testovací cesta** na stránce Nová **nabídka** umožňuje potenciálním zákazníkům poskytnout praktickou ukázku klíčových funkcí a výhod produktu, která je demonstrována ve standardizovaném scénáři.  Test Drive je volitelná funkce pro typy nabídek, které podporují testovací jízdu.  Test Drive vyžaduje podporu prostředků, které mají být správně implementovány.  Další informace naleznete v článku [Azure Marketplace Test Drive](https://azure.microsoft.com/blog/azure-marketplace-test-drive/).  <!--TD: Replace with migrated version of Test Drive article! -->

Chcete-li tuto funkci povolit, klepněte na kartě **Test Drive** na možnost **Ano** v části **Povolit testovací jízdu**.  Na kartě **Testovací jednotka** se zobrazí pole dostupná pro úpravy.  Připojená hvězdička (*) k názvu pole označuje, že je požadována.

![Karta Testovat jízdu ve formuláři Nová nabídka pro virtuální počítače](./media/publishvm_007.png)


## <a name="field-values"></a>Hodnoty polí

Následující tabulka popisuje účel a obsah těchto polí.  Povinná pole jsou obžalována hvězdičkou (*).


|    Pole                  |       Popis                                                            |
|  ---------                |     ---------------                                                          |
|  *Podrobnosti*   |  |
| **Popis\***           | Poskytněte přehled scénáře testovací jednotky. Tento text se zobrazí uživateli při zřizování testovací jednotky. Toto pole podporuje základní kód HTML, pokud chcete poskytnout formátovaný obsah.  |
| **Uživatelská příručka\***           | Nahrajte podrobnou uživatelskou příručku (.pdf), která uživatelům testovacího disku pomůže pochopit, jak používat vaše řešení.  |
| **Ukázkové video testovací jednotky** | Nahrajte video, které představuje vaše řešení.  Pokud jste zvolili tuto možnost, musíte zadat název, adresu URL videa (hostované na YouTube nebo Vimeo) a miniaturu (533 × 324 pixelů) pro video. |
| *Technická konfigurace* |  |
| **Instance\***             | Zadejte dostupnost oblasti a relativně dostupnost instance virtuálního zařízení (kliknutím na ikonu informace pro další podrobnosti).  <br/>Potenciální souběžné relace testovací jednotky by neměly překročit limit kvóty pro vaše předplatné.  První se vypočítá takto: [Počet vybraných oblastí] x [Aktivní instance] + [Počet vybraných oblastí] x [Warm instance] + [Počet vybraných oblastí] x [Studené instance] |
| **Doba trvání testovací jednotky\***   | Maximální doba trvání relace v hodinách. Relace testovací jednotky se automaticky ukončí po překročení tohoto časového období.  |
|**Šablona ARM testovací jednotky\***| Nahrajte šablonu Azure Resource Manager přidruženou k této testovací jednotce. Další informace naleznete [v tématu Transformace šablony nasazení virtuálního počítače pro testovací jednotku](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive). |
| **Přístup k informacím\***    | Přístup k Azure Resource Manager a zkušební přihlašovací údaje, napsané jako prostý text nebo jednoduchý HTML. |
| *Podrobnosti o předplatném nasazení testovací jednotky* |  |
| **Id předplatného Azure\*** | Lze získat po přihlášení k [portálu Microsoft Azure](https://ms.portal.azure.com) a kliknutím na **Předplatná** na levém panelu nabídek. (Příklad: "a83645ac-1234-5ab6-6789-1h234g764ghty")    Tento identifikátor by měl být `a83645ac-1234-5ab6-6789-1h234g764ghty`identifikátorEM GUID formuláře .|
| **Id klienta Azure AD\***    | ID klienta služby Azure Active Directory.  Lze získat po přihlášení k [portálu Microsoft Azure](https://ms.portal.azure.com) a kliknutím na **Azure Active Directory** na levém panelu nabídky, potom kliknutím na **vlastnosti** na středním řádku nabídky a zkopírováním **ID adresáře** z formuláře.  Tento identifikátor by měl být také identifikátor GUID.  Pokud je prázdné, musíte vytvořit ID klienta pro vaši organizaci. |
| **ID aplikace Azure AD\***       | Identifikátor registrovaného řešení virtuálních počítačů Azure  |
| **Klíč aplikace Azure AD\***      | Ověřovací klíč pro registrované řešení |
|   |   |


## <a name="next-steps"></a>Další kroky

Na další kartě [Marketplace](./cpp-marketplace-tab.md) poskytnete marketingové a právní informace o vašem řešení.

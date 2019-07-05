---
title: Zůstatek kreditu Azure sledovat zákaznické smlouvy Microsoft
description: Zjistěte, jak zkontrolovat zůstatek kreditu Azure pro zákaznické smlouvy společnosti Microsoft.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 352737b3ea61a51a39e066d4211c8f4ceae74184
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490968"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Sledovat zůstatek kreditu Azure pro smlouvu zákazníků společnosti Microsoft

Zůstatek kreditu Azure pro smlouvy zákazníků společnosti Microsoft najdete na webu Azure Portal. Kredity můžete použít k platbám za poplatky, které jsou předmětem kredity.

Bude se vám účtovat při použití produktů, které nejsou pokryty kredity nebo využití překročí, váš zůstatek kreditu. Další informace najdete v tématu [produktů, které nejsou pokryty kredity Azure. () #products-that-aren't-covered-by-azure-credits).

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Zkontrolujte váš zůstatek kreditu

1. Přihlaste se k webu [Azure Portal]( https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky zobrazující vyhledávání v portálu pro správu nákladů a fakturace](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Vyberte **kredity Azure** na levé straně. V závislosti na přístup, budete muset vybrat fakturační účet nebo fakturační profil a potom vyberte **kredity Azure**.

4. Na stránce kredity Azure se zobrazí následující informace:

   ![Snímek obrazovky zůstatek kreditu a transakce pro profil fakturace](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Termín               | Definice                           |
   |--------------------|--------------------------------------------------------|
   | Odhadované zůstatek  | Odhadované množství kreditů máte po zvážení všech účtovat a čekající transakce |
   | Aktuální zůstatek    | Množství kreditů od vaší poslední faktury. Nezahrnuje všechny čekající transakce |
   | Transakce       | Všechny fakturační transakce, které měla vliv na zůstatek vašeho kreditu Azure |

   Když zůstatek na vašem odhadované klesne na hodnotu 0, bude vám účtována veškeré využití, včetně produktů, které jsou předmětem kredity.

6. Vyberte **kredity seznamu** do zobrazení seznamu kredity pro fakturační profil. Kredity seznam obsahuje následující informace:

   ![Snímek obrazovky s kredity seznamy pro profil fakturace](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Termín | Definice |
   |---|---|
   | Odhadované zůstatek | Výši kreditu Azure, ke kterým máte po odečtení nefakturované kredit oprávněné účtování poplatků za zůstatek na vašem aktuálním|
   | Aktuální zůstatek | Množství kreditu Azure, které jste předtím, než kredit nefakturované oprávněné poplatky. Počítá se tak, že přidáte nový kredity Azure, který jste dostali k zůstatek kreditu současně s vaší poslední faktury|
   | source | Získání zdroje kreditu |
   | Počáteční datum | Datum, kdy jste získali kreditu |
   | Datum vypršení platnosti | Datum, kdy vyprší platnost kreditu |
   | Zůstatek | Zůstatek na účtu od poslední faktury |
   | Původní velikost | Původní množství kreditu |
   | Status | Aktuální stav úvěru. Stav může být aktivní, používá, vypršela nebo vyprší |

## <a name="how-credits-are-used"></a>Používání kreditů

Ve fakturační účet zákaznické smlouvy Microsoft používáte ke správě faktury a platby fakturační profily. Měsíční faktuře je vygenerována pro každý profil, fakturace a způsobů platby můžete zaplatit fakturu.

Kredity Azure jsou jedním z způsobů platby. Získáte kredit od Microsoftu jako propagační kredit a výši kreditu na úrovni služeb. Tyto kredity se přiřazují fakturační profilu. Při vygenerování faktury pro fakturační profil, kredity se automaticky použijí pro Celková fakturovaná částka k výpočtu velikosti, budete muset platit. Plaťte jiný způsob platby jako kontrola zůstatek nebo svážete přenosu.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty, které nejsou pokryty kredity Azure

 Následující produkty nejsou pokryty kredity Azure. Za použití těchto produktů bez ohledu na to, váš zůstatek kreditu:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registrovaný uživatel
- Openlogic
- Vzdálený přístup práva XenApp Essentials registrovaný uživatel
- Ubuntu Advantage
- Visual Studio Enterprise (měsíčně)
- Visual Studio Enterprise (roční)
- Visual Studio Professional (měsíčně)
- Visual Studio Professional (Annual)
- Produkty Azure Marketplace
- plánům podpory Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu na základě smlouvy Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktujte podporu.

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- [Vysvětlení fakturační účet pro smlouvy Microsoft zákazníka](billing-mca-overview.md)
- [Vysvětlení podmínek na faktuře smlouvy zákazníka se společností Microsoft](billing-mca-understand-your-invoice.md)

---
title: Sledovat zůstatek kreditu Azure pro smlouvy Microsoft zákazníka | Dokumentace Microsoftu
description: Zjistěte, jak zkontrolovat zůstatek kreditu Azure pro smlouvy zákazníka se společností Microsoft.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: amberb
ms.openlocfilehash: 1e8c3e6863b9cd8f2f5ced18a57918c32c865e75
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57544592"
---
# <a name="track-azure-credit-balance-for-microsoft-customer-agreement"></a>Sledovat zůstatek kreditu Azure pro smlouvu zákazníků společnosti Microsoft

Zůstatek kreditu Azure pro smlouvy zákazníků společnosti Microsoft najdete na webu Azure Portal. Kredity můžete použít k platbám za produktů, které jsou předmětem kredity.

Bude se vám účtovat při použití produktů, které nejsou pokryty kredity nebo využití překročí, váš zůstatek kreditu. Další informace najdete v tématu [produktů, které nejsou pokryty kredity Azure.](#products-that-arent-covered-by-azure-credits)

Tento článek se týká fakturační účet Microsoft zákaznické smlouvy. [Zaškrtněte, pokud máte přístup k zákaznické smlouvy Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-credit-balance-in-the-azure-portal"></a>Zkontrolujte zůstatek kreditu na webu Azure Portal

1. Přihlaste se k webu [Azure Portal]( https://portal.azure.com).

2. Hledat na **Cost Management a fakturace**.

   ![Snímek obrazovky zobrazující vyhledávání v portálu pro správu nákladů a fakturace](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. Přejdete na fakturační profil. V závislosti na přístup budete muset vybrat fakturační účet. Fakturační účet, vyberte **fakturace profily** a potom fakturační profil.

4. Vyberte **kredity Azure**.

5. Na stránce kredity Azure se zobrazí následující informace:

   ![Snímek obrazovky zůstatek kreditu a transakce pro profil fakturace](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Označení               | Definice                           |
   |--------------------|--------------------------------------------------------|
   | Odhadovaný zůstatek  | Odhadované množství kreditů máte po zvážení všech účtovat a čekající transakce |
   | Aktuální zůstatek    | Množství kreditů od vaší poslední faktury. Nezahrnuje všechny čekající transakce |
   | Transakce       | Všechny fakturační transakce, které měla vliv na zůstatek vašeho kreditu Azure |

   Když zůstatek na vašem odhadované klesne na hodnotu 0, bude vám účtována veškeré využití, včetně produktů, které jsou předmětem kredity.

6. Vyberte **kredity seznamu** do zobrazení seznamu kredity pro fakturační profil. Kredity seznam obsahuje následující informace:

   ![Snímek obrazovky s kredity seznamy pro profil fakturace](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Označení                 | Definice                           |
   |----------------------|--------------------------------------------------------|
   | Zdroj               | Získání zdroje kreditu |
   | Počáteční datum           | Datum, kdy jste získali kreditu |
   | Datum konce platnosti      | Datum, kdy vyprší platnost kreditu |
   | Zůstatek              | Zůstatek na účtu od poslední faktury |
   | Původní velikost      | Původní množství kreditu |
   | Status               | Aktuální stav úvěru. Stav může být aktivní, používá, vypršela nebo vyprší |

## <a name="how-credits-are-used-in-microsoft-customer-agreement"></a>Použití kredity ve smlouvě Microsoft zákazníka

Ve fakturační účet zákaznické smlouvy Microsoft používáte ke správě faktury a platby fakturační profily. Měsíční faktuře je vygenerována pro každý profil, fakturace a způsobů platby můžete zaplatit fakturu.

Kredity Azure jsou jedním z způsobů platby. Získáte kredit od Microsoftu jako propagační kredit a výši kreditu na úrovni služeb. Tyto kredity se přiřazují fakturační profilu. Při vygenerování faktury pro fakturační profil, kredity se automaticky použijí pro Celková fakturovaná částka k výpočtu velikosti, budete muset platit. Plaťte jiný způsob platby jako kontrola zůstatek nebo svážete přenosu.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty, které nejsou pokryty kredity Azure

 Následující produkty nejsou pokryty kredity Azure. Za použití těchto produktů bez ohledu na to, váš zůstatek kreditu:

- Canonical
- Základy Citrix XenApp
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

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.

## <a name="next-steps"></a>Další postup

- [Vysvětlení fakturační účet pro smlouvy Microsoft zákazníka](billing-mca-overview.md)
- [Vysvětlení podmínek na faktuře smlouvy zákazníka se společností Microsoft](billing-mca-understand-your-invoice.md)
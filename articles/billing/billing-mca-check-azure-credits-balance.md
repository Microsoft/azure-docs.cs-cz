---
title: Sledování zůstatku kreditu Azure u smlouvy se zákazníkem Microsoftu
description: Přečtěte si, jak sledovat zůstatek kreditu Azure u smlouvy se zákazníkem Microsoftu.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ea3fc21891f1e4d4e744449032a4b2cfcdfbb2f0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177539"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Sledování zůstatku kreditu Azure u smlouvy se zákazníkem Microsoftu

Zůstatek kreditu Azure u smlouvy se zákazníkem Microsoftu můžete zobrazit na webu Azure Portal. Pomocí kreditů můžete platit poplatky, na které se tyto kredity vztahují.

Pokud používáte produkty, na které se kredity nevztahují, nebo při využívání překročíte zůstatek kreditu, začnou se vám účtovat poplatky. Další informace najdete v seznamu [produktů, na které se nevztahují kredity Azure](#products-that-arent-covered-by-azure-credits).

Tento článek se týká fakturačního účtu smlouvy se zákazníky Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Kontrola zůstatku kreditu

1. Přihlaste se k webu [Azure Portal]( https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Na levé straně vyberte **Kredity Azure**. V závislosti na úrovni přístupu možná budete muset vybrat fakturační účet nebo fakturační profil a teprve pak zvolit **Kredity Azure**.

4. Stránka Kredity Azure obsahuje následující informace:

   ![Snímek obrazovky se zůstatkem kreditu a transakcemi pro určitý fakturační profil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Označení               | Definice                           |
   |--------------------|--------------------------------------------------------|
   | Odhadovaný zůstatek  | Odhadované množství vašich kreditů po zvážení všech fakturovaných a nevyřízených transakcí |
   | Aktuální zůstatek    | Množství kreditů podle vaší poslední faktury. Nezahrnuje žádné nevyřízené transakce |
   | Transakce       | Všechny fakturační transakce, které ovlivnily zůstatek kreditu Azure |

   Když odhadovaný zůstatek klesne na 0, začne se vám účtovat veškeré využití, a to i produktů, na které se jinak vztahují kredity.

6. Seznam kreditů pro daný fakturační profil zobrazíte výběrem položky **Seznam kreditů**. Seznam kreditů obsahuje následující informace:

   ![Snímek obrazovky se seznamy kreditů pro určitý fakturační profil](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Označení | Definice |
   |---|---|
   | Odhadovaný zůstatek | Množství vašich kreditů Azure po odečtení nezaúčtovaných poplatků s nárokem na uplatnění kreditů od aktuálního zůstatku|
   | Aktuální zůstatek | Množství vašich kreditů Azure před zvážením nezaúčtovaných poplatků s nárokem na uplatnění kreditů. Počítá se přičtením počtu získaných kreditů Azure k zůstatku kreditů na základě poslední faktury|
   | Zdroj | Zdroj pořízení kreditu |
   | Počáteční datum | Datum získání kreditu |
   | Datum vypršení platnosti | Datum, kdy vyprší platnost kreditu |
   | Zůstatek | Zůstatek na základě poslední faktury |
   | Původní množství | Původní množství kreditů |
   | Status | Aktuální stav kreditů. Možné stavy: Aktivní, Použito, Platnost vypršela nebo Platnost vyprší |

## <a name="how-credits-are-used"></a>Způsob uplatňování kreditů

Na fakturačním účtu pro smlouvu se zákazníkem Microsoftu spravujete své faktury a způsoby platby pomocí fakturačních profilů. Pro každý fakturační profil se jednou za měsíc generuje faktura a k úhradě této faktury používáte zvolené způsoby platby.

Kredity Azure představují jeden ze způsobů platby. Kredity získáváte od Microsoftu, třeba v podobě propagačních kreditů nebo kreditů na úrovni služeb. Tyto kredity se přiřadí k určitému fakturačnímu profilu. Ve chvíli vytváření faktury pro tento fakturační profil se kredity automaticky uplatní na celkovou fakturovanou částku a vypočítá se částka, kterou máte zaplatit. Zbývající částku uhradíte pomocí jiného způsobu platby, například šekem nebo bezhotovostním převodem.

## <a name="products-that-arent-covered-by-azure-credits"></a>Produkty, na které se nevztahují kredity Azure

 Kredity Azure se nevztahují na následující produkty. Za využívání těchto produktů se vám budou bez ohledu na zůstatek kreditu účtovat poplatky:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Registrovaný uživatel
- Openlogic
- Registrovaný uživatel s oprávněním ke vzdálenému přístupu XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise (měsíční)
- Visual Studio Enterprise (roční)
- Visual Studio Professional (měsíční)
- Visual Studio Professional (roční)
- Produkty z Azure Marketplace
- plánům podpory Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Ověření přístupu ke smlouvě se zákazníkem Microsoftu
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Obraťte se na podporu.

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Vysvětlení fakturačních účtů pro smlouvu se zákazníkem Microsoftu](billing-mca-overview.md)
- [Vysvětlení výrazů na faktuře za smlouvu se zákazníkem Microsoftu](billing-mca-understand-your-invoice.md)

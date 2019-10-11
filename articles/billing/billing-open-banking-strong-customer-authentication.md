---
title: Ověřování PSD2 (otevřené bankovnictví) a SCA (Strong Customer Authentication) pro zákazníky Azure
description: Tento článek vysvětluje, proč se některé nákupy Azure vyžaduje vícefaktorové ověřování a jak ho dokončit.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: banders
ms.openlocfilehash: 629a14a08b523016981300181dc4cad4549c798f
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174529"
---
# <a name="open-banking-psd2-and-strong-customer-authentication-sca-for-azure-customers"></a>Ověřování PSD2 (otevřené bankovnictví) a SCA (Strong Customer Authentication) pro zákazníky Azure

Od 14. září 2019 musí banky v 31 zemích [Evropského hospodářského prostoru](https://en.wikipedia.org/wiki/European_Economic_Area) ověřovat identitu osoby provádějící online nákup dřív, než se příslušná platba zpracuje. Toto ověření vyžaduje vícefaktorové ověřování, které pomáhá zajistit ochranu a zabezpečení vašich online nákupů. V některých zemích je datum tohoto ověřovacího požadavku odloženo. Další informace najdete v tématu Microsoftu věnovaném [nejčastějším dotazům k PSD2](https://support.microsoft.com/en-us/help/4517854?preview).

## <a name="what-psd2-means-for-azure-customers"></a>Co PSD2 znamená pro zákazníky Azure

Pokud za Azure platíte platební kartou, kterou vydala banka v [Evropském hospodářském prostoru](https://en.wikipedia.org/wiki/European_Economic_Area), je možné, že se pro způsob platby vašeho účtu bude vyžadovat kompletní vícefaktorové ověřování. Výzva k vícefaktorovému ověření se může zobrazit při přihlašování k účtu Azure nebo jeho upgradování, a to i v případě, že zrovna nic nekupujete. K vícefaktorovému ověřování můžete být také vyzváni při změně způsobu platby pro váš účet Azure, zrušení limitu na nákupy nebo provedení okamžité platby z webu Azure Portal, například při vyrovnávání neuhrazeného zůstatku nebo nákupu kreditů Azure.

Pokud vaše banka zamítne vaše měsíční platby za Azure, dostanete od Azure e-mail s pokyny, jak tuto situaci vyřešit. Můžete použít vícefaktorové ověřování a uhradit nezaplacené poplatky na webu Azure Portal.

## <a name="complete-multi-factor-authentication-in-the-azure-portal"></a>Dokončení vícefaktorového ověření na webu Azure Portal

Následující části popisují dokončení vícefaktorového ověření na webu Azure Portal. Využijte informace, které se na vás vztahují.

### <a name="change-the-active-payment-method-of-your-azure-account"></a>Změna aktivního způsobu platby pro váš účet Azure

Aktivní způsob platby vašeho účtu Azure můžete změnit pomocí tohoto postupu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako správce účtu a přejděte na **Cost Management a fakturace**.
2. Na stránce **Přehled** vyberte odpovídající předplatné v tabulce **Moje předplatná**.
3. V části Fakturace vyberte **Způsoby platby**. Jako aktivní způsob platby pro příslušné předplatné můžete přidat novou platební kartu nebo nastavit stávající. Pokud vaše banka vyžaduje vícefaktorové ověřování, budete během tohoto procesu vyzváni k ověření.

Další informace najdete v tématu [Přidání, aktualizace nebo odebrání platební karty v Azure](billing-how-to-change-credit-card.md).

### <a name="settle-outstanding-charges-for-azure-services"></a>Vyrovnání neuhrazených poplatků za služby Azure

Pokud vaše banka platbu zamítne, stav vašeho účtu Azure na webu Azure Portal se změní na **Po termínu**. Stav vašeho účtu můžete zjistit pomocí následujících kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce účtu.
2. Vyhledejte **Cost Management a fakturace**.
3. Na stránce s **přehledem pro** **Cost Management a fakturaci** zkontrolujte stavový sloupec v tabulce **Moje předplatná**.
4. Pokud má vaše předplatné označení **Po splatnosti**, klikněte na odkaz **Vyrovnat zůstatek**. Během tohoto procesu se zobrazí výzva k dokončení vícefaktorového ověřování.

### <a name="settle-outstanding-charges-for-marketplace-and-reservation-purchases"></a>Vyrovnání neuhrazených poplatků za nákupy na Marketplace a nákupy rezervací

Nákupy na Marketplace a nákupy rezervací se účtují odděleně od služeb Azure. Pokud vaše banka zamítne poplatky za Marketplace nebo rezervace, vaše faktura se na webu Azure Portal zobrazí se stavem **Po termínu**. Stav vašich faktur za Marketplace a rezervace můžete zjistit pomocí následujících kroků:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/) jako správce účtu.
2. Vyhledejte **Cost Management a fakturace**.
3. V části Fakturace vyberte **Faktury**.
4. Klikněte vpravo na kartu **Azure Marketplace a rezervace**.
5. Vyberte odpovídající předplatné.
6. V tabulce faktur zkontrolujte sloupec stavu. Pokud je faktura označena jako **splatná** nebo **po splatnosti**, klikněte na **Zaplatit**. Během tohoto procesu se zobrazí výzva k dokončení vícefaktorového ověřování.

## <a name="next-steps"></a>Další kroky
- Pokud potřebujete zaplatit vyúčtování Azure, projděte si téma [Řešení zůstatku po splatnosti u předplatného Azure](billing-azure-subscription-past-due-balance.md).

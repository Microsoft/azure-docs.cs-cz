---
title: Odstranění způsobu platby pro fakturaci v Azure
description: Popisuje, jak odstranit způsob platby, který se používá pro předplatné Azure.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/07/2020
ms.author: banders
ms.openlocfilehash: 15d6c7731b541de638ceaf7828a7ce962cbf154a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91827545"
---
# <a name="delete-an-azure-billing-payment-method"></a>Odstranění způsobu platby v Azure

Tento dokument obsahuje pokyny, které vám pomohou odstranit způsob platby, například platební kartu, z různých typů předplatných Azure. Způsob platby můžete odstranit pro:

- Smlouva se zákazníkem Microsoftu (MCA)
- Program odběru služeb online společnosti Microsoft (MOSP) neboli průběžné platby

Pokud chcete odstranit způsob platby přidružený k předplatnému Azure, musíte nejdříve dané předplatné zrušit.

Odebrání způsobu platby pro jiné typy předplatného Azure, jako je smlouva s partnerem Microsoftu (MPA) a smlouva Enterprise (EA), není podporováno.

## <a name="delete-an-mca-payment-method"></a>Odstranění způsobu platby ke smlouvě MCA

Způsob platby může odstranit jedině uživatel, který účet smlouvy se zákazníkem Microsoftu vytvořil.

Pokud chcete odstranit způsob platby smlouvy se zákazníkem Microsoftu, proveďte následující kroky.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com/.
1. Přejděte na **Cost Management a fakturace**.
1. V případě potřeby vyberte rozsah fakturace.
1. V seznamu nabídek nalevo v části **Fakturace** vyberte **Fakturační profily**.  
    :::image type="content" source="./media/delete-azure-payment-method/billing-profiles.png" alt-text="Ukázkový snímek obrazovky s možností Fakturační profily na portálu Azure Portal" lightbox="./media/delete-azure-payment-method/billing-profiles.png" :::
1. V seznamu fakturačních profilů vyberte ten, ve kterém se daný způsob platby používá.  
    :::image type="content" source="./media/delete-azure-payment-method/select-billing-profile.png" alt-text="Ukázkový snímek obrazovky s možností Fakturační profily na portálu Azure Portal" :::
1. V seznamu nabídek nalevo v části **Nastavení** vyberte **Způsoby platby**.
1. Na stránce způsobů platby vašeho fakturačního profilu je v části **Vaše platební karty** tabulka způsobů platby. Najděte platební kartu, kterou chcete odstranit, vyberte tři tečky ( **...** ) a pak vyberte **Odstranit**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-credit-card.png" alt-text="Ukázkový snímek obrazovky s možností Fakturační profily na portálu Azure Portal" :::
1. Zobrazí se stránka Odstranit způsob platby. Azure zkontroluje, jestli se způsob platby používá.
    - Pokud se nepoužívá, je povolená možnost **Odstranit**. Jejím výběrem odstraníte informace o platební kartě.
    - Pokud se používá, musí být nahrazen nebo odpojen. Další informace si můžete přečíst v následujících částech. Vysvětlují, jak **odpojit** způsob platby používaný vaším předplatným.

### <a name="detach-payment-method-used-by-an-mca-billing-profile"></a>Odpojení způsobu platby používaného ve fakturačním profilu MCA

Pokud se váš způsob platby používá ve fakturačním profilu MCA, zobrazí se zpráva podobná té v následujícím příkladu.

:::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-microsoft-customer-agreement.png" alt-text="Ukázkový snímek obrazovky s možností Fakturační profily na portálu Azure Portal" :::

Aby bylo možné způsob platby odpojit, musí být splněny podmínky uvedené v seznamu. Pokud některé podmínky splněné nejsou, zobrazí se pokyny vysvětlující, jak je splnit. Zobrazí se také odkaz, který vás přesměruje na místo, kde můžete splnění podmínky vyřešit.

Pokud jsou všechny podmínky splněny, můžete způsob platby odpojit od fakturačního profilu.

> [!NOTE]
> Po odpojení výchozího způsobu platby se fakturační profil přepne do _neaktivního_ stavu. Cokoli, co bude v rámci tohoto procesu odstraněno, nebude možné obnovit. Poté, co je fakturační profil nastaven jako neaktivní, si budete muset v případě, že budete chtít vytvořit nové prostředky, zaregistrovat nové předplatné Azure.

#### <a name="to-detach-a-payment-method"></a>Odpojení způsobu platby

1. V oblasti Odstranit způsob platby vyberte odkaz **Odpojit aktuální způsob platby**.
1. Pokud jsou splněny všechny podmínky, vyberte **Odpojit**. V opačném případě pokračujte k dalšímu kroku.
1. Pokud není možnost Odpojit k dispozici, zobrazí se seznam podmínek. Proveďte uvedené akce. Vyberte odkaz, který je zobrazený v oblasti Odpojit výchozí způsob platby. Tady je příklad opravné akce vysvětlující, jaké akce je potřeba provést.  
    :::image type="content" source="./media/delete-azure-payment-method/azure-subscriptions.png" alt-text="Ukázkový snímek obrazovky s možností Fakturační profily na portálu Azure Portal" :::
1. Když vyberete odkaz na opravnou akci, budete přesměrováni na stránku Azure, na které danou akci provedete. Proveďte všechny potřebné opravné akce.
1. V případě potřeby proveďte všechny ostatní opravné akce.
1. Přejděte zpět na **Správa nákladů a fakturace** > **Fakturační profily** > **Způsoby platby**. Vyberte **Odpojit**. V dolní části stránky Odpojit výchozí způsob platby vyberte **Odpojit**.

> [!NOTE]
> - Po zrušení předplatného to může trvat až 90 dní, než se předplatné odstraní. Pokud chcete čekání zkrátit, otevřete žádost o podporu Azure a požádejte o okamžité odstranění předplatného.
> - Způsob platby můžete odstranit až po zaplacení všech předchozích poplatků pro daný fakturační profil. Pokud se nacházíte v aktivním fakturačním období, musíte s odstraněním způsobu platby počkat až na konec fakturačního období. **Při čekání na skončení fakturačního období zajistěte, aby byly splněny všechny ostatní podmínky pro odpojení způsobu platby**.

## <a name="delete-a-mosp-payment-method"></a>Odstranění způsobu platby MOSP

Pokud chcete odstranit způsob platby, musíte být správcem účtu.

Pokud se váš způsob platby používá v rámci předplatného MOSP, proveďte následující kroky.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com/.
1. Přejděte na **Cost Management a fakturace**.
1. V případě potřeby vyberte rozsah fakturace.
1. V seznamu nabídek nalevo v části **Fakturace** vyberte **Způsoby platby**.
1. V oblasti Způsoby platby vyberte _řádek_, na kterém je váš způsob platby uvedený. Nevybírejte odkaz na způsob platby. To, že jste způsob platby vybrali, nemusí být nijak vizuálně označeno.
1. Vyberte **Odstranit**.  
    :::image type="content" source="./media/delete-azure-payment-method/delete-mosp-payment-method.png" alt-text="Ukázkový snímek obrazovky s možností Fakturační profily na portálu Azure Portal" :::
1. Pokud jsou splněné všechny podmínky, vyberte v oblasti Odstranit způsob platby možnost **Odstranit**. Pokud není možnost Odstranit dostupná, pokračujte k dalšímu kroku.
1. Zobrazí se seznam podmínek. Proveďte uvedené akce. Vyberte odkaz, který je zobrazený v oblasti Odstranit způsob platby.  
    :::image type="content" source="./media/delete-azure-payment-method/payment-method-in-use-mosp.png" alt-text="Ukázkový snímek obrazovky s možností Fakturační profily na portálu Azure Portal" :::
1. Když vyberete odkaz na opravnou akci, budete přesměrováni na stránku Azure, na které danou akci provedete. Proveďte všechny potřebné opravné akce.
1. V případě potřeby proveďte všechny ostatní opravné akce.
1. Přejděte zpět na **Správa nákladů a fakturace** > **Fakturační profily** > **Způsoby platby** a odstraňte způsob platby.

> [!NOTE]
> Po zrušení předplatného to může trvat až 90 dní, než se předplatné odstraní. Pokud chcete čekání zkrátit, otevřete žádost o podporu Azure a požádejte o okamžité odstranění předplatného.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [rušení předplatných Azure](cancel-azure-subscription.md).
- Přečtěte si další informace o [přidávání nebo aktualizaci platebních karet pro Azure](change-credit-card.md).
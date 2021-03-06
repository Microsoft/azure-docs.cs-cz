---
title: Použití řídicího panelu pro dodržování předpisů v Azure Security Center
description: Přečtěte si, jak přidat a odebrat zákonné standardy z řídicího panelu dodržování předpisů regulativního předpisu v Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/21/2021
ms.author: memildin
ms.openlocfilehash: 4ba65517b698896c57ca2254394efcadd6efbb1d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835033"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Přizpůsobení sady standardů na řídicím panelu dodržování předpisů

Azure Security Center průběžně porovnává konfiguraci vašich prostředků s požadavky v oborových normách, nařízeních a srovnávacích testech. **Řídicí panel dodržování předpisů** poskytuje přehledy o stav dodržování předpisů na základě toho, jak splňujete konkrétní požadavky na dodržování předpisů.

> [!TIP]
> Přečtěte si další informace o řídicím panelu dodržování předpisů Security Center v [nejčastějších dotazech](security-center-compliance-dashboard.md#faq---regulatory-compliance-dashboard).

## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Jak jsou ve Security Center standardy dodržování předpisů předpisy?

Oborové standardy, regulativní normy a srovnávací testy jsou představovány na řídicím panelu dodržování předpisů v Security Center. Každý Standard je iniciativou definovaný v Azure Policy.

Pokud chcete zobrazit data o dodržování předpisů namapovaná jako posouzení na řídicím panelu, přidejte do skupiny pro správu nebo předplatného na stránce **zásady zabezpečení** Standard dodržování předpisů. Další informace o Azure Policy a iniciativách najdete v tématu [práce se zásadami zabezpečení](tutorial-security-policy.md).

Po přiřazení standardu nebo srovnávacího testu k vybranému oboru se standard zobrazuje na řídicím panelu dodržování předpisů, kde jsou všechna přidružená data o dodržování předpisů namapovaná jako posouzení. Můžete si také stáhnout souhrnné sestavy pro libovolný ze standardů, které byly přiřazeny.

Společnost Microsoft sleduje zákonné normy samy a automaticky zlepšuje jejich pokrytí v některých balíčcích v průběhu času. Když společnost Microsoft vydává nový obsah pro iniciativu, zobrazí se na řídicím panelu automaticky jako nové zásady namapované na ovládací prvky ve standardu.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Jaké standardy dodržování předpisů jsou k dispozici v Security Center?

Ve výchozím nastavení má každé předplatné přiřazené **srovnávací testy zabezpečení Azure** . Toto jsou pokyny specifické pro Azure, které se týkají zabezpečení a dodržování předpisů, a to pro osvědčené postupy na základě běžných architektur dodržování předpisů. [Další informace o srovnávacím testu zabezpečení Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)

Můžete také přidat standardy, jako například:

- NIST SP 800-53
- SWIFT CSP CSCF-v2020
- Oficiální Velká Británie a Velká Británie NHS
- Canada Federal PBMM
- Azure CIS 1.3.0
- CMMC úrovně 3
- Nový Zéland s omezením pro ISM

Do řídicího panelu se přidají standardy, jakmile budou k dispozici.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Přidání regulativního standardu na řídicí panel

Následující postup vysvětluje, jak přidat balíček, abyste mohli monitorovat vaše dodržování předpisů s jedním z podporovaných regulativních standardů.

> [!NOTE]
> K přidání standardů na řídicí panel musí mít předplatné povolený program Azure Defender. K přidání standardů dodržování předpisů mají také oprávnění pouze uživatelé, kteří jsou vlastníkem nebo přispěvatelem zásad. 

1. Z bočního panelu Security Center vyberte **dodržování předpisů** pro otevření řídicího panelu dodržování předpisů regulativním předpisem. Tady vidíte standardy dodržování předpisů, které jsou aktuálně přiřazené k aktuálně vybraným předplatným.   

1. V horní části stránky vyberte **Spravovat zásady dodržování předpisů**. Zobrazí se stránka Správa zásad.

1. Vyberte předplatné nebo skupinu pro správu, pro které chcete spravovat dodržování legislativních předpisů stav. 

    > [!TIP]
    > Doporučujeme vybrat nejvyšší rozsah, pro který je standard použitelný, aby se data o dodržování předpisů agreguje a sledovala pro všechny vnořené prostředky. 

1. Pokud chcete přidat standardy týkající se vaší organizace, klikněte na **Přidat další standardy**. 

1. Na stránce **Přidat standardy dodržování předpisů** můžete vyhledat libovolné dostupné standardy, včetně:

    - **NIST SP 800-53**
    - **NIST SP 800 171**
    - **SWIFT CSP CSCF v2020**
    - **UKO a Velká Británie NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.3.0**
    - **CMMC úrovně 3**
    - **Nový Zéland s omezením pro ISM**
    
    ![Přidání regulativních standardů do řídicího panelu dodržování předpisů v Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Vyberte **Přidat** a zadejte všechny potřebné údaje o konkrétní iniciativě, jako je například rozsah, parametry a náprava.

1. Z bočního panelu Security Center vyberte znovu **dodržování předpisů** , abyste se mohli vrátit na řídicí panel dodržování předpisů.

    Vaše nové standardní zobrazení se zobrazí v seznamu oborových & regulativních standardů. 

    > [!NOTE]
    > Může trvat několik hodin, než se nově přidaný Standard zobrazí na řídicím panelu dodržování předpisů.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Řídicí panel dodržování předpisů v legislativě" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="remove-a-standard-from-your-dashboard"></a>Odebrání standardu z řídicího panelu

Pokud některý ze zadaných regulativních standardů není pro vaši organizaci podstatný, je to jednoduchý proces, který je odebere z uživatelského rozhraní. To vám umožní lépe přizpůsobit řídicí panel dodržování předpisů a soustředit se jenom na standardy, které jsou pro vás vhodné.

Postup odebrání standardu:

1. V nabídce Security Center vyberte **zásady zabezpečení**.

1. Vyberte příslušné předplatné, ze kterého chcete odebrat Standard.

    > [!NOTE]
    > Můžete odebrat Standard z předplatného, ale ne ze skupiny pro správu. 

    Otevře se stránka zásady zabezpečení. U vybraného předplatného se zobrazí výchozí zásady, oborové a regulativní normy a všechny vlastní iniciativy, které jste vytvořili.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Odstranění regulativního standardu z řídicího panelu dodržování předpisů v Azure Security Center":::

1. U Standard, který chcete odebrat, vyberte **Zakázat**. Zobrazí se okno pro potvrzení.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Potvrďte, že opravdu chcete odstranit vybraný regulativní Standard":::

1. Vyberte **Ano**. Standard bude odebrán. 


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak **Přidat standardy dodržování předpisů** a monitorovat dodržování předpisů pomocí regulativních a průmyslových standardů.

Související materiály najdete na následujících stránkách:

- [Srovnávací test zabezpečení Azure](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Řídicí panel dodržování předpisů v Security Center](security-center-compliance-dashboard.md) – Naučte se sledovat a exportovat data dodržování předpisů pomocí Security Center a externích nástrojů.
- [Práce se zásadami zabezpečení](tutorial-security-policy.md)
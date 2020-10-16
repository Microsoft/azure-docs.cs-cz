---
title: Použití řídicího panelu pro dodržování předpisů v Azure Security Center
description: Přečtěte si, jak přidat a odebrat zákonné standardy z řídicího panelu dodržování předpisů regulativního předpisu v Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2020
ms.author: memildin
ms.openlocfilehash: 5e32aa8491e18a66a3e8b90f065e6b785d1e3bd9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106259"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Přizpůsobení sady standardů na řídicím panelu dodržování předpisů

Azure Security Center průběžně porovnává konfiguraci vašich prostředků s požadavky v oborových normách, nařízeních a srovnávacích testech. **Řídicí panel dodržování předpisů** poskytuje přehledy o stav dodržování předpisů na základě toho, jak splňujete konkrétní kontrolu a požadavky na dodržování předpisů.


## <a name="overview-of-compliance-packages"></a>Přehled balíčků dodržování předpisů

Oborové standardy, regulativní normy a srovnávací testy jsou v Security Center jako *balíčky dodržování předpisů*.  Každý balíček je iniciativou určenou v Azure Policy. Pokud chcete zobrazit data o dodržování předpisů namapovaná jako posouzení na řídicím panelu, přidejte do skupiny pro správu nebo předplatného na stránce **zásady zabezpečení** balíček pro dodržování předpisů. (Další informace o Azure Policy a iniciativách při [práci se zásadami zabezpečení](tutorial-security-policy.md).)

Když jste zavedli standardní nebo srovnávací test na vybraný rozsah, přiřadí se iniciativa k oboru a Standard se zobrazí na řídicím panelu dodržování předpisů, kde jsou všechna přidružená data o dodržování předpisů mapovaná jako posouzení. Můžete si také stáhnout souhrnné sestavy pro všechny standardy, které byly zaregistrované.

Microsoft také sleduje zákonné standardy a automaticky zlepšuje jejich pokrytí v některých balíčcích v průběhu času. Když společnost Microsoft vydává nový obsah pro iniciativu (nové zásady, které se mapují na více ovládacích prvků standardně), zobrazí se na řídicím panelu automaticky tento další obsah.

> [!TIP]
> Jeden standard, který se v průběhu času vylepšuje od Microsoftu [Microsoft Azure](https://www.cisecurity.org/benchmark/azure/), je **Azure CIS 1.1.0 (novinka)** Tuto možnost budete muset přidat na řídicí panel vedle položky "Azure CIS 1.1.0", která je ve výchozím nastavení nakonfigurovaná v každém Security Center prostředí. Tento balíček spoléhá na statickou sadu pravidel. Novější balíček zahrnuje další zásady a automaticky se aktualizuje v průběhu času. Aktualizujte na nový dynamický balíček, jak je popsáno níže.


## <a name="available-packages"></a>Dostupné balíčky

Můžete přidat standardy, jako je NIST SP 800-53 R4, SWIFT CSP CSCF-v2020, UK oficiální a Velká Británie NHS, Kanada – federální PBMM a Azure CIS 1.1.0 (nový) – ucelená reprezentace 1.1.0 pro Azure CIS. 

Kromě toho můžete přidat **srovnávací testy zabezpečení Azure**, které jsou specifické pro Azure, a to v souladu s osvědčenými postupy zabezpečení a dodržování předpisů, které jsou založené na běžných architekturách dodržování předpisů. (Další[informace o srovnávacích testech zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction).)

Další standardy budou na řídicím panelu podporovány, jakmile budou k dispozici. 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Přidání regulativního standardu na řídicí panel

Následující postup vysvětluje, jak přidat balíček, abyste mohli monitorovat vaše dodržování předpisů s jedním z podporovaných regulativních standardů.

> [!NOTE]
> Pouze uživatelé, kteří jsou vlastníkem nebo přispěvatel zásad, mají potřebná oprávnění k přidání standardů dodržování předpisů. 

1. Z bočního panelu Security Center vyberte **dodržování předpisů** pro otevření řídicího panelu dodržování předpisů regulativním předpisem. Tady vidíte standardy dodržování předpisů, které jsou aktuálně přiřazené k aktuálně vybraným předplatným.   

1. V horní části stránky vyberte **Spravovat zásady dodržování předpisů**. Zobrazí se stránka Správa zásad.

1. Vyberte předplatné nebo skupinu pro správu, pro které chcete spravovat dodržování legislativních předpisů stav. 

    > [!TIP]
    > Doporučujeme vybrat nejvyšší rozsah, pro který je standard použitelný, aby se data o dodržování předpisů agreguje a sledovala pro všechny vnořené prostředky. 

1. Pokud chcete přidat standardy týkající se vaší organizace, klikněte na **Přidat další standardy**. 

1. Na stránce **Přidat standardy dodržování předpisů** můžete vyhledat balíčky pro libovolnou z dostupných standardů. K dispozici jsou tyto standardy:

    - **Srovnávací test zabezpečení Azure**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO a Velká Británie NHS**
    - **PBMMa Kanady**
    
    ![Přidání regulativních balíčků do řídicího panelu dodržování předpisů pro Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Vyberte **Přidat** a zadejte všechny potřebné údaje o konkrétní iniciativě, jako je například rozsah, parametry a náprava.

1. Z bočního panelu Security Center vyberte znovu **dodržování předpisů** , abyste se mohli vrátit na řídicí panel dodržování předpisů.
    * Vaše nové standardní zobrazení se zobrazí v seznamu oborových & regulativních standardů. 
    * Pokud jste přidali **Azure CIS 1.1.0 (nové)**, zůstane současně i nadále původní *statické* zobrazení dodržování předpisů Azure CIS 1.1.0. V budoucnu může být automaticky odebrána.

    > [!NOTE]
    > Může trvat několik hodin, než se nově přidaný Standard zobrazí na řídicím panelu dodržování předpisů.

    [![Řídicí panel dodržování předpisů, který ukazuje starou a novou službu Azure SNS](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>Odebrání standardu z řídicího panelu

Pokud některý ze zadaných regulativních standardů není relevantní pro vaši organizaci, je jednoduchý proces, který je jednoduše odebere z uživatelského rozhraní. To vám umožní lépe přizpůsobit řídicí panel dodržování předpisů a soustředit se jenom na standardy, které jsou pro vás vhodné.

Postup odebrání standardu:

1. V nabídce Security Center vyberte **zásady zabezpečení**.

1. Vyberte příslušné předplatné, ze kterého chcete odebrat Standard.

    > [!NOTE]
    > Můžete odebrat Standard z předplatného, ale ne ze skupiny pro správu. 

    Otevře se stránka zásady zabezpečení. U vybraného předplatného se zobrazí výchozí zásady, oborové a regulativní normy a všechny vlastní iniciativy, které jste vytvořili.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Odstranění regulativního standardu z řídicího panelu dodržování předpisů v Azure Security Center":::

1. U Standard, který chcete odebrat, vyberte **Zakázat**. Zobrazí se okno pro potvrzení.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Odstranění regulativního standardu z řídicího panelu dodržování předpisů v Azure Security Center":::

1. Vyberte **Ano**. Standard bude odebrán. 


## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak **Přidat balíčky dodržování předpisů** , abyste mohli monitorovat dodržování předpisů pomocí dalších standardů. 

Další související materiály najdete v následujících článcích: 

- [Srovnávací test zabezpečení Azure](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [Řídicí panel dodržování legislativních předpisů služby Security Center](security-center-compliance-dashboard.md)
- [Práce se zásadami zabezpečení](tutorial-security-policy.md)
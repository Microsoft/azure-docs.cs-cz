---
title: Vlastní pravidla výstrah v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument vám pomůže vytvořit vlastní pravidla výstrah v Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f335d8c4-0234-4304-b386-6f1ecda07833
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 9bf4f604386b47e4ca36df69fb140124b097f548
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576892"
---
# <a name="custom-alert-rules-in-azure-security-center-preview"></a>Vlastní pravidla výstrah v Azure Security Center (Preview)
Tento dokument vám pomůže vytvořit vlastní pravidla výstrah v Azure Security Center.

> [!NOTE]
> Vlastní upozornění se už brzy nebude podporováno, přečtěte si další na alternativní následujícími službami.

## <a name="retirement-of-custom-alert-rules-in-azure-security-center"></a>Vyřazení oznámení na vlastní pravidla ve službě Azure Security Center

Prostředí vlastní upozornění se vyřadí z provozu 30. června 2019 kvůli vyřazení služby Azure Monitor upozornění classic. V časovém rámci až do vyřazení uživatelé budou moci upravit existující vlastní pravidla výstrah ale nebudete moct přidat nové.
Uživatelé by měli povolit [Azure Sentinelu](https://azure.microsoft.com/en-us/services/azure-sentinel/) s připojováním jedním kliknutím automaticky migrovat své existující výstrahy a vytvářet nové nebo případně znovu vytvořit jejich výstrahy se službou Azure Monitor upozornění protokolů.
                                     
Chcete-li zachovat existující výstrahy a migrací do Azure Sentinelu, [spusťte Azure Sentinelu](https://portal.azure.com/#create/Microsoft.ASI/preview). Jako první krok vyberte pracovní prostor, kde jsou uložené vaše vlastní výstrahy a poté vyberte položku nabídky "Analytics" automaticky migrovat vaše upozornění.

> [!NOTE]
> Vlastní upozornění migrace do Azure Sentinelu je jednorázovou migraci všech vlastních výstrah do vybraného pracovního prostoru. Po dokončení migrace vlastní upozornění pro tento vybraný pracovní prostor nebude přístupná prostřednictvím Azure Security Center.
>
> Vlastní upozornění pomocí [hledání](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/search-queries) nebo [sjednocení](https://docs-analytics-eus.azurewebsites.net/queryLanguage/query_language_unionoperator.html) dotazy příkazy nejsou podporované v Azure Sentinelu a nebudou migrována. Před provedením migrace prosím upravte tyto výstrahy.

Znovu vytvořte upozornění pomocí Azure monitoru upozornění protokolů, najdete v tématu: [Vytvoření, zobrazení a Správa upozornění protokolů pomocí Azure monitoru](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-log) pokyny o tom, jak vytvořit upozornění protokolů. Obecný přehled o upozornění protokolů ve službě Azure Monitor, klikněte na tlačítko [tady](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/alerts-unified-log).

## <a name="what-are-custom-alert-rules-in-security-center"></a>Co jsou vlastní pravidla výstrah ve službě Security Center?

Služba Security Center obsahuje sadu předdefinovaných [výstrah zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), které se aktivují v případě výskytu hrozby nebo podezřelé aktivity. V některých scénářích můžete chtít vytvořit vlastní výstrahu, která by řešila konkrétní požadavky vašeho prostředí.

Vlastní pravidla výstrah ve službě Security Center umožňují definovat nové výstrahy zabezpečení na základě již shromážděných dat z vašeho prostředí. Můžete vytvořit dotazy a výsledky těchto dotazů použít jako kritéria pro vlastní pravidlo, které se provede po splnění těchto kritérií. K vytváření vlastních dotazů můžete použít také události zabezpečení počítačů, protokoly partnerských řešení zabezpečení nebo data ingestovaná pomocí rozhraní API.

> [!NOTE]
> Vlastní výstrahy nejsou podporované ve službě Security Center [pročtěte](security-center-investigation.md).
>
>

## <a name="how-to-create-a-custom-alert-rule-in-security-center"></a>Jak vytvořit vlastní pravidlo výstrah ve službě Security Center?

Otevřete řídicí panel **Security Center** a podle těchto kroků vytvořte vlastní pravidlo výstrah:

1.  V levém podokně v části **Detekce** klikněte na **Vlastní pravidla výstrah (Preview)**.
2.  Na stránce **Security Center – Vlastní pravidla výstrah (Preview)** klikněte na **Nové vlastní pravidlo výstrah**.

    ![Vlastní výstraha](./media/security-center-custom-alert/security-center-custom-alert-fig1.png)

3.  Zobrazí se stránka Vytvořit vlastní pravidlo výstrah, která obsahuje následující možnosti:

    ![Vytvořit](./media/security-center-custom-alert/security-center-custom-alert-fig2.png)

4.  Do pole **Název** zadejte název tohoto vlastního pravidla.
5.  Do pole **Popis** zadejte stručný popis vyjadřující záměr tohoto pravidla.
6.  V poli **Závažnost** vyberte úroveň závažnosti (Vysoká, Střední, Nízká) podle svých potřeb.
7.  V poli **Předplatné** vyberte předplatné, na které se toto pravidlo vztahuje.
8.  Vyberte pracovní prostor, který chcete monitorovat pomocí tohoto pravidla v **pracovní prostor** pole a **vyhledávací dotaz** pole dotazu, který chcete použít k získání výsledků.

    > [!NOTE]
    > V pracovním prostoru, kterou Pokud chcete ukládat vlastní upozornění potřebujete oprávnění zapisovat.
    >
    >

    Výsledek tohoto dotazu aktivuje výstrahu. Všimněte si, že při zadání platného dotazu se v pravém rohu tohoto pole zobrazí zelená značka zaškrtnutí:

    ![Dotaz](./media/security-center-custom-alert/security-center-custom-alert-fig3.png)

10. V poli **Období** vyberte časové období, během kterého se výše uvedený dotaz bude provádět. Všimněte si, že v závislosti na vybraném časovém období se změní výsledek vyhledávání v dolní části tohoto pole.

    ![Období](./media/security-center-custom-alert/security-center-custom-alert-fig4.png)

11. V poli **Vyhodnocování** vyberte frekvenci, s jakou se má toto pravidlo vyhodnocovat a provádět.
12. V poli **Počet výsledků** vyberte operátor (větší než nebo menší než).
13. Do pole **Prahová hodnota** zadejte číslo, které bude sloužit jako referenční hodnota pro operátor vybraný dříve.
14. Pokud chcete nastavit dobu čekání před tím, než služba Security Center pro toto pravidlo odešle další výstrahu, povolte možnost **Potlačit výstrahy**.
15. Kliknutím na **OK** vytváření dokončete.

Po dokončení vytváření se nové pravidlo výstrah zobrazí v seznamu vlastních pravidel výstrah. Jakmile budou splněny podmínky tohoto pravidla, aktivuje se nová výstraha, kterou můžete zobrazit na řídicím panelu **Výstrahy zabezpečení**.

![Výstrahy](./media/security-center-custom-alert/security-center-custom-alert-fig5.png)

Všimněte si, že výstraha vlastního pravidla obsahuje parametry (vyhledávací dotaz, prahová hodnota atd.) vytvořené při vytváření tohoto pravidla.

## <a name="see-also"></a>Další informace najdete v tématech
V tomto dokumentu jste zjistili, jak vytvořit vlastní pravidlo výstrah v Azure Security Center. Pokud se o službě Azure Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Zjistěte, jak spravovat výstrahy a reagovat na incidenty zabezpečení ve službě Security Center.
* [Monitorování stavu zabezpečení ve službě Azure Security Center](security-center-monitoring.md). Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Principy výstrah zabezpečení ve službě Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Seznamte se s dalšími typy výstrah zabezpečení.
* [Průvodce odstraňováním potíží pro službu Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Zjistěte, jak řešit běžné problémy ve službě Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md). Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/). Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

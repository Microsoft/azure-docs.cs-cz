---
title: Kurz reakce na výstrahy – Azure Security Center
description: V tomto kurzu se dozvíte, jak řadit výstrahy zabezpečení a určit & rozsah výstrahy hlavní příčinu.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 41f96202bffc0169be9705cd7e67c1eedbecbc05
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453440"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Kurz: Určování priorit podle dostupnosti zdrojů, prošetřování a reakce na upozornění zabezpečení
Security Center průběžně analyzuje úlohy hybridního cloudu pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na potenciálně škodlivé aktivity v cloudových prostředcích. Do Security Center také můžete integrovat výstrahy z dalších produktů a služeb zabezpečení. Po vyvolání výstrahy je potřeba k prozkoumání a nápravě potenciálního problému se zabezpečením použít akci SWIFT. 

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Analyzovat výstrahy zabezpečení
> * Prověření výstrahy zabezpečení a určení hlavní příčiny
> * Reakce na výstrahu zabezpečení a zmírnění této hlavní příčiny

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky
Pro krokování s funkcemi popsanými v tomto kurzu musíte mít povolený Azure Defender. Azure Defender si můžete vyzkoušet bez jakýchkoli nákladů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Rychlý Start [Začínáme s Security Center](security-center-get-started.md) vás provede upgradem.


## <a name="triage-security-alerts"></a>Analyzovat výstrahy zabezpečení
Security Center poskytuje jednotné zobrazení všech výstrah zabezpečení. Výstrahy zabezpečení jsou seřazené podle závažnosti zjištěné aktivity. 

Třídění upozornění ze stránky **výstrahy zabezpečení** :

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Seznam výstrah zabezpečení Azure Security Center":::

Pomocí této stránky můžete zkontrolovat aktivní výstrahy zabezpečení ve vašem prostředí, abyste se rozhodli, která výstraha se má prozkoumat jako první.

Při třídění výstrah zabezpečení Stanovujte výstrahy na základě závažnosti výstrahy tím, že nejprve vyřešíte výstrahy s vyšší závažností. Další informace o závažnosti výstrah v tématu [jak jsou výstrahy klasifikovány?](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> Azure Security Center můžete propojit s nejoblíbenějšími řešeními SIEM, včetně Azure Sentinel a využívat výstrahy z vašeho nástroje podle vlastního výběru. Další informace o [výstrahách služby streamování pro Siem, společnosti nebo řešení správy služeb](export-to-siem.md)v oddělení IT.


## <a name="investigate-a-security-alert"></a>Prozkoumat výstrahu zabezpečení

Až se rozhodnete, která výstraha se má prozkoumat jako první:

1. Vyberte požadovanou výstrahu.
1. Na stránce Přehled výstrah vyberte prostředek, který chcete prozkoumat jako první.
1. Zahajte šetření v levém podokně, ve kterém se zobrazí informace vysoké úrovně výstrahy zabezpečení.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Levé podokno stránky s podrobnostmi výstrahy, které zvýrazňuje informace vysoké úrovně":::

    V tomto podokně se zobrazí:
    - Závažnost výstrahy, stav a čas aktivity
    - Popis, který vysvětluje přesnost zjištěné aktivity
    - Ovlivněné prostředky
    - Záměr dezaktivačního řetězu aktivity v MITRE ATT&CK matice

1. Podrobnější informace, které vám pomůžou prozkoumat podezřelou aktivitu, najdete na kartě **Podrobnosti výstrahy** .

1. Po kontrole informací na této stránce je možné, že budete mít k dispozici dostatek, abyste mohli pokračovat v reakci. Pokud potřebujete další podrobnosti:

    - Obraťte se na vlastníka prostředku a ověřte, zda je zjištěná aktivita falešně pozitivní.
    - Prozkoumat nezpracované protokoly generované prostředkem napadení

## <a name="respond-to-a-security-alert"></a>Reakce na výstrahu zabezpečení
Po prozkoumání výstrahy a porozumění jejímu oboru můžete reagovat na výstrahu zabezpečení z Azure Security Center:

1.  Otevřete kartu **provést akci** a zobrazte doporučené odpovědi.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Karta akce přijmout výstrahy zabezpečení" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Přečtěte si část **zmírnění hrozby** pro kroky ručního šetření potřebné k vyřešení problému.
1.  Pokud chcete posílit svoje prostředky a zabránit budoucím útokům tohoto druhu, opravte doporučení zabezpečení v části **prevence budoucích útoků** .
1.  Chcete-li aktivovat aplikaci logiky pomocí automatizovaných kroků odezvy, použijte část **Automatická odpověď triggeru** .
1.  Pokud zjištěná aktivita *není* škodlivá, můžete potlačit budoucí výstrahy tohoto druhu pomocí oddílu **potlačit podobné výstrahy** .

1.  Až se dokončí šetření výstrahy a odpovídajícím způsobem odpovíte, změňte stav na **neúspěšné**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Nastavení stavu výstrahy":::

    Tím se výstraha z hlavního seznamu výstrah odstraní. Pomocí filtru ze stránky seznam výstrah můžete zobrazit všechny výstrahy se stavem **odeslané** .

1.  Doporučujeme vám poskytnout zpětnou vazbu k této výstraze Microsoftu:
    1. Označení výstrahy jako **užitečné** nebo **neužitečné**.
    1. Vyberte důvod a přidejte komentář.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Poskytněte Microsoftu zpětnou vazbu na užitečnost výstrahy.":::

    > [!TIP]
    > Prověříme váš názor, abychom vylepšili naše algoritmy a zajistili lepší výstrahy zabezpečení.

## <a name="end-the-tutorial"></a>Ukončení kurzu

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, nechte Automatické zřizování a povolený program Azure Defender. 

Pokud pokračovat nechcete, nebo pokud chcete zakázat některou z těchto funkcí:

1. Vraťte se do hlavní nabídky Security Center a vyberte **ceny a nastavení**.
1. Vyberte příslušné předplatné.
1. Pokud chcete downgradovat, vyberte **Azure Defender vypnuto**.
1. Pokud chcete zakázat Automatické zřizování, otevřete stránku **shromažďování dat** a nastavte **Automatické zřizování** na **vypnuto**.
1. Vyberte **Uložit**.

>[!NOTE]
> Zakázání automatického zřizování neodebere agenta Log Analytics z virtuálních počítačů Azure, které už mají agenta. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se dozvěděli o Security Center funkcích, které se mají použít při reakci na výstrahu zabezpečení. Související materiál najdete v těchto tématech:

- [Reakce na upozornění Azure Defenderu pro Key Vault](defender-for-key-vault-usage.md)
- [Upozornění zabezpečení – Referenční příručka](alerts-reference.md)
- [Úvod do Azure Defenderu](azure-defender.md)

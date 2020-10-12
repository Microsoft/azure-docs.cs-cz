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
ms.date: 09/30/2020
ms.author: memildin
ms.openlocfilehash: a04f94f5ebc7c1fdaf7b95e71dc8549e19863b39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614132"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Kurz: třídění, zkoumání a reakce na výstrahy zabezpečení
Security Center nepřetržitě analyzuje vaše hybridní cloudové úlohy pomocí pokročilých analytických algoritmů a inteligentního rozpoznávání hrozeb a upozorní vás na škodlivé aktivity. Můžete také integrovat výstrahy z dalších produktů a služeb zabezpečení do Security Center a vytvářet vlastní výstrahy na základě vlastních ukazatelů nebo zdrojů informací. Jakmile je vygenerována výstraha, je pro prozkoumání a nápravu nutná rychlá akce. 

V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Analyzovat výstrahy zabezpečení
> * Prověření výstrahy zabezpečení a určení hlavní příčiny
> * Reakce na výstrahu zabezpečení a zmírnění této hlavní příčiny

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky
Pro krokování s funkcemi popsanými v tomto kurzu musíte mít povolený Azure Defender. Azure Defender si můžete vyzkoušet bez jakýchkoli nákladů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Rychlý Start [Začínáme s Security Center](security-center-get-started.md) vás provede upgradem.


## <a name="triage-security-alerts"></a>Analyzovat výstrahy zabezpečení
Security Center poskytuje jednotné zobrazení všech výstrah zabezpečení. Výstrahy zabezpečení jsou seřazené podle závažnosti zjištěné aktivity. 

Třídění upozornění ze stránky **výstrahy zabezpečení** :

:::image type="content" source="./media/tutorial-security-incident/alerts-list.png" alt-text="Stránka seznam výstrah zabezpečení" lightbox="./media/tutorial-security-incident/alerts-list.png":::

Pomocí této stránky můžete zkontrolovat aktivní výstrahy zabezpečení ve vašem prostředí, abyste se rozhodli, která výstraha se má prozkoumat jako první.

Při třídění výstrah zabezpečení Stanovujte výstrahy na základě závažnosti výstrahy tím, že nejprve vyřešíte výstrahy s vyšší závažností. Další informace o závažnosti výstrah v tématu [jak jsou výstrahy klasifikovány?](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> Azure Security Center můžete propojit s nejoblíbenějšími řešeními SIEM, včetně Azure Sentinel a využívat výstrahy z vašeho nástroje podle vlastního výběru. Další informace najdete v [exportu výstrah do Siem](continuous-export.md).


## <a name="investigate-a-security-alert"></a>Prozkoumat výstrahu zabezpečení

Až se rozhodnete, která výstraha se má prozkoumat jako první:

1. Vyberte požadovanou výstrahu.
1. Na stránce Přehled výstrah vyberte prostředek, který chcete prozkoumat jako první.
1. Zahajte šetření v levém podokně, ve kterém se zobrazí informace vysoké úrovně výstrahy zabezpečení.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="Stránka seznam výstrah zabezpečení":::

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

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Stránka seznam výstrah zabezpečení" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Přečtěte si část **zmírnění hrozby** pro kroky ručního šetření potřebné k vyřešení problému.
1.  Pokud chcete posílit svoje prostředky a zabránit budoucím útokům tohoto druhu, opravte doporučení zabezpečení v části **prevence budoucích útoků** .
1.  Chcete-li aktivovat aplikaci logiky pomocí automatizovaných kroků odezvy, použijte část **Automatická odpověď triggeru** .
1.  Pokud zjištěná aktivita *není* škodlivá, můžete potlačit budoucí výstrahy tohoto druhu pomocí oddílu **potlačit podobné výstrahy** .

1.  Až se dokončí šetření výstrahy a odpovídajícím způsobem odpovíte, změňte stav na **neúspěšné**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Stránka seznam výstrah zabezpečení":::

    Tím se výstraha z hlavního seznamu výstrah odstraní. Pomocí filtru ze stránky seznam výstrah můžete zobrazit všechny výstrahy se stavem **odeslané** .

1.  Volitelně můžete poskytnout zpětnou vazbu k výstraze Microsoftu:
    1. Označení výstrahy jako **užitečné** nebo **neužitečné** a poskytování
    1. Vyberte důvod a přidejte komentář.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Stránka seznam výstrah zabezpečení":::

    > [!TIP]
    > Prověříme váš názor, abychom vylepšili naše algoritmy a zajistili lepší výstrahy zabezpečení.

## <a name="end-the-tutorial"></a>Ukončení kurzu

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud máte v úmyslu pokračovat v práci s dalšími rychlými starty a kurzy, nechte Automatické zřizování a povolený program Azure Defender. 

Pokud pokračovat nechcete, nebo pokud chcete zakázat některou z těchto funkcí:

1. Vraťte se do hlavní nabídky Security Center a vyberte **ceny a nastavení**.
1. Vyberte příslušné předplatné.
1. Pokud chcete downgradovat, vyberte **Azure Defender vypnuto**.
1. Pokud chcete zakázat Automatické zřizování, otevřete stránku **shromažďování dat** a nastavte **Automatické zřizování** na **vypnuto**.
4. Vyberte **Uložit**.

>[!NOTE]
> Zakázání automatického zřizování neodebere agenta Log Analytics z virtuálních počítačů Azure, které už mají agenta. Vypnutí automatického zřizování omezí sledování zabezpečení pro vaše prostředky.
>

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se dozvěděli o Security Center funkcích, které se mají použít při reakci na výstrahu zabezpečení. Související materiál najdete v těchto tématech:

- [Reakce na upozornění Azure Defenderu pro Key Vault](defender-for-key-vault-usage.md)
- [Výstrahy zabezpečení – referenční příručka](alerts-reference.md)
- [Seznámení s Azure Defenderem](azure-defender.md)
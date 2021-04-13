---
title: Glosář programu pro zařízení s certifikací Azure
description: Seznam běžných výrazů používaných v programu zařízení s certifikací Azure
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/03/2021
ms.custom: template-concept
ms.openlocfilehash: f58c29c6bd2c22f37d2285ac6e93c6f54e47c4e0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305085"
---
# <a name="azure-certified-device-program-glossary"></a>Glosář programu pro zařízení s certifikací Azure

Tato příručka poskytuje definice termínů, které se běžně používají v programu a portálu pro zařízení s certifikací Azure. Vyjasnění procesu certifikace najdete v tomto glosáři. Pro usnadnění práce se tento Glosář zařadí do kategorií na základě hlavních konceptů certifikace, na které můžete mít dotazy.

## <a name="device-class"></a>Třída zařízení

Při vytváření projektu certifikace budete požádáni o zadání třídy zařízení. Třída zařízení odkazuje na faktor nebo klasifikaci formuláře, které nejlépe představují vaše zařízení.

- **brána**

    Zařízení, které zpracovává data odesílaná prostřednictvím sítě IoT.

- **Elektrické**

    Zařízení, které detekuje a reaguje na změny prostředí a připojuje se k bránám za účelem zpracování změn.

- **Další**

    Pokud vyberete jiný, přidejte do svých slov popis vaší třídy zařízení. V průběhu času můžeme do tohoto seznamu dál přidávat nové hodnoty, zejména v případě, že budeme dál sledovat názory našich partnerů.

## <a name="device-type"></a>Typ zařízení

V rámci procesu certifikace budete také požádáni o výběr jednoho ze dvou typů zařízení.

- **Dokončený produkt**

    Zařízení, které je připraveno k nasazení v produkčním prostředí a připraveno. Obvykle se jedná o faktor kompletního formuláře se firmwarem a operačním systémem. Můžou to být zařízení pro obecné účely, která vyžadují další přizpůsobení nebo specializovaná zařízení, která nevyžadují žádné úpravy pro použití.
- **Sada dev SDK připravená pro řešení**

    Vývojová sada obsahující hardware a software, který je ideální pro snadné vytváření prototypů, obvykle ne v dokončeném faktoru. Obvykle obsahuje vzorový kód a kurzy pro povolení rychlého vytváření prototypů.

## <a name="component-type"></a>Typ součásti

V části Podrobnosti o zařízení popíšete své zařízení seznamem součástí podle typu součásti. Další pokyny k součástem můžete zobrazit [zde](./how-to-using-the-components-feature.md).

- **Produkt připravený pro zákazníky**

    Reprezentace komponenty celkového nebo primárního zařízení. To se liší od **dokončeného produktu**, což je klasifikace zařízení jako připravená pro použití zákazníkem bez dalšího vývoje. Dokončený produkt bude obsahovat součást produktu připraveného pro zákazníky.
- **Vývojová deska**

    Integrovanou nebo odnímatelnou desku s mikroprocesorem pro snadné přizpůsobení.
- **Okraj**

    Integrovaný nebo Oddálený doplněk k produktu (například příslušenství). Obvykle se jedná o zařízení, která se připojují k hlavnímu zařízení, ale nepřispívají k primárním funkcím zařízení. Místo toho poskytuje další funkce. Paměť, paměť RAM, úložiště, pevné disky a procesory nejsou považovány za periferní zařízení (místo toho by měly být uvedeny v části Další specifikace produktu připravené pro zákazníka).
- **Systém – modul**  

    Okruh na úrovni panelu, který integruje systémovou funkci v jednom modulu.

## <a name="component-attachment-method"></a>Metoda přílohy součásti

Metoda přílohy součásti je další podrobnost komponenty, která informuje zákazníka o tom, jak je komponenta integrovaná do celkového produktu.

- **Integrované**
 
    Odkazuje na to, že součást zařízení je součástí hlavního skříně produktu. Nejčastěji se odkazuje na typ periferní komponenty, který se ze zařízení nedá odebrat.  
    Příklad: integrovaný senzor teploty uvnitř skříně brány.

- **Oddělen**

    Odkazuje na to, že součást **není součástí** hlavního skříně produktu.  
    Příklad: externí senzor teploty, který musí být připojen k zařízení.


## <a name="next-steps"></a>Další kroky

Tento Glosář vás provede procesem certifikace projektu na portálu. Teď jste připraveni začít svůj projekt.
- [Kurz: vytvoření projektu](./tutorial-01-creating-your-project.md)
